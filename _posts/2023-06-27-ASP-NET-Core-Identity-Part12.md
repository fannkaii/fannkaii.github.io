---
layout: post
title: "转：ASP.NET Core Identity 系列之十二：Lockout功能"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486301&idx=1&sn=be1c52b47c25f41121f2866c23e63a0e

这节我们主要介绍在ASP.NET Core Identity中如何锁定用户，Lockout功能为改善应用程序安全提供保障，当用户多次输入错误密码之后，用户账号就会被锁定，这个技术在防止黑客暴力攻击方面是非常有用的，特别是反复尝试猜测密码

# 1. 配置用户锁定功能
在Identity中启用用户的Lockout功能,可以通过IdentityOptions 来配置，代码如下：
![20240205143952](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143952.png)

通过设置AllowedForNewUsers属性为true启用类用户Lockout特性，另外设置DefaultLockoutTimeSpan 为10 并指定用户Lockout的时间跨度为10分钟，通过设置MaxFailedAccessAttempts=3来指定用户最大的访问失败次数 

在AspNetUsers表中和lockout设置相关的有3列：
- LockoutEnabled列指用户是否启用lockout功能
- AccessFailedCount列记录用户登录失败的次数并且在账户锁定后重置为0
- LockoutEnd列是一个DateTime 的类型，表示用户结束锁定的时间。我们通过下图来展示一下：

![20240205144018](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205144018.png)

# 2. 在登录页面实现用户的Lockout功能
我们已经在ASP.NET Core Identity中创建了用户登录功能并实现了用户在登录过程中的认证，我们登录的功能在 AccountController 中，我们已经添加了一个检查功能(result.IsLockedOut)判断用户是否LockOut, 并且告诉用户等待10分钟后再次登录
![20240205144051](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205144051.png)

第四列lockoutOnFailure设置为true，PasswordSignInAsync启用了Lockout功能

# 3. 测试用户Lockout功能
运行应用程序测试该功能，我们使用错误的密码尝试登录几次，查看一下表AccessFailedCount 列，我们注意到这个值增长。
![20240205144108](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205144108.png)

如果登录次数大于两次，我们将收到错误信息(你的账户已经被锁定，请等待10分钟后再次尝试)，如下图所示：
![20240205144120](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205144120.png)

事实上，我们可以进一步通知用户锁定的账号，请他重置密码或向用户发送通知报告，如果他们没有登录，这意味着有人试图破账号

# 4. 总结
在这节中我们主要涉及到图如下内容，如何在ASP.NET Core Identity 中创建Lockout 功能，如何实现用户的Lockout功能

源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/AspNetCore.Identity/Identity

