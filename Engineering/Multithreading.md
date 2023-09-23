### Concurrency vs Parallelism

Concurrency - Multiple task appears to make progress in the same time period.
Parallelism - Multiple task make progress in the same time instant.

Parallelism is a special case of concurrency 
All parallel task are concurrent but not all concurrent tasks are parallel in nature

- **Threads**
    
    are heavy-weight objects that require a significant amount of resources to create and manage. They are also preemptively scheduled by the operating system, which can lead to context switches that can be expensive.
    
- **Coroutines**
    
    are lightweight objects that require a much smaller amount of resources to create and manage. They are also cooperatively scheduled by the programmer, which means that they can yield control voluntarily, avoiding context switches.
    

As a result, coroutines are generally preferred over threads for tasks that are computationally expensive or that require high throughput. However, threads can be useful for tasks that need to be highly responsive or that require access to shared data structures.