---
layout: post
title: "转：Visual Studio如何调试异步代码"
author: "Kai"
header-style: text
tags:
  - Visual Studio
  - 异步
---

转自：https://www.duidaima.com/Group/Topic/ASP.NET/10666

虽然异步代码可以提高程序的整体吞吐量，但异步代码仍然无法免除错误！当潜在的死锁、模糊的错误消息以及查找导致 Bug 的 Task 时，编写异步代码会使调试更加困难。幸运的是，Visual Studio 具有与托管的、本地的和 JavaScript 兼容的多个新旧功能，可帮助缓解调试异步代码的挫折感。下面，让我们开始吧。

# 在哪里可以看到程序的所有Task
当你在异步编程时遇到 Bug，你可能想看下你所有的 Task，然后确定到底是哪出错了。如果你调试过多线程应用，你可能会对“Threads”窗口比较熟悉。好消息，对于 Task 来说也是等效的窗口。“Tasks”窗口允许你查看所有任务，显示它们的 id、当前位置、最初传递给它们的方法以及它们在中断时的当前状态（active 活动的、scheduled 计划的、blocked 阻塞的或 deadlocked 死锁的）。如果你的程序是多线程的，此窗口还将显示运行每个任务的线程。这有助于识别可能导致问题的特定线程。
可以在***Debug > Windows > Task***或者使用***CTRL+SHIFT+D, K***来访问Tasks窗口。
![20240121215924](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121215924.png)


# 如何在我的异步代码中定位异常的源头
在异步编程时，确定抛出异常的源头往往是令人沮丧的。当一个异常被多次抛出，Visual Studio 通常返回调用堆栈，其中的异常是最近通过 Exception Helper 抛出的。不幸的是，这并不总是有助于异步调试。为了解决这个问题，我们在 VS16.4 版本中实现了重新抛出异常。使用此更新，Exception Helper 将在重新抛出异常时显示原始调用堆栈。要了解更多关于这个新特性的信息，请查看 Andy Sterland 的博客文章（https://devblogs.microsoft.com/visualstudio/exception-helper-rethrown-exceptions ）。
![20240121215935](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121215935.png)

# 有没有办法更好地可视化任务和异步代码流？
对于异步代码执行的图形化描述，Visual Studio 具有线程和任务的并行堆栈（Parallel Stacks）窗口。对 Visual Studio 16.6 进行了改进，任务的并行堆栈窗口（或并行任务窗口 Parallel Tasks）直观地显示活动、等待和计划的任务以及它们之间的关系。双击活动任务或等待任务，将在“调用堆栈（Call Stack）”窗口中显示异步调用堆栈。若要了解哪个线程正在运行特定任务，可以在“并行线程（Parallel Threads）”和“并行任务（Parallel Tasks）”窗口之间进行交换。可以通过右键单击并在上下文菜单中选择“转到线程（Go To Thread）”来完成此操作。
![20240121215949](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121215949.png)

# 准备好使用这些工具来调试异步代码了吗?
现在你有了更多的工具来帮助你调试代码，和我们分享你的反馈吧！您的想法和功能建议将帮助我们创建最好的异步调试体验。

# 原文链接
https://devblogs.microsoft.com/visualstudio/how-do-i-debug-async-code-in-visual-studio/?utm_source=vs_developer_news&utm_medium=referral