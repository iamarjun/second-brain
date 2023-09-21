- Run your work **off of the main thread** (this assumes you are extending the `Worker` class, as shown above in `UploadWorker`).
- **Guarantee** your work will execute (it won’t forget to run your work, even if you restart the device or the app exits).

 Behind the scenes, WorkManager includes the following parts:

- **Internal TaskExecutor**: A single threaded `[Executor](https://developer.android.com/reference/java/util/concurrent/Executor)` that handles all the requests to enqueue work. If you’re not familiar with `Executors` you can read more about them [here](https://developer.android.com/reference/java/util/concurrent/Executor).
- **WorkManager database**: A local database that tracks all of the information and statuses of all of your work. This includes things like the current state of the work, the inputs and outputs to and from the work and any constraints on the work. This database is what enables WorkManager to guarantee your work will finish — if your user’s device restarts and work gets interrupted, all of the details of the work can be pulled from the database and the work can be restarted when the device boots up again.
- **WorkerFactory****: A default factory that creates instances of your `Worker`s. We’ll cover why and how to configure this in a future blog post.
- **Default Executor****: A default executor that runs your work unless you specify otherwise. This ensures that by default, your work runs synchronously and off of the main thread.

** These are parts that can be overridden to have different behaviours.

![](https://miro.medium.com/v2/resize:fit:1400/1*1Wbw_Hi1u5SJ1QtJYemV4g.png)

Credit: [Working with WorkManager](https://youtu.be/83a4rYXsDs0) Presentation Android Developer Summit 2018

When you enqueue your `WorkRequest`:

1. The Internal TaskExecutor immediately saves your `WorkRequest` info to the WorkManager database.
2. Later, when the `Constraints` for the `WorkRequest` are met (which could be immediately), the Internal TaskExecutor tells the `WorkerFactory` to create a `Worker`.
3. Then the default `Executor` calls your `Worker`’s `doWork()` method **off of the main thread.**