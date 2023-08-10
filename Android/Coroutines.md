## Scope Cancellation vs Scope's Children Cancellation

**1. Scope Cancellation(scope.cancel())**
Once the scope itself is cancelled, we cannot launch new coroutines with that scope and the launch of any new coroutine with the cancelled scope will fail silently without any logs. 

**2. Scope's Children Cancellation (scope.coroutineScope.cancelChildren())**
Cancelling a scope's children cancels all the jobs attached to the scope but keeps the scope active, thereby allowing us to relaunch any other coroutines in the future.


## Structured Concurrency
1. Ability to "pause" the code execution and "wait" for all the concurrent flows which can be traced back to a specific "ancestor" to complete.
2. Structured concurrency  allows for more straightforward implementation of some concurrent flows

## Design Rule For Concurrent Code In Android
1. UseCases/Interactors classes can encapsulate multithreaded flows
2. Use DI to injects usecases class


## Dispatchers
Responsible for dispatching coroutines to the underlying thread
(Thread are independent concurrent tasks)

**1. Main Dispatcher**
2 types - 
1. Main - Similar to `Handler(Looper.getMainLooper()).post()
2. Main.Immediate - Similar to `Activity::runOnUiThread()

**2. Default**
Thread pool with max thread = Max(2, NUM_OF_CPU_CORES)
Used for computation intensive tasks

**3. IO**
Thread pool with max thread = Max(64, NUM_OF_CPU_CORES), can be increase by adjusting system properties.
Used of IO tasks which are mostly "waiting"


## NonCancellable
A non cancellable job that is always be active, designed to be used with `withContext(NonCancellable) {}`

This object shouldn't be used with coroutine builders like `launch` and `async` because

1. **Child job not cancelled**: If the parent coroutine is cancelled, typically by calling `cancel()` on its `Job`, the child coroutine launched with `NonCancellable` will not be cancelled automatically. This means that even if the parent coroutine is cancelled, the child coroutine will continue executing until completion.

2. **Parent not waiting for child**: Normally, when a parent coroutine is cancelled, it waits for its child coroutines to complete before fully cancelling itself. However, when a child coroutine is launched with `NonCancellable`, the parent coroutine does not wait for the child coroutine to complete. It proceeds with its cancellation process immediately.

4. **Parent not cancelled on child crash**: If an unhandled exception occurs in the child coroutine launched with `NonCancellable`, it crashes. In this case, the parent coroutine is not cancelled automatically. The crash in the child coroutine does not propagate to the parent, potentially leaving the parent unaware of the child's failure.


## Coroutine Mechanics

`CoroutineScope` and `CoroutineContext` and `Job`
![[Pasted image 20230709105618.png]]
A `CoroutineScope` has a `job` by default, we can also provide a `job` externally

Each `CoroutineScope` has a context.

`CoroutineScope` is a wrapper around `CoroutineContext

Any changes to a `CoroutineContext` creates a new  `CoroutineScope`


## Parallel Decomposition

It is a technique which allows to take the advantage of concurrent execution to speed up the algorithm.

```
fun wrongUseOfCoroutines() = runBlocking {  
    var totalIterations = 0  
    withContext(Dispatchers.Default) {  
        for (duration in 1..5) {  
            launch {  
                val startTimeNano = System.nanoTime()  
                var iterations = 0  
                while (System.nanoTime() < startTimeNano + (duration * 10f.pow(9))) {  
                    iterations++  
                }  
                totalIterations += iterations  
            }  
        }  
    }  
    println("total iterations: $totalIterations")  
}
```

```
fun atomicityProblemDemo() = runBlocking {  
    var totalIterations = 0  
    withContext(Dispatchers.Default) {  
        repeat(100) {  
            launch {  
                delay(100)  
                totalIterations++  
            }  
        }    }    println("total iterations: $totalIterations")  
}
```

`totalIterations` is a shared mutable state in both the examples, but this state is not thread safe, both these test cases are flaky in nature.

Couple of ways to deal with this:
1. Using Atomicity
2. Using mutex to acquire a lock
3. Returning the value


# Exceptions Handling

CancellationException 

1. Used by coroutine framework for cancellation
2. These exception should only be caught to be used as an addition source for debugging.


If a coroutine encounters exception other than `CancellationException` it cancels the parent with that exception, this is used to provide a stable hierarchies for structured concurrency. eg.
```
@Test  
fun uncaughtExceptionInConcurrentCoroutines() {  
    runBlocking {  
        val scopeJob = Job()  
        val scope = CoroutineScope(scopeJob + Dispatchers.Default)  
        val job1 = scope.launch {  
            delay(100)  
            println("inside coroutine")  
        }  
        val job2 = scope.launch {  
            delay(50)  
            throw RuntimeException()  
        }  
        joinAll(job1, job2)  
        println("scopeJob: $scopeJob")  
        println("job1: $job1")  
        println("job2: $job2")  
    }  
    Thread.sleep(100)  
    println("test completed")  
}
```

Logs
![[Pasted image 20230723112231.png]]


When a coroutine is cancelled using `Job.cancel()`, it terminates, but it does not cancel its parent. eg.
```
@Test  
fun uncaughtExceptionInConcurrentCoroutines() {  
    runBlocking {  
        val scopeJob = Job()  
        val scope = CoroutineScope(scopeJob + Dispatchers.Default)  
        val job1 = scope.launch {  
            delay(100)  
            println("inside coroutine")  
        }  
        val job2 = scope.launch {  
            delay(50)  
            throw CancellationException()  
        }  
        joinAll(job1, job2)  
        println("scopeJob: $scopeJob")  
        println("job1: $job1")  
        println("job2: $job2")  
    }  
    Thread.sleep(100)  
    println("test completed")  
}
```

Logs
![[Pasted image 20230723112113.png]]



SuperVisorJob

One way to prevent cascading of exception in coroutines is to use a `supervisorJob()`

```
@Test  
fun uncaughtExceptionInConcurrentCoroutinesWithSupervisorJob() {  
    runBlocking {  
        val scopeJob = SupervisorJob()  
        val scope = CoroutineScope(scopeJob + Dispatchers.Default)  
        val job1 = scope.launch {  
            delay(100)  
            println("inside coroutine")  
        }  
        val job2 = scope.launch {  
            delay(50)  
            throw RuntimeException()  
        }  
        joinAll(job1, job2)  
        println("scopeJob: $scopeJob")  
        println("job1: $job1")  
        println("job2: $job2")  
    }  
    Thread.sleep(100)  
    println("test completed")  
}
```

Logs
![[Pasted image 20230723114326.png]]


Best way to handle exception is to catch them locally and prevent their propagation to the coroutine scope.