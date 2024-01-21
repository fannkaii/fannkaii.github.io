---
layout: post
title: "SSIS基本术语"
author: "Kai"
header-style: text
tags:
  - Windows
---

### Package 包
The unit of work for SSIS. Any development done in SSIS is within what’s known as a package.
SSIS的工作单元。在SSIS中完成的任何开发都在所谓的包中。
### Connection Manager 连接管理器
Stores the connection string information for your sources and destinations that will be used in a package
存储将在包中使用的源和目标的连接字符串信息。
### Control Flow 控制流
The component of SSIS packages that controls the order of operations and flow of a package. There can only be one of these per package.
SSIS包的组件，用于控制包的操作顺序和流程。每个包只能有一个。
### Tasks 任务
These tell SSIS what operations to perform in the Control Flow.
这些告诉SSIS在控制流中执行哪些操作。
### Precedence Contraints 优先约束
A part of the Control Flow that directs movement between multiple Control Flow Tasks.
控制流的一部分，用于指导多个控制流任务之间的移动。
### Data Flow 数据流
This is a Task that coordinates data movement and transformations. This is generally where most ETL work is done.
这是一个协调数据移动和转换的任务。这通常是大多数ETL工作完成的地方。
### Transformations 转换
Within a Data Flow transforms allow you to apply business rules to your data prior to loading it into a destination.
在数据流转换中，您可以在将数据加载到目标之前将业务规则应用于数据。
### Event Handler 事件处理程序
Component of SSIS that allows you to control what happens in the event of an error.
SSIS的组件，允许您控制发生错误时发生的情况。(可以创建数据日志的事件处理程序；发送Email创建一个事件；如果发生错误或其他事情，我的包将如何处理执行不同的逻辑)

------------


### Connection Managers 连接管理器
Reusable 可重复使用的
Define a connection manager once and reuse over and over Project Connection Managers
一次定义连接管理器并重复使用项目连接管理器

### Naming Conventions 命名约定
Proper naming conventions can help you clearly identify connections across multiple packages.
正确的命名约定可以帮助您清楚地识别多个包之间的连接。

命名约定：ProviderType_DatabaseType_DatabaseName
命名举例：OLEDB_SQL_PWInsurance

### Control Flow 控制流
![20240121214504](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121214504.png)

- 图为一个SSIS包的示例，图中3个箭头分别指向控制流的不同部分
- 图中任何一个有框的都是Task，都是控制流的一部分。这些Task将根据它们的类型执行不同种类的操作
- 图中Log Failure Status是一段自定义脚本
- 为你做某些事情的Task可以用.NET或VB或文件系统任务来写。
### Task 任务
Executes an operation in the Control Flow. 在控制流中执行操作
### Percedence Constraint 优先约束
Success, Failure, Completion 成功、失败、完成
AND/OR Operations AND/OR 操作
优先约束帮助我们定义控制流的操作顺序，以及它将如何运行绿色、红色、蓝色，成功、失败、完成。例如Step-1成功，它将进入Step-2。如果Step-2成功，它将进入Step-3。Get customer List完成后将进入Step-3。然后，现在有Step-3的三种方式，可以转到完成，但是如果失败了，它会记录失败的原因。
### Containers 容器
Can be used for organizing elements of a package 可用于组织包的元素
Some containers have the ability to loop or iterate over control flow tasks. 一些容器能够循环或迭代控制流任务
有几种不同类型的容器，有的容器只是用来组织元素，例如序列容器。还有一些容器可以执行循环或迭代控制流任务。
```
Data Flow
Sources(Extract)
SQL Server, Flat Files, Excel, etc…

Transformations(Transform)
Data Conversion, Aggregate, etc…

Destinations(Load)
SQL Server, Flat Files, Excel, etc…
```