Decomposition of an an application's logic into multiple concurrent tasks.
### CPU Operation Basics

- **Single Task Operating System** - Executes the machine code of one single ask until it's completion. Single task takes full ownership of the CPU. Eg. MS DOS
- **Cooperative Multitasking Systems** - Individual executing tasks release CPU resources by "yielding", allowing others tasks to be executed, with the intention to be resumed later. This allows us to concurrent executions of multiple tasks. One caveat is a single misbehaving task can claim the CPU resources for long time, thus making the entire system unresponsive. Eg. Windows 3.1
- **Preemptive Multitasking System** - Operating system manages the CPU resources by allocating CPU time slices to different tasks, according to it's own scheduling algorithm. Preemptive multitasking allows concurrent execution of multiple tasks and guarantees system responsiveness. 

**Preemptive** - The act of temporarily interrupting an executing task, with the intention of resuming it at a later time.

### Concurrency vs Parallelism

Concurrency - Multiple task appears to make progress in the same time period.
Parallelism - Multiple task make progress in the same time instant.

Parallelism is a special case of concurrency 
All parallel task are concurrent but not all concurrent tasks are parallel in nature

- **Threads**
    
    Thread are heavy-weight objects that require a significant amount of resources to create and manage. They are also preemptively scheduled by the operating system, which can lead to context switches that can be expensive.
    
- **Coroutines**
    
    Coroutines are lightweight objects that require a much smaller amount of resources to create and manage. They are also cooperatively scheduled by the programmer, which means that they can yield control voluntarily, avoiding context switches.
    

As a result, coroutines are generally preferred over threads for tasks that are computationally expensive or that require high throughput. However, threads can be useful for tasks that need to be highly responsive or that require access to shared data structures.

- **Stackless**:
    
    Stackless programming is a style of concurrent or parallel programming in which each task or coroutine doesn't have its own dedicated stack. Instead, it relies on a shared stack or a smaller, fixed-size stack. The lack of a separate stack for each task reduces memory overhead, making it possible to have a large number of tasks or coroutines running concurrently.
    
    Stackless programming can be more efficient in terms of memory usage and context switching because there is no need to allocate and manage separate stacks for each task. However, it may also limit the ability to handle deep recursion in individual tasks.
    
- **Stackful**:
    
    Stackful programming, on the other hand, uses a separate stack for each task or thread. Each task has its own stack memory for function calls and local variables. This allows for deep recursion within individual tasks, as each task can have a dedicated stack with a larger size.
    
    Stackful programming tends to be more straightforward for developers to work with because it mimics the traditional function call stack behaviour. However, it can be less memory-efficient when dealing with a large number of concurrent tasks or threads since each stack consumes additional memory.
    

In summary, the choice between stackless and stackful programming models depends on the specific requirements of your application. Stackless models are often used in situations where you need to support a large number of lightweight tasks or coroutines with minimal memory overhead, while stackful models are more suitable when you need to handle deep recursion within individual tasks or threads. The choice between the two depends on your specific use case and performance considerations.

### Android Process And Threads

**Process** - Each running app gets it's own task and this task is associated with an isolated execution env (sandbox), together they constitute a process.
This sandboxing doesn't allow one app to access the env of the other app. This ensures reliability and security.

**Threads** 
- Multitasking systems allowed multiple programs to run concurrently in separate processes.
- It became evident that a single process can benefit from being decomposed into multiple concurrent tasks.
- In Android these tasks within a process is called a thread.
- Threads share the same execution env of the parent process, allowing easy exchange of data and resources.

## java.lang.Thread Class

2 ways to instantiate a new Thread:

via Inheritance
``` java
class MyThreas extends Thread {
	private final int mSeed;

	MyThread(int mSeed) {
		this.mSeed = mSeed;
	}

	@Override
	public void run() {
		// logic
	}
}

Thread thread = new MyThread(10);

thread.start();
```

via Composition
``` java
Runnable runnable = new Runnable() {
	@Override
	public void run() {
		int seed = 10;
	}
};

Thread thread = new Thread(runnable);

thread.start();
```

### Garbage Collector

System process which automatically reclaimed memory by discarding objects that are no longer in use.

**Object Reachability**

![[Whiteboard 24-Sept-2023_1-1.jpg]]
Eg: 

:MainActivity -> :MyObject1 -> MyObject2

- is MyObject2 reachable?
- is MyObject2 a root - NO
- is MyObject2 referenced - YES
- is MyObject1 a root? - NO
- is MyObject1 referenced - YES
- is MainActivity reachable?

Therefore is MainActivity is reachable then MyObject1 and MyObject2 are also reachable.

GC can handle circular references as well, they are considered not reachable.

###  Thread Termination
- return from run() after successful execution.
- return from run() in response to an internal error.
- return from run() in response to an externally set flag.
- return from run() in response to interruption.


### Thread Safety Concerns
- **Visibility** - Multiple threads access shared state/variable.
- **Atomicity** - When multiple threads perform operation on some state with scheduled operation.
	- Thread Synchronisation
	- Immutability
- **Happens Before Rule** - 

**Final** - final variables are thread safe according to java memory model.
