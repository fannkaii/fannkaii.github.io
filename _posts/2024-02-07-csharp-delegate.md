---
layout: post
title: "C#高级--特性详解"
author: "Kai"
header-style: text
tags:
  - C#
---

转自：https://blog.csdn.net/liyou123456789/article/details/119704294

# 1. 委托是什么
## 1.1 委托是什么
委托和类一样是一种用户自定义类型，它存储的就是一系列具有相同签名和返回类型的方法的地址，调用委托的时候，它所包含的所有方法都会被执行。

## 1.2 委托声明
（1）委托可以声明在类外部，也可以在类内部

（2）跟方法有点类似，有参数，返回值，访问修饰符，比方法声明多一个关键字delegate

```csharp
namespace MyDelegate
{
    /// <summary>
    /// 1.无参数无返回值委托
    /// </summary>
    public delegate void NoReturnNoParaOutClass();

    public class CustomDelegate
    {
        /// <summary>
        /// 2.无参数无返回值委托
        /// </summary>
        public delegate void NoReturnNoPara();
        /// <summary>
        /// 3.有参数无返回值委托
        /// </summary>
        /// <param name="x"></param>
        /// <param name="y"></param>
        public delegate void NoReturnWithPara(int x, int y);
        /// <summary>
        /// 4.无参数有返回值的委托
        /// </summary>
        /// <returns></returns>
        public delegate int WithReturnNoPara();
        /// <summary>
        /// 5.带参数带返回值的委托
        /// </summary>
        /// <param name="x"></param>
        /// <param name="y"></param>
        /// <returns></returns>
        public delegate int WithReturnWithPara(out int x, ref int y);
    }
}
```

## 1.3 委托的本质
反编译程序看委托NoReturnNoParaOutClass的IL代码
```csharp
.class public auto ansi sealed NoReturnNoParaOutClass
    extends [System.Runtime]System.MulticastDelegate
{
    .method public hidebysig specialname rtspecialname instance void .ctor(object 'object', native int 'method') runtime managed
    {
    }

    .method public hidebysig newslot virtual instance class [System.Runtime]System.IAsyncResult BeginInvoke(class [System.Runtime]System.AsyncCallback callback, object 'object') runtime managed
    {
    }

    .method public hidebysig newslot virtual instance void EndInvoke(class [System.Runtime]System.IAsyncResult result) runtime managed
    {
    }

    .method public hidebysig newslot virtual instance void Invoke() runtime managed
    {
    }
}
```

（1）委托是一个类，继承自MulticastDelegate  
MulticastDelegate这个类我们自己定义的类是无法继承的

（2）委托的构造函数，需要传递一个方法作为参数  
（3）委托的内部有三个方法Invoke，BeginInvoke，EndInvoke

# 2. 委托实例化和执行
## 2.1委托实例化
（1）通过New来实例化
（2）直接 = 一个方法，这个是编译器提供的语法糖
（3）直接 = 一个匿名委托
（4）直接 = 一个Lambda

## 2.2 委托执行
（1）Inovke执行委托  
    如果委托定义没有参数，则Inovke也没有参数；委托没有返回值，则Inovke也没有返回值  
（2）BeginInvoke开启一个新线程执行委托  
    NetCore不支持，NetFamework支持 NetCore有更好的多线程功能来支持实现类似功能  
（3）EndInvoke等待BeginInvoke执行完成后再执行





































































































































































