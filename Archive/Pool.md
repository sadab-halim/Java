# Pools in Java

## Fixed ThreadPoolExecutor
`newFixedThreadPool` method creates a thread pool executed with a fixed no of threads.

| Info | Info |
|------|------|
| Min and Max Pool | Same |
| Queue Size | Unbounded Queue |
| Thread Alive when Idle | Yes |
| When to use | Exact Info, how many Async task is needed |
| Disadvantage | Not good when workload is heavy, as it will lead to limited concurrency |

//fixed thread pool executor
ExecutorService poolExecutor1 Executors.newFixedThreadPool(5);
poolExecutor1.submit(() -> "this is the async task");

## Cached ThreadPoolExecutor
`newCachedThreadPool` method creates a thread pool that creates a new thread as needed (dynamically).

| Info | Info |
|------|------|
| Min and Max Pool | Min: 0 <br> Max: Integer.MAX_VALUE |
| Queue Size | Blocking Queue with Size 0 |
| Thread Alive when Idle | 60 secs |
| When to use | Good for handling burst of short lived tasks |
| Disadvantage | Many long lived tasks and submitted rapidly, ThreadPool can create so many threads which might lead to increase memory usage. |

//cached thread pool executor
ExecutorService poolExecutor = Executors.newCachedThreadPool();
poolExecutor.submit(() -> "this is async task");

## Single Thread Executor
`newSingleThreadExecutor` creates Executor with just single Worker thread.

| Info                   | Info                                    |
|------------------------|-----------------------------------------|
| Min and Max Pool       | Min: 1 <br> Max: 1                      |
| Queue Size             | Unblocking Queue                        |
| Thread Alive when Idle | Yes                                     |
| When to use            | When need to process tasks sequentially |
| Disadvantage           | No concurrency at all                   |

## WorkStealing Pool Executor
- It creates a Fork-Join Pool Executor
- Number of threads depends upon the available processors or we can specify in the parameter.
- There are 2 queues:
  - Submission Queue
  - Work-Stealing Queue for each thread (it's a Deuque)
- Steps:
  - If all threads are busy, task would be placed in "Submission Queue". (or whenever we call submit() method, tasks goes into submission queue only)
  - Lets say task1 picked by ThreadA. And if 2 subtasks created using fork() method. Subtask1 will be executed by ThreadA only and Subtask2 is put into the ThreadA work-stealing queue.
  - If any other thread becomes free, and there is no task in Submission queue, it can "STEAL" the task from the other thread work-stealing queue.
- Task can be split into multiple small sub-tasks. For that task should extend:
  - RecursiveTask
  - RecursiveAction
- We can create Fork-Join Pool using "newWorkStealingPool" method in ExecutorService. OR by calling ForkJoinPool.commonPool() method.

```java
public class ExecutorsUtilityExample {
    public static void main (String args[]) {
        ForkJoinPool pool = ForkJoinPool.commonPool();
        Future<Integer> futureObj = pool.submit(new ComputeSumTask(0, 100));
        try {
            System.out.println(futureObj.get());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

```java
public class ComputeSumTask extends RecursiveTask<Integer> {
    private Integer start;
    private Integer end;
    
    ComputeSumTask(Integer start, Integer end) {
        this.start = start;
        this.end = end;
    }
    
    protected Integer compute() {
        if(end - start <= 4) {
            int totalSum = 0;
            for(int i = start; i <= end; i++) {
                totalSum += i;
            }
            return totalSum;
        } else {
            //split the task
            int mid = (start + end) / 2;
            ComputeSumTask leftTask = new ComputeSumTask(start, mid);
            ComputeSumTask rightTask = new ComputeSumTask(mid + 1, end);
            
            //fork the subtasks for parallel execution
            leftTask.fork();
            rightTask.fork();
            
            //combine the results of subtasks
            int leftResult = leftTask.join();
            int rightResult = rightTask.join();
        
            //combine the results
            return leftResult + rightResult;
        }
    }
}
```

## ScheduledThreadPool Executor

### shutdown vs await Termination vs shutdownNow
#### shutdown
- Initiates orderly shutdown of the ExecutorService.
- After calling 'Shutdown', Executor will not accept new task submission.
- Already submitted tasks, will continue to execute.

#### AwaitTermination
- It's an Optional functionality. Return true/false.
- It is used after calling 'Shutdown' method.
- Blocks calling thread for specific timeout period, and wait for ExecutorService shutdown.
- Return true, if ExecutorService gets shutdown within specific timeout else false.

#### shutdownNow
- Best effort attempt to stoop/interrupt the actively executing tasks
- Halt the processing of tasks which are waiting
- Return the list of tasks which are awaiting execution.

### Scenario 1: Task submission after Shutdown
```java
public static void main (String args[]) {
    ExecutorService poolObj = Executors.newFixedThreadPool(5);
    poolObj.submit(() -> {
        System.out.println("Thread going to start its work.");
    });
    
    poolObj.shutdown();
    
    poolObj.submit(() -> {
       System.out.println("Thread going to start its work"); 
    });
}
```

### Scenario 2: Shutdown do not impact the already submitted task
```java
public static void main (String args[]) {
    ExecutorService poolExecutorObj = Executors.newFixedThreadPool(5);
    poolExecutorObj.submit(() -> {
       try {
           Thread.sleep(5000);
       } catch (Exception e) {
           e.printStackTrace();
       }
       System.out.println("New Task");
    });
    
    poolExecutorObj.shutdown();
    System.out.println("Main thread unblocked and finished processing");
}
```

### Scenario 3: usage of 'awaitTermination'
```java
public static void main (String[] args) {
    ExecutorService poolExecutorObj = Executors.newFixedThreadPool(5);
    poolExecutorObj.submit(() -> {
       try {
           Thread.sleep(6000);
       } catch (Exception e) {
           e.printStackTrace();
       }
       System.out.println("New Task");
    });
    
    poolExecutorObj.shutdown();
    try {
        boolean isExecutorTerminated = poolExecutorObj.awaitTermination(3, TimeUnit.SECONDS);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

## ScheduledThreadPoolExecutor
- helps to schedule the tasks
- ThreadPoolExecutor --> ScheduledThreadPoolExecutor

| S.No. | Method Name | Description |
|-------|-------------|-------------|
| 1     | schedule (Runnable command, long delay, TimeUnit unit) | Schedules a runnable task after specific delay. <br> Only one time task runs. |
| 2     | schedule (Callable<V> callable, long delay, TimeUnit unit) | Schedules a callable task after specific delay. <br> Only one time task runs. |
| 3     | scheduleAtFixedRate (Runnable command, long initalDelay, long period, TimeUnit unit) | Schedules a Runnable task for repeated execution with fixed rate. <br> We can use cancel method to stop this repeated task. <br> Also lets say, if thread1 is taking too much time to compelte the task and next task is ready to run, till previous task will not get completed, new task cannot be start (it will wait in queue). |
| 4     | scheduleWithFixedDelay (Runnable command, long initialDelay, long delay, TimeUnit unit) | Schedules a Runnable task for repeated execution with a fixed delay (means next task delay counter start only after previous one task completed) |




