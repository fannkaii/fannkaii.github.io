---
layout: post
title: "设计模式之美"
author: "Kai"
header-style: text
tags:
  - 编程思想
---

转自：https://juejin.cn/post/7123029355365662734

# 1. 概述
## 1.1 学习导读
本文是极客时间专栏《设计模式之美》的学习笔记，详情请看原文。
**学习算法**：是为了写出 高效 的代码；
**学习设计模式**：是为了写出 高质量 (可扩展、可读、可维护)的代码；

## 1.2 为什么学习设计模式
- **应对面试**，算法、设计模式之类问题是常问题，有备无患。
- **告别烂代码**，代码能力是一个程序员最基础的能力，是一个程序员基础素养的最直接的衡量标准。代码写得好，能让你在团队中脱颖而出。写出一份漂亮的代码，你自己也会很有成就感。
- **提高复杂代码的设计和开发能力**，只是完成功能、代码能用，可能并不复杂，但是要想写出易扩展、易用、易维护的代码，并不容易。刻意练习这方面的能力，让写出高质量代码成为一种习惯。
- **让读源码、学框架事半功倍**，优秀的开源项目、框架、中间件，代码量、类的个数都会比较多，类结构、类之间的关系极其复杂，代码中会使用到很多设计模式、设计原则或者设计思想，学好相关知识，能让你更轻松地读懂开源项目，还能参透技术精髓，做到事半功倍。
- **职场发展做铺垫**，如果你想成长为技术大牛，那就要重视基本功。如果你需要承担一些指导培养初级员的工作，你需要一套写好代码的方法论。如果你是一个技术leader，你需要为项目质量负责，代码质量低会导致线上 bug 频发，排查困难，整个团队都陷在成天修改无意义的低级 bug、在烂代码中添补丁的事情中，而一个设计良好、易维护的系统，可以解放我们的时间，让我们做些更加有意义、更能提高自己和团队能力的事情。如果你需要招聘技术人员，你要考察候选人的设计能力、代码能力，那设计模式相关的问题便是一个很好的考察点。

# 2. 代码质量评判标准
## 2.1 如何评价代码质量的高低？
对一段代码的质量评价，标准多，常常具有很强的主观性，需要综合各个维度。

## 2.2 最常用的评价标准有哪几个？
- **可维护性**：在不破坏原有代码设计、不引入新的bug的情况下，能够快速地修改或者添加代码。
- **可读性**：需要看代码是否符合编码规范、命名是否达意、注释是否详尽、函数是否长短合适、模块划分是否清晰、是否符合高内聚低耦合等等。
- **可扩展性**：代码预留扩展点，你可以把新功能代码，直接插到扩展点上，无需改动大量的原始代码。
- **灵活性**：一段代码易扩展、易复用或者易用，我们都可以称这段代码写得比较灵活。
- **简洁性**：代码简单、逻辑清晰，也就意味着易读、易维护。思从深而行从简，真正的高手能云淡风轻地用最简单的方法解决最复杂的问题。
- **可复用性**：尽量减少重复代码的编写，复用已有的代码。
- **可测试性**：代码的可测试性差，比较难写单元测试，那基本上就能说明代码设计得有问题。

## 2.3 如何才能写出高质量的代码？
- **面向对象**：因为其具有丰富的特性（封装、抽象、继承、多态），可以实现很多复杂的设计思路，是很多设计原则、设计模式等编码实现的基础。
- **设计原则**：设计原则是指导我们代码设计的一些经验总结。对于某些场景下，是否应该应用某种设计模式，具有指导意义。比如，“开闭原则”是很多设计模式（策略、模板等）的指导原则。
- **设计模式**：设计模式是针对软件开发中经常遇到的一些设计问题，总结出来的一套解决方案或者设计思路。大部分设计模式要解决的都是代码的可扩展性问题。从抽象程度上来讲，设计原则比设计模式更抽象。设计模式更加具体、更加可执行。
- **编程规范**：编程规范主要解决的是代码的可读性问题，更加偏重代码细节，是持续的小重构依赖的理论基。
- **代码重构**：利用前面四种理论，保持代码质量不下降的有效手段。

# 3. 面向对象
## 3.1 面向对象概述
### 3.1.1 三种主流的编程范式
- **面向过程**
- **面向对象**
- **函数式编程**

### 3.1.2 面向对象
- **面向对象编程 (OOP，Object Oriented Programming)**：是一种编程范式或编程风格。它以类或对象作为组织代码的基本单元，并将封装、抽象、继承、多态四个特性，作为代码设计和实现的基石 。
- **面向对象编程语言 (OOPL，Object Oriented Language)**：是支持类或对象的语法机制，并有现成的语法机制，能方便地实现面向对象编程四大特性（封装、抽象、继承、多态）的编程语言。

## 3.2 面向对象四大特性
### 3.2.1 封装
**概念**：信息隐藏或数据访问保护，类通过暴露有限的访问接口，授权外部仅能通过类提供的方式访问内部信息或数据。
**特点**：需要编程语言提供权限访问控制语法来支持，例如 Java 中的 private、protected、public 关键字
```java
public class Wallet {
  private String id;
  private long createTime;
  private BigDecimal balance;
  private long balanceLastModifiedTime;
  // ...省略其他属性...

  public Wallet() {
     this.id = IdGenerator.getInstance().generate();
     this.createTime = System.currentTimeMillis();
     this.balance = BigDecimal.ZERO;
     this.balanceLastModifiedTime = System.currentTimeMillis();
  }

  // 注意：下面对get方法做了代码折叠，是为了减少代码所占文章的篇幅
  public String getId() { return this.id; }
  public long getCreateTime() { return this.createTime; }
  public BigDecimal getBalance() { return this.balance; }
  public long getBalanceLastModifiedTime() { return this.balanceLastModifiedTime;  }

  public void increaseBalance(BigDecimal increasedAmount) {
    if (increasedAmount.compareTo(BigDecimal.ZERO) < 0) {
      throw new InvalidAmountException("...");
    }
    this.balance.add(increasedAmount);
    this.balanceLastModifiedTime = System.currentTimeMillis();
  }

  public void decreaseBalance(BigDecimal decreasedAmount) {
    if (decreasedAmount.compareTo(BigDecimal.ZERO) < 0) {
      throw new InvalidAmountException("...");
    }
    if (decreasedAmount.compareTo(this.balance) > 0) {
      throw new InsufficientAmountException("...");
    }
    this.balance.subtract(decreasedAmount);
    this.balanceLastModifiedTime = System.currentTimeMillis();
  }
}
```

**意义**：
- 保护数据不被随意修改，提高代码的可维护性
- 仅暴露有限的必要接口，提高类的易用性

### 3.2.2 抽象
**概念**：隐藏方法的具体实现，让调用者只需要关心方法提供了哪些功能，并不需要知道这些功能是如何实现的。
**特点**：常利用编程语言提供的 接口类(如Java中的Interface)或抽象类(如Java中的abstract) 这两种语法机制来实现抽象。
```java
public interface IPictureStorage {
  void savePicture(Picture picture);
  Image getPicture(String pictureId);
  void deletePicture(String pictureId);
  void modifyMetaInfo(String pictureId, PictureMetaInfo metaInfo);
}

public class PictureStorage implements IPictureStorage {
  // ...省略其他属性...
  @Override
  public void savePicture(Picture picture) { ... }
  @Override
  public Image getPicture(String pictureId) { ... }
  @Override
  public void deletePicture(String pictureId) { ... }
  @Override
  public void modifyMetaInfo(String pictureId, PictureMetaInfo metaInfo) { ... }
}
```

**意义**：
- 修改实现不需要改变定义
- 处理复杂系统的有效手段，能有效地过滤掉不必要关注的信息

### 3.2.3 继承
**概念**：表示类之间的is-a关系，比如：猫是一种哺乳动物。
**特点**：编程语言需要提供特殊的语法机制来支持。比如 Java 使用 extends 关键字来实现继承，C++ 使用冒号（class B : public A），Python 使用 parentheses ()，Ruby 使用 <。

**2种模式**：
- 单继承表示一个子类只继承一个父类
- 多继承表示一个子类可以继承多个父类

**意义**：解决代码复用的问题，两个类具有相同属性或方法，将这部分代码抽取到父类中，让两个类继承父类，子类重用父类代码，避免代码重复。

**缺陷**：过度使用继承，继承层次过深过复杂，就会导致代码可读性、可维护性变差。

### 3.2.4 多态
**概念**：子类可以替代父类，在实际的运行过程中，调用子类的方法实现。
**特点**：需要编程语言提供特殊的语法机制来实现，比如继承、接口类、duck-typing。

**利用继承实现多态特性。**
- 第一个语法机制是编程语言要支持父类对象可以引用子类对象。
- 第二个语法机制是编程语言要支持继承。
- 第三个语法机制是编程语言要支持子类可以重写（override）父类中的方法。

```java
public class DynamicArray {
  private static final int DEFAULT_CAPACITY = 10;
  protected int size = 0;
  protected int capacity = DEFAULT_CAPACITY;
  protected Integer[] elements = new Integer[DEFAULT_CAPACITY];

  public int size() { return this.size; }
  public Integer get(int index) { return elements[index];}
  //...省略n多方法...

  public void add(Integer e) {
    ensureCapacity();
    elements[size++] = e;
  }

  protected void ensureCapacity() {
    //...如果数组满了就扩容...代码省略...
  }
}

public class SortedDynamicArray extends DynamicArray {
  @Override
  public void add(Integer e) {
    ensureCapacity();
    int i;
    for (i = size-1; i>=0; --i) { //保证数组中的数据有序
      if (elements[i] > e) {
        elements[i+1] = elements[i];
      } else {
        break;
      }
    }
    elements[i+1] = e;
    ++size;
  }
}

public class Example {
  public static void test(DynamicArray dynamicArray) {
    dynamicArray.add(5);
    dynamicArray.add(1);
    dynamicArray.add(3);
    for (int i = 0; i < dynamicArray.size(); ++i) {
      System.out.println(dynamicArray.get(i));
    }
  }

  public static void main(String args[]) {
    DynamicArray dynamicArray = new SortedDynamicArray();
    test(dynamicArray); // 打印结果：1、3、5
  }
}
```

**利用接口类来实现多态特性。**

```java
public interface Iterator {
  boolean hasNext();
  String next();
  String remove();
}

public class Array implements Iterator {
  private String[] data;

  public boolean hasNext() { ... }
  public String next() { ... }
  public String remove() { ... }
  //...省略其他方法...
}

public class LinkedList implements Iterator {
  private LinkedListNode head;

  public boolean hasNext() { ... }
  public String next() { ... }
  public String remove() { ... }
  //...省略其他方法...
}

public class Demo {
  private static void print(Iterator iterator) {
    while (iterator.hasNext()) {
      System.out.println(iterator.next());
    }
  }

  public static void main(String[] args) {
    Iterator arrayIterator = new Array();
    print(arrayIterator);

    Iterator linkedListIterator = new LinkedList();
    print(linkedListIterator);
  }
}
```

**使用duck-typing 实现多态特性。**
只要两个类具有相同的方法，就可以实现多态，并不要求两个类之间有任何关系，这就是所谓的 duck-typing。
```python
class Logger:
    def record(self):
        print(“I write a log into file.”)

class DB:
    def record(self):
        print(“I insert data into db. ”)

def test(recorder):
    recorder.record()

def demo():
    logger = Logger()
    db = DB()
    test(logger)
    test(db)
```
**意义**：**提高代码的扩展性和复用性，很多设计原则、设计模式、编程技巧的代码实现基础**。比如策略模式、基于接口而非实现编程、依赖倒置原则、里式替换原则、利用多态去掉冗长的 if-else 语句等等。


## 3.3 面向过程 VS 面向对象
### 3.3.1 面向过程
- **面向过程编程(POP)**：一种编程范式或编程风格。它以过程（可以理解为方法、函数、操作）作为组织代码的基本单元，以数据（可以理解为成员变量、属性）与方法相分离为最主要的特点。
- **面向过程编程语言(POPL)**：最大的特点是不支持类和对象两个语法概念，不支持丰富的面向对象编程特性（比如继承、多态、封装），仅支持面向过程编程。
- **面向过程和面向对象最基本的区别就是**，代码的组织方式不同。

### 3.3.2 面向对象编程 VS 面向过程编程
- 对于大规模复杂程序的开发，程序的处理流程并非单一的一条主线，而是错综复杂的网状结构。面向对象编程比起面向过程编程，**更能应对这种复杂类型的程序开发**。
- 面向对象编程相比面向过程编程，具有更加丰富的特性（封装、抽象、继承、多态）。利用这些特性编写出来的代码，**更加易扩展、易复用、易维护**。
- 从编程语言跟机器打交道的方式的演进规律中，我们可以总结出：面向对象编程语言比起面向过程编程语言，**更加人性化、更加高级、更加智能**。

### 3.3.3 违反面向对象编程风格的典型设计
- 滥用getter、setter方法
- Constants类、Utils类的设计问题
- 基于贫血模型的开发模式

### 3.3.4 在OOP中，为什么容易写出面向过程代码
- **面向过程编程风格恰恰符合人的这种流程化思维方式**。而面向对象编程风格正好相反。它是一种自底向上的思考方式。
- **面向对象编程要比面向过程编程难一些**。在面向对象编程中，类的设计还是挺需要技巧，挺需要一定设计经验的。

### 3.3.5 面向过程编程使用场景
开发的是微小程序，面向过程的编程风格就更适合一些。
面向过程编程是面向对象编程的基础，面向对象编程离不开基础的面向过程编程。
只要我们能避免面向过程编程风格的一些弊端，控制好它的副作用，在掌控范围内为我们所用，我们就大可不用避讳在面向对象编程中写面向过程风格的代码。

## 3.4 面向对象分析、设计与编程
### 3.4.1 面向对象分析、设计与编程
面向对象分析（OOA，Object Oriented Analysis) 、面向对象设计（OOD，Object Oriented Design)、面向对象编程（OOP，Object Oriented Program)，是面向对象开发的三个主要环节。
- 面向对象分析：搞清楚做什么，产出详细的需求分析
- 面向对象设计：搞清楚怎么做，将需求描述转化为具体的类
- 面向对象编程：将分析和设计的结果翻译成代码的过程

### 3.4.2 面向对象分析（OOA，Object Oriented Analysis)
需求分析的过程实际上是一个不断迭代优化的过程。我们不要试图一下就给出一个完美的解决方案，而是**先给出一个粗糙的、基础的方案，有一个迭代的基础，通过“提出问题 - 解决问题”的方式，循序渐进地进行优化，最后得到一个足够清晰、可落地的需求描述**。这样一个思考过程能让我们摆脱无从下手的窘境。

### 3.4.3 面向对象设计（OOD，Object Oriented Design)
面向对象设计和实现要做的事情就是把合适的代码放到合适的类中。至于到底选择哪种划分方法，判定的标准是让代码尽量地满足“松耦合、高内聚”、单一职责、对扩展开放对修改关闭等我们之前讲到的各种设计原则和思想，尽量地做到代码可复用、易读、易扩展、易维护。

面向对象分析的产出是详细的需求描述。面向对象设计的产出是类。在面向对象设计这一环节中，我们将需求描述转化为具体的类的设计。这个环节的工作可以拆分为下面四个部分。

- 划分职责进而识别出有哪些类
根据需求描述，我们把其中涉及的功能点，一个一个罗列出来，然后再去看哪些功能点职责相近，操作同样的属性，可否归为同一个类。

- 定义类及其属性和方法
我们识别出需求描述中的动词，作为候选的方法，再进一步过滤筛选出真正的方法，把功能点中涉及的名词，作为候选属性，然后同样再进行过滤筛选。

- 定义类与类之间的交互关系
UML 统一建模语言中定义了六种类之间的关系。它们分别是：泛化、实现、关联、聚合、组合、依赖。我们从更加贴近编程的角度，对类与类之间的关系做了调整，保留四个关系：泛化、实现、组合、依赖。

- 将类组装起来并提供执行入口
我们要将所有的类组装在一起，提供一个执行入口。这个入口可能是一个 main() 函数，也可能是一组给外部用的 API 接口。通过这个入口，我们能触发整个代码跑起来。

### 3.4.4 统一建模语言(UML，Unified Model Language)
UML 统一建模语言，面向对象设计分析的工具，常用来表达设计思路。

UML 统一建模语言定义了六种类之间的关系。它们分别是：泛化、实现、关联、聚合、组合、依赖。

- 泛化（Generalization）可以简单理解为继承关系。（在一般的语境中，泛化指的是从具体的例子或情况中提炼出一般性的概念、规则或原则。**所以在UML图中，泛化连线的箭头肯定会指向父类**）
```java
public class A { ... }
public class B extends A { ... }
```

- 实现（Realization）一般是指接口和实现类之间的关系。
```java
public interface A {...}
public class B implements A { ... }
```

- 聚合（Aggregation）是一种包含关系，A 类对象包含 B 类对象，B 类对象可以单独存在，比如课程与学生之间的关系。
```java
public class A {
  private B b;
  public A(B b) {
    this.b = b;
  }
}
```

- 组合（Composition）也是一种包含关系，A 类对象包含 B 类对象，B 类对象不可单独存在，比如鸟与翅膀之间的关系。
```java
public class A {
  private B b;
  public A() {
    this.b = new B();
  }
}
```

- 关联（Association）是一种非常弱的关系，B 类对象是 A 类的成员变量，那 B 类和 A 类就是关联关系。
```java
public class A {
  private B b;
  public A(B b) {
    this.b = b;
  }
}
或者
public class A {
  private B b;
  public A() {
    this.b = new B();
  }
}
```

- 依赖（Dependency）是一种比关联关系更加弱的关系，只要 B 类对象和 A 类对象有任何使用关系，我们都称它们有依赖关系。
```java
public class A {
  private B b;
  public A(B b) {
    this.b = b;
  }
}
或者
public class A {
  private B b;
  public A() {
    this.b = new B();
  }
}
或者
public class A {
  public void func(B b) { ... }
}
```

**图形说明**
- 泛化：空心三角箭头实线
- 实现：空心三角箭头虚线
- 聚合：空心菱形箭头实线
- 组合：实心菱形箭头实线
- 关联：实心三角箭头实现
- 依赖：实心三角箭头虚线

![20240204105724](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204105724.png)

## 3.5 接口VS抽象类
### 3.5.1 抽象类特性
- 抽象类不允许被实例化，只能被继承。也就是说，你不能 new 一个抽象类的对象出来。
- 抽象类可以包含属性和方法。方法既可以包含代码实现，也可以不包含代码实现。不实现的方法叫作抽象方法。
- 子类继承抽象类，必须实现抽象类中的所有抽象方法。

```java
// 抽象类
public abstract class Logger {
  private String name;
  private boolean enabled;
  private Level minPermittedLevel;

  public Logger(String name, boolean enabled, Level minPermittedLevel) {
    this.name = name;
    this.enabled = enabled;
    this.minPermittedLevel = minPermittedLevel;
  }

  public void log(Level level, String message) {
    boolean loggable = enabled && (minPermittedLevel.intValue() <= level.intValue());
    if (!loggable) return;
    doLog(level, message);
  }

  protected abstract void doLog(Level level, String message);
}
// 抽象类的子类：输出日志到文件
public class FileLogger extends Logger {
  private Writer fileWriter;

  public FileLogger(String name, boolean enabled,
    Level minPermittedLevel, String filepath) {
    super(name, enabled, minPermittedLevel);
    this.fileWriter = new FileWriter(filepath); 
  }

  @Override
  public void doLog(Level level, String mesage) {
    // 格式化level和message,输出到日志文件
    fileWriter.write(...);
  }
}
// 抽象类的子类: 输出日志到消息中间件(比如kafka)
public class MessageQueueLogger extends Logger {
  private MessageQueueClient msgQueueClient;

  public MessageQueueLogger(String name, boolean enabled,
    Level minPermittedLevel, MessageQueueClient msgQueueClient) {
    super(name, enabled, minPermittedLevel);
    this.msgQueueClient = msgQueueClient;
  }

  @Override
  protected void doLog(Level level, String mesage) {
    // 格式化level和message,输出到消息中间件
    msgQueueClient.send(...);
  }
}
```

### 3.5.2 接口特性
- 接口不能包含属性（也就是成员变量）。
- 接口只能声明方法，方法不能包含代码实现。
- 类实现接口的时候，必须实现接口中声明的所有方法。

```java
// 接口
public interface Filter {
  void doFilter(RpcRequest req) throws RpcException;
}
// 接口实现类：鉴权过滤器
public class AuthencationFilter implements Filter {
  @Override
  public void doFilter(RpcRequest req) throws RpcException {
    //...鉴权逻辑..
  }
}
// 接口实现类：限流过滤器
public class RateLimitFilter implements Filter {
  @Override
  public void doFilter(RpcRequest req) throws RpcException {
    //...限流逻辑...
  }
}
// 过滤器使用Demo
public class Application {
  // filters.add(new AuthencationFilter());
  // filters.add(new RateLimitFilter());
  private List<Filter> filters = new ArrayList<>();

  public void handleRpcRequest(RpcRequest req) {
    try {
      for (Filter filter : filters) {
        filter.doFilter(req);
      }
    } catch(RpcException e) {
      // ...处理过滤结果...
    }
    // ...省略其他处理逻辑...
  }
}
```

### 3.5.3 抽象类和接口意义
- 抽象类是对成员变量和方法的抽象，是一种 is-a 关系，是为了解决代码复用问题。子类必须实现抽象方法，在某些需求下实现代码会更加优雅。
- 接口仅仅是对方法的抽象，是一种 has-a 关系，表示具有某一组行为特性，是为了解决解耦问题，隔离接口和具体的实现，提高代码的扩展性。

### 3.5.4 抽象类和接口应用场景
- 如果要表示一种 is-a 的关系，并且是为了解决代码复用问题，我们就用抽象类。
- 如果要表示一种 has-a 关系，并且是为了解决抽象而非代码复用问题，那我们就用接口。

## 3.6 基于接口而非实现编程
### 3.6.1 为什么基于接口而非实现编程
- 将接口和实现相分离，封装不稳定的实现，暴露稳定的接口。
- 即“基于抽象而非实现编程"，抽象就是提高代码扩展性、灵活性、可维护性最有效的手段之一。

### 3.6.2 如何应用基于接口而非实现编程
- 函数的命名不能暴露任何实现细节。
- 封装具体的实现细节。
- **为实现类定义抽象的接口。** 依赖统一的接口定义，使用者依赖接口，而不是具体实现类来编程。

### 3.6.3 是否需要为每个类定义接口
某个功能只有一种实现方式，未来也不可能被其他实现方式替换，那我们就没有必要为其设计接口。

## 3.7 多用组合少用继承
### 3.7.1 为什么不推荐使用继承
**继承层次过深、过复杂，也会影响到代码的可维护性。** 在这种情况下，我们应该尽量少用，甚至不用继承。

### 3.7.2 组合相比继承有哪些优势
- 继承的特性可以通过组合、接口、委托来达成。
- 组合还能解决层次过深、过复杂的继承关系影响代码可维护性的问题。

```java
public interface Flyable {
  void fly();
}
public interface Tweetable {
  void tweet();
}
public interface EggLayable {
  void layEgg();
}
public class Ostrich implements Tweetable, EggLayable {//鸵鸟
  //... 省略其他属性和方法...
  @Override
  public void tweet() { //... }
  @Override
  public void layEgg() { //... }
}
public class Sparrow impelents Flyable, Tweetable, EggLayable {//麻雀
  //... 省略其他属性和方法...
  @Override
  public void fly() { //... }
  @Override
  public void tweet() { //... }
  @Override
  public void layEgg() { //... }
}
```

```java
public interface Flyable {
  void fly()；
}
public class FlyAbility implements Flyable {
  @Override
  public void fly() { //... }
}
//省略Tweetable/TweetAbility/EggLayable/EggLayAbility

public class Ostrich implements Tweetable, EggLayable {//鸵鸟
  private TweetAbility tweetAbility = new TweetAbility(); //组合
  private EggLayAbility eggLayAbility = new EggLayAbility(); //组合
  //... 省略其他属性和方法...
  @Override
  public void tweet() {
    tweetAbility.tweet(); // 委托
  }
  @Override
  public void layEgg() {
    eggLayAbility.layEgg(); // 委托
  }
}
```

### 3.7.3 如何判断该用组合还是继承
鼓励多用组合少用继承，但组合也并不是完美的，继承也并非一无是处。
- 如果类之间的继承结构稳定，层次比较浅，关系不复杂，用继承。反之，用组合。
- 有一些设计模式、特殊的应用场景，会固定使用继承或者组合。

## 3.8 贫血模型VS充血模型
### 3.8.1 基于贫血模型的传统开发模式
MVC 三层架构中的 M 表示 Model，V 表示 View，C 表示 Controller。

很多 Web 或者 App 项目都是前后端分离的，后端负责暴露接口给前端调用。这种情况下，我们一般就将后端项目分为 Repository 层、Service 层、Controller 层。其中，Repository 层负责数据访问，Service 层负责业务逻辑，Controller 层负责暴露接口。

我们平时开发 Web 后端项目的时候，基本上都是这么组织代码的。其中，UserEntity 和 UserRepository 组成了数据访问层，UserBo 和 UserService 组成了业务逻辑层，UserVo 和 UserController 在这里属于接口层。

我们可以发现，UserBo 是一个纯粹的数据结构，只包含数据，不包含任何业务逻辑。业务逻辑集中在 UserService 中。我们通过 UserService 来操作 UserBo。换句话说，Service 层的数据和业务逻辑，被分割为 BO 和 Service 两个类中。像 UserBo 这样，只包含数据，不包含业务逻辑的类，就叫作贫血模型（Anemic Domain Model）。同理，UserEntity、UserVo 都是基于贫血模型设计的。这种贫血模型将数据与操作分离，破坏了面向对象的封装特性，是一种典型的面向过程的编程风格。

基于贫血模型的传统开发模式受欢迎原因主要有三点
- 大部分情况下，我们开发的系统业务可能都比较简单。
- 充血模型的设计要比贫血模型更加有难度。
- 思维已固化，转型有成本。

```java
////////// Controller+VO(View Object) //////////
public class UserController {
  private UserService userService; //通过构造函数或者IOC框架注入

  public UserVo getUserById(Long userId) {
    UserBo userBo = userService.getUserById(userId);
    UserVo userVo = [...convert userBo to userVo...];
    return userVo;
  }
}

public class UserVo {//省略其他属性、get/set/construct方法
  private Long id;
  private String name;
  private String cellphone;
}

////////// Service+BO(Business Object) //////////
public class UserService {
  private UserRepository userRepository; //通过构造函数或者IOC框架注入

  public UserBo getUserById(Long userId) {
    UserEntity userEntity = userRepository.getUserById(userId);
    UserBo userBo = [...convert userEntity to userBo...];
    return userBo;
  }
}

public class UserBo {//省略其他属性、get/set/construct方法
  private Long id;
  private String name;
  private String cellphone;
}

////////// Repository+Entity //////////
public class UserRepository {
  public UserEntity getUserById(Long userId) { //... }
}

public class UserEntity {//省略其他属性、get/set/construct方法
  private Long id;
  private String name;
  private String cellphone;
}
```

### 3.8.2 基于充血模型的 DDD 开发模式
- 什么是DDD领域驱动设计
	领域驱动设计，即 DDD，主要是用来指导如何解耦业务系统，划分业务模块，定义业务领域模型及其交互。领域驱动设计这个概念并不新颖，早在 2004 年就被提出了，到现在已经有十几年的历史了。不过，它被大众熟知，还是基于另一个概念的兴起，那就是微服务。

	除了监控、调用链追踪、API 网关等服务治理系统的开发之外，微服务还有另外一个更加重要的工作，那就是针对公司的业务，合理地做微服务拆分。而领域驱动设计恰好就是用来指导划分服务的。所以，微服务加速了领域驱动设计的盛行。

	做好领域驱动设计的关键是，看你对自己所做业务的熟悉程度，而并不是对领域驱动设计这个概念本身的掌握程度。即便你对领域驱动搞得再清楚，但是对业务不熟悉，也并不一定能做出合理的领域设计。所以，不要把领域驱动设计当银弹，不要花太多的时间去过度地研究它。


- 什么是充血模型
充血模型（Rich Domain Model），数据和对应的业务逻辑被封装到同一个类中。因此，这种充血模型满足面向对象的封装特性，是典型的面向对象编程风格。

- 基于充血模型的DDD开发模式
在基于充血模型的 DDD 开发模式中，Service 层包含 Service 类和 Domain 类两部分。Domain 就相当于贫血模型中的 BO。不过，Domain 与 BO 的区别在于它是基于充血模型开发的，既包含数据，也包含业务逻辑。而 Service 类变得非常单薄。

### 3.8.3 贫血模型 VS 充血模型
基于充血模型的DDD开发模式，跟基于贫血模型的传统开发模式的**主要区别就在 Service 层，包含Service和Domain，Controller 层和 Repository 层的代码基本上相同**，这两层包含的业务逻辑并不多，没必要做充血建模。

**区别于Domain，Service 有几个职责：**
- **Service 类负责与 Repository 交流。保持领域模型的独立性，与其他层解耦。**
- **Service 类负责跨领域模型的业务聚合功能。**
- **Service 类负责一些非功能性及与三方系统交互的工作。** 比如幂等、事务、发邮件、发消息、记录日志、调用其他系统的 RPC 接口等。

虚拟钱包 VirtualWallet 类设计成一个充血的Domain领域模型，并且将原来在 Service 类中的部分业务逻辑移动到 VirtualWallet 类中，让 Service 类的实现依赖 VirtualWallet 类。
```java
public class VirtualWallet { // Domain领域模型(充血模型)
  private Long id;
  private Long createTime = System.currentTimeMillis();;
  private BigDecimal balance = BigDecimal.ZERO;
  
  public VirtualWallet(Long preAllocatedId) {
    this.id = preAllocatedId;
  }
  
  public BigDecimal balance() {
    return this.balance;
  }
  
  public void debit(BigDecimal amount) {
    if (this.balance.compareTo(amount) < 0) {
      throw new InsufficientBalanceException(...);
    }
    this.balance = this.balance.subtract(amount);
  }
  
  public void credit(BigDecimal amount) {
    if (amount.compareTo(BigDecimal.ZERO) < 0) {
      throw new InvalidAmountException(...);
    }
    this.balance = this.balance.add(amount);
  }
}

public class VirtualWalletService {
  // 通过构造函数或者IOC框架注入
  private VirtualWalletRepository walletRepo;
  private VirtualWalletTransactionRepository transactionRepo;
  
  public VirtualWallet getVirtualWallet(Long walletId) {
    VirtualWalletEntity walletEntity = walletRepo.getWalletEntity(walletId);
    VirtualWallet wallet = convert(walletEntity);
    return wallet;
  }
  
  public BigDecimal getBalance(Long walletId) {
    return walletRepo.getBalance(walletId);
  }
  
  @Transactional
  public void debit(Long walletId, BigDecimal amount) {
    VirtualWalletEntity walletEntity = walletRepo.getWalletEntity(walletId);
    VirtualWallet wallet = convert(walletEntity);
    wallet.debit(amount);
    VirtualWalletTransactionEntity transactionEntity = new VirtualWalletTransactionEntity();
    transactionEntity.setAmount(amount);
    transactionEntity.setCreateTime(System.currentTimeMillis());
    transactionEntity.setType(TransactionType.DEBIT);
    transactionEntity.setFromWalletId(walletId);
    transactionRepo.saveTransaction(transactionEntity);
    walletRepo.updateBalance(walletId, wallet.balance());
  }
  
  @Transactional
  public void credit(Long walletId, BigDecimal amount) {
    VirtualWalletEntity walletEntity = walletRepo.getWalletEntity(walletId);
    VirtualWallet wallet = convert(walletEntity);
    wallet.credit(amount);
    VirtualWalletTransactionEntity transactionEntity = new VirtualWalletTransactionEntity();
    transactionEntity.setAmount(amount);
    transactionEntity.setCreateTime(System.currentTimeMillis());
    transactionEntity.setType(TransactionType.CREDIT);
    transactionEntity.setFromWalletId(walletId);
    transactionRepo.saveTransaction(transactionEntity);
    walletRepo.updateBalance(walletId, wallet.balance());
  }

  @Transactional
  public void transfer(Long fromWalletId, Long toWalletId, BigDecimal amount) {
    //...跟基于贫血模型的传统开发模式的代码一样...
  }
}
```

## 3.9 接口鉴权面向对象分析实战
### 3.9.1 第一轮基础分析
调用方每次进行接口请求的时候，都携带自己的 AppID 和密码。微服务在接收到接口调用请求之后，会解析出 AppID 和密码，跟存储在微服务端的 AppID 和密码进行比对。

### 3.9.2 第二轮分析优化
调用方将请求接口的 URL 跟 AppID、密码拼接在一起，然后进行加密，生成一个 token。调用方在进行接口请求的的时候，将这个 token 及 AppID，随 URL 一块传递给微服务端。微服务端接收到这些数据之后，根据 AppID 从数据库中取出对应的密码，并通过同样的 token 生成算法，生成另外一个 token。用这个新生成的 token 跟调用方传递过来的 token 对比。
![20240204105757](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204105757.png)

### 3.9.3 第三轮分析优化
我们将 URL、AppID、密码、时间戳四者进行加密来生成 token。调用方在进行接口请求的时候，将 token、AppID、时间戳，随 URL 一并传递给微服务端。

微服务端在收到这些数据之后，会验证当前时间戳跟传递过来的时间戳，是否在一定的时间窗口内（比如一分钟）。
![20240204105813](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204105813.png)

### 3.9.4 第四轮分析优化
针对 AppID 和密码的存储，我们最好能灵活地支持各种不同的存储方式，比如 ZooKeeper、本地配置文件、自研配置中心、MySQL、Redis 等。我们不一定针对每种存储方式都去做代码实现，但起码要留有扩展点，保证系统有足够的灵活性和扩展性，能够在我们切换存储方式的时候，尽可能地减少代码的改动。

### 3.9.5 最终确定需求
- 调用方进行接口请求的时候，将 URL、AppID、密码、时间戳拼接在一起，通过加密算法生成 token，并且将 token、AppID、时间戳拼接在 URL 中，一并发送到微服务端。
- 微服务端在接收到调用方的接口请求之后，从请求中拆解出 token、AppID、时间戳。
- 微服务端首先检查传递过来的时间戳跟当前时间，是否在 token 失效时间窗口内。如果已经超过失效时间，那就算接口调用鉴权失败，拒绝接口调用请求。
- 如果 token 验证没有过期失效，微服务端再从自己的存储中，取出 AppID 对应的密码，通过同样的 token 生成算法，生成另外一个 token，与调用方传递过来的 token 进行匹配；如果一致，则鉴权成功，允许接口调用，否则就拒绝接口调用。

## 3.10 接口鉴权面向对象设计实战
### 3.10.1 划分职责进而识别出有哪些类
下面是我逐句拆解上述需求描述之后，得到的功能点列表
1. 把 URL、AppID、密码、时间戳拼接为一个字符串；
2. 对字符串通过加密算法加密生成 token；
3. 将 token、AppID、时间戳拼接到 URL 中，形成新的 URL；
4. 解析 URL，得到 token、AppID、时间戳等信息；
5. 从存储中取出 AppID 和对应的密码；
6. 根据时间戳判断 token 是否过期失效；
7. 验证两个 token 是否匹配；

从上面的功能列表中，我们发现，1、2、6、7 都是跟 token 有关，负责 token 的生成、验证；3、4 都是在处理 URL，负责 URL 的拼接、解析；5 是操作 AppID 和密码，负责从存储中读取 AppID 和密码。所以，我们可以粗略地得到三个核心的类：AuthToken、Url、CredentialStorage。AuthToken 负责实现 1、2、6、7 这四个操作；Url 负责 3、4 两个操作；CredentialStorage 负责 5 这个操作。

但如果我们面对的是更加大型的软件开发、更加复杂的需求开发，我们首先要做的是进行模块划分，将需求先简单划分成几个小的、独立的功能模块，然后再在模块内部，应用我们刚刚讲的方法，进行面向对象设计。

### 3.10.2 定义类及其属性和方法
识别出需求描述中的动词，作为候选的方法，再进一步过滤筛选。类比一下方法的识别，我们可以把功能点中涉及的名词，作为候选属性，然后同样进行过滤筛选。

从业务模型上来说，不应该属于这个类的属性和方法，不应该被放到这个类里。

在设计类具有哪些属性和方法的时候，不能单纯地依赖当下的需求，还要分析这个类从业务模型上来讲，理应具有哪些属性和方法。这样可以一方面保证类定义的完整性，另一方面不仅为当下的需求还为未来的需求做些准备。
![20240204105832](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204105832.png)
![20240204105843](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204105843.png)
![20240204105855](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204105855.png)

### 3.10.3 定义类与类之间的交互关系
目前只有三个核心的类，所以只用到了实现关系，也即 CredentialStorage 和 MysqlCredentialStorage 之间的关系。接下来讲到组装类的时候，我们还会用到依赖关系、组合关系，但是泛化关系暂时没有用到。

### 3.10.4 将类组装起来并提供执行入口
我们封装所有的实现细节，设计了一个最顶层的 ApiAuthenticator 接口类，暴露一组给外部调用者使用的 API 接口，作为触发执行鉴权逻辑的入口。
![20240204105909](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204105909.png)

## 4. 设计原则
## 4.1 SOLID 原则：SRP 单一职责原则
### 4.1.1 概念
**一个类只负责完成一个职责或者功能。** 不要设计大而全的类，要设计粒度小、功能单一的类。

### 4.1.2 意义
- 提高类的内聚性。
- 实现代码高内聚、低耦合。

### 4.1.3 不满足的 5 种情况
- **类中的代码行数、函数或者属性过多；** 会影响代码的可读性和可维护性，考虑拆下
- **类依赖的其他类过多，或者依赖类的其他类过多；** 不符合高内聚低耦合，考虑拆下
- **私有方法过多；** 考虑下能否独立到新的类中，设置为public方法，供更多类使用，提高复用性
- **比较难给类起一个合适的名字；** 很难用一个业务名词概括，说明类的职责定义得可能不够清晰
- **类中大量的方法都是集中操作类中的某几个属性。**

### 4.1.4 实际开发中的情况
- **根据具体的场景和需求判断**，不同的应用场景、不同阶段的需求背景、不同的业务层面，对同一个类的职责是否单一，可能会有不同的判定结果。
- 在真正的软件开发中，我们可以**先写一个粗粒度的类，满足业务需求。随着业务的发展，拆分成更细粒度的类**。这就是所谓的持续重构。
- **一个宽泛的量化标准是一个类的代码行数最好不能超过 200 行，函数个数及属性个数都最好不要超过 10 个。**
- **类也不能拆分得过细，拆分过细会适得其反，反倒会降低内聚性，也会影响代码的可维护性。**

## 4.2 SOLID 原则：OCP 开闭原则
### 4.2.1 概念
添加一个新的功能应是在已有代码基础上扩展代码（新增模块、类、方法等），而非修改已有代码（修改模块、类、方法等）。对扩展开放、修改关闭。

### 4.2.2 如何理解
- 开闭原则并不是说完全杜绝修改，而是以最小的修改代码的代价来完成新功能的开发。
- 同样的代码改动，在粗代码粒度下，可能被认定为“修改”；在细代码粒度下，可能又被认定为“扩展”。

### 4.2.3 如何做到
- 我们要时刻具备扩展意识、抽象意识、封装意识。
- 最常用来提高代码扩展性的方法
- 多态
- 依赖注入
- 基于接口而非实现编程
- 以及大部分的设计模式（比如，装饰、策略、模板、职责链、状态）

将可变部分封装起来，隔离变化，提供抽象化的不可变接口，供上游系统调用。当具体实现发生改变时，只需基于相同的抽象接口，扩展一个新的实现，替换掉旧实现即可，上游系统的代码几乎不需要修改。

## 4.3 SOLID 原则：LSP 里式替换原则
### 4.3.1 概念
子类对象能够替换程序中父类对象出现的任何地方，并且保证原来程序的逻辑行为（behavior）不变及正确性不被破坏。

### 4.3.2 如何理解
父类定义了函数的“约定”（或者叫协议），那子类可以改变函数的内部实现逻辑，但不能改变函数原有的“约定”。

### 4.3.3 里氏替换原则VS多态
- 多态是面向对象编程的一大特性，也是面向对象编程语言的一种语法。它是一种代码实现的思路。
- 而里式替换是一种设计原则，用来指导继承关系中子类该如何设计，子类的设计要保证在替换父类的时候，不改变原有程序的逻辑及不破坏原有程序的正确性。

### 4.3.4 不满足的情况
- 子类违背父类声明要实现的功能。
- 子类违背父类对输入、输出、异常的约定。
- 子类违背父类注释中所罗列的任何特殊说明。

## 4.4 SOLID 原则：ISP 接口隔离原则
### 4.4.1 概念
客户端不应该强迫依赖它不需要的接口。其中的“客户端”，可以理解为接口的调用者或者使用者。

### 4.4.2 接口的3种理解
- 一组 API 接口集合
- 单个 API 接口或函数
- OOP 中的接口

**把“接口”理解为一组 API 接口集合**
可以从代码设计的层面，尽量避免接口被误用。我们参照接口隔离原则，调用者不应该强迫依赖它不需要的接口，将删除接口单独放到另外一个接口 RestrictedUserService 中，然后将 RestrictedUserService 只打包提供给后台管理系统来使用。

```java
public interface UserService {
  boolean register(String cellphone, String password);
  boolean login(String cellphone, String password);
  UserInfo getUserInfoById(long id);
  UserInfo getUserInfoByCellphone(String cellphone);
}

public interface RestrictedUserService {
  boolean deleteUserByCellphone(String cellphone);
  boolean deleteUserById(long id);
}

public class UserServiceImpl implements UserService, RestrictedUserService {
  // ...省略实现代码...
}
```

**把“接口”理解为单个 API 接口或函数**
函数的设计要功能单一，不要将多个不同的功能逻辑在一个函数中实现。

部分调用者只需要函数中的部分功能，那我们就需要把函数拆分成粒度更细的多个函数，让调用者只依赖它需要的那个细粒度函数。

**把“接口”理解为 OOP 中的接口概念**
如果我们不遵守接口隔离原则，不设计 Updater 和 Viewer 两个小接口，而是设计一个大而全的 Config 接口，让 RedisConfig、KafkaConfig、MysqlConfig 都实现这个 Config 接口。第一种设计思路显然要比第二种好很多。

- 首先，第一种设计思路更加灵活、易扩展、易复用。
- 其次，第二种设计思路在代码实现上做了一些无用功。

```java
public interface Updater {
  void update();
}

public interface Viewer {
  String outputInPlainText();
  Map<String, String> output();
}

public class RedisConfig implemets Updater, Viewer {
  //...省略其他属性和方法...
  @Override
  public void update() { //... }
  @Override
  public String outputInPlainText() { //... }
  @Override
  public Map<String, String> output() { //...}
}

public class KafkaConfig implements Updater {
  //...省略其他属性和方法...
  @Override
  public void update() { //... }
}

public class MysqlConfig implements Viewer {
  //...省略其他属性和方法...
  @Override
  public String outputInPlainText() { //... }
  @Override
  public Map<String, String> output() { //...}
}

public class SimpleHttpServer {
  private String host;
  private int port;
  private Map<String, List<Viewer>> viewers = new HashMap<>();

  public SimpleHttpServer(String host, int port) {//...}

  public void addViewers(String urlDirectory, Viewer viewer) {
    if (!viewers.containsKey(urlDirectory)) {
      viewers.put(urlDirectory, new ArrayList<Viewer>());
    }
    this.viewers.get(urlDirectory).add(viewer);
  }

  public void run() { //... }
}

public class Application {
    ConfigSource configSource = new ZookeeperConfigSource();
    public static final RedisConfig redisConfig = new RedisConfig(configSource);
    public static final KafkaConfig kafkaConfig = new KakfaConfig(configSource);
    public static final MySqlConfig mysqlConfig = new MySqlConfig(configSource);

    public static void main(String[] args) {
        ScheduledUpdater redisConfigUpdater =
            new ScheduledUpdater(redisConfig, 300, 300);
        redisConfigUpdater.run();

        ScheduledUpdater kafkaConfigUpdater =
            new ScheduledUpdater(kafkaConfig, 60, 60);
        redisConfigUpdater.run();

        SimpleHttpServer simpleHttpServer = new SimpleHttpServer(“127.0.0.1”, 2389);
        simpleHttpServer.addViewer("/config", redisConfig);
        simpleHttpServer.addViewer("/config", mysqlConfig);
        simpleHttpServer.run();
    }
}
```

### 4.4.3 接口隔离原则VS单一职责原则
- 单一职责原则针对的是模块、类、接口的设计。
- 接口隔离原则提供了一种判断接口的职责是否单一的标准：通过调用者如何使用接口来间接地判定。

## 4.5 SOLID 原则：DIP 依赖倒置原则
### 4.5.1 控制反转（IOC）
**控制反转是一个比较笼统的设计思想，并不是一种具体的实现方法**，一般用来指导框架层面的设计。这里所说的“控制”指的是对程序执行流程的控制，而“反转”指的是在没有使用框架之前，程序员自己控制整个程序的执行。在使用框架之后，整个程序的执行流程通过框架来控制。流程的控制权从程序员“反转”给了框架。

### 4.5.2 依赖注入（DI）
**依赖注入和控制反转恰恰相反，它是一种具体的编码技巧**。我们不通过 new 的方式在类内部创建依赖类的对象，而是将依赖的类对象在外部创建好之后，通过构造函数、函数参数等方式传递（或注入）给类来使用。

### 4.5.3 依赖注入框架（DI Framework）
**通过依赖注入框架提供的扩展点，简单配置一下所有需要的类及其类与类之间依赖关系，就可以实现由框架来自动创建对象、管理对象的生命周期、依赖注入等原本需要程序员来做的事情。**

### 4.5.4 依赖反转原则（DIP）
- **依赖反转原则也叫作依赖倒置原则。这条原则跟控制反转有点类似，主要用来指导框架层面的设计。**
- **高层模块不依赖低层模块，它们共同依赖同一个抽象。抽象不要依赖具体实现细节，具体实现细节依赖抽象。**

## 4.6 KISS原则，YAGNI原则
### 4.6.1 KISS原则
- **概念：** Keep It Short and Simple，尽量保持简单。
	代码足够简单，也就意味着很容易读懂，bug 比较难隐藏。即便出现 bug，修复起来也比较简单。
	并不是代码行数越少就越简单，还要考虑逻辑复杂度、实现难度、代码可读性等。本身就复杂的问题，用复杂的方法解决，并不违背 KISS 原则。同样的代码，在某个业务场景下满足 KISS 原则，换一个应用场景可能就不满足了。

- **意义：** 保持代码可读和可维护的重要手段。

- **如何写出满足 KISS 原则的代码**
	- 不要使用同事可能不懂的技术来实现代码；
	- 不要重复造轮子，要善于使用已经有的工具类库；
	- 不要过度优化。

下面这三段代码可以实现同样一个功能：检查输入的字符串 ipAddress 是否是合法的 IP 地址。

第一种实现方式利用的是正则表达式，只用三行代码就把这个问题搞定了。它的代码行数最少，看似最简单，实际上却很复杂。这正是因为它使用了正则表达式。

第二种实现方式使用了 StringUtils 类、Integer 类提供的一些现成的工具函数，来处理 IP 地址字符串。

第三种实现方式，不使用任何工具函数，而是通过逐一处理 IP 地址中的字符，来判断是否合法。

从可读性上来说，第二种实现方式的代码逻辑更清晰、更好理解。所以，在这两种实现方式中，第二种实现方式更加“简单”，更加符合 KISS 原则。

尽管第三种实现方式性能更高些，但我还是更倾向于选择第二种实现方法。那是因为第三种实现方式实际上是一种过度优化。除非 isValidIpAddress() 函数是影响系统性能的瓶颈代码，否则，这样优化的投入产出比并不高，增加了代码实现的难度、牺牲了代码的可读性，性能上的提升却并不明显。

```java
// 第一种实现方式: 使用正则表达式
public boolean isValidIpAddressV1(String ipAddress) {
  if (StringUtils.isBlank(ipAddress)) return false;
  String regex = "^(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|[1-9])\\."
          + "(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|\\d)\\."
          + "(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|\\d)\\."
          + "(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|\\d)$";
  return ipAddress.matches(regex);
}

// 第二种实现方式: 使用现成的工具类
public boolean isValidIpAddressV2(String ipAddress) {
  if (StringUtils.isBlank(ipAddress)) return false;
  String[] ipUnits = StringUtils.split(ipAddress, '.');
  if (ipUnits.length != 4) {
    return false;
  }
  for (int i = 0; i < 4; ++i) {
    int ipUnitIntValue;
    try {
      ipUnitIntValue = Integer.parseInt(ipUnits[i]);
    } catch (NumberFormatException e) {
      return false;
    }
    if (ipUnitIntValue < 0 || ipUnitIntValue > 255) {
      return false;
    }
    if (i == 0 && ipUnitIntValue == 0) {
      return false;
    }
  }
  return true;
}

// 第三种实现方式: 不使用任何工具类
public boolean isValidIpAddressV3(String ipAddress) {
  char[] ipChars = ipAddress.toCharArray();
  int length = ipChars.length;
  int ipUnitIntValue = -1;
  boolean isFirstUnit = true;
  int unitsCount = 0;
  for (int i = 0; i < length; ++i) {
    char c = ipChars[i];
    if (c == '.') {
      if (ipUnitIntValue < 0 || ipUnitIntValue > 255) return false;
      if (isFirstUnit && ipUnitIntValue == 0) return false;
      if (isFirstUnit) isFirstUnit = false;
      ipUnitIntValue = -1;
      unitsCount++;
      continue;
    }
    if (c < '0' || c > '9') {
      return false;
    }
    if (ipUnitIntValue == -1) ipUnitIntValue = 0;
    ipUnitIntValue = ipUnitIntValue * 10 + (c - '0');
  }
  if (ipUnitIntValue < 0 || ipUnitIntValue > 255) return false;
  if (unitsCount != 3) return false;
  return true;
}
```

### 4.6.2 YAGNI原则
- **概念：** You Ain’t Gonna Need It，不要去设计当前用不到的功能；不要去编写当前用不到的代码
- **核心：** 不要做过度设计。

### 4.6.3 YAGNI 原则VS KISS 原则
- **KISS 原则：** “如何做”的问题（尽量保持简单）
- **YAGNI 原则：** “要不要做”，当前不需要的就不要做。

## 4.7 DRY 原则
### 4.7.1 概念
Don’t Repeat Yourself，不要写重复的代码。

### 4.7.2 意义
- 减少代码量，提高代码的可读性、可维护性。
- 复用已经经过测试的老代码，bug 会比从零重新开发要少。

### 4.7.3 3种代码重复的情况
- 实现逻辑重复
尽管代码的实现逻辑是相同的，但语义不同，我们判定它并不违反 DRY 原则。对于包含重复代码的问题，我们可以通过抽象成更细粒度函数的方式来解决。

- 功能语义重复
尽管两段代码的实现逻辑不重复，但语义重复，也就是功能重复，我们认为它违反了 DRY 原则。

- 代码执行重复
代码，既没有逻辑重复，也没有语义重复，但仍然违反了 DRY 原则。这是因为代码中存在“执行重复”。

### 4.7.4 复用概念理解
- 代码复用：表示一种行为，我们在开发新功能的时候，尽量复用已经存在的代码。
- 代码复用性：表示一段代码可被复用的特性或能力，我们在编写代码的时候，让代码尽量可复用。
- DRY 原则：是一条原则，不要写重复的代码。

### 4.7.5 怎么提高代码复用性
- **减少代码耦合**
- **满足单一职责原则**
- **模块化**
- **业务与非业务逻辑分离**，越与业务无关的代码越易复用，抽取成通用的框架、类库、组件等
- **通用代码下沉**，分层角度: 越底层代码越通用，应设计得足够可复用，杜绝下层代码调用上层代码
- **继承、多态、抽象、封装**
- **应用模板等设计模式**

我们在第一次写代码的时候，如果当下没有复用的需求，而未来的复用需求也不是特别明确，并且开发可复用代码的成本比较高，那我们就不需要考虑代码的复用性。在之后开发新的功能的时候，发现可以复用之前写的这段代码，那我们就重构这段代码，让其变得更加可复用。

相比于代码的可复用性，DRY 原则适用性更强一些。我们可以不写可复用的代码，但一定不能写重复的代码。

## 4.8 迪米特法则（LOD）
### 4.8.1 迪米特法则
- **概念：** Law of Demeter，不该有直接依赖关系的类之间，不要有依赖；有依赖关系的类之间，尽量只依赖必要的接口。
- **意义：** 减少类之间的耦合，让类越独立越好。每个类都应该少了解系统的其他部分。一旦发生变化，需要了解这一变化的类就会比较少。

### 4.8.2 如何理解“高内聚、松耦合”
- **高内聚、松耦合：** 是一个非常重要的设计思想，能够有效提高代码的可读性和可维护性，缩小功能改动导致的代码改动范围。
- **高内聚：** 用来指导类本身的设计，松耦合：用来指导类与类之间依赖关系的设计
- **高内聚：** 就是指相近的功能应该放到同一个类中，不相近的功能不要放到同一类中。相近的功能往往会被同时修改，放到同一个类中，修改会比较集中。
- **松耦合：** 类与类之间的依赖关系简单清晰，即使两个类有依赖关系，一个类的代码改动也不会或者很少导致依赖类的代码改动。

## 4.9 业务系统的开发实战
### 4.9.1 需求分析
- 学会借鉴类似成熟系统，既可以节约时间，也可以更全面。
- 通过线框图和用户用例来细化业务流程，挖掘一些比较细节的、不容易想到的功能点。

### 4.9.2 系统设计
- 合理地将功能划分到不同模块。
- 设计模块与模块之间的交互关系。
- 设计模块的接口、数据库、业务模型。

### 4.9.3 业务开发包括哪些工作
- 数据库设计。
- 接口设计。
- 业务模型的设计。

### 4.9.4 为什么要分层开发
- 分层能起到代码复用的作用。
- 分层能起到隔离变化的作用。
- 分层能起到隔离关注点的作用。
- 分层能提高代码的可测试性。
- 分层能应对系统的复杂性。

### 4.9.5 BO、VO、Entity 存在的意义是什么
- 从设计的角度来说，**VO、BO、Entity 的设计思路并不违反 DRY 原则，为了分层清晰、减少耦合，多维护几个类的成本也并不是不能接受的**。但是，如果你真的有代码洁癖，对于代码重复的问题，我们可以通过继承或者组合来解决。
- 如何进行数据对象之间的转化？最简单的方式就是手动复制。当然，**使用数据对象转化工具，可以大大简化繁琐的对象转化工作**。
- 尽管 VO、BO、Entity 的设计违背 OOP 的封装特性，有被随意修改的风险。**但 Entity 和 VO 的生命周期是有限的，都仅限在本层范围内，相对来说是安全的**。Service 层包含比较多的业务逻辑代码，所以 BO 就存在被任意修改的风险了。为了使用方便，我们只能做一些妥协，放弃 BO 的封装特性，由程序员自己来负责这些数据对象的不被错误使用。


## 4.10 通用框架开发实战
### 4.10.1 需求分析
除了功能性需求分析之外，还需要考虑框架的非功能性需求。比如，框架的易用性、性能、扩展性、容错性、通用性等。

### 4.10.2 复杂框架设计技巧
- 画产品线框图
- 聚焦简单应用场景
- 设计实现最小原型
- 画系统设计图

# 5. 规范与重构
## 5.1 重构概述
## 5.1.1 重构的目的：为什么重构（why）
- **重构可以保持代码质量持续处于一个可控状态，不至于腐化到无可救药的地步。**
- **优秀的代码或架构都是迭代出来的**，无法100%预见未来的需求，随着系统演进，重构不可避免。
- **对于个人而言，重构非常锻炼一个人的代码能力**。它是我们学习的经典设计思想、原则、模式、编程规范等理论知识的练兵场。

## 5.1.2 重构的对象：重构什么（what）
按照重构的规模，我们可以将重构大致分为大规模高层次的重构和小规模低层次的重构。
- 大规模高层次的重构
	- 内容：对代码分层、模块化、解耦、梳理类之间的交互关系、抽象复用组件等等。
	- 理论基础：设计思想、原则、模式。

- 小规模低层次的重构
	- 内容：规范命名、注释、修正函数参数过多、消除超大类、提取重复代码等等编程细节问题，主要是针对类、函数级别的重构。
	- 理论基础：编码规范。

## 5.1.3 重构的时机：什么时候重构（when）
我反复强调，我们一定要**建立持续重构意识，把重构作为开发必不可少的部分，融入到日常开发中**，而不是等到代码出现很大问题的时候，再大刀阔斧地重构。

对于重构这件事，没事就重构下代码，时刻保持代码质量处于一个良好的状态。否则一旦出现 "**破窗效应**"，一个人往里堆了一些烂代码，之后就会有更多的人往里面堆更烂的代码，毕竟往项目堆砌烂代码的成本太低了。保持代码质量最好的方法还是：打造一种好的技术氛围，以此驱动大家主动关注代码质量，持续重构代码。

## 5.1.4 重构的方法：如何重构（how）
- 大规模高层次的重构**难度比较大，需要组织、有计划地进行，分阶段地小步快跑**，时刻让代码处于一个可运行的状态。
- 而小规模低层次的重构，因为**影响范围小，改动耗时短，随时随地都可以去做**。

## 5.2 保证重构不出错：单元测试
### 5.2.1 什么是单元测试
- **概念：** 单元测试是代码层面的测试，由研发自己来编写，用于测试“自己”编写的代码的逻辑的正确性。
- **单元：** 测试顾名思义是测试一个“单元”，有别于集成测试，这个“单元”一般是类或函数，而不是模块或者系统。

### 5.2.2 为什么要写单元测试
- **写单元测试的过程本身就是代码 Code Review 和重构的过程。**
- **能有效地发现代码中的 bug 和代码设计上的问题。** 节省fix低级bug的时间，写出Bug Free代码是判断工程师编码能力的重要标准之一，代码的可测试性是评判代码质量的重要标准，难写单元测试一般说明代码设计可能有问题。
- **单元测试还是对集成测试的有力补充，还能帮助我们快速熟悉代码，是 TDD 可落地执行的改进方案。** 单元测试案例实际上就是用户案例，反映了代码的功能及使用，在没有文档或注释的情况下，它可以起替代性作用，借助单元测试案例，无需深入阅读代码，即可了解代码实现了什么功能。

### 5.2.3 如何编写单元测试
- **概念：** 针对代码设计覆盖各种输入、异常、边界条件的测试用例，并将其翻译成代码的过程。
- **方法：** 我们可以利用一些测试框架来简化单元测试的编写。
- **5个正确的编写认知：**
	- **编写单元测试尽管繁琐，但并不是太耗时**；因为不用考虑太多代码设计上的问题，大部分是cv操作。
	- **我们可以稍微放低对单元测试代码质量的要求**；单元测试毕竟不会在生产环境运行，类的测试代码都相对独立，代码质量要求可以放低些，命名不是很规范、代码重复有些重复，也是可以的。
	- **覆盖率作为衡量单元测试质量的唯一标准是不合理的**；测试覆盖率是比较容易量化的指标，有很多现成的工具专门用来做覆盖率统计，盲目追求高覆盖率是不可取的，更重要的是看测试用例是否覆盖了所有可能的情况，特别是一些边界条件。
	- **单元测试不要依赖被测代码的具体实现逻辑**；单元测试只关心被测函数实现了什么功能
	- **单元测试框架无法测试，多半是因为代码的可测试性不好。**

### 5.2.4 单元测试为何难落地执行
- 写单元测试本身比较繁琐，技术挑战不大，很多程序员不愿意去写。
- 国内研发比较偏向“快、糙、猛”，容易因为开发进度紧，导致单元测试的执行虎头蛇尾。
- 团队没有建立对单元测试正确的认识，觉得可有可无，单靠督促很难执行得很好。

## 5.3 代码的可测试性
### 5.3.1 什么是代码的可测试性
粗略地讲，所谓代码的可测试性，就是针对代码编写单元测试的难易程度。对于一段代码，如果很难为其编写单元测试，或者单元测试写起来很费劲，需要依靠单元测试框架中很高级的特性，那往往就意味着代码设计得不够合理，代码的可测试性不好。

### 5.3.2 编写可测试性代码的最有效手段
依赖注入是编写可测试性代码的最有效手段：
- 通过 mock 的方法将不可控的依赖变得可控。
- 还可以利用二次封装来解决某些代码行为不可控的情况。

### 5.3.3 常见的5种测试不友好代码
- 代码中包含未决行为逻辑
- 滥用可变全局变量
- 滥用静态方法
- 使用复杂的继承关系
- 高度耦合的代码

## 5.4 大型重构：解耦
### 5.4.1 “解耦”为何如此重要
- 解耦保证代码松耦合、高内聚，是控制代码复杂度的有效手段。
- 代码高内聚、松耦合，也就是意味着，代码结构清晰、分层模块化合理、依赖关系简单、模块或类之间的耦合小，那代码整体的质量就不会差。

### 5.4.2 代码是否需要“解耦”
- 间接的衡量标准
	- 改动一个模块或类的代码受影响的模块或类是否有很多。
	- 改动一个模块或者类的代码依赖的模块或者类是否需要改动。
	- 代码的可测试性是否好。

- 直接的衡量标准
	- 把模块与模块之间及其类与类之间的依赖关系画出来，根据依赖关系图的复杂性来判断。

### 5.4.3 如何给代码“解耦”
- 封装与抽象，隐藏复杂性，隔离变化，对外提供稳定易用接口
- 中间层，简化模块或类之间的依赖关系
- 模块化，模块只提供封装了内部实现细节的接口给其它模块使用，以此减少不同模块间的耦合度
- 其他的设计思想与原则
	- 单一职责原则
	- 基于接口而非实现编程
	- 依赖注入
	- 多用组合少用继承
	- 迪米特法则
- 设计模式

## 5.5 小型重构：编码规范
### 5.5.1 命名与注释
- **命名的关键是能准确达意**。对于不同作用域的命名，我们可以适当地选择不同的长度。作用域小的变量（比如临时变量），可以适当地选择短一些的命名方式。除此之外，命名中也可以使用一些耳熟能详的缩写。
- **可以借助类的信息来简化属性、函数的命名，利用函数的信息来简化函数参数的命名**。如User类中的name可以不写username。
- **命名要可读、可搜索。不要使用生僻的、不好读的英文单词来命名**。除此之外，命名要符合项目的统一规范，不要用些反直觉的命名。
- **接口有两种命名方式：一种是在接口中带前缀“I”；另一种是在接口的实现类中带后缀“Impl”。对于抽象类的命名**，也有两种方式，一种是带上前缀“Abstract”，一种是不带前缀，**更倾向于带有前缀“Abstract”**。这两种命名方式都可以，关键是要在项目中统一。
- 注释的目的就是让代码更容易看懂。只要符合这个要求的内容，你就可以将它写到注释里。总结一下，**注释的内容主要包含这样三个方面：做什么、为什么、怎么做。对于一些复杂的类和接口，我们可能还需要写明“如何用”**。
- 注释本身有一定的维护成本，所以并非越多越好。**类和函数一定要写注释，而且要写得尽可能全面、详细**，而函数内部的注释要相对少一些，一般都是靠好的命名、提炼函数、解释性变量、总结性注释来提高代码可读性。

### 5.5.2 代码风格
- 函数、类多大才合适
**函数的代码行数不要超过一屏幕的大小，比如 50 行**。类的大小限制比较难确定。

- 一行代码多长最合适
**一行代码长度最好不要超过 IDE 显示的宽度**。当然，限制也不能太小，太小会导致很多稍微长点的语句被折成两行，也会影响到代码的整洁，不利于阅读。

- 善用空行分割单元块
对于比较长的函数，为了让逻辑更加清晰，可以使用空行来分割各个代码块。在类内部，成员变量与函数之间、静态成员变量与普通成员变量之间、函数之间，甚至成员变量之间，都可以通过添加空行的方式，让不同模块的代码之间的界限更加明确。

- 四格缩进还是两格缩进
我个人比较**推荐使用两格缩进，这样可以节省空间**，特别是在代码嵌套层次比较深的情况下。除此之外，值得强调的是，不管是用两格缩进还是四格缩进，一定不要用 tab 键缩进。

- 大括号是否要另起一行
**将大括号放到跟上一条语句同一行的风格，这样可以节省代码行数**。但是，**将大括号另起一行**，也有它的优势，那就是，左右括号可以垂直对齐，**代码结构清晰**。

- 类中成员的排列顺序
在 Google Java 编程规范中，依赖类按照字母序从小到大排列。类中先写成员变量后写函数。成员变量之间或函数之间，先写静态成员变量或函数，后写普通变量或函数，并且按照作用域大小依次排列。

### 5.5.3 编程技巧
- **将复杂的逻辑提炼拆分成函数和类。**
- **通过拆分成多个函数或将参数封装为对象的方式，来处理参数过多的情况。**
- **函数中不要使用参数来做代码执行逻辑的控制。**
- **函数设计要职责单一。**
- **移除过深的嵌套层次**，方法包括：去掉多余的 if 或 else 语句，使用 continue、break、return 关键字提前退出嵌套，调整执行顺序来减少嵌套，将部分嵌套逻辑抽象成函数。
- **用字面常量取代魔法数。**
- **用解释性变量来解释复杂表达式**，以此提高代码可读性。

### 5.5.4 统一编码规范
项目、团队，甚至公司，一定要制定统一的编码规范，并且通过 Code Review 督促执行，这对提高代码质量有立竿见影的效果。

## 5.6 高质量代码落地
### 5.6.1 代码质量常规检查项
- 目录设置是否合理、模块划分是否清晰、代码结构是否满足“高内聚、松耦合”？
- 是否遵循经典的设计原则和设计思想（SOLID、DRY、KISS、YAGNI、LOD 等）？
- 设计模式是否应用得当？是否有过度设计？
- 代码是否容易扩展？如果要添加新功能，是否容易实现？
- 代码是否可以复用？是否可以复用已有的项目代码或类库？是否有重复造轮子？
- 代码是否容易测试？单元测试是否全面覆盖了各种正常和异常的情况？
- 代码是否易读？是否符合编码规范（比如命名和注释是否恰当、代码风格是否一致等）？

### 5.6.2 代码是否满足需求检查项
- 代码是否实现了预期的业务需求？
- 逻辑是否正确？是否处理了各种异常情况？
- 日志打印是否得当？是否方便 debug 排查问题？
- 接口是否易用？是否支持幂等、事务等？
- 代码是否存在并发问题？是否线程安全？
- 性能是否有优化空间，比如，SQL、算法是否可以优化？
- 是否有安全漏洞？比如输入输出校验是否全面？

### 5.6.4 重构的过程
- 第一轮重构：提高代码的可读性
- 第二轮重构：提高代码的可测试性
- 第三轮重构：编写完善的单元测试
- 第四轮重构：所有重构完成之后添加注释

### 5.6.5 程序出错该返回啥
- 返回错误码
**C 语言没有异常这样的语法机制，返回错误码便是最常用的出错处理方式**。而 Java、Python 等比较新的编程语言中，大部分情况下，我们都用异常来处理函数出错的情况，极少会用到错误码。

- 返回 NULL 值
在多数编程语言中，我们用 NULL 来表示“不存在”这种语义。对于查找函数来说，数据不存在并非一种异常情况，是一种正常行为，**所以返回表示不存在语义的 NULL 值比返回异常更加合理**。

- 返回空对象
返回 NULL 值有各种弊端，对此有一个比较经典的应对策略，那就是应用空对象设计模式。**当函数返回的数据是字符串类型或者集合类型的时候，我们可以用空字符串或空集合替代 NULL 值，来表示不存在的情况**。这样，我们在使用函数的时候，就可以不用做 NULL 值判断。

- 抛出异常对象
	尽管前面讲了很多函数出错的返回数据类型，但是，**最常用的函数出错处理方式是抛出异常**。异常有两种类型：受检异常和非受检异常。

	运行时异常也叫作非受检异常（Unchecked Exception），编译时异常也叫作受检异常（Checked Exception）。

	**函数抛出的异常处理方法：直接吞掉、直接往上抛出、包裹成新的异常抛出。**

### 5.6.6 程序执行入口（上帝类）
面向对象设计中的最后一步是组装类并提供执行入口，所以，组装前三部分逻辑的上帝类是必须要有的。我们**可以将上帝类做的很轻量级，把核心逻辑都剥离出去，形成独立的类，上帝类只负责组装类和串联执行流程。这样做的好处是，代码结构更加清晰，底层核心逻辑更容易被复用。**

### 5.6.7 开发思想总结
**我们要对代码质量有所追求，不能只是凑活能用就好**。即便是非常简单的需求，不同水平的人写出来的代码，差别可能会很大。“能用”和“好用”，天壤之别。花点心思写一段高质量的代码，比写 100 段凑活能用的代码，对你的代码能力提高更有帮助。看上去再完美的代码，只要我们下功夫去推敲，总有可以优化的空间，就看你愿不愿把事情做到极致。

**知其然知其所以然，了解优秀代码设计的演变过程，比学习优秀设计本身更有价值**。如果你内功不够深厚，理论知识不够扎实，那你就很难参透开源项目的代码到底优秀在哪里。知道为什么这么做，比单纯地知道怎么做更重要，这样可以避免你过度使用设计模式、思想和原则。

设计思想、原则、模式本身并没有太多“高大上”的东西，都是一些简单的道理，而且知识点也并不多，**关键还是锻炼具体代码具体分析的能力，把知识点恰当地用在项目中。**

我经常讲，高手之间的竞争都是在细节。大的架构设计、分层、分模块思路实际上都差不多。没有项目是靠一些不为人知的设计来取胜的，即便有，很快也能被学习过去。所以，关键还是看代码细节处理得够不够好。这些细节的差别累积起来，会让代码质量有质的差别。所以，**要想提高代码质量，还是要在细节处下功夫。**

# 6. 要点总结
## 6.1 概述
![20240204105947](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204105947.png)

## 6.2 代码质量评判标准
![20240204105959](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204105959.png)

## 6.3 面向对象
![20240204110009](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204110009.png)

## 6.4 设计原则
![20240204110028](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204110028.png)

## 6.5 规范与重构
![20240204110047](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240204110047.png)
