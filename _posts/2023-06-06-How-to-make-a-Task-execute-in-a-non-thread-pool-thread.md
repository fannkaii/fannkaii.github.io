---
layout: post
title: "如何让Task在非线程池线程中执行？"
author: "Kai"
header-style: text
tags:
  - C#
  - 多线程
---

转自：https://www.cnblogs.com/artech/p/DedicatedThreadTaskScheduler.html

Task承载的操作需要被调度才能被执行，由于.NET默认采用基于线程池的调度器，所以Task默认在线程池线程中执行。但是有的操作并不适合使用线程池，比如我们在一个ASP.NET Core应用中承载了一些需要长时间执行的后台操作，由于线程池被用来处理HTTP请求，如果这些后台操作也使用线程池来调度，就会造成相互影响。在这种情况下，使用独立的一个或者多个线程来执行这些后台操作可能是一个更好的选择。

> 一、基于线程池的调度
二、TaskCreationOptions.LongRunning
三、换成异步操作呢？
四、换种写法呢？
五、调用Wait方法
六、自定义TaskScheduler
七、独立线程池

# 1. 基于线程池的调度
我们通过如下这个简单的程序来验证默认基于线程池的Task调度。我们调用Task类型的静态属性Factory返回一个TaskFactory对象，并调用其StartNew方法启动一个Task对象，这个Task指向的Run方法会在一个循环中调用Do方法。Do方法使用自旋等待的方式模拟一段耗时2秒的操作，并在控制台输出当前线程的IsThreadPoolThread属性确定是否是线程池线程。
```csharp
Task.Factory.StartNew(Run);
Console.Read();

void Run()
{
    while (true)
    {
        Do();
    }
}

void  Do()
{
    var end = DateTime.UtcNow.AddSeconds(2);
    SpinWait.SpinUntil(() => DateTimeOffset.UtcNow > end);
    var isThreadPoolThread = Thread.CurrentThread.IsThreadPoolThread;
    Console.WriteLine($"[{DateTimeOffset.Now}]Is thread pool thread: {isThreadPoolThread}");
}
```
通过如下所示的输出结果，我们得到了答案：利用TaskFactory创建的Task在默认情况下确实是通过线程池的形式被调度的。
![20240131172244](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240131172244.png)

# 2. TaskCreationOptions.LongRunning
很明显，上述Run方法是一个需要永久执行的LongRunning操作，并不适合使用线程池来执行，实际上TaskFactory在设计的时候就考虑到了这一点，我们利用它创建一个Task的时候可以指定对应的TaskCreationOptions选项，其中一个选项就是LongRuning。我们通过如下的方式修改了上面这段程序，在调用StartNew方法时指定了这个选项。
```csharp
Task.Factory.StartNew(Run, TaskCreationOptions.LongRunning);
Console.Read();

void Run()
{
    while (true)
    {
        Do();
    }
}

void  Do()
{
    var end = DateTime.UtcNow.AddSeconds(2);
    SpinWait.SpinUntil(() => DateTimeOffset.UtcNow > end);
    var isThreadPoolThread = Thread.CurrentThread.IsThreadPoolThread;
    Console.WriteLine($"[{DateTimeOffset.Now}]Is thread pool thread: {isThreadPoolThread}");
}
```
再次执行我们的程序，就会通过如下的输出结果看到Do方法将不会在线程池线程中执行了。
![20240131172256](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240131172256.png)

# 3. 换成异步操作呢？
由于LongRunning操作经常会涉及IO操作，所以我们执行方法经常会写成异步的形式。如下所示的代码中，我们将Do方法替换成DoAsync，将2秒的自旋等待替换成Task.Delay。由于DoAsync写成了异步的形式，Run也换成对应的RunAsync。
```csharp
Task.Factory.StartNew(RunAsync, TaskCreationOptions.LongRunning);
Console.Read();

async Task RunAsync()
{
    while (true)
    {
       await DoAsync();
    }
}

async Task  DoAsync()
{
    await Task.Delay(2000);
    var isThreadPoolThread = Thread.CurrentThread.IsThreadPoolThread;
    Console.WriteLine($"[{DateTimeOffset.Now}]Is thread pool thread: {isThreadPoolThread}");
}
```
再次启动程序后，我们发现又切换成了线程池调度了。为什么会这样呢？其实很好理解，由于原来返回void的Run方法被替换成了返回Task的RunAsync，传入StartNew方法表示执行操作的委托类型从Action切换成了Func<Task>，虽然我们指定了LongRunning选项，但是StartNew方法只是采用这种模式执行Func<Task>这个委托对象而已，而这个委托在遇到await的时候就返回了。至于返回的Task对象，还是按照默认的方式进行调度执行。

![20240131172320](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240131172320.png)

# 4. 换种写法呢？
有人说，上面我们使用的是一个方法来表示作为参数的委托对象，如果我们按照如下的方式使用基于async/await的Lambda表达式呢？实际上这样的Lambda表达式就是Func<Task>的另一种编程方式而已。
```csharp
Task.Factory.StartNew(async () => { while (true) await DoAsync();}, TaskCreationOptions.LongRunning);
Console.Read();

async Task  DoAsync()
{
    await Task.Delay(2000);
    var isThreadPoolThread = Thread.CurrentThread.IsThreadPoolThread;
    Console.WriteLine($"[{DateTimeOffset.Now}]Is thread pool thread: {isThreadPoolThread}");
}
```

# 5. 调用Wait方法
其实这个问题很好解决，按照如下的方式将DoAsync方法换成同步形式的Do，将基于await的等待替换成针对Wait方法的调用就可以了。我想当你接触Task的时候，就有很多人不断提醒你，谨慎使用Wait方法，因为它会阻塞当前线程。实际上对于我们的当前的应用场景，调用Wait方法才是正确的选择，因为我们的初衷就是使用一个独立的线程以独占的方式来执行后台操作。
```csharp
Task.Factory.StartNew(() => { while (true) Do(); }, TaskCreationOptions.LongRunning);
Console.Read();

void  Do()
{
    Task.Delay(2000).Wait();
    var isThreadPoolThread = Thread.CurrentThread.IsThreadPoolThread;
    Console.WriteLine($"[{DateTimeOffset.Now}]Is thread pool thread: {isThreadPoolThread}");
}
```

# 6. 自定义TaskScheduler
既然针对线程池的使用是“Task调度”导致的，我们自然可以通过重写TaskScheduler的方式来解决这个问题。如下这个自定义的DedicatedThreadTaskScheduler 会采用独立的线程来执行被调度的Task，线程的数量可以参数来指定。
```csharp
internal sealed class DedicatedThreadTaskScheduler : TaskScheduler
{
    private readonly BlockingCollection<Task> _tasks = new();
    private readonly Thread[] _threads;
    protected override IEnumerable<Task>? GetScheduledTasks() => _tasks;
    protected override void QueueTask(Task task) => _tasks.Add(task);
    protected override bool TryExecuteTaskInline(Task task, bool taskWasPreviouslyQueued) => false;
    public DedicatedThreadTaskScheduler(int threadCount)
    {
        _threads = new Thread[threadCount];
        for (int index = 0; index < threadCount; index++)
        {
            _threads[index] = new Thread(_ =>
            {
                while (true)
                {
                    TryExecuteTask(_tasks.Take());
                }
            });
        }
        Array.ForEach(_threads, it => it.Start());
    }
}
```
我们演示实例中Run/Do方法再次还原成如下所示的纯异步模式的RunAsync/DoAsync，并在调用StartNew方法的时候创建一个DedicatedThreadTaskScheduler对象作为最后一个参数。
```csharp
Task.Factory.StartNew(RunAsync, CancellationToken.None, TaskCreationOptions.LongRunning, new DedicatedThreadTaskScheduler(1));
Console.Read();

async Task RunAsync()
{
    while (true)
    {
        await DoAsync();
    }
}

async Task DoAsync()
{
    await Task.Delay(2000);
    var isThreadPoolThread = Thread.CurrentThread.IsThreadPoolThread;
    Console.WriteLine($"[{DateTimeOffset.Now}]Is thread pool thread: {isThreadPoolThread}");
}
```

由于创建的Task将会使用指定的DedicatedThreadTaskScheduler 对象来调度，DoAsync方法自然就不会在线程池线程中执行了。
![20240131172336](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240131172336.png)

# 7. 独立线程池
.NET提供的线程池是一个全局共享的线程池，而我们定义的DedicatedThreadTaskScheduler相当于创建了一个独立的线程池，对象池的效果可以通过如下这个简单的程序展现出来。
```csharp
Task.Factory.StartNew(()=> Task.WhenAll( Enumerable.Range(1,6).Select(it=>DoAsync(it))),
        CancellationToken.None,
        TaskCreationOptions.None,
        new DedicatedThreadTaskScheduler(2));

async Task DoAsync(int index)
{
    await Task.Yield();
    Console.WriteLine($"[{DateTimeOffset.Now.ToString("hh:MM:ss")}]Task {index} is executed in thread {Environment.CurrentManagedThreadId}");
    var endTime = DateTime.UtcNow.AddSeconds(4);
    SpinWait.SpinUntil(() => DateTime.UtcNow > endTime);
    await Task.Delay(1000);
}
Console.ReadLine();
```

如上面的代码片段所示，异步方法DoAsync利用自旋等待模拟了一段耗时4秒的操作，通过调用Task.Delay方法模拟了一段耗时1秒的IO操作。我们在其中输出了任务开始执行的时间和当前线程ID。在调用的StartNew方法中，我们调用这个DoAsync方法创建了6个Task，这些Task交给创建的DedicatedThreadTaskScheduler进行调度。我们为这个DedicatedThreadTaskScheduler指定的线程数量为2。从如下所示的输出结果可以看出，6个操作确实在两个线程中执行的。
![20240131172345](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240131172345.png)
