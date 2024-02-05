---
layout: post
title: "转：Quartz.Net详解（3.X）下"
author: "Kai"
header-style: text
tags:
  - Quartz
---

转自：https://juejin.cn/post/7136928319450136606

# 10. 监听器Listener实现AOP
注意：确保监听器永远不会抛出异常（使用 try-catch）并且它们可以处理内部问题。当监听器失败时，Quartz 无法确定监听器中所需的逻辑是否成功完成后，作业可能会卡住。

侦听器在运行时向调度程序注册，并且不与作业和触发器一起存储在 JobStore 中。这是因为侦听器通常是与您的应用程序的集成点。因此，每次您的应用程序运行时，侦听器都需要重新注册到调度程序。

## 10.1 TriggerListener
TriggerListeners 接收与触发器相关的事件，用于根据调度程序中发生的事件执行操作。

**ITriggerListener 接口**
```csharp
public interface ITriggerListener
{
	 string Name { get; }
	 Task TriggerFired(ITrigger trigger, IJobExecutionContext context);	 
	 Task<bool> VetoJobExecution(ITrigger trigger, IJobExecutionContext context);

	 Task TriggerMisfired(ITrigger trigger);

	 Task TriggerComplete(ITrigger trigger, IJobExecutionContext context, int triggerInstructionCode);
}
```

**定义一个类，实现ITriggerListener 接口或者扩展TriggerListenerSupport类简单地覆盖您感兴趣的事件，而不是实现这些接口**
```csharp
public class CustomTriggerListener : ITriggerListener
{
    public string Name => "CustomTriggerListener";

    /// <summary>
    /// 触发
    /// </summary>
    /// <param name="trigger"></param>
    /// <param name="context"></param>
    /// <param name="cancellationToken"></param>
    /// <returns></returns>
    public async Task TriggerFired(ITrigger trigger, IJobExecutionContext context, CancellationToken cancellationToken = default)
    {

        Console.WriteLine("【***************************************************************************************************************】");
        Console.WriteLine($"【{Name}】---【TriggerFired】-【触发】");
        await Task.CompletedTask;
    }

    /// <summary>
    /// 判断作业是否继续
    /// </summary>
    /// <param name="trigger"></param>
    /// <param name="context"></param>
    /// <param name="cancellationToken"></param>
    /// <returns></returns>
    public async Task<bool> VetoJobExecution(ITrigger trigger, IJobExecutionContext context, CancellationToken cancellationToken = default)
    {

        Console.WriteLine($"【{Name}】---【VetoJobExecution】-【判断作业是否继续】-{true}");
        return await Task.FromResult(cancellationToken.IsCancellationRequested);
    }


    /// <summary>
    ///  触发完成
    /// </summary>
    /// <param name="trigger"></param>
    /// <param name="context"></param>
    /// <param name="triggerInstructionCode"></param>
    /// <param name="cancellationToken"></param>
    /// <returns></returns>
    public async Task TriggerComplete(ITrigger trigger, IJobExecutionContext context, SchedulerInstruction triggerInstructionCode, CancellationToken cancellationToken = default)
    {
        Console.WriteLine($"【{Name}】---【TriggerComplete】-【触发完成】");
        await Task.CompletedTask;
    }

    /// <summary>
    /// 触发作业
    /// </summary>
    /// <param name="trigger"></param>
    /// <param name="cancellationToken"></param>
    /// <returns></returns>
    public async Task TriggerMisfired(ITrigger trigger, CancellationToken cancellationToken = default)
    {
        Console.WriteLine($"【{Name}】---【TriggerMisfired】【触发作业】");
        await Task.CompletedTask;
    }
}
```

**注册到调度器**
```csharp
//实例化调度器工厂
ISchedulerFactory schedulefactory = new StdSchedulerFactory();
//实例化调度器
IScheduler scheduler = schedulefactory.GetScheduler().Result;
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
        b.WithIntervalInSeconds(2);//2秒执行一次
    })
    .Build();

//将trigger监听器注册到调度器
scheduler.ListenerManager.AddTriggerListener(new CustomTriggerListener());

//把作业，触发器加入调度器
scheduler.ScheduleJob(job1, trigger1);
```

**运行结果**
```
【***************************************************************************************************************】
【CustomTriggerListener】---【TriggerFired】-【触发】
【CustomTriggerListener】---【VetoJobExecution】-【判断作业是否继续】-True
【任务执行】：2022/8/15 10:47:19
【触发时间】：2022/8/15 10:47:19
【下次触发时间】：2022/8/15 10:47:21
【CustomTriggerListener】---【TriggerComplete】-【触发完成】
```

## 10.2 JobListener
JobListeners 接收与作业相关的事件，用于根据调度程序中发生的事件执行操作。

**IJobListener 接口**
```csharp
public interface IJobListener
{
	string Name { get; }
	Task JobToBeExecuted(IJobExecutionContext context);
	Task JobExecutionVetoed(IJobExecutionContext context);
	Task JobWasExecuted(IJobExecutionContext context,       JobExecutionException jobException);
}
```

**定义一个类，实现IJobListener接口或者扩展JobListenerSupport类简单地覆盖您感兴趣的事件，而不是实现这些接口**
```csharp
public class CustomJobListener : IJobListener
{
    public string Name => "CustomJobListener";

    /// <summary>
    /// 任务执行前
    /// </summary>
    /// <param name="context"></param>
    /// <param name="cancellationToken"></param>
    /// <returns></returns>
    public async Task JobToBeExecuted(IJobExecutionContext context, CancellationToken cancellationToken = default)
    {
        Console.WriteLine($"【{Name}】-【JobToBeExecuted】-【要执行的任务】");
        await Task.CompletedTask;
    }


    /// <summary>
    /// 任务执行后
    /// </summary>
    /// <param name="context"></param>
    /// <param name="jobException"></param>
    /// <param name="cancellationToken"></param>
    /// <returns></returns>
    public async Task JobWasExecuted(IJobExecutionContext context, JobExecutionException? jobException, CancellationToken cancellationToken = default)
    {
        Console.WriteLine($"【{Name}】-【JobWasExecuted】-【作业已执行】");
        await Task.CompletedTask;
    }

    /// <summary>
    /// 任务被拒绝执行的时候
    /// </summary>
    /// <param name="context"></param>
    /// <param name="cancellationToken"></param>
    /// <returns></returns>
    public async Task JobExecutionVetoed(IJobExecutionContext context, CancellationToken cancellationToken = default)
    {
        Console.WriteLine($"【{Name}】-【JobExecutionVetoed】-【工作执行被否决】");
        await Task.CompletedTask;
    }
}
```

**注册到调度器**
```csharp
//实例化调度器工厂
ISchedulerFactory schedulefactory = new StdSchedulerFactory();
//实例化调度器
IScheduler scheduler = schedulefactory.GetScheduler().Result;
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
        b.WithIntervalInSeconds(2);//2秒执行一次
    })
    .Build();

//将job监听器注册到调度器
scheduler.ListenerManager.AddJobListener(new CustomJobListener());

//把作业，触发器加入调度器
scheduler.ScheduleJob(job1, trigger1);
```

**运行结果**
```
【CustomJobListener】-【JobToBeExecuted】-【要执行的任务】
【任务执行】：2022/8/15 11:02:25
【触发时间】：2022/8/15 11:02:25
【下次触发时间】：
【CustomJobListener】-【JobWasExecuted】-【作业已执行】
```

**多种使用场景**
**添加一个对特定工作感兴趣的 JobListener：**
```csharp
scheduler.ListenerManager.AddJobListener(myJobListener, KeyMatcher<JobKey>.KeyEquals(new JobKey("myJobName", "myJobGroup")));
```

**添加一个对特定组的所有作业感兴趣的 JobListener：**
```csharp
scheduler.ListenerManager.AddJobListener(myJobListener, GroupMatcher<JobKey>.GroupEquals("myJobGroup"));
```

**添加一个对两个特定组的所有作业感兴趣的 JobListener：**
```csharp
scheduler.ListenerManager.AddJobListener(myJobListener,
	OrMatcher<JobKey>.Or(GroupMatcher<JobKey>.GroupEquals("myJobGroup"), GroupMatcher<JobKey>.GroupEquals("yourGroup")));
```

**添加一个对所有作业感兴趣的 JobListener：**
```csharp
scheduler.ListenerManager.AddJobListener(myJobListener, GroupMatcher<JobKey>.AnyGroup());
```

## 10.3 SchedulerListener
`SchedulerListener` ，它们只接收调度程序本身的事件通知 - 不一定是与特定触发器或作业相关的事件。

与调度器相关的事件包括：作业/触发器的添加、作业/触发器的移除、调度器内部的严重错误、调度器被关闭的通知等。

**ISchedulerListener 接口**
```csharp
public interface ISchedulerListener
{
	Task JobScheduled(Trigger trigger);
	Task JobUnscheduled(string triggerName, string triggerGroup);
	Task TriggerFinalized(Trigger trigger);
	Task TriggersPaused(string triggerName, string triggerGroup);
	Task TriggersResumed(string triggerName, string triggerGroup);
	Task JobsPaused(string jobName, string jobGroup);
	Task JobsResumed(string jobName, string jobGroup);
	Task SchedulerError(string msg, SchedulerException cause);
	Task SchedulerShutdown();
}
```

**添加调度器监听器：**
```csharp
scheduler.ListenerManager.AddSchedulerListener(mySchedListener);
```

**删除 SchedulerListener：**
```csharp
scheduler.ListenerManager.RemoveSchedulerListener(mySchedListener);
```

# 11. 线程池ThreadPool
## 11.1 简介
SimpleThreadPool是Quartz.Net中自带的线程池，默认个数为10个，代表一个Scheduler同一时刻并发的最多只能执行10个job，超过10个的job需要排队等待。

下面通过四种配置方式来实现线程的配置，同时了解下有四种参数配置的方式。

**4种方式的优先级为：quartz.config < app.config < 环境变量 < namevaluecollection**

## 11.2 NameValueCollection方式配置
需要利用StdSchedulerFactory的构造函数进行传进去，向哪个Sheduler中传，即配置哪个Sheduler的对应的线程池。
```csharp
NameValueCollection pars = new NameValueCollection
{
    //线程池个数20
    ["quartz.threadPool.threadCount"] = "20"
};
ISchedulerFactory schedulefactory = new StdSchedulerFactory(pars);
IScheduler scheduler = schedulefactory.GetScheduler().Result;
```

## 11.3 系统配置文件的方式配置
**App.config/web.config**
在.net framwork程序中，可以配置在App.config/web.config文件中，该模式代码中不需要进行任何的额外配置，应用于所有的Sheduler。
```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <!--线程池个数设置   开始-->

  <configSections>
    <section name="quartz" type="System.Configuration.NameValueSectionHandler, System, Version=1.0.5000.0,Culture=neutral, PublicKeyToken=b77a5c561934e089"/>
  </configSections>
  <quartz>
    <!--设置Sheduler的线程池个数为22-->
    <add key="quartz.threadPool.threadCount" value="22"/>
  </quartz>

  <!--线程池个数设置   结束-->
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6"/>
  </startup>
</configuration>
```

**appsettings.json**
在net core程序中，可以配置在appsettings.json文件中，该模式代码中不需要进行任何的额外配置，应用于所有的Sheduler。
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "Quartz": {
    "quartz.scheduler.instanceName": "Quartz ASP.NET Core Sample Scheduler"
  }
}
```

## 11.4 quartz.config的方式进行配置
添加一个配置文件quartz.config，属性设置成 **始终复制**，该模式代码中不需要进行任何的额外配置，应用于所有的Sheduler。
```csharp
quartz.threadPool.threadCount=15
```

## 11.5 设置环境变量来实现
应用于所有的Sheduler。
```csharp
Environment.SetEnvironmentVariable("quartz.threadPool.threadCount", "26");
var factory = new StdSchedulerFactory();
var scheduler = factory.GetScheduler();
```

# 12. 工作存储JobStore
## 12.1 RAMJobStore
`RAMJobStore`是最简单的 JobStore，它也是性能最高，速度最快。它将所有数据保存在 RAM 中。缺点是当您的应用程序结束（或崩溃）时，所有调度信息都会丢失 - 这意味着 RAMJobStore 无法遵守作业和触发器的“非易失性”设置。

**配置 Quartz 以使用 RAMJobStore**
```ini
quartz.jobStore.type = Quartz.Simpl.RAMJobStore, Quartz
```
如果使用`StdSchedulerFactory`构建Schedule，不需要做任何特别的配置。Quartz.NET 的默认配置`RAMJobStore`用作作业存储实现。

## 12.2 AdoJobStore
AdoJobStore通过 ADO.NET 将所有数据保存在数据库中。它的配置要复杂一些，而且速度也没有那么快。

**首先创建数据库表**
创建表的SQL: https://github.com/quartznet/quartznet/tree/main/database/tables
- qrtz_blob_triggers : 以Blob 类型存储的触发器。
- qrtz_calendars：日历信息， quartz可配置一个日历来指定一个时间范围。
- qrtz_cron_triggers：cron类型的触发器。
- qrtz_fired_triggers：已触发的触发器。
- qrtz_job_details：jobDetail信息。
- qrtz_locks： 程序的悲观锁的信息(假如使用了悲观锁)。
- qrtz_paused_trigger_graps：暂停掉的触发器。
- qrtz_scheduler_state：调度器状态。
- qrtz_simple_triggers：简单触发器的信息。
- qrtz_simprop_triggers：简单触发器的一些其他信息。
- qrtz_triggers：将Trigger和job进行关联的表。

**配置使用 AdoJobStore**
目前，作业存储内部实现的唯一选择是`JobStoreTX`自己创建事务。
```ini
quartz.jobStore.type = Quartz.Impl.AdoJobStore.JobStoreTX, Quartz
```

**配置使用 DriverDelegate**
选择一个`IDriverDelegate`实现供 JobStore 使用， `StdAdoDelegate`是一个通用委托。但是针对不同类型数据库的特殊委托通常具有更好的性能或针对性。可以在`Quartz.Impl.AdoJobStore`命名空间中找到这些委托，具体的可以参考配置参考。
```ini
quartz.jobStore.driverDelegateType = Quartz.Impl.AdoJobStore.StdAdoDelegate, Quartz
```

**配置表前缀**
提供的sql脚本中所有表格都以前缀`QRTZ_`开头，这个前缀实际上可以配置的，使用不同的前缀可能有助于在同一数据库中为多个调度程序实例创建多组表。
```ini
quartz.jobStore.tablePrefix = QRTZ_
```

**配置数据源名称**
```ini
quartz.jobStore.dataSource = myDS
```

**配置数据源的连接字符串和数据库提供者**
具体的配置可以查看配置参考
```ini
quartz.dataSource.myDS.connectionString = Server=localhost;Database=quartz;Uid=quartznet;Pwd=quartznet
quartz.dataSource.myDS.provider = MySql
```
如果您的调度程序非常繁忙（即几乎总是执行与线程池大小相同数量的作业，那么您可能应该将数据源中的连接数设置为大约线程池大小 + 1。这通常在 ADO.NET 连接字符串中配置。

**配置使用字符串作为 JobDataMap 值**
可以设置为“ `quartz.jobStore.useProperties=true`（默认为 false），以指示 AdoJobStore JobDataMaps 中的所有值都是字符串，因此可以存储为键值对，而不是以序列化形式存储更复杂的对象在 BLOB 列。大大降低了类型序列化问题的可能性。
```ini
quartz.jobStore.useProperties = true
```

**配置存储数据用的序列化程序**
Quartz.NET 支持二进制和 JSON 序列化来存储数据到数据库。JSON序列化来自单独的NuGet 包 `Quartz.Serialization.Json`。建议使用JSON序列化。
```ini
quartz.serializer.type = json
```

## 12.3 实现Quartz持久化
- nuget引入mysql驱动程序`MySql.Data`
- nuget引入序列化包`Quartz.Serialization.Json`
- 安装数据库：这里使用docker安装数据库`docker run --name mysqlserver -v /data/mysql/conf:/etc/mysql/conf.d -v /data/mysql/logs:/logs -v /data/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d -i -p 3306:3306 mysql:latest --lower_case_table_names=1`，linux相关知识请参考[linux详解](https://blog.csdn.net/liyou123456789/article/details/121548156 "linux详解")，docker相关知识请参考[docker详解](https://blog.csdn.net/liyou123456789/article/details/122292877 "docker详解")，mysql相关知识请参考[mysql详解](https://link.juejin.cn/?target=https%3A%2F%2Fblog.csdn.net%2Fliyou123456789%2Farticle%2Fdetails%2F126023696 "mysql详解")，创建数据库quartzmanager。
- 数据库`quartzmanager`中执行mysql数据库对应的sql脚本: https://github.com/quartznet/quartznet/tree/main/database/tables
- 连接字符串参考：[www.connectionstrings.com](https://www.connectionstrings.com/ "www.connectionstrings.com")，这里填上自己的数据库服务器IP地址。
- 运行程序
	```csharp
	NameValueCollection pars = new NameValueCollection
	{
		//scheduler名字
		["quartz.scheduler.instanceName"] = "MyAdoJobStoreScheduler",
		//类型为JobStoreXT,事务
		["quartz.jobStore.type"] = "Quartz.Impl.AdoJobStore.JobStoreTX, Quartz",
		//数据源名称
		["quartz.jobStore.dataSource"] = "QuartzDb",
		//使用mysql的Ado操作代理类
		["quartz.jobStore.driverDelegateType"] = "Quartz.Impl.AdoJobStore.MySQLDelegate, Quartz",
		//数据源连接字符串
		["quartz.dataSource.QuartzDb.connectionString"] = @"server=数据库服务器IP地址;Database=quartzmanager;user id=root;password=123456;SslMode=none;",
		//数据源的数据库
		["quartz.dataSource.QuartzDb.provider"] = "MySql",
		//序列化类型
		["quartz.serializer.type"] = "json",
		//自动生成scheduler实例ID，主要为了保证集群中的实例具有唯一标识
		["quartz.scheduler.instanceId"] = "AUTO"
	};
	//实例化调度器
	ISchedulerFactory schedulefactory = new StdSchedulerFactory(pars);
	IScheduler scheduler = schedulefactory.GetScheduler().Result;

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
			 .RepeatForever();
		})
		.Build();

	//把作业，触发器加入调度器
	scheduler.ScheduleJob(job1, trigger1);
	```
- 查看数据库，数据已经自动存入
![20240205125455](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125455.png)

- 注释掉job和trigger，重启Scheduler，程序自动继续运行，说明调度器运行的是数据库中取出的任务，持久化成功了。
	```csharp
	NameValueCollection pars = new NameValueCollection
	{
		//scheduler名字
		["quartz.scheduler.instanceName"] = "MyAdoJobStoreScheduler",
		//类型为JobStoreXT,事务
		["quartz.jobStore.type"] = "Quartz.Impl.AdoJobStore.JobStoreTX, Quartz",
		//数据源名称
		["quartz.jobStore.dataSource"] = "QuartzDb",
		//使用mysql的Ado操作代理类
		["quartz.jobStore.driverDelegateType"] = "Quartz.Impl.AdoJobStore.MySQLDelegate, Quartz",
		//数据源连接字符串
		["quartz.dataSource.QuartzDb.connectionString"] = @"server=数据库服务器IP地址;Database=quartzmanager;user id=root;password=123456;SslMode=none;",
		//数据源的数据库
		["quartz.dataSource.QuartzDb.provider"] = "MySql",
		//序列化类型
		["quartz.serializer.type"] = "json",
		//自动生成scheduler实例ID，主要为了保证集群中的实例具有唯一标识
		["quartz.scheduler.instanceId"] = "AUTO"
	};
	//实例化调度器
	ISchedulerFactory schedulefactory = new StdSchedulerFactory(pars);
	IScheduler scheduler = schedulefactory.GetScheduler().Result;

	//开启调度器
	scheduler.Start();
	```

# 13. 集群cluster
## 13.1 集群配置
**配置使用 AdoJobStore**
集群目前仅适用于 AdoJobstore ( `JobStoreTX`)。功能包括负载平衡和作业故障转移（如果 JobDetail 的“请求恢复”标志设置为 true）。
```ini
quartz.jobStore.type = Quartz.Impl.AdoJobStore.JobStoreTX, Quartz
```

配置使用集群
```ini
quartz.jobStore.clustered=true
```

**配置调度器实例id**
集群中的每个实例都应该使用相同的属性副本。只有线程池大小（不同机器硬件不同可以配置不同）和`quartz.scheduler.instanceId`可以不同，集群中的每个节点都必须有一个唯一的 instanceId，可以设置成`AUTO`让系统自动分配。
```ini
quartz.scheduler.instanceId=AUTO
```

## 13.2 注意事项
- 切勿针对任何其他正在运行的实例的同一组数据库表启动非集群实例，可能会遇到严重的数据损坏，并且肯定会遇到不稳定的行为。
- 监控并确保您的节点有足够的 CPU 资源来完成作业。当某些节点处于 100% CPU 时，它们可能无法更新作业存储，而其他节点可以认为这些作业丢失并通过重新运行来恢复它们。

## 13.3 实现Quartz集群热备
集群热备：即一主多备，高可用，主挂掉了，备会自动顶上去， Quartz.Net集群采用的就是这种形式。

其他操作同Quartz持久化，多一个配置开启集群`["quartz.jobStore.clustered"] = "true"`
```csharp
NameValueCollection pars = new NameValueCollection
{
    //scheduler名字
    ["quartz.scheduler.instanceName"] = "MyAdoJobStoreScheduler",
    //类型为JobStoreXT,事务
    ["quartz.jobStore.type"] = "Quartz.Impl.AdoJobStore.JobStoreTX, Quartz",
    //数据源名称
    ["quartz.jobStore.dataSource"] = "QuartzDb",
    //使用mysql的Ado操作代理类
    ["quartz.jobStore.driverDelegateType"] = "Quartz.Impl.AdoJobStore.MySQLDelegate, Quartz",
    //数据源连接字符串
    ["quartz.dataSource.QuartzDb.connectionString"] = @"server=数据库服务器IP地址;Database=quartzmanager;user id=root;password=123456;SslMode=none;",
    //数据源的数据库
    ["quartz.dataSource.QuartzDb.provider"] = "MySql",
    //序列化类型
    ["quartz.serializer.type"] = "json",
    //自动生成scheduler实例ID，主要为了保证集群中的实例具有唯一标识
    ["quartz.scheduler.instanceId"] = "AUTO",
    //添加一个开启集群的配置，默认是不开启的
    ["quartz.jobStore.clustered"] = "true"
};
//实例化调度器
ISchedulerFactory schedulefactory = new StdSchedulerFactory(pars);
IScheduler scheduler = schedulefactory.GetScheduler().Result;

//开启调度器
scheduler.Start();
```

开启两个exe，先开的执行任务，后开的等待，过几分钟，后开的执行，先开的等待，循环往复，实现负载均衡，关掉一个，另一个继续运行，实现热备。
![20240205124944](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205124944.png)

# 14. 配置文件调度
## 14.1 配置文件
新建配置文件`quartz_jobs.xml`，右键其属性设置为`始终复制`
```xml
<job-scheduling-data xmlns="http://quartznet.sourceforge.net/JobSchedulingData" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="2.0">
	<processing-directives>
		<overwrite-existing-data>true</overwrite-existing-data>
	</processing-directives>

	<schedule>
		<!--开始执行一个调度-->
		<!--任务，可添加多个-->
		<job>
			<!--任务名称【必填】：同一group中多个job的name名不能相同-->
			<name>jobName1</name>
			<!--任务所属分组【选填】-->
			<group>jobGroup1</group>
			<!--任务描述【选填】-->
			<description>jobDescription1</description>
			<!--任务类型【必填】：实现IJob接口完整命名空间的类名，程序集名-->
			<job-type>MyQuartZ.Net.QuartZJob.MyJob, MyQuartZ.Net</job-type>
			<durable>true</durable>
			<recover>false</recover>
			<!--任务数据【选填】-->
			<job-data-map>
				<entry>
					<key>key0</key>
					<value>value0</value>
				</entry>
				<entry>
					<key>key1</key>
					<value>value1</value>
				</entry>
				<entry>
					<key>key2</key>
					<value>value2</value>
				</entry>
			</job-data-map>
		</job>

		<!--触发器，可添加多个-->
		<trigger>
			<!--simple:简单触发器类型-->
			<simple>
				<!--触发器名称【必填】：同一group中多个trigger的name不能相同-->
				<name>simpleName</name>
				<!--触发器组【选填】-->
				<group>simpleGroup</group>
				<!--触发器描述【选填】-->
				<description>SimpleTriggerDescription</description>
				<!--要调度的任务名称【必填】：必须和对应job节点中的name相同-->
				<job-name>jobName1</job-name>
				<!--要调度的任务分组【必填】：必须和对应job节点中的name相同-->
				<job-group>jobGroup1</job-group>
				<!--任务的开始时间-->
				<start-time>2022-01-01T18:15:00.0Z</start-time>
				<!--任务的结束时间-->
				<end-time>2040-05-04T18:13:51.0Z</end-time>
				<!--失效策略-->
				<misfire-instruction>SmartPolicy</misfire-instruction>
				<!--重复次数-->
				<repeat-count>100</repeat-count>
				<!--间隔时间 单位毫秒-->
				<repeat-interval>3000</repeat-interval>
			</simple>
		</trigger>

		<!--触发器，可添加多个-->
		<trigger>
			<!--cron:复杂触发器类型【推荐】-->
			<cron>
				<!--触发器名称【必填】：同一group中多个trigger的name不能相同-->
				<name>cronName</name>
				<!--触发器组【选填】-->
				<group>cronGroup</group>
				<!--触发器描述【选填】-->
				<description>CronTriggerDescription</description>
				<!--要调度的任务名称【必填】：必须和对应job节点中的name相同-->
				<job-name>jobName1</job-name>
				<!--要调度的任务分组【必填】：必须和对应job节点中的name相同-->
				<job-group>jobGroup1</job-group>
				<!--任务cron表达式-->
				<cron-expression>0/2 * * * * ?</cron-expression>
			</cron>
		</trigger>
		<!--结束一个调度-->
	</schedule>
</job-scheduling-data>
```

## 14.2 代码实现
nuget引入程序集`Quartz.Plugins`
```csharp
NameValueCollection pars = new NameValueCollection
{
    //配置读取配置文件
    ["quartz.plugin.jobInitializer.type"] = "Quartz.Plugin.Xml.XMLSchedulingDataProcessorPlugin, Quartz.Plugins",
    ["quartz.plugin.jobInitializer.fileNames"] = "quartz_jobs.xml",
    ["quartz.plugin.jobInitializer.failOnFileNotFound"] = "true"
};
//实例化调度器
ISchedulerFactory schedulefactory = new StdSchedulerFactory(pars);
IScheduler scheduler = schedulefactory.GetScheduler().Result;

//开启调度器
scheduler.Start();
```

# 15. 配置参考
默认情况下，`StdSchedulerFactory`加载一个名为`quartz.config`的属性文件。如果失败，则Quartz使用默认值，如果您希望使用`quartz.config`以外的文件，则必须定义系统属性`quartz.properties`以指向您想要的文件名称。

## 15.1 主要配置
这些属性配置调度程序的标识，以及各种其他“顶级”设置。

| 属性名称 | 必需 | 类型 | 默认值 |
| ------------ | ------------ | ------------ | ------------ |
| quartz.scheduler.instanceName | no | string | 'QuartzScheduler' |
| quartz.scheduler.instanceId | no | string | 'NON_CLUSTERED' |
| quartz.scheduler.instanceIdGenerator.type | no | string | Quartz.Simpl.SimpleInstanceIdGenerator, Quartz |
| quartz.scheduler.threadName | no | string | instanceName + '_QuartzSchedulerThread' |
| quartz.scheduler.makeSchedulerThreadDaemon | no | boolean | false |
| quartz.scheduler.idleWaitTime | no | long | 30000 |
| quartz.scheduler.typeLoadHelper.type | no | string | Quartz.Simpl.SimpleTypeLoadHelper |
| quartz.scheduler.jobFactory.type | no | string | Quartz.Simpl.PropertySettingJobFactory |
| quartz.context.key.SOME_KEY | no | string | none |
| quartz.scheduler.wrapJobExecutionInUserTransaction | no | boolean | false |
| quartz.scheduler.batchTriggerAcquisitionMaxCount | no | int | 1 |
| quartz.scheduler.batchTriggerAcquisitionFireAheadTimeWindow | no | long | 0 |

- `quartz.scheduler.instanceName`
可以是任何字符串，并且该值对调度程序本身没有意义——而是作为客户端代码在同一程序中使用多个实例时区分调度程序的一种机制。如果您使用集群功能，则必须为集群中“逻辑上”相同的调度程序的每个实例使用相同的名称

- `quartz.scheduler.instanceId`
可以是任何字符串，但对于所有工作的调度程序必须是唯一的，就好像它们是集群内的同一个“逻辑”调度程序一样。如果您希望为您生成 Id，则可以使用值“AUTO”作为 instanceId。如果您希望值来自系统属性“quartz.scheduler.instanceId”，则为值“SYS_PROP”。

- `quartz.scheduler.instanceIdGenerator.type`
仅在quartz.scheduler.instanceId 设置为`AUTO`时使用。默认为“Quartz.Simpl.SimpleInstanceIdGenerator”，它根据主机名和时间戳生成实例 id。其他`InstanceIdGenerator`实现包括`SystemPropertyInstanceIdGenerator` （从系统属性“quartz.scheduler.instanceId”获取实例ID，并`HostnameInstanceIdGenerator`使用本地主机名（`Dns.GetHostEntry(Dns.GetHostName())`）。您也可以自己实现InstanceIdGenerator接口。

- `quartz.scheduler.threadName`
可以是主调度程序线程的有效名称的任何字符串。如果未指定此属性，线程将接收调度程序的名称（“quartz.scheduler.instanceName”）加上附加的字符串“_QuartzSchedulerThread”。

- `quartz.scheduler.makeSchedulerThreadDaemon`
一个布尔值（“true”或“false”），指定调度程序的主线程是否应该是守护线程。另请参阅`quartz.scheduler.makeSchedulerThreadDaemon`属性以调整`DefaultThreadPool`是否是您正在使用的线程池实现（很可能是这种情况）。

- `quartz.scheduler.idleWaitTime`
是当调度程序空闲时，调度程序在重新查询可用触发器之前等待的时间量（以毫秒为单位）。通常，您不必“调整”此参数，除非您正在使用 XA 事务，并且在延迟触发应立即触发的触发器时遇到问题。不建议使用小于 5000 毫秒的值，因为它会导致过多的数据库查询。小于 1000 的值是不合法的。

- `quartz.scheduler.typeLoadHelper.type`
默认使用最健壮的方法，即使用“Quartz.Simpl.SimpleTypeLoadHelper”类型 - 只需使用`Type.GetType()`.

- `quartz.scheduler.jobFactory.type`
要使用的 IJobFactory 的类型名称。作业工厂负责生成`IJob`实现的实例。默认值为“Quartz.Simpl.PropertySettingJobFactory”，`Activator.CreateInstance`每次执行即将发生时，它都会以给定类型简单地调用以生成一个新实例。 `PropertySettingJobFactory`还使用调度程序上下文和作业的内容反射性地设置作业的属性并触发 JobDataMaps。

- `quartz.context.key.SOME_KEY`
表示将作为字符串放入“调度程序上下文”的名称-值对（请参阅 IScheduler.Context）。因此，例如，设置“quartz.context.key.MyKey = MyValue”将执行相当于`scheduler.Context.Put("MyKey", "MyValue")`.

- `quartz.scheduler.batchTriggerAcquisitionMaxCount`
	允许调度程序节点一次获取（用于触发）的最大触发器数。默认值为 1。数字越大，触发效率越高（在需要一次触发的触发器非常多的情况下）——但代价是集群节点之间的负载可能不平衡。

	如果此属性的值设置为 > 1，并且使用了 AdoJobStore，则必须将属性“quartz.jobStore.acquireTriggersWithinLock”设置为“true”以避免数据损坏。

- `quartz.scheduler.batchTriggerAcquisitionFireAheadTimeWindow`
允许在预定触发时间之前获取和触发触发器的时间量（以毫秒为单位）。默认为 0。数字越大，触发的批量获取触发器就越有可能一次选择并触发超过 1 个触发器 - 代价是触发器计划没有被精确遵守（触发器可能会提前触发这个数量）。
在调度程序有大量触发器需要同时或几乎同时触发的情况下，这可能很有用（出于性能考虑）。

## 15.2 线程池ThreadPool

| 属性名称 | 必需 | 类型 | 默认值 |
| ------------ | ------------ | ------------ | ------------ |
| quartz.threadPool.type | no | string | Quartz.Simpl.DefaultThreadPool |
| quartz.threadPool.maxConcurrency | no | int | 10 |

`quartz.threadPool.type`
是您希望使用的 ThreadPool 实现的名称。Quartz 附带的线程池是“Quartz.Simpl.DefaultThreadPool”，应该可以满足几乎所有用户的需求。

它的行为非常简单，并且经过了很好的测试。它将任务分派到 .NET 任务队列，并确保遵守配置的最大并发任务数量限制。你应该学习`CLR的托管线程池`如果您想在 CLR 级别微调线程池。

`quartz.threadPool.maxConcurrency`
这是可以分派到 CLR 线程池的并发任务数。如果你只有几份工作每天解雇几次，那么 1 个任务就足够了！如果您有数以万计的作业，每分钟触发许多作业，那么您可能希望最大并发数更像 50 或 100（这在很大程度上取决于您的作业执行的工作的性质以及您的系统资源！）。还要注意 CLR 线程池配置与 Quartz 本身分开。

如果您使用自己的线程池实现，您可以通过简单地命名属性来反射地设置属性，如下所示：
```ini
quartz.threadPool.type = MyLibrary.FooThreadPool, MyLibrary
quartz.threadPool.somePropOfFooThreadPool = someValue
```

## 15.3 监听器Listener
全局监听器可以通过实例化和配置`StdSchedulerFactory`，或者你的应用程序可以在运行时自己做，然后向调度器注册监听器。“全局”侦听器侦听每个作业/触发器的事件，而不仅仅是直接引用它们的作业/触发器。

通过配置文件配置侦听器包括给定一个名称，然后指定类型名称以及要在实例上设置的任何其他属性。该类型必须有一个无参数的构造函数，并且属性是反射设置的。仅支持原始数据类型值（包括字符串）。

因此，定义“全局”TriggerListener 的一般模式是：
```ini
quartz.triggerListener.NAME.type = MyLibrary.MyListenerType, MyLibrary
quartz.triggerListener.NAME.propName = propValue
quartz.triggerListener.NAME.prop2Name = prop2Value
```

定义“全局” JobListener 的一般模式是：
```ini
quartz.jobListener.NAME.type = MyLibrary.MyListenerType, MyLibrary
quartz.jobListener.NAME.propName = propValue
quartz.jobListener.NAME.prop2Name = prop2Value
```

## 15.4 插件Plugin
就像侦听器通过配置文件配置插件一样，包括给定一个名称，然后指定类型名称以及要在实例上设置的任何其他属性。该类型必须有一个无参数的构造函数，并且属性是反射设置的。仅支持原始数据类型值（包括字符串）。

因此，定义插件的一般模式是：
```ini
quartz.plugin.NAME.type = MyLibrary.MyPluginType, MyLibrary
quartz.plugin.NAME.propName = propValue
quartz.plugin.NAME.prop2Name = prop2Value
```
Quartz 附带了几个插件，可以在`Quartz.Plugins`包裹。配置其中几个的示例如下：

**Logging Trigger History 插件的示例配置**
日志触发历史插件捕获触发事件（它也是一个触发侦听器），然后使用日志基础设施进行日志记录。
```ini
quartz.plugin.triggHistory.type = Quartz.Plugin.History.LoggingTriggerHistoryPlugin, Quartz.Plugins
quartz.plugin.triggHistory.triggerFiredMessage = Trigger {1}.{0} fired job {6}.{5} at: {4:HH:mm:ss MM/dd/yyyy}
quartz.plugin.triggHistory.triggerCompleteMessage = Trigger {1}.{0} completed firing job {6}.{5} at {4:HH:mm:ss MM/dd/yyyy} with resulting trigger instruction code: {9}
```

**XML调度数据处理器插件的示例配置**
作业初始化插件从 XML 文件中读取一组作业和触发器，并在初始化期间将它们添加到调度程序中。它还可以删除现有数据。
该文件的 XML 模式定义可以在https://github.com/quartznet/quartznet/blob/master/src/Quartz/Xml/job_scheduling_data_2_0.xsd 找到
```ini
quartz.plugin.jobInitializer.type = Quartz.Plugin.Xml.XMLSchedulingDataProcessorPlugin, Quartz.Plugins
quartz.plugin.jobInitializer.fileNames = data/my_job_data.xml
quartz.plugin.jobInitializer.failOnFileNotFound = true
```

**Shutdown Hook插件的示例配置**
shutdown-hook 插件捕获 CLR 终止的事件，并在调度程序上调用 shutdown。
```ini
quartz.plugin.shutdownhook.type = Quartz.Plugin.Management.ShutdownHookPlugin, Quartz.Plugins
quartz.plugin.shutdownhook.cleanShutdown = true
```

**作业中断监视器插件的示例配置**
该插件捕获作业长时间运行（超过配置的最大时间）的事件，并告诉调度程序“尝试”在启用时中断它。插件默认在 5 分钟后发出信号中断，可以配置不同的值，配置中的值以毫秒为单位。
```ini
quartz.plugin.jobAutoInterrupt.type = Quartz.Plugin.Interrupt.JobInterruptMonitorPlugin, Quartz.Plugins
quartz.plugin.jobAutoInterrupt.defaultMaxRunTime = 3000000
```

## 15.5 远程服务器和客户端

| 属性名称 | 必需 | 类型 | 默认值 |
| ------------ | ------------ | ------------ | ------------ |
| quartz.scheduler.exporter.type | yes | string |  |
| quartz.scheduler.exporter.port | yes | int |  |
| quartz.scheduler.exporter.bindName | no | string | 'QuartzScheduler' |
| quartz.scheduler.exporter.channelType | no | string | 'tcp' |
| quartz.scheduler.exporter.channelName | no | string | 'http' |
| quartz.scheduler.exporter.typeFilterLevel | no | string | 'Full' |
| quartz.scheduler.exporter.rejectRemoteRequests | no | boolean | false |

如果您希望 Quartz 调度程序通过远程处理将自身导出为服务器，则将 'quartz.scheduler.exporter.type' 设置为 "Quartz.Simpl.RemotingSchedulerExporter, Quartz"。

- `quartz.scheduler.exporter.type`
的类型ISchedulerExporter，目前仅支持“Quartz.Simpl.RemotingSchedulerExporter, Quartz”。

- `quartz.scheduler.exporter.port`
要监听的端口。

- `quartz.scheduler.exporter.bindName`
绑定到远程基础结构时使用的名称。

- `quartz.scheduler.exporter.channelType`
无论是“tcp”还是“http”，TCP 的性能都更高。

- `quartz.scheduler.exporter.channelName`
绑定到远程基础结构时使用的通道名称。

- `quartz.scheduler.exporter.typeFilterLevel`
	**Low**：.NET Framework 远程处理的低反序列化级别。它支持与基本远程功能相关的类型
	**Full**：.NET Framework 远程处理的完整反序列化级别。它支持所有情况下远程支持的所有类型

- `quartz.scheduler.exporter.rejectRemoteRequests`
一个布尔值（真或假），指定是否拒绝来自其他计算机的请求。指定 true 仅允许来自本地计算机的远程调用。

## 15.6 内存运行RAMJobStore
RAMJobStore 用于在内存中存储调度信息（作业、触发器和日历）。RAMJobStore 快速且轻量级，但当进程终止时，所有调度信息都会丢失。

**将 Scheduler 的 JobStore 设置为 RAMJobStore**
```ini
quartz.jobStore.type = Quartz.Simpl.RAMJobStore, Quartz
```

RAMJobStore 可以使用以下属性进行调整：

| 属性名称 | 必需 | 类型 | 默认值 |
| ------------ | ------------ | ------------ | ------------ |
| quartz.jobStore.misfireThreshold | no | int | 60000 |

- `quartz.jobStore.misfireThreshold`
在被视为“未触发”之前，调度程序将“容忍”触发器通过其下一次触发时间的毫秒数。默认值（如果您没有在配置中输入此属性）是 60000（60 秒）。

## 15.7 事务JobStoreTX (ADO.NET)
AdoJobStore 用于在关系数据库中存储调度信息（作业、触发器和日历）。实际上有两个单独的 AdoJobStore 实现可供您选择，具体取决于您需要的事务行为。

JobStoreTX 通过在每次操作（例如添加作业）后调用`Commit()`（或）数据库连接来管理所有事务本身。`Rollback()`这是您通常应该使用的作业存储，除非您想集成到某些事务感知框架。

JobStoreTX 是通过如下设置`quartz.jobStore.type`属性来选择的：

**将 Scheduler 的 JobStore 设置为 JobStoreTX**
```ini
quartz.jobStore.type = Quartz.Impl.AdoJobStore.JobStoreTX, Quartz
```

JobStoreTX 可以使用以下属性进行调整：

| 属性名称 | 必需 | 类型 | 默认值 |
| ------------ | ------------ | ------------ | ------------ |
| quartz.jobStore.dbRetryInterval | no | long | 15000 (15 seconds) |
| quartz.jobStore.driverDelegateType | yes | string | null |
| quartz.jobStore.dataSource | yes | string | null |
| quartz.jobStore.tablePrefix | no | string | "QRTZ_" |
| quartz.jobStore.useProperties | no | boolean | false |
| quartz.jobStore.misfireThreshold | no | int | 60000 |
| quartz.jobStore.clustered | no | boolean | false |
| quartz.jobStore.clusterCheckinInterval | no | long | 15000 |
| quartz.jobStore.maxMisfiresToHandleAtATime | no | int | 20 |
| quartz.jobStore.selectWithLockSQL | no | string | "SELECT * FROM {0}LOCKS WHERE SCHED_NAME = {1} AND LOCK_NAME = ? FOR UPDATE" |
| quartz.jobStore.txIsolationLevelSerializable | no | boolean | false |
| quartz.jobStore.acquireTriggersWithinLock | no | boolean | false (or true - see doc below) |
| quartz.jobStore.lockHandler.type | no | string | null |
| quartz.jobStore.driverDelegateInitString | no | string | null |

- `quartz.scheduler.dbRetryInterval`
是调度程序在检测到 JobStore 中的连接丢失（例如到数据库）时将在重试之间等待的时间量（以毫秒为单位）。在使用 RamJobStore 时，这个参数显然不是很有意义。

- `quartz.jobStore.driverDelegateType`
	驱动程序代表了解不同数据库系统的特定“方言”。可能的内置选项包括：
	- Quartz.Impl.AdoJobStore.StdAdoDelegate, Quartz - 没有特定实现时的默认值
	- Quartz.Impl.AdoJobStore.SqlServerDelegate，Quartz - 适用于 Microsoft SQL Server
	- Quartz.Impl.AdoJobStore.PostgreSQLDelegate, Quartz
	- Quartz.Impl.AdoJobStore.OracleDelegate, Quartz
	- Quartz.Impl.AdoJobStore.SQLiteDelegate, Quartz
	- Quartz.Impl.AdoJobStore.MySQLDelegate, Quartz

- `quartz.jobStore.dataSource`
此属性的值必须是配置属性文件中定义的数据源之一的名称。

- `quartz.jobStore.tablePrefix`
	AdoJobStore 的“表前缀”属性是一个字符串，它等于在您的数据库中创建的 Quartz 表的前缀。如果它们使用不同的表前缀，您可以在同一个数据库中拥有多组 Quartz 的表。

	**在 tablePrefix 中包含模式名称**
	对于支持模式的后备数据库（例如 Microsoft SQL Server），您可以使用 tablePrefix 来包含模式名称。即对于名为**foo**前缀的模式可以设置为：
	```ini
	[foo].QRTZ_
	```
	**注意：** 任何使用显式架构（例如`dbo`）运行的数据库表创建脚本都需要修改以反映此配置。

- `quartz.jobStore.useProperties`
“使用属性”标志指示 AdoJobStore JobDataMaps 中的所有值都是字符串，因此可以存储为名称-值对，而不是将更复杂的对象以其序列化形式存储在 BLOB 列中。这很方便，因为您避免了将非字符串类型序列化为 BLOB 时可能出现的类型版本控制问题。

- `quartz.jobStore.misfireThreshold`
在被视为“未触发”之前，调度程序将“容忍”触发器通过其下一次触发时间的毫秒数。默认值（如果您没有在配置中输入此属性）是 60000（60 秒）。

- `quartz.jobStore.clustered`
设置为“true”以打开聚类功能。如果您有多个 Quartz 实例使用同一组数据库表，则此属性必须设置为“true”......否则您将遇到严重破坏。有关更多信息，请参阅集群的配置文档。

- `quartz.jobStore.clusterCheckinInterval`
设置此实例与集群的其他实例“签入”* 的频率（以毫秒为单位）。影响检测失败实例的速度。

- `quartz.jobStore.maxMisfiresToHandleAtATime`
作业存储在给定通道中将处理的最大未触发触发器数。一次处理许多（超过几十个）可能会导致数据库表被锁定足够长的时间，以至于触发其他（尚未触发的）触发器的性能可能会受到阻碍。

- `quartz.jobStore.selectWithLockSQL`
必须是选择“LOCKS”表中的一行并在该行上放置锁的 SQL 字符串。如果未设置，默认为“SELECT * FROM {0}LOCKS WHERE SCHED_NAME = {1} AND LOCK_NAME = ? FOR UPDATE”，适用于大多数数据库。“{0}”在运行时被您在上面配置的 TABLE_PREFIX 替换。“{1}”替换为调度程序的名称。

- `quartz.jobStore.txIsolationLevelSerializable`
“true”值告诉 Quartz（使用 JobStoreTX 或 CMT 时）设置事务级别以在 ADO.NET 连接上进行序列化。这有助于防止某些数据库在高负载和“持久”事务下发生锁定超时。

- `quartz.jobStore.acquireTriggersWithinLock`
	获取下一个要触发的触发器是否应在显式数据库锁内发生。这曾经是必要的（在以前的 Quartz 版本中）以避免特定数据库的死锁，但不再被认为是必要的，因此默认值为“false”。

	如果 "quartz.scheduler.batchTriggerAcquisitionMaxCount" 设置为 > 1，并且使用 AdoJobStore，则必须将此属性设置为 "true" 以避免数据损坏（从 Quartz 2 开始，如果设置了 batchTriggerAcquisitionMaxCount，则默认设置为 "true" > 1）。

- `quartz.jobStore.lockHandler.type`
	用于生成`Quartz.Impl.AdoJobStore.ISemaphore`用于锁定作业存储数据控制的实例的类型名称。这是一项高级配置功能，大多数用户不应该使用它。

	默认情况下，Quartz 会选择最合适的（预先捆绑的）信号量实现来使用。

	**自定义StdRowLockSemaphore**
	如果您明确选择使用此 DB Semaphore，您可以进一步自定义轮询 DB 锁的频率。

	**使用自定义 StdRowLockSemaphore 实现的示例**
	```ini
	quartz.jobStore.lockHandler.type = Quartz.Impl.AdoJobStore.StdRowLockSemaphore
	quartz.jobStore.lockHandler.maxRetry = 7     # Default is 3
	quartz.jobStore.lockHandler.retryPeriod = 3000  # Default is 1000 millis
	```

- `quartz.jobStore.driverDelegateInitString`
	可以在初始化期间传递给 DriverDelegate 的以竖线分隔的属性（及其值）列表。字符串的格式如下：
	```ini
	settingName=settingValue|otherSettingName=otherSettingValue|...
	```
	StdAdoDelegate 及其所有后代（Quartz 附带的所有委托）都支持一个名为“triggerPersistenceDelegateTypes”的属性，该属性可以设置为以逗号分隔的类型列表，这些类型实现了`ITriggerPersistenceDelegate`用于存储自定义触发器类型的接口。请参阅实现`SimplePropertiesTriggerPersistenceDelegateSupport`和`SimplePropertiesTriggerPersistenceDelegateSupport`为自定义触发器编写持久性委托的示例。

## 15.8 持久化AdoJobstore
如果您使用的是 AdoJobstore，则需要一个 DataSource 供其使用（或两个 DataSource，如果您使用的是 JobStoreCMT）。

您定义的每个 DataSource（通常是一个或两个）都必须指定一个名称，并且您为每个定义的属性必须包含该名称，如下所示。DataSource 的“NAME”可以是任何你想要的，除了在分配给 AdoJobStore 时能够识别它之外没有任何意义。

Quartz 创建的数据源定义了以下属性：

| 属性名称 | 必需 | 类型 | 默认值 |
| ------------ | ------------ | ------------ | ------------ |
| quartz.dataSource.NAME.provider | yes | string |  |
| quartz.dataSource.NAME.connectionString |  | string |  |
| quartz.dataSource.NAME.connectionStringName |  | string |  |
| quartz.dataSource.NAME.connectionProvider.type |  | string |  |

- quartz.dataSource.NAME.provider
	目前支持以下数据库提供程序：
	- `SqlServer` - 微软 SQL 服务器
	- `OracleODP` - 甲骨文的甲骨文驱动程序
	- `OracleODPManaged` - 适用于 Oracle 11 的 Oracle 托管驱动程序
	- `MySql` - MySQL 连接器/.NET
	- `SQLite` - SQLite ADO.NET 提供程序
	- `SQLite-Microsoft` - Microsoft SQLite ADO.NET 提供程序
	- `Firebird` - Firebird ADO.NET 提供程序
	- `Npgsql` - PostgreSQL Npgsql

- quartz.dataSource.NAME.connectionString
要使用的 ADO.NET 连接字符串。如果您在下面使用 connectionStringName，则可以跳过此步骤。

- quartz.dataSource.NAME.connectionStringName
要使用的连接字符串名称。在 app.config 或 appsettings.json 中定义。

- quartz.dataSource.NAME.connectionProvider.type
允许您定义实现 IDbProvider 接口的自定义连接提供程序。

**Quartz 定义的数据源示例**
```ini
quartz.dataSource.myDS.provider = SqlServer
quartz.dataSource.myDS.connectionString = Server=localhost;Database=quartznet;User Id=quartznet;Password=quartznet;
```

## 15.9 集群cluster
Quartz 的集群功能通过故障转移和负载平衡功能为您的调度程序带来高可用性和可扩展性。

集群目前仅适用于 AdoJobstore (`JobStoreTX`或`JobStoreCMT`)，并且本质上是通过让集群的每个节点共享相同的数据库来工作的。

负载平衡会自动发生，集群的每个节点都会尽快触发作业。当触发器的触发时间发生时，获取它的第一个节点（通过对其加锁）就是将触发它的节点。

每次触发时只有一个节点会触发作业。我的意思是，如果作业有一个重复触发器，告诉它每 10 秒触发一次，那么在 12:00:00 恰好一个节点将运行该作业，而在 12:00:10 恰好一个节点将运行作业等。它不一定每次都是同一个节点 - 哪个节点运行它或多或少是随机的。负载平衡机制对于繁忙的调度程序（很多触发器）是近乎随机的，但对于非繁忙（例如，很少触发器）调度程序有利于相同的节点。

当其中一个节点在执行一项或多项作业期间发生故障时，就会发生故障转移。当一个节点发生故障时，其他节点会检测该状况并识别数据库中故障节点中正在进行的作业。任何标记为要恢复的作业（在 JobDetail 上具有“requests recovery”属性）将由其余节点重新执行。未标记为恢复的作业将在下次触发相关触发器时被释放以供执行。

集群功能最适合扩展长时间运行和/或 CPU 密集型作业（将工作负载分配到多个节点）。如果您需要向外扩展以支持数千个短期运行（例如 1 秒）的作业，请考虑使用多个不同的调度程序（包括用于 HA 的多个集群调度程序）对作业集进行分区。调度程序使用集群范围的锁，这种模式会随着您添加更多节点而降低性能（当超过大约三个节点时 - 取决于您的数据库的功能等）。

通过将属性`quartz.jobStore.clustered`设置为“true”来启用集群。集群中的每个实例都应该使用相同的属性副本。只有线程池大小和`quartz.scheduler.instanceId`可以不同，集群中的每个节点都必须有一个唯一的 instanceId，可以设置成`AUTO`让系统自动分配。

**集群调度程序的示例属性**
```ini
#============================================================================
# Configure Main Scheduler Properties
#============================================================================

quartz.scheduler.instanceName = MyClusteredScheduler
quartz.scheduler.instanceId = AUTO

#============================================================================
# Configure ThreadPool
#============================================================================

quartz.threadPool.type = Quartz.Simpl.DefaultThreadPool, Quartz
quartz.threadPool.threadCount = 25
quartz.threadPool.threadPriority = 5

#============================================================================
# Configure JobStore
#============================================================================

quartz.jobStore.misfireThreshold = 60000

quartz.jobStore.type = Quartz.Impl.AdoJobStore.JobStoreTX
quartz.jobStore.driverDelegateType = Quartz.Impl.AdoJobStore.SqlServerDelegate
quartz.jobStore.useProperties = true
quartz.jobStore.dataSource = myDS
quartz.jobStore.tablePrefix = QRTZ_

quartz.jobStore.clustered = true
quartz.jobStore.clusterCheckinInterval = 20000

#============================================================================
# Configure Datasources
#============================================================================

quartz.dataSource.myDS.provider = SqlServer
quartz.dataSource.myDS.connectionString = Server=localhost;Database=quartznet;User Id=quartznet;Password=quartznet;
```

# 16. 最佳实践
## 16.1 JobDataMap
- **仅在 JobDataMap 中存储原始数据类型（包括字符串）**
仅在 JobDataMap 中存储原始数据类型（包括字符串）以避免短期和长期的数据序列化问题。

- **使用合并的 JobDataMap**
	在 Job 执行期间找到的 JobDataMap `JobExecutionContext`起到了方便的作用。它是在 JobDetail 上找到的 JobDataMap 和在 Trigger 上找到的 JobDataMap 的合并，后者中的值会覆盖前者中的任何同名值。

	如果您有一个作业存储在调度程序中以供多个触发器定期/重复使用，但对于每个独立的触发，您希望为作业提供不同的数据输入，则在触发器上存储 JobDataMap 值可能很有用。

	鉴于上述所有情况，我们推荐以下最佳实践：`IJob.Execute(..)`方法中的代码通常应从 JobExecutionContext 上的 JobDataMap 中检索值，而不是直接从 JobDetail 上的那个中检索值。

## 16.2 触发器trigger
- **使用 TriggerUtils**
	触发器工具：
	- 提供一种创建日期的简单方法（用于开始/结束日期）
	- 提供分析触发器的助手（例如计算未来的触发时间）

## 16.4 持久化AdoJobStore
- **永远不要直接写入 Quartz 的表**
	将调度数据直接写入数据库（通过 SQL）而不是使用调度 API：
	- 导致数据损坏（已删除数据、加扰数据）
	- 当触发器的触发时间到达时，导致作业看似“消失”而不执行
	- 当触发器的触发时间到达时，导致作业不执行“只是坐在那里”
	- 可能导致：死锁
	- 其他奇怪的问题和数据损坏

- **切勿将非集群调度程序与具有相同调度程序名称的另一个调度程序指向同一数据库**
	如果您将多个调度程序实例指向同一组数据库表，并且其中一个或多个实例未配置为集群，则可能会发生以下任何情况：
	- 导致数据损坏（已删除数据、加扰数据）
	- 当触发器的触发时间到达时，导致作业看似“消失”而不执行
	- 当触发器的触发时间到达时，导致作业未执行，“只是坐在那里”
	- 可能导致：死锁
	- 其他奇怪的问题和数据损坏

- **确保足够的数据源连接大小**
建议您的 Datasource 最大连接大小至少配置为线程池中的工作线程数加 3。如果您的应用程序还频繁调用调度程序 API，您可能需要额外的连接。

## 16.5 夏令时
- **避免在夏令时转换时间附近安排作业**
	注意：转换时间的细节和时钟向前或向后移动的时间量因地区而异，请参阅：https://secure.wikimedia.org/wikipedia/en/wiki/Daylight_saving_time_around_the_world .

	SimpleTriggers 不受夏令时的影响，因为它们总是以精确的毫秒时间触发，并重复精确的毫秒数。

	因为 CronTriggers 在给定的小时/分钟/秒触发，所以当 DST 转换发生时它们会受到一些奇怪的影响。

	作为可能问题的示例，在美国的时区/位置安排遵守夏令时，如果在凌晨 1:00 和凌晨 2:00 使用 CronTrigger 并安排火灾时间，可能会出现以下问题：
	- 凌晨 1:05 可能会出现两次！- 可能在 CronTrigger 上重复触发
	- 凌晨 2:05 可能永远不会发生！- 可能错过 CronTrigger 上的触发

	同样，调整的具体时间和数量因地区而异。

	基于沿日历滑动（而不是精确的时间量）的其他触发器类型，例如 CalenderIntervalTrigger，将受到类似影响 - 但不是错过一次触发或触发两次，最终可能会使其触发时间偏移一个小时。

## 16.6 作业job
- **等待条件**
	长时间运行的作业会阻止其他作业运行（如果 ThreadPool 中的所有线程都忙）。

	如果您觉得需要在执行作业的工作线程上调用 Thread.sleep()，这通常表明作业尚未准备好完成其余工作，因为它需要等待某些条件（例如数据记录的可用性）成为真实。

	更好的解决方案是释放工作线程（退出作业）并允许其他作业在该线程上执行。该作业可以在退出之前重新安排自己或其他作业。

- **抛出异常**
	Job 的执行方法应该包含一个处理所有可能异常的 try-catch 块。

	如果作业抛出异常，Quartz 通常会立即重新执行它，这意味着该作业可以并且很可能会再次抛出相同的异常。这可能会导致资源浪费，在最坏的情况下，还会导致应用程序不稳定或崩溃。如果作业捕获它可能遇到的所有异常、处理它们并重新安排自己或其他作业以解决该问题，那就更好了。

- **可恢复性和幂等性**
	在调度程序失败后，标记为“可恢复”的进行中作业会自动重新执行。这意味着某些作业的“工作”将被执行两次。

	这意味着作业应该以使其工作是幂等的方式编码。

## 16.7 监听器Listener
- **保持监听器中的代码简洁高效**
不鼓励执行大量工作，因为将执行作业（或完成触发器并继续触发另一个作业等）的线程将被绑定在侦听器中。

- **处理异常**
	每个侦听器方法都应包含一个处理所有可能异常的 try-catch 块。

	如果某个监听器抛出异常，可能会导致其他监听器无法被通知和/或阻止作业的执行等。

## 16.8 公开调度程序功能
一些用户通过应用程序用户界面公开 Quartz 的调度程序功能。这可能非常有用，尽管它也可能非常危险。

确保您不会错误地允许用户使用他们想要的任何参数来定义他们想要的任何类型的作业。例如，Quartz.Jobs 包附带了一个预制作业 `NativeJob`，它将执行它定义的任意本机（操作系统）系统命令。恶意用户可以使用它来控制或破坏您的系统。

同样，其他作业（例如 `SendEmailJob`，以及几乎任何其他作业）都可能被用于恶意目的。

允许用户定义他们想要的任何作业，从而有效地打开您的系统，从而使您的系统容易受到与 OWASP 和 MITRE 定义的命令注入攻击相当/等效的各种漏洞的攻击。

# 17. 实现UI管理任务调度
代码第一条，不要自己造轮子，懂了原理之后，就去github上面找开源的项目，如果有找那个写的最好的拿过来修改下为我所用，如果找不到，那就恭喜你，你的想法可能是个没人想过的创意。

推荐一个好的开源项目：https://github.com/zhaopeiym/quartzui

**本项目实现了如下需求**
- 基于.NET6和Quartz.NET3.2.4的任务调度Web界面管理。
- docker方式开箱即用
- 内置SQLite持久化
- 支持 RESTful风格接口
- 业务代码零污染
- 语言无关
- 傻瓜式配置
- 异常请求邮件通知

**更换数据源**
默认使用的是SQLite，如果需要使用其他数据源请自行在appsettings.json进行正确配置。如：
```json
"dbProviderName":"OracleODPManaged",
"connectionString": "Data Source=(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=localhost)(PORT=1521)))(CONNECT_DATA=(SERVICE_NAME=xe)));User Id=system;Password=oracle;";

"dbProviderName":"SqlServer",
"connectionString": "Server=localhost;Database=quartznet;User Id={SqlServerUser};Password={SqlServerPassword};";

"dbProviderName":"SQLServerMOT",
"connectionString": "Server=localhost,1444;Database=quartznet;User Id={SqlServerUser};Password={SqlServerPassword};"

"dbProviderName":"MySql", // MySql 测试通过
"connectionString": "Server = localhost; Database = quartznet; Uid = quartznet; Pwd = quartznet";

"dbProviderName":"Npgsql", // Npgsql 测试通过
"connectionString": "Server=127.0.0.1;Port=5432;Userid=quartznet;Password=quartznet;Pooling=true;MinPoolSize=1;MaxPoolSize=20;Timeout=15;SslMode=Disable;Database=quartznet";

"dbProviderName":"SQLite",
"connectionString": "Data Source=test.db;Version=3;";

"dbProviderName":"SQLite-Microsoft", // SQLite-Microsoft 测试通过
"connectionString": "Data Source=test.db;";

"dbProviderName":"Firebird",
"connectionString": "User=SYSDBA;Password=masterkey;Database=/firebird/data/quartz.fdb;DataSource=localhost;Port=3050;Dialect=3;Charset=NONE;Role=;Connection lifetime=15;Pooling=true;MinPoolSize=0;MaxPoolSize=50;Packet Size=8192;ServerType=0;";
```

## 17.1 下载源码
git相关知识请参考[git详解](https://blog.csdn.net/liyou123456789/article/details/121411053 "git详解")
![20240205125056](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125056.png)

## 17.2 前端项目编译打包
进入前端项目，右键vscode打开，进入命令终端，`npm install`先还原包，再`npm run build`将代码打包进dist文件夹，相关前端工程化的知识请自行学习。

将生成的dist中的前端文件全部拷贝到后台接口项目的wwwroot文件夹下

## 17.3 配置首次登录的用户密码
密码是存储在文件中的，可以自己设置一个初始密码
![20240205125103](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125103.png)

## 17.4 发布启动项目
发布项目到一个具体的路径 `D:\NetDemos\quartzui` 下，然后在发布的目录下执行命令，启动项目。
```bash
dotnet Host.dll --urls=http://*:8100
```
## 17.5 运行结果
浏览器输入 `http://localhost:8100/`
![20240205125112](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125112.png)

输入密码admin登录系统
![20240205125121](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125121.png)

可以进行任务的添加，修改，执行，删除，暂停等操作
![20240205125130](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125130.png)
![20240205125137](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125137.png)

# 18. 部署程序
## 18.1 IIS自动回收问题解决
如果是netframwork的web程序是需要挂载在IIS里面的，但是IIS会进行自动回收，默认回收是1740分钟，也就是29小时。IIS自动回收相当于IIS重启，应用程序池内存清空，所有数据被清除，为了减小数据库负担，内存中暂存了很多信息，不适合频繁的回收，如果没有及时保存到数据库中，可能导致程序出现问题。

**解决方案：关闭该项目在IIS上对应的进程池的回收机制。**

选中IIS中部署的项目对应的进程池，点击【高级设置】，里面有5个核心参数：
- 发生配置更改时禁止回收：如果为True,应用程序池在发生配置更改时将不会回收。
- 固定时间间隔（分钟）：超过设置的时间后，应用程序池回收，设置为0意味着应用程序池不回收。系统默认设置的时间是1740（29小时）。
- 禁用重叠回收：如果为true，将发生应用程序池回收，以便在创建另一个工作进程之前退出现有工作进程
- 请求限制：应用程序池在回收之前可以处理的最大请求数。如果值为0，则表示应用程序池可以处理的请求数没有限制。
- 生成回收事件日志条目：每发生一次指定的回收事件时便产生一个事件日志条目。

![20240205125148](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125148.png)
**即使可以将IIS进程池回收关掉，仍然不建议把Quartz挂到IIS下，长时间不回收，会存在其他问题。所以推荐NetCore实质上是控制台程序，不存在内存回收问题。**

## 18.2 部署成服务
如果是exe程序或者是netcore的web程序可以部署成系统服务。可以借助nssm工具来实现，nssm相关知识可以参考[nssm详解](https://blog.csdn.net/liyou123456789/article/details/123094277 "nssm详解")，现在将上面的ui管理程序做成一个服务。
![20240205125209](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125209.png)
![20240205125217](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125217.png)

浏览器输入 `http://localhost:8100/` 正常访问
![20240205125225](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125225.png)

## 18.3 容器化运行
docker相关知识请参考[docker详解](https://blog.csdn.net/liyou123456789/article/details/122292877 "docker详解")，Linux相关知识请参考[Linux详解](https://blog.csdn.net/liyou123456789/article/details/121548156 "Linux详解")
```bash
docker run -v /fileData/quartzuifile:/app/File  --restart=unless-stopped --privileged=true --name quartzui -dp 5088:80 bennyzhao/quartzui
```

一行命令开箱即用，赶快体验下docker的便捷吧！
1. 其中`/fileData/quartzuifile`为映射的文件地址，如SQLite数据库和log日志
2. `5088`为映射到主机的端口
3. 直接在浏览器 `ip:5088` 即可访问。（注意防火墙是否打开了`5088`端口，或者在主机测试 `curl 127.0.0.1:5088`）

![20240205125248](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125248.png)

浏览器输入 `http://服务器IP:5088/` 正常访问
![20240205125255](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205125255.png)
