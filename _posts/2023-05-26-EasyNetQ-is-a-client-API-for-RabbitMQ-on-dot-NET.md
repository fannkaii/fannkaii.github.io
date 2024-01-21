---
layout: post
title: "转：EasyNetQ 一款.NET上RabbitMQ的领先客户端API"
author: "Kai"
header-style: text
tags:
  - RabbitMQ
  - C#
---

转自：https://www.duidaima.com/Group/Topic/ASP.NET/10511

## 背景
在现在的项目中，消息队列的使用比较的频繁，消息队列的种类也较多，如：ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ等。消息队列中间件是分布式系统中重要的组件，主要解决应用耦合，异步消息，流量削锋等问题。实现高性能，高可用，可伸缩和最终一致性架构。是大型分布式系统不可缺少的中间件。

其中RabbitMQ是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。RabbitMQ服务器是用Erlang语言编写的，而集群和故障转移是构建在开放电信平台框架上的。所有主要的编程语言均有与代理接口通讯的客户端库。

## 特性
- 可伸缩性：集群服务
- 消息持久化：从内存持久化消息到硬盘，再从硬盘加载到内存 
- 目前是.NET使用率比较高的消息队列；

## 好用的轮子
其中EasyNetQ是.NET上RabbitMQ的领先客户端API，在NuGet.org上有超过400万次下载。这是一个开放源代码项目。EasyNetQ是为了提供一个尽可能简洁的适用与RabbitMQ的.NET类库。为了实现这些目标，EasyNetQ提供一种自认为你会在.NET下使用RabbitMQ的视图。为了保持使用灵活性，简单起见，EasyNetQ强制使用了一些简单的约定。包括如下:
1. 消息用 .NET 类型表示
2. 消息通过.NET类型路由

## 目标
尽可能简化在.NET上使用RabbitMQ的工作。

## 入门
要连接到RabbitMQ代理
```csharp
var bus = RabbitHutch.CreateBus("host=localhost");
```

要发布消息
```csharp
bus.Publish(message);
```

订阅消息
```csharp
bus.Subscribe<MyMessage>("my_subscription_id", msg => Console.WriteLine(msg.Text));
```

远程过程调用
```csharp
var request = new TestRequestMessage {Text = "Hello from the client! "};
bus.Request<TestRequestMessage, TestResponseMessage>(request, response =>
    Console.WriteLine("Got response: '{0}'", response.Text));
```

RPC服务器
```csharp
bus.Respond<TestRequestMessage, TestResponseMessage>(request =>
  new TestResponseMessage{ Text = request.Text + " all done!" });
```

## 开源地址
https://github.com/EasyNetQ/EasyNetQ