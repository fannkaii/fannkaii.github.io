---
layout: post
title: "转：Quartz.Net详解（3.X）上"
author: "Kai"
header-style: text
tags:
  - Quartz
---

转自：https://juejin.cn/post/7136928350919983134

# 1. 概述
## 1.1 背景
业务中总是会有需要定时执行的任务，我们可以用timer实现最简单的定时需求，也可以借助Quartz.NET框架实现复杂定时任务的功能
```csharp
//2秒后每隔3秒执行一次，传入参数"1"
Timer timer = new Timer((n) =>
{
    Console.WriteLine("我是定时器中的业务逻辑{0}", n);
}, "1", 2000, 3000);
```

System.Timers.Timer 类具有“内置”定时器功能，为什么有人会使用 Quartz 而不是这些标准功能？主要有几个原因：
- 定时器没有持久化机制。
- 定时器具有不灵活的调度（只能设置开始时间和重复间隔，不能基于日期、时间等）。
- 定时器不使用线程池（每个定时器一个线程）
- 定时器没有真正的管理方案——你必须编写自己的机制来记忆、组织和检索任务名称等。

## 1.2 概述
- Quartz.NET 是一个功能齐全的开源作业调度系统，可用于从最小的应用程序到大型企业系统。
- Quartz 非常灵活，包含多个可以单独或一起使用的使用范例，以实现您想要的行为，并使您能够以对您的项目最“自然”的方式编写代码。
- Quartz 非常轻巧，需要很少的设置/配置 - 如果您的需求相对基本，它实际上可以“开箱即用”使用。
- Quartz 是容错的，并且可以在系统重新启动之间保留（“记住”）您计划的作业。
- 尽管 Quartz 对于在给定的时间表上简单地运行某些系统进程非常有用，但是当您学习如何使用它来驱动应用程序的业务流程流时，可以充分发挥 Quartz 的潜力。
- 官网：https://www.quartz-scheduler.net/
- 源码：https://github.com/quartznet/quartznet
- 文档地址：https://www.quartz-scheduler.net/documentation/
- API文档地址：https://quartznet.sourceforge.io/apidoc/3.0/html/

## 1.3 特性
- 运行时环境：可以嵌入在应用程序中运行，甚至可以作为独立程序集群实例化（具有负载平衡和故障转移功能）
- 作业调度：作业被安排在给定触发器发生时运行，触发器支持多种调度选项
- 作业执行：作业可以是任何实现简单 IJob 接口的 .NET 类，从而为作业可以执行的工作留下无限可能
- 工作持久化：可以实现作业存储以提供各种存储作业的机制，开箱即用地支持内存和多个关系数据库
- 故障转移：内置支持负载平衡您的工作和优雅的故障转移
- 监听器和插件：应用程序可以通过实现一个或多个侦听器接口来捕获调度事件以监视或控制作业/触发行为。

## 1.4 Quartz五大元素
- Scheduler：调度器，quartz工作时的独立容器
- Trigger：触发器，定义了调度任务的时间规则—定义什么时间去执行
- Job：调度的任务---具体要做的什么事儿---刷数据库的数据
- ThreadPool：线程池（不是clr中的线程池），任务最终交给线程池中的线程执行
- JobStore：RAWStore和DbStore两种，job和trigger都存放在JobStore中

## 1.5 工作流程
- scheduler是quartz的独立运行容器，trigger和job都可以注册在scheduler容器中，一个job可以有多个触发器，而一个触发器只能属于一个job。
- Quartz中有一个调度线程QuartzSchedulerThread，调度线程可以找到将要被触发的trigger和job，然后在ThreadPool中获取一个线程来执行这个job。
- JobStore主要作用是存放job和trigger的信息。

# 2. 快速开始
## 2.1 引入nuget包
```
Quartz  基于3.4.0 Net6
```

## 2.2 创建一个作业类，继承 `IJob` 接口
```csharp
public class MyJob : IJob
{
    public async Task Execute(IJobExecutionContext context)
    {
        Console.WriteLine($"【任务执行】：{DateTime.Now}");
        Console.WriteLine($"【触发时间】：{context.ScheduledFireTimeUtc?.LocalDateTime}");
        Console.WriteLine($"【下次触发时间】：{context.NextFireTimeUtc?.LocalDateTime}"); 
        await Task.CompletedTask;
    }
}
```

## 2.3 创建调度器，作业，触发器
```csharp
//实例化调度器
IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler().Result;

//开启调度器
scheduler.Start();

//创建一个作业
IJobDetail job1 = JobBuilder.Create<MyJob>()
    .WithIdentity("job1", "groupa")//名称，分组
    .Build();

//创建一个触发器
ITrigger trigger1 = TriggerBuilder.Create()
    .WithIdentity("trigger1", "groupa")//名称，分组
    .StartNow()//从启动的时候开始执行
    .WithSimpleSchedule(b =>
    {
        b.WithIntervalInSeconds(2)//2秒执行一次
         .WithRepeatCount(3);//重复执行3+1次
    })
    .Build();

//把作业，触发器加入调度器
scheduler.ScheduleJob(job1, trigger1);
```

## 2.4 运行结果
```
【任务执行】：2022/8/11 13:47:02
【触发时间】：2022/8/11 13:47:02
【下次触发时间】：2022/8/11 13:47:04
【任务执行】：2022/8/11 13:47:04
【触发时间】：2022/8/11 13:47:04
【下次触发时间】：2022/8/11 13:47:06
【任务执行】：2022/8/11 13:47:06
【触发时间】：2022/8/11 13:47:06
【下次触发时间】：2022/8/11 13:47:08
【任务执行】：2022/8/11 13:47:08
【触发时间】：2022/8/11 13:47:08
【下次触发时间】：
```

# 3. 时间类型
## 3.1 时间类型
- DateTime：表示的时区有限，国内采用这个时间。
- DateTimeOffset：可以表示任何时区，通过偏移量来控制。**（Quartz中提供DateBuilder类来创建DateTimeOffset类型）**

## 3.2 两种类型相互转换
- DateTime→DateTimeOffset 利用DateTimeOffset的构造函数
- DateTimeOffset→DateTime 利用Convert.ToDateTime方法

```csharp
DateTime date1 = DateTime.Parse("2022-01-01 12:00:00");
DateTimeOffset date2 = DateBuilder.DateOf(12, 00, 00, 1, 1, 2022);
//DateTime 转换成 DateTimeOffset
DateTimeOffset date3 = new DateTimeOffset(date1, TimeSpan.Zero);
//DateTimeOffset 转换成 DateTime
DateTime date4 = Convert.ToDateTime(date2);
```

## 3.3 一些常用时间表示API
```csharp
//表示固定时间
DateTime date5 = DateTime.Parse("2022-01-01 12:00:00");
DateTime date6 = new DateTime(2022, 1, 1, 12, 0, 0);
DateTimeOffset date7 = DateBuilder.DateOf(12, 00, 00, 1, 1, 2022);
//2022-01-01 12:00:00  往后增加6天5小时4分3秒
DateTimeOffset date8 = new DateTimeOffset(12, 00, 00, 1, 1, 2022, new TimeSpan(6,5,4,3));
//今天的3点2分1秒
DateTimeOffset date9=DateBuilder.TodayAt(3,2,1);
//明天的3点2分1秒
DateTimeOffset date10 = DateBuilder.TomorrowAt(3, 2, 1);

//四舍五入
DateTimeOffset date11 = DateBuilder.TodayAt(6, 5, 4);
DateTimeOffset date12 = DateBuilder.EvenHourDate(date11);           //小时维度上入：7:00:00
DateTimeOffset date13 = DateBuilder.EvenHourDateBefore(date11);     //小时维度上舍：6:00:00

//时间周期
//第一个参数传入null以当前时间为依据，假设当前时间为：14:43:29
//第一个参数传入时间以传入时间为基准
//第二个参数传入10表示以整10分钟作为一个周期，10,20,30,40,50,60
//第二个参数传入20表示以整20分钟作为一个周期，20,40,60
DateTimeOffset date14 = DateBuilder.NextGivenMinuteDate(null, 10);                              //14:50:00
DateTimeOffset date15 = DateBuilder.NextGivenMinuteDate(null, 20);                              //15:00:00
DateTimeOffset date16 = DateBuilder.NextGivenMinuteDate(DateBuilder.TodayAt(1, 45, 30), 10);    //1:50:00

//增加时间
DateTime date17 = DateTime.Now.AddYears(1);//当前时间+1年
DateTime date18 = DateTime.Now.AddMonths(1);//当前时间+1月
DateTime date19 = DateTime.Now.AddDays(1);//当前时间+1天
DateTime date20 = DateTime.Now.AddHours(1);//当前时间+1小时
DateTime date21 = DateTime.Now.AddMinutes(1);//当前时间+1分钟
DateTime date22 = DateTime.Now.AddSeconds(1);//当前时间+1秒
```

# 4. 调度器Scheduler
## 4.1 创建方式
- 直接通过StdSchedulerFactory类的GetDefaultScheduler方法创建
- 先创建StdSchedulerFactory，然后通过GetScheduler方法创建。该方式可以在实体化StdSchedulerFactory的时候配置一些额外的信息，比如：配置SimpleThreadPool的个数、RemoteScheduler的远程控制、数据库的持久化等。
- 通过SchedulerBuilder.Create()创建。
- 通过DirectSchedulerFactory创建，需要传入线程池对象和jobstore对象，配置硬编码。

```csharp
//方式1
IScheduler scheduler = StdSchedulerFactory.GetDefaultScheduler().Result;
//方式2
ISchedulerFactory schedulefactory = new StdSchedulerFactory();
IScheduler scheduler2 = schedulefactory.GetScheduler().Result;
//方式2 传入参数
NameValueCollection pars = new NameValueCollection
{
    //scheduler名字
    ["quartz.scheduler.instanceName"] = "MySchedulerAdvanced",
    //线程池个数
    ["quartz.threadPool.threadCount"] = "20"
};
ISchedulerFactory schedulefactory2 = new StdSchedulerFactory(pars);
IScheduler scheduler3 = schedulefactory2.GetScheduler().Result;
//方式3
IScheduler scheduler4 = SchedulerBuilder.Create().BuildScheduler().Result;
//方式4
var serializer = new JsonObjectSerializer();
serializer.Initialize();
JobStoreTX jobStore = new JobStoreTX
{
    DataSource = "default",
    TablePrefix = "QRTZ_",
    InstanceId = "AUTO",
    DriverDelegateType = typeof(MySQLDelegate).AssemblyQualifiedName,
    ObjectSerializer = serializer,
};
DirectSchedulerFactory.Instance.CreateScheduler("myScheduler", "AUTO", new DefaultThreadPool(), jobStore);
IScheduler scheduler5 = await SchedulerRepository.Instance.Lookup("myScheduler");
```

## 4.2 单例封装
单例封装，可以控制所有地方操作的都是同一个实例。
```csharp
public class MySchedulerFactory
{
    /// <summary>
    /// 由CLR保证，在程序第一次使用该类之前被调用，而且只调用一次
    /// </summary>
    private static IScheduler _Scheduler = StdSchedulerFactory.GetDefaultScheduler().Result;
    public static IScheduler GetScheduler()
    {
        return _Scheduler;
    }
}
```

## 4.3 常用方法
- 开启：Start
- 关闭：ShutDown
- 暂停job或Trigger：PauseAll、PauseJob、PauseJobs、PauseTrigger、PauseTriggers
- 恢复job或Trigger：ResumeAll、ResumeJob、ResumeJobs、ResumeTrigger、ResumeTriggers
- 将job和trigger加入Scheduler中：ScheduleJob
- 添加Job：AddJob

```csharp
//常用API
//实例化调度器
IScheduler scheduler =MySchedulerFactory.GetScheduler();

//开启调度器
scheduler.Start();

//创建一个作业
IJobDetail job1 = JobBuilder.Create<MyJob>()
    .WithIdentity("job1", "groupa")//名称，分组
    .Build();

//创建一个触发器
ITrigger trigger1 = TriggerBuilder.Create()
    .WithIdentity("trigger1", "groupa")//名称，分组
    .StartNow()//从启动的时候开始执行
    .WithSimpleSchedule(b =>
    {
        b.WithIntervalInSeconds(2)//2秒执行一次
         .WithRepeatCount(3);//重复执行3+1次
    })
    .Build();

//把作业，触发器加入调度器
scheduler.ScheduleJob(job1, trigger1);

//添加作业
scheduler.AddJob(job1,true);

//暂停作业
scheduler.PauseJobs(GroupMatcher<JobKey>.GroupEquals("groupa"));

//恢复作业
scheduler.ResumeJobs(GroupMatcher<JobKey>.GroupEquals("groupa"));

//停止调度
scheduler.Shutdown();
```

## 4.4 日志记录
从 Quartz.NET 3.1 开始可以配置`Microsoft.Extensions.Logging.Abstractions` 用来代替 LibLog。

Quartz.NET 使用`LibLog` 库来满足其日志记录需求。Quartz 不会产生太多的日志信息，通常只是初始化期间的一些信息，然后只有在 Jobs 执行时记录严重问题的消息。为了调整日志设置（例如输出量和输出去向），需要配置选择的日志框架，因为 LibLog 主要将工作委托给更成熟的日志框架，如 log4net， serilog 等。

**手动配置**
```csharp
// obtain your logger factory, for example from IServiceProvider
ILoggerFactory loggerFactory = ...;

// Quartz 3.1
Quartz.LogContext.SetCurrentLogProvider(loggerFactory);

// Quartz 3.2 onwards
Quartz.Logging.LogContext.SetCurrentLogProvider(loggerFactory);
```

**使用 Microsoft DI 集成进行配置**
```csharp
services.AddQuartz(q =>
{
    // this automatically registers the Microsoft Logging
});
```

# 5. 作业Job
## 5.1 几个重要类型
- JobBuilder：用来创建JobDetail。
- IJob：具体作业任务需要实现该接口，并实现里面的方法
- IJobDetail：用来定义工作实例，添加到调度器中运行

您可以创建单个作业类实现`IJob`接口，并通过创建多个 JobDetails 实例，每个都有自己的一组属性和 JobDataMap，并将它们全部添加到调度程序。

如下，SalesReportJob实现IJob接口，创建了两个JobDetails实例reportForJoe，reportForMike，两个任务运行互不影响。
```csharp
IJobDetail reportForJoe = JobBuilder.Create<SalesReportJob>()
	.WithIdentity("myJob", "group1")
    .UsingJobData("salename", "Joe")
	.Build();

IJobDetail reportForMike = JobBuilder.Create<SalesReportJob>()
	.WithIdentity("myJob", "group1")
    .UsingJobData("salename", "Mike")
	.Build();
```

## 5.2 IJobDetail两种创建方式
- Create的泛型方式：写起来代码简洁方便。
- 反射 + OfType的方式：用于实现动态绑定，通过程序集的反射。
```csharp
//方式1
IJobDetail job1 = JobBuilder.Create<MyJob>()
    .WithIdentity("job1", "groupa")//名称，分组
    .Build();

//方式2
var type = Assembly.Load("MyQuartZ.Net.QuartZJob").CreateInstance("MyJob");
IJobDetail job2 = JobBuilder.Create().OfType(type.GetType())
    .WithIdentity("job2", "groupa")//名称，分组
    .Build();
```

## 5.3 常用方法
- UsingJobData：给Job添加一些附加值，存储在JobDataMap里，可以在具体的Job中获取。（通过context.JobDetail.JobDataMap获取）
- StoreDurably：让该job持久化，不被销毁.(默认情况下为false，即job没有对应的trigger的话，job就被销毁)
- WithIdentity：身份标记，给job起个名称，便于和Trigger关联的时候使用.
- WithDescription：用来对job进行描述，并没有什么实际作用

## 5.4 JobDataMap
`JobDataMap`可用于保存任何数量的（可序列化的）对象，您希望在作业实例执行时可以使用这些对象。`JobDataMap`是`IDictionary`接口的一个实现，并且增加了一些方便的方法来存储和检索原始类型的数据。

以下是在将作业添加到调度程序之前将数据放入 JobDataMap 的一些快速片段：
```csharp
IJobDetail job = JobBuilder.Create<DumbJob>()
	.WithIdentity("myJob", "group1") 
	.UsingJobData("jobSays", "Hello World!")
	.UsingJobData("myFloatValue", 3.141f)
	.Build();
```

下面是一个在作业执行期间从 JobDataMap 获取数据的快速示例：
```csharp
public class DumbJob : IJob
{
	public async Task Execute(IJobExecutionContext context)
	{
		JobKey key = context.JobDetail.Key;
		JobDataMap dataMap = context.JobDetail.JobDataMap;
		string jobSays = dataMap.GetString("jobSays");
		float myFloatValue = dataMap.GetFloat("myFloatValue");
		await Console.Error.WriteLineAsync("Instance " + key + " of DumbJob says: " + jobSays + ", and val is: " + myFloatValue);
	}
}
```

## 5.5 防止作业完成后被删除
设置属性 JobDetail.Durable = true - 指示 Quartz 在 Job 成为“孤儿”时不要删除 Job（当 Job 不再有 Trigger 引用它时）。

## 5.6 作业状态和并发
`[DisallowConcurrentExecution]`是一个可以添加到 Job 类的特性，它告诉 Quartz 不要同时执行给定作业定义（引用给定作业类）的多个实例。请注意措辞，在上一节的示例中，如果“SalesReportJob”具有此属性，则在给定时间只能执行一个“SalesReportForJoe”实例，但它可以与“SalesReportForMike”实例同时执行。

`[PersistJobDataAfterExecution]`是一个可以添加到 Job 类中的特性，它告诉 Quartz 在 Execute() 方法成功完成后（不抛出异常）更新 JobDetail 的 JobDataMap 的存储副本，以便下一次执行相同的作业（JobDetail）接收更新的值而不是最初存储的值。与`[DisallowConcurrentExecution]`属性一样，这适用于作业定义实例，而不是作业类实例。

如果您使用`[PersistJobDataAfterExecution]`特性，您应该强烈考虑使用`[DisallowConcurrentExecution]`特性，以避免在同时执行同一作业 (JobDetail) 的两个实例时可能会留下存储的数据的混淆（竞争条件）。

定义job类，标记特性，让job运行时间超过trigger的间隔时间
```csharp
[DisallowConcurrentExecution, PersistJobDataAfterExecution]
public class ConcurrentJob : IJob
{
    public async Task Execute(IJobExecutionContext context)
    {
        Thread.Sleep(3000);
        JobDataMap dataMap = context.JobDetail.JobDataMap;
        string testdata = dataMap.GetString("testdata");
        dataMap.Put("testdata", testdata+"1");
        await Console.Error.WriteLineAsync($"testdata：{testdata} time:{DateTime.Now.ToString()}");
    }
}
```

定义调度器，触发器

```csharp
//实例化调度器工厂
ISchedulerFactory schedulefactory = new StdSchedulerFactory();
//实例化调度器
IScheduler scheduler = schedulefactory.GetScheduler().Result;
scheduler.Start();

//创建一个作业
IJobDetail job1 = JobBuilder.Create<ConcurrentJob>()
    .WithIdentity("job1", "groupa")//名称，分组
    .UsingJobData("testdata", "Hello World!")
    .Build();

//创建一个触发器
ITrigger trigger1 = TriggerBuilder.Create()
    .WithIdentity("trigger1", "groupa")//名称，分组
    .StartNow()//从启动的时候开始执行
    .WithSimpleSchedule(b =>
    {
        b.WithIntervalInSeconds(2)//2秒执行一次
         .WithRepeatCount(3);//重复执行3+1次
    })
    .Build();

//把作业，触发器加入调度器
scheduler.ScheduleJob(job1, trigger1);
```

运行效果
```
testdata：Hello World! time:2022/8/11 19:31:45
testdata：Hello World!1 time:2022/8/11 19:31:48
testdata：Hello World!11 time:2022/8/11 19:31:51
testdata：Hello World!111 time:2022/8/11 19:31:54
```

## 5.7 作业执行异常
您应该从 execute 方法中抛出的唯一异常类型是 JobExecutionException。因此，您通常应该使用“try-catch”块包装执行方法的全部内容。

## 5.8 停止正在执行的作业
IJobExecutionContext见CancellationToken.IsCancellationRequested

# 6. 触发器Trigger
## 6.1 几个重要的类
- TriggerBuilder：用来创建ITrigger实例
- ITrigger：触发器实例

## 6.2 常用方法
- StartNow：Trigger马上触发.
- StartAt和EndAt：设置Trigger触发的开始时间和结束时间 （省略设置开始时间的话，默认从当前时间开始执行）
- UsingJobData：给Trigger添加一些附加值（通过context.Trigger.JobDataMap获取）
- WithDescription：用来描述该触发器，并没有什么实际左右
- WithPriority：设置Trigger的优先级，默认为5，数字越大，优先级越高.（该优先级用于一个job对应多个Trigger，且Trigger的触发时间相同，优先级越大的越先执行）
- ForJob：将job和trigger进行关联，该方法有多个重载，关联后ScheduleJob方法进行调度时，只需将trigger传入进去即可

## 6.3 常用属性
- JobKey：指示触发器触发时应执行的作业的标识。
- StartTimeUtc：指示触发器的计划何时首次生效。该值是一个 DateTimeOffset 对象，用于定义给定日历日期的某个时刻。对于某些触发器类型，触发器实际上会在开始时间触发，而对于其他触发器类型，它只是标记应该开始遵循计划的时间。这意味着您可以在 1 月期间使用诸如“每月第 5 天”之类的计划存储触发器，并且如果 StartTimeUtc 属性设置为 4 月 1 日，则它将在第一次触发前几个月。
- EndTimeUtc：指示触发器的计划何时不再有效。换句话说，计划为“每月第 5 天”且结束时间为 7 月 1 日的触发器将在 6 月 5 日最后一次触发。

## 6.4 优先级
当你有许多触发器同时触发，Quartz.NET 可能没有足够的资源来同时执行。在这种情况下，您可能希望控制哪些触发器优先获得可用的工作线程。

您可以在触发器上设置优先级，调度器首先执行优先级高的Trigger，默认优先级 5。优先级允许使用任何整数值，正数或负数。数字越大表示优先级越高。

仅当触发器具有相同的触发时间时才比较优先级。计划在 10:59 触发的触发器总是会在计划在 11:00 触发的触发器之前触发。

当触发器的作业需要恢复时，它的恢复将按照与原始触发器相同的优先级进行调度。
```csharp
ITrigger trigger1 = TriggerBuilder.Create()
    .WithIdentity("trigger1", "groupa")//名称，分组
    .WithPriority(10)//设置优先级，默认5，数值越大优先级越高
    .WithSimpleSchedule(b =>
    {
        b.WithIntervalInSeconds(2);//2秒执行一次
    })
    .Build();
```

## 6.5 SimpleTrigger
用途：时、分、秒上的轮询(和timer类似)，实际开发中，该场景占绝大多数。

执行间隔：
- WithInterval(TimeSpan timeSpan)：通用的间隔执行方法
- WithIntervalInHours(int hours)：以小时为间隔单位进行执行
- WithIntervalInMinutes(int minutes)：以分钟为间隔单位进行执行
- WithIntervalInSeconds(int seconds)：以秒为间隔单位进行执行

执行时间：
- WithRepeatCount(int repeatCount)：执行多少次以后结束
- RepeatForever()：永远执行
- repeatMinutelyForever()：一分钟执行一次(永远执行)
- repeatMinutelyForever(int minutes)：每隔几分钟执行一次(永远执行)
- repeatMinutelyForTotalCount(int count, int minutes)：每隔几分钟执行一次(执行次数为count)类似的还有秒、小时。

**为特定时刻构建触发器，不重复：**
```csharp
ISimpleTrigger trigger = (ISimpleTrigger) TriggerBuilder.Create()
    .WithIdentity("trigger1", "group1")
    .StartAt(myStartTime)
    .ForJob("job1", "group1")
    .Build();
```

**为特定时刻构建触发器，然后每十秒重复十次：**
```csharp
ITrigger trigger = TriggerBuilder.Create()
    .WithIdentity("trigger3", "group1")
    .StartAt(myStartTime)
    .WithSimpleSchedule(x => x
        .WithIntervalInSeconds(10)
        .WithRepeatCount(10))
    .ForJob(myJob)
    .Build();
```

**构建一个将在未来五分钟触发一次的触发器：**
```csharp
ITrigger trigger = TriggerBuilder.Create()
    .WithIdentity("trigger5", "group1")
    .StartAt(DateBuilder.FutureDate(5, IntervalUnit.Minute))
    .ForJob(myJobKey)
    .Build();
```

**构建一个立即触发的触发器，然后每五分钟重复一次，直到 22:00 小时：**
```csharp
ITrigger trigger = TriggerBuilder.Create()
    .WithIdentity("trigger7", "group1")
    .WithSimpleSchedule(x => x
        .WithIntervalInMinutes(5)
        .RepeatForever())
    .EndAt(DateBuilder.DateOf(22, 0, 0))
    .Build();
```

**构建一个将在下一小时开始触发的触发器，然后每 2 小时重复一次，直到永远：**
```csharp
ITrigger trigger = TriggerBuilder.Create()
    .WithIdentity("trigger8")
    .StartAt(DateBuilder.EvenHourDate(null))
    .WithSimpleSchedule(x => x
        .WithIntervalInHours(2)
        .RepeatForever())
    .Build();
```

## 6.6 CronTrigger
用途：使用cron表达式代替硬编码，可以代替其他类型的trigger

**构建一个触发器，该触发器将在每天上午 8 点到下午 5 点之间每隔一分钟触发一次：**
```csharp
ITrigger trigger = TriggerBuilder.Create()
    .WithIdentity("trigger3", "group1")
    .WithCronSchedule("0 0/2 8-17 * * ?")
    .ForJob("myJob", "group1")
    .Build();
```

**构建一个每天上午 10:42 触发的触发器：**
```csharp
ITrigger trigger = TriggerBuilder.Create()
    .WithIdentity("trigger3", "group1")
    .WithSchedule(CronScheduleBuilder.DailyAtHourAndMinute(10, 42))
    .ForJob(myJobKey)
    .Build();
```

**构建一个触发器，该触发器将在周三上午 10:42 触发，在系统默认的 TimeZone 中：**
```csharp
ITrigger trigger = TriggerBuilder.Create()
    .WithIdentity("trigger3", "group1")
    .WithSchedule(CronScheduleBuilder
        .WeeklyOnDayAndHourAndMinute(DayOfWeek.Wednesday, 10, 42)
        .InTimeZone(TimeZoneInfo.FindSystemTimeZoneById("Central America Standard Time")))
    .ForJob(myJobKey)
    .Build();
```

或者 -

```csharp
ITrigger trigger = TriggerBuilder.Create()
    .WithIdentity("trigger3", "group1")
    .WithCronSchedule("0 42 10 ? * WED", x => x
        .InTimeZone(TimeZoneInfo.FindSystemTimeZoneById("Central America Standard Time")))
    .ForJob(myJobKey)
    .Build();
```

## 6.7 DailyTimeInterval
用途：解决时间点的增、减、排除。

核心函数：
- OnEveryDay：每天
- OnMondayThroughFriday:周一至周五,即工作日
- OnSaturdayAndSunday:周六至周天，即休息日
- OnDaysOfTheWeek:用数组的形式单独来指定一周中的哪几天
- StartingDailyAt：表示开始于几点 （区别于前面的StartAt）
- EndingDailyAt：表示结束于几点 （区别于前面的EndAt）

**实现周四周五的早上8点到晚上20点之间每隔2秒执行一次，一共执行4次**
```csharp
ITrigger trigger1 = TriggerBuilder.Create()
    .WithIdentity("trigger1", "groupa")
    .WithDailyTimeIntervalSchedule(x =>
    {
        //周四和周五
        x.OnDaysOfTheWeek(new DayOfWeek[] { DayOfWeek.Thursday, DayOfWeek.Friday })
         .StartingDailyAt(TimeOfDay.HourMinuteAndSecondOfDay(8, 00, 00)) //8点开始
         .EndingDailyAt(TimeOfDay.HourMinuteAndSecondOfDay(20, 00, 00))  //20点结束
         .WithIntervalInSeconds(2) //两秒执行一次，可设置时分秒维度
         .WithRepeatCount(3);  //一共执行3+1次
    })
    .Build();
```

## 6.8 CalendarInterval
用途：与日历相关

参数中的几个函数：
- WithInterval(TimeSpan timeSpan)：通用的间隔执行方法
- WithIntervalInHours(int hours)：以小时为间隔单位进行执行
- WithIntervalInMinutes(int minutes)：以分钟为间隔单位进行执行
- WithIntervalInSeconds(int seconds)：以秒为间隔单位进行执行
- WithIntervalInDays(int days)：以天为间隔单位进行执行
- WithIntervalInMonths(int months)：以月为间隔单位进行执行

```csharp
ITrigger trigger1 = TriggerBuilder.Create()
    .WithIdentity("trigger1", "groupa") //名称，分组
    .StartNow() //从启动的时候开始执行
    .WithCalendarIntervalSchedule(x =>
    {
        x.WithIntervalInSeconds(3); //每3秒执行一次
    })
    .Build();
```

## 6.9 Job和Trigger关联问题
- 1个job对应1个trigger：调用ScheduleJob(IJobDetail jobDetail, ITrigger trigger)，直接关联即可，无须做特别处理
- 1个job对应多个trigger： 将job持久化（StoreDurably(true)），然后通过AddJob方法加入调度池中，Trigger上通过ForJob方法和指定job进行关联，然后调用ScheduleJob(ITrigger trigger)方法，将trigger全部加入调度池中
- 2个job对应1个trigger (不常用)：利用JobChainingJobListener实现

```csharp
{
    //job和trigger关联问题
    //1个job对应1个trigger
    //实例化调度器
    IScheduler scheduler = MySchedulerFactory.GetScheduler();

    //开启调度器
    scheduler.Start();

    //创建一个作业
    IJobDetail job1 = JobBuilder.Create<MyJob>()
        .WithIdentity("job1", "groupa")//名称，分组
        .Build();

    //创建一个触发器
    ITrigger trigger1 = TriggerBuilder.Create()
        .WithIdentity("trigger1", "groupa")//名称，分组
        .StartNow()//从启动的时候开始执行
        .WithSimpleSchedule(b =>
        {
            b.WithIntervalInSeconds(2)//2秒执行一次
             .WithRepeatCount(3);//重复执行3+1次
        })
        .Build();

    //把作业，触发器加入调度器
    scheduler.ScheduleJob(job1, trigger1);
}

{
    //job和trigger关联问题
    //1个job对应2个trigger
    //实例化调度器
    IScheduler scheduler = MySchedulerFactory.GetScheduler();

    //开启调度器
    scheduler.Start();

    //创建一个作业
    IJobDetail job1 = JobBuilder.Create<MyJob>()
        .WithIdentity("job1", "groupa")//名称，分组
        .StoreDurably(true)//持久化job
        .Build();

    //创建一个触发器1
    ITrigger trigger1 = TriggerBuilder.Create()
        .WithIdentity("trigger1", "groupa")//名称，分组
        .StartNow()//从启动的时候开始执行
        .WithSimpleSchedule(b =>
        {
            b.WithIntervalInSeconds(2)//2秒执行一次
             .WithRepeatCount(3);//重复执行3+1次
        })
        .ForJob("job1", "groupa")//通过表名和组名进行关联
        .Build();

    //创建一个触发器2
    ITrigger trigger2 = TriggerBuilder.Create()
        .WithIdentity("trigger1", "groupa")//名称，分组
        .StartNow()//从启动的时候开始执行
        .WithSimpleSchedule(b =>
        {
            b.WithIntervalInSeconds(2)//2秒执行一次
             .WithRepeatCount(3);//重复执行3+1次
        })
        .ForJob(job1)//直接IJobDetail关联
        .Build();

    //把作业，触发器加入调度器
    scheduler.AddJob(job1, true);
    scheduler.ScheduleJob(trigger1);
    scheduler.ScheduleJob(trigger2);
}

{
    //job和trigger关联问题
    //2个job对应1个trigger
    //实例化调度器
    IScheduler scheduler = MySchedulerFactory.GetScheduler();

    //开启调度器
    scheduler.Start();

    //创建一个作业1
    IJobDetail job1 = JobBuilder.Create<MyJob>()
        .WithIdentity("job1", "groupa")//名称，分组
        .Build();

    //创建一个作业1
    IJobDetail job2 = JobBuilder.Create<MyJob>()
        .WithIdentity("job2", "groupa")//名称，分组
        .Build();

    //创建一个触发器
    ITrigger trigger1 = TriggerBuilder.Create()
        .WithIdentity("trigger1", "groupa")//名称，分组
        .StartNow()//从启动的时候开始执行
        .WithSimpleSchedule(b =>
        {
            b.WithIntervalInSeconds(2)//2秒执行一次
             .WithRepeatCount(3);//重复执行3+1次
        })
        .ForJob("job1", "groupa")//通过表名和组名进行关联
        .Build();

    //创建监听，添加到调度器
    JobChainingJobListener listener = new JobChainingJobListener("mytest");
    listener.AddJobChainLink(job1.Key, job2.Key);
    scheduler.ListenerManager.AddJobListener(listener);

    //把作业，触发器加入调度器
    scheduler.AddJob(job2, true);
    scheduler.ScheduleJob(job1, trigger1);
}
```

# 7. Canlander
Quartz.NET 日历对象实现`ICalendar`接口可以在触发器存储在调度程序中时与触发器相关联。日历对于从触发器的触发时间表中排除时间块很有用。

Quartz.NET预置了有六种，也可以自定义。日历必须通过`AddCalendar(..)`方法注册到调度程序。同一个日历实例可以与多个触发器一起使用。
```csharp
namespace Quartz
{
	public interface ICalendar
	{
		string Description { get; set; }
		ICalendar CalendarBase { set; get; }
		bool IsTimeIncluded(DateTimeOffset timeUtc);
		DateTime GetNextIncludedTimeUtc(DateTimeOffset timeUtc);
		ICalendar Clone();
	}
}
```

## 7.1 DailyCalendar：一天的某个时间段不执行
（需求：21-22点这个区间不执行）
```csharp
//实例化调度器
IScheduler scheduler = MySchedulerFactory.GetScheduler();

//开启调度器
scheduler.Start();

//实例化日历
DailyCalendar calendar = new DailyCalendar(DateBuilder.DateOf(21, 0, 0).DateTime,DateBuilder.DateOf(22, 0, 0).DateTime);

//将日历添加到调度器
scheduler.AddCalendar("mycalendar", calendar, true, true);

//创建一个作业
var job1 = JobBuilder.Create<MyJob>().Build();

//创建一个触发器
ITrigger trigger1 = TriggerBuilder.Create()
    .StartNow()//从启动的时候开始执行
    .WithSimpleSchedule(b =>
    {
        b.WithIntervalInSeconds(2).RepeatForever();//2秒执行一次
    })
    .ModifiedByCalendar("mycalendar") // but not on holidays
    .Build();

//把作业，触发器加入调度器
scheduler.ScheduleJob(job1, trigger1);
```

## 7.2 WeeklyCalendar：一个星期的某一天不执行
（需求：周五这一天不执行）
```csharp
WeeklyCalendar calendar = new WeeklyCalendar();
calendar.SetDayExcluded(DayOfWeek.Friday, true);
```

## 7.3 HolidayCalendar：当年的某一天不能执行
（需求：今年的6月16号这一天不执行）
```csharp
HolidayCalendar calendar = new HolidayCalendar();
calendar.AddExcludedDate(DateTime.Parse("06-16"));
```

## 7.4 MonthlyCalendar：一个月的某一天不能执行
（需求：每月的27号不执行）
```csharp
MonthlyCalendar calendar = new MonthlyCalendar();
calendar.SetDayExcluded(27, true);
```

## 7.5 AnnualCalendar：每年的某一天不能执行
（需求：每年的6月16号这一天不执行）
```csharp
AnnualCalendar calendar = new AnnualCalendar();
calendar.SetDayExcluded(DateTime.Parse("06-16"), true);
```

## 7.7 CronCalendar：Corn表达式来排除时间不能执行
(需求：2月27号这天不执行)
```csharp
CronCalendar calendar = new CronCalendar("* * * 27 2 ?");
scheduler.AddCalendar("mycalendar", calendar, true, true);
```

# 8. misfire指令失效策略
## 8.1 misfire简介
由于某些原因，导致作业在应该执行的时间没有执行，此时这个Trigger变为misfire，当下次调度器启动或者有可以线程时，会检查处于misfire状态的Trigger。而misfire的状态值决定了调度器如何处理这个Trigger。

不同类型触发器有不同的失效恢复策略。所有触发器不指定都是默认策略`MisfirePolicy.SmartPolicy`。

## 8.2 misfire产生的原因
- 当job达到触发时间时，所有线程都被其他job占用，没有可用线程。
- 在job需要触发的时间点，scheduler停止了（可能是意外停止的）。
- job使用了@DisallowConcurrentExecution注解，job不能并发执行，当达到下一个job执行点的时候，上一个任务还没有完成。
- job指定了过去的开始执行时间，例如当前时间是8点00分00秒，指定开始时间为7点00分00秒。

## 8.3 SimpleTrigger的Misfire策略
SimpleTrigger 的 Misfire策略常量
- MisfireInstruction.IgnoreMisfirePolicy
- MisfirePolicy.SimpleTrigger.FireNow
- MisfirePolicy.SimpleTrigger.RescheduleNowWithExistingRepeatCount
- MisfirePolicy.SimpleTrigger.RescheduleNowWithRemainingRepeatCount
- MisfirePolicy.SimpleTrigger.RescheduleNextWithRemainingCount
- MisfirePolicy.SimpleTrigger.RescheduleNextWithExistingCount

如果使用`MisfirePolicy.SmartPolicy`，SimpleTrigger 根据给定 SimpleTrigger 实例的配置和状态，在其各种 MISFIRE 指令之间动态选择。该`SimpleTrigger.UpdateAfterMisfire()`方法的文档解释了这种动态行为的确切细节。

这里分为三种情况，第一是只执行一次的job，第二是固定次数执行的job，第三是无限次数执行的job。

**只执行一次的job**

设置job只执行一次，开始时间设置设置为当前时间的前10秒，代码片段如下：
```csharp
Date next = DateUtils.addSeconds(new Date(), -10);
SimpleTrigger trigger = TriggerBuilder.newTrigger()
         .withIdentity("trigger", "g1")
         .startAt(next)
         .withSchedule(SimpleScheduleBuilder.simpleSchedule()
         .withMisfireHandlingInstructionFireNow()/*可以指定为任意一个可用的misfire策略*/)
                .build();
```

假设job设定的执行时间是8点00分00秒，而当前时间是8点00分10秒，由于misfireThreshold设置为1秒，则发生了misfire。各misfire策略如下：

| 命令 | 说明 |
| ------------ | ------------ |
| MISFIRE_INSTRUCTION_SMART_POLICY--default | 默认策略等同于MISFIRE_INSTRUCTION_FIRE_NOW。 |
| MISFIRE_INSTRUCTION_IGNORE_MISFIRE_POLICY | Quartz不会判断job发生misfire，但是当Quartz有可用资源的时候，会尽可能早的执行所有发生misfire的任务，结果等同于MISFIRE_INSTRUCTION_FIRE_NOW。 |
| withMisfireHandlingInstructionFireNow |
| MISFIRE_INSTRUCTION_FIRE_NOW | 立即执行job，即在8点00分10秒发现了misfire以后立即执行job。 |
| withMisfireHandlingInstructionNowWithExistingCount MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_EXISTING_REPEAT_COUNT | 等同于MISFIRE_INSTRUCTION_FIRE_NOW。 |
| withMisfireHandlingInstructionNowWithRemainingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_REMAINING_REPEAT_COUNT | 等同于MISFIRE_INSTRUCTION_FIRE_NOW。 |
| withMisfireHandlingInstructionNextWithExistingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_EXISTING_COUNT | 不会执行job。此命令会等待下一次执行时间来执行job，但是只执行一次的job，在发生misfire以后没有下次的执行时间，因此使用此命令不会再执行job。 |
| withMisfireHandlingInstructionNextWithRemainingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_REMAINING_COUNT | 等同于MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_EXISTING_COUNT。 |

**固定次数执行的job**
设置job开始执行时间是早上8点，执行间隔是1小时，执行次数是5次，那么job总的执行次数是6次，则计划的执行时间是8:00，9:00，10:00，11:00，12:00，13:00，代码片段如下：
```csharp
SimpleTrigger trigger = TriggerBuilder.newTrigger()
      .withIdentity("trigger1", "g1")
      .startAt(nextOne)
      .withSchedule(simpleSchedule()
            .withIntervalInHours(1)
            .withRepeatCount(5)             .withMisfireHandlingInstructionNowWithRemainingCount()/*可以指定为任意可用的策略*/)
       .build();
```

假设8:00的任务执行了，但是由于某些原因，scheduler没有执行9:00和10:00的任务，在10:15分的时候scheduler发现job有两次没有执行，这两次的延迟执行时间分别是1小时15分和15分，都大于设置的misfireThreshold=1秒，因此发生了两次misfire。各misfire策略如下：

| 命令 | 说明 |
| ------------ | ------------ |
| MISFIRE_INSTRUCTION_SMART_POLICY--default | 默认执行策略，在固定次数执行的情况下，等同于MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_EXISTING_REPEAT_COUNT |
| MISFIRE_INSTRUCTION_IGNORE_MISFIRE_POLICY | Quartz不会判断发生misfire，在Quartz资源可用时会尽可能早的执行所有发生misfire的任务。 |
| 例如：Quartz会在10:15执行9:00和10:00的任务，然后按照原计划继续执行剩下的任务。最后任务执行完成时间还是13:00。 |
| withMisfireHandlingInstructionFireNow |
| MISFIRE_INSTRUCTION_FIRE_NOW | 等同于MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_REMAINING_REPEAT_COUNT。 |
| withMisfireHandlingInstructionNowWithExistingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_EXISTING_REPEAT_COUNT | 立即执行第一个发生misfire的任务，并且修改startTime为当前时间，然后按照设定的间隔时间执行下一次任务，直到所有的任务执行完成，此命令不会遗漏任务的执行次数。 |
| 例如：10:15会立即执行9:00的任务，startTime修改为10:15，然后后续的任务执行时间为,11:15,12:15,13:15,14:15，也就是说任务完成时间延迟到了14:15，但是任务的执行次数还是总共的6次。 |
| withMisfireHandlingInstructionNowWithRemainingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_REMAINING_REPEAT_COUNT | 立即执行第一个发生misfire的任务，并且修改startTime为当前时间，然后按照设定的间隔时间执行下一个任务，直到所有剩余任务执行完成，此命令会忽略已经发生misfire的任务（第一个misfire任务除外，因为会被立即执行），继续执行剩余的正常任务。 |
| 例如：10:15会立即执行9:00的任务，并且修改startTime为10:15，然后Quartz会忽略10:00发生的misfire的任务，然后后续的执行时间为：11:15,12:15,13:15，由于10:00的任务被忽略了，因此总的执行次数实际上是5次。 |
| withMisfireHandlingInstructionNextWithExistingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_EXISTING_COUNT | 不会立即执行任务，会等到下一次的计划执行时间开始执行，然后按照设定的间隔时间执行直到执行到计划的任务结束时间。 |
| 这个地方需要注意一下，不要被命令的名字所迷惑，第一眼印象可能觉得这个命令会把已经misfire的任务也执行了，而且好多博文也是这么讲解的，实际上并没有，我也是在自己测试的时候发现的，其实这个命令在发现存在misfire以后，后续并没有再执行发生misfire的任务，而是继续执行剩下的任务，直到结束时间，因此此命令与MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_REMAINING_COUNT的执行结果相同，至于原因后面会讲。 |
| 例如：10:15发现9:00和10:00发生了misfire，并不会立即执行，由于原计划的下一次执行时间是11:00，因此Quartz会等到11:00执行任务，然后在原计划的13:00执行最后一个任务结束，因此实际上总的执行次数是4次。 |
| withMisfireHandlingInstructionNextWithRemainingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_REMAINING_COUNT | 不会立即执行任务，会等到下一次计划执行时间开始执行，忽略已经发生了misfire的任务，然后按照设定的间隔时间执行直到计划的任务结束时间。 |
| 例如：10:15发现9:00和10:00发生了misfire，并不会立即执行，忽略掉发生misfire的9:00和10:00的任务，按照计划在11:00执行任务，直到13:00执行最后一个任务结束，因此总的执行次数是4次。 |

**无限次数执行的job**
设定一个job开始执行时间是早上8点，执行间隔是1小时，无限执行次数，代码片段如下：
```csharp
SimpleTrigger trigger = TriggerBuilder.newTrigger()
      .withIdentity("trigger", "g")
      .startAt(next) 
      .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        .withIntervalInHours(1)
                        .repeatForever())
      .build();
```

假设8:00的任务执行了，但是由于某些原因，scheduler没有执行9:00和10:00的任务，在10:15分的时候scheduler发现job有两次没有执行，这两次的延迟执行时间分别是1小时15分和15分，都大于设置的misfireThreshold=1秒，因此发生了两次misfire。各misfire策略如下：

| 命令 | 说明 |
| ------------ | ------------ |
| MISFIRE_INSTRUCTION_SMART_POLICY--default | 等同于MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_REMAINING_COUNT。 |
| MISFIRE_INSTRUCTION_IGNORE_MISFIRE_POLICY | Quartz不会判断发生misfire，在Quartz资源可用时会尽可能早的执行所有发生misfire的任务。 |
| 例如：Quartz会在10:15执行9:00和10:00的任务，然后按照原计划继续执行下去。 |
| withMisfireHandlingInstructionFireNow |
| MISFIRE_INSTRUCTION_FIRE_NOW | 等同于MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_REMAINING_REPEAT_COUNT。 |
| withMisfireHandlingInstructionNowWithExistingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_EXISTING_REPEAT_COUNT | 因为执行次数为无限次，所以等同于MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_REMAINING_REPEAT_COUNT。 |
| withMisfireHandlingInstructionNowWithRemainingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_REMAINING_REPEAT_COUNT | 立即执行第一个发生misfire的任务，并且修改startTime为当前时间，然后按照设定的间隔时间执行下一个任务，一直执行下去，执行次数是无限的，但是计划的执行时间会被改变，因为此策略会修改startTime。 |
| 例如：10:15会立即执行9:00的任务，并且修改startTime为10:15，后续的执行时间被修改为了11:15，12:15，13:15以此类推。 |
| withMisfireHandlingInstructionNextWithExistingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_EXISTING_COUNT | 等同于MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_REMAINING_COUNT。 |
| withMisfireHandlingInstructionNextWithRemainingCount |
| MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_REMAINING_COUNT | 不会立即执行任务，会等到下一次计划执行时间开始执行，忽略已经发生了misfire的任务，然后按照原计划执行时间继续执行下去。实际上就相当于不管有没有发生misfire，就按照原计划继续执行下去。 |
| 例如：10:15发现9:00和10:00发生了misfire，并不会立即执行，忽略掉发生misfire的9:00和10:00的任务，按照计划在11:00执行任务，然后一直按照原计划执行下去。 |

**几个重要策略实现原理**

**先讲解一下SimpleTrigger中几个比较重要的属性：**
- startTime：SimpleTrigger的开始执行时间。
- endTime：SimpleTrigger的结束执行时间，可以不指定。
- repeatCount：重复执行的次数，如果指定为无限次数，则此值被设置为-1。
- repeatInterval：执行的时间间隔。
- finalFireTime：SimpleTrigger的最后触发时间，这个属性很重要，下面讲解的几个策略都跟这个属性有关。

**finalFireTime的计算方法：**
- repeatCount=0，则finalFireTime等于startTime。
- repeatCount为无限次数即-1，则先判断是否存在endTime，如果不存在则finalFireTime为null。如果存在endiTime，则会根据starTime和repeatInterval计算小于或者等于endiTime的最后一次触发时间，此时间作为finalFireTime。
- repeatCount为固定次数，则finalFireTime=startTime+(repeatCount*repeatInterval)，计算结果与endTime比较，如果比endTime小，则直接返回，否则会根据starTime和repeatInterval计算小于或者等于endTime的最后一次触发时间并返回。

**四个策略的实现原理：**

用固定次数例子来进行讲解，8点00分开始执行，执行间隔是1小时，执行次数是5次，计划执行时间是：8:00,9:00,10:00,11:00,12:00,13:00。8:00正常执行，在10:15发现了9:00和10:00的任务发生了misfire。
- MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_EXISTING_REPEAT_COUNT：在10:15分立即执行9:00任务，然后修改starTime为10:15，并且会修改repeatCount为4（原计划中10:00,11:00,12:00,13:00这4个任务），因此计算的finalFireTime为10:15 + (1 * 4) = 14:15，所以最后一次执行时间为14:15，与上诉讲解吻合。
- MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_REMAINING_REPEAT_COUNT：在10:15分立即执行9:00任务，然后修改starTime为10:15，并且会修改repeatCount为3（原计划中11:00,12:00，13:00,10:00的任务会被忽略掉），因此计算的finalFireTime为10:15 + (1 * 3) = 13:15，所以最后一次执行时间为13:15，与上诉讲解吻合。
- MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_EXISTING_COUNT：在10:15分不会执行job，等待下一次执行计划，即在11:00执行任务。这个策略不会修改starTime，也不会修改repeatCount，因此finalFireTime并没有改变，从当前时间到finalFireTime还是剩余原计划中的执行次数。所以说这个策略与MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_REMAINING_COUNT相同，即使发生了misfire也还是按照原计划来执行。
- MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_REMAINING_COUNT：在10:15分不会执行job，等待下一次执行计划，即在11:00执行任务。这个策略不会修改starTime，也不会修改repeatCount，因此finalFireTime并没有改变，忽略misfire任务，按照原计划继续执行下去。

## 8.4 CronTrigger的Misfire策略
CronTrigger的 Misfire策略常量
- MisfireInstruction.IgnoreMisfirePolicy
- MisfireInstruction.CronTrigger.DoNothing
- MisfireInstruction.CronTrigger.FireOnceNow

设定一个job，开始时间为早上8:00，每一个小时执行一次job，代码片段如下：
```csharp
CronTrigger trigger = TriggerBuilder.newTrigger()
     .withIdentity("trigger", "g")
     .startAt(next)
     .withSchedule(
            CronScheduleBuilder.cronSchedule("0 0 0/1 * * ?"))
      .build();
```

假设8:00的任务执行了，但是由于某些原因，scheduler没有执行9:00和10:00的任务，在10:15分的时候scheduler发现job有两次没有执行，这两次的延迟执行时间分别是1小时15分和15分，都大于设置的misfireThreshold=1秒，因此发生了两次misfire。各misfire策略如下：

| 命令 | 说明 |
| ------------ | ------------ |
| MISFIRE_INSTRUCTION_SMART_POLICY--default | 等同于MISFIRE_INSTRUCTION_FIRE_ONCE_NOW。 |
| MISFIRE_INSTRUCTION_IGNORE_MISFIRE_POLICY | Quartz不会判断发生了misfire，立即执行所有发生了misfire的任务，然后按照原计划进行执行。 |
| 例如：10:15分立即执行9:00和10:00的任务，然后等待下一个任务在11:00执行，后续按照原计划执行。 |
| withMisfireHandlingInstructionFireAndProceed |
| MISFIRE_INSTRUCTION_FIRE_ONCE_NOW | 立即执行第一个发生misfire的任务，忽略其他发生misfire的任务，然后按照原计划继续执行。 |
| 例如：在10:15立即执行9:00任务，忽略10:00任务，然后等待下一个任务在11:00执行，后续按照原计划执行。 |
| withMisfireHandlingInstructionDoNothing |
| MISFIRE_INSTRUCTION_DO_NOTHING | 所有发生misfire的任务都被忽略，只是按照原计划继续执行。 |

# 9. Cron表达式
## 9.1 介绍
cron 是一个已经存在很长时间的 UNIX 工具，因此它的调度功能强大且经过验证。CronTrigger 类基于 cron 的调度功能。

CronTrigger 使用“cron 表达式”，它能够创建触发时间表，例如：“每周一至周五上午 8:00”或“每月最后一个周五上午 1:30”。

Cron 表达式很强大，但可能会很混乱。本教程旨在揭开创建 cron 表达式的一些神秘面纱，为用户提供在论坛或邮件列表中询问之前可以访问的资源。

## 9.2 格式
在线生成网站：https://www.pppet.net
cron 表达式是由 6 或 7 个由空格分隔的字段组成的字符串。字段可以包含任何允许的值，以及该字段允许的特殊字符的各种组合。字段如下：

| 字段名称 | 强制的 | 允许值 | 允许的特殊字符 |
| ------------ | ------------ | ------------ | ------------ |
| 秒 | 是的 | 0-59 | , - * / |
| 分钟 | 是的 | 0-59 | , - * / |
| 小时 | 是的 | 0-23 | , - * / |
| 一个月中的哪一天 | 是的 | 1-31 | , - * ? / L W |
| 月 | 是的 | 1-12 或 1-12 月 | , - * / |
| 星期几 | 是的 | 1-7 或 SUN-SAT | , - * ? /L# |
| 年 | 不 | 空的，1970-2099 | , - * / |

所以 cron 表达式可以像这样简单：`* * * * ? *`
或更复杂，像这样：`0/5 14,18,3-39,52 * ? JAN,MAR,SEP MON-FRI 2002-2010`

## 9.3 特殊字符
- `*`("all values") - 用于选择字段中的所有值。例如，`*`在分钟字段中表示“每分钟”。
- `?`（“无特定值”） - 当您需要在允许该字符的两个字段之一中指定某些内容时很有用，但另一个字段中不允许。例如，如果我希望触发器在一个月中的特定日期（例如，10 号）触发，但不关心恰好是星期几，我会`10`在 day-of-month 字段中输入，并`?`在星期几字段中。请参阅下面的示例进行说明。
- `-`- 用于指定范围。例如，`10-12`在小时字段中表示“10、11 和 12 小时”。
- `,`- 用于指定附加值。例如，`MON,WED,FRI`在星期几字段中表示“星期一、星期三和星期五”。
- `/`- 用于指定增量。例如，`0/15`在 seconds 字段中表示“秒 0、15、30 和 45”。而`5/15`在 seconds 字段中的意思是“秒 5、20、35 和 50”。您还可以/在“字符 - 在这种情况下”之后指定等同于在 '/' 之前有 '0'。 `1/3`在 day-of-month 字段中的意思是“从每月的第一天开始每 3 天触发一次”。
- `L`("last") - 在允许的两个字段中的每个字段中都有不同的含义。例如，`L`day-of-month 字段中的值表示“该月的最后一天” - 1 月的第 31 天，非闰年的 2 月的第 28 天。如果单独在星期几字段中使用，它仅表示“7”或“SAT”。但如果在星期几字段中使用另一个值，则表示“本月的最后 xxx 天” - 例如`6L`表示“本月的最后一个星期五”。您还可以指定与该月最后一天的偏移量，例如`L-3`表示日历月的倒数第三天。使用该`L`选项时，重要的是不要指定列表或值范围，因为您会得到令人困惑/意外的结果。
- `W`("weekday") - 用于指定最接近给定日期的工作日（周一至周五）。例如，如果您要指定`15W`为 day-of-month 字段的值，则其含义是：“距每月 15 日最近的工作日”。因此，如果 15 日是星期六，触发器将在 14 日星期五触发。如果 15 日是星期日，触发器将在 16 日星期一触发。如果 15 号是星期二，那么它将在 15 号星期二触发。但是，如果您指定`1W`日期的值，并且第 1 天是星期六，则触发器将在第 3 天的星期一触发，因为它不会“跳过”一个月的日期边界。`W`仅当月份中的某天是一天，而不是日期范围或日期列表时，才能指定该字符。
- 和字符`L`也`W`可以在 day-of-month 字段中组合为 yield `LW`，它转换为 *"该月的最后一个工作日"。
- `#`- 用于指定一个月中的“第 n 个”XXX 天。例如，`6#3`星期几字段中的值表示“本月的第三个星期五”（第 6 天 = 星期五，“#3” = 本月的第三个星期五）。其他示例：`2#1`= 每月的第一个星期一和`4#5`= 每月的第五个星期三。请注意，如果您指定`#5`并且该月没有 5 个给定的星期几，则该月不会发生触发。
- 合法字符以及月份和星期几的名称不区分大小写。MON 与 mon 相同。

## 9.4 常用表达式

| 表达 | 意义 |
| ------------ | ------------ |
| 0 0 12 * * ? | 每天中午 12 点（中午）开火 |
| 0 15 10 ? * * | 每天上午 10:15 开火 |
| 0 15 10 * * ? | 每天上午 10:15 开火 |
| 0 15 10 * * ? * | 每天上午 10:15 开火 |
| 0 15 10 * * ? 2005年 | 2005 年每天上午 10:15 开火 |
| 0 * 14 * * ? | 每天从下午 2 点开始到下午 2:59 结束，每分钟触发一次 |
| 0 0/5 14 * * ? | 每天从下午 2 点开始到下午 2:55 结束，每 5 分钟触发一次 |
| 0 0/5 14,18 * * ? | 从下午 2 点开始每 5 分钟发射一次，到下午 2:55 结束，并且从下午 6 点开始每 5 分钟发射一次，到下午 6:55 结束，每天 |
| 0 0-5 14 * * ? | 每天从下午 2 点开始到下午 2:05 结束，每分钟触发一次 |
| 0 10,44 14 ? 3 WED | 在 3 月的每个星期三下午 2:10 和下午 2:44 开火。 |
| 0 15 10 ? * MON-FRI | 每周一、二、三、四、五上午 10:15 开火 |
| 0 15 10 15 * ? | 每月 15 日上午 10:15 开火 |
| 0 15 10 升 * ? | 每月最后一天上午 10:15 开火 |
| 0 15 10 L-2 * ? | 每月倒数最后一天上午 10 点 15 分开火 |
| 0 15 10 ? * 6L | 每个月的最后一个星期五上午 10:15 开火 |
| 0 15 10 ? * 6L | 每个月的最后一个星期五上午 10:15 开火 |
| 0 15 10 ? * 6L 2002-2005 | 在 2002 年、2003 年、2004 年和 2005 年期间每个月的最后一个星期五上午 10:15 开火 |
| 0 15 10 ? * 6#3 | 每个月的第三个星期五上午 10:15 开火 |
| 0 0 12 1/5 * ? | 从每月的第一天开始，每月每 5 天在中午 12 点（中午）触发一次。 |
| 0 11 11 11 11 ? | 每年 11 月 11 日上午 11:11 开火。 |
