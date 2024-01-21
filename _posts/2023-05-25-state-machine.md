---
layout: post
title: "状态机"
author: "Kai"
header-style: text
tags:
  - 状态机
---

状态机（state machine）是一种计算模型，用于描述系统或程序在不同状态之间转换的行为。它可以用于建模和控制复杂系统的行为，包括软件、硬件和人类行为等。

状态机通常由一组状态、一组转换规则和一个初始状态组成。每个状态表示系统或程序所处的特定状态或情况，转换规则定义了在不同状态之间转换的条件和动作，初始状态表示系统或程序启动时的状态。

在状态机中，当一个系统或程序接收到一个输入时，它将根据当前状态和转换规则执行相应的操作，并将系统或程序的状态更新为新状态。状态机可以是有限状态机（finite state machine，FSM）或无限状态机（infinite state machine，ISM）。

有限状态机（FSM）是一种最简单的状态机，它的状态数是有限的。FSM 通常用于建模系统的简单行为，例如电子设备中的开关控制。无限状态机（ISM）是一种状态机，其状态数是无限的，例如计算机中的操作系统或网络协议等。

状态机的应用范围广泛，包括自动化控制、编译器、通信协议、人机界面等。状态机的概念是许多计算机科学领域的基础，也是软件设计和开发中不可或缺的一部分。

一个简单的状态机的例子是自动售货机（vending machine），其状态包括“待机”（idle）、“选择商品”（selecting）、“投入硬币”（coin-inserting）、“出货中”（dispensing）和“退币”（refund）等。以下是该状态机的状态转换图示例：

```
               ┌───────────┐
       ┌───────│ 选择商品 │─────────┐
       │       └───────────┘        │
       │             ▲                   │
┌──────┴──────┐  投入硬币  ┌───────┴─────┐
│   待机       ├───────────►│ 投入硬币  │
└─────────────┘         └─────────────┘
       │                            ▲
       │             ┌───────────┐  │
       └─────────────│ 退币        │──┘
                     └───────────┘
```
在“待机”状态下，自动售货机等待用户进行选择。当用户选择商品时，状态机转换到“选择商品”状态。在这个状态下，用户可以选择商品。一旦用户选择了商品，状态机转换到“投入硬币”状态，等待用户投入硬币。如果用户投入了足够的硬币，状态机转换到“出货中”状态，并出售商品。如果用户没有投入足够的硬币，则状态机会转换回“选择商品”状态，等待用户选择其他商品或继续投入硬币。在任何时候，用户都可以选择退还硬币，状态机会转换到“退币”状态，并返还用户硬币。

这个状态机的状态转换可以通过编程实现自动售货机的行为。状态机的使用可以使得程序的行为更清晰、简单和可控，从而提高软件的可靠性和可维护性。

以下是使用 C# 语言实现自动售货机状态机的示例代码：
```csharp
using System;

class VendingMachine
{
    private enum State
    {
        Idle,
        Selecting,
        CoinInserting,
        Dispensing,
        Refunding
    }

    private State currentState = State.Idle;

    public void SelectProduct()
    {
        if (currentState == State.Idle)
        {
            Console.WriteLine("Please select a product.");
            currentState = State.Selecting;
        }
        else
        {
            Console.WriteLine("Invalid operation in current state: {0}", currentState);
        }
    }

    public void InsertCoin(int coinValue)
    {
        if (currentState == State.Selecting)
        {
            Console.WriteLine("Coin inserted: {0}", coinValue);
            currentState = State.CoinInserting;
        }
        else if (currentState == State.CoinInserting)
        {
            Console.WriteLine("Coin inserted: {0}", coinValue);
        }
        else
        {
            Console.WriteLine("Invalid operation in current state: {0}", currentState);
        }
    }

    public void DispenseProduct()
    {
        if (currentState == State.CoinInserting)
        {
            Console.WriteLine("Product dispensed.");
            currentState = State.Dispensing;
        }
        else
        {
            Console.WriteLine("Invalid operation in current state: {0}", currentState);
        }
    }

    public void RefundCoins()
    {
        Console.WriteLine("Coins refunded.");
        currentState = State.Refunding;
    }

    public void Reset()
    {
        Console.WriteLine("Machine reset.");
        currentState = State.Idle;
    }
}
```
在上面的示例代码中，使用了枚举类型 State 表示自动售货机的不同状态。VendingMachine 类中包含了选择商品、投入硬币、出货和退币等操作的实现，其中使用了状态机来控制自动售货机的行为。

这个示例代码只是一个简单的演示，实际的自动售货机状态机可能更加复杂，但是基本的原理和方法都是相似的。