---
layout: post
title: "C#高级–泛型详解"
author: "Kai"
header-style: text
tags:
  - C#
---

转自：https://blog.csdn.net/liyou123456789/article/details/119113577

# 1. 泛型是什么
泛型是C#2.0推出的新语法，不是语法糖，而是2.0由框架升级提供的功能。

我们在编程程序时，经常会遇到功能非常相似的模块，只是它们处理的数据不一样。但我们没有办法，只能分别写多个方法来处理不同的数据类型。这个时候，那么问题来了，有没有一种办法，用同一个方法来处理传入不同种类型参数的办法呢？泛型的出现就是专门来解决这个问题的。

使用泛型是一种增强程序功能的技术，具体表现在以下几个方面：
- 它有助于您最大限度地重用代码、保护类型的安全以及提高性能。
- 您可以创建泛型集合类。.NET 框架类库在 System.Collections.Generic 命名空间中包含了一些新的泛型集合类。您可以使用这些泛型集合类来替代 System.Collections 中的集合类。
- 您可以创建自己的泛型接口、泛型类、泛型方法、泛型事件和泛型委托。
- 您可以对泛型类进行约束以访问特定数据类型的方法。
- 关于泛型数据类型中使用的类型的信息可在运行时通过使用反射获取。

# 2. 为什么使用泛型
## 2.1 不同类型参数，重复写多个方法
**方法实现**
```csharp
using System;

namespace MyGeneric
{
    public class CommonMethod
    {
        /// <summary>
        /// 打印个int值
        /// 声明方法时，指定了参数类型，确定了只能传递某个类型
        /// </summary>
        /// <param name="iParameter"></param>
        public static void ShowInt(int iParameter)
        {
            Console.WriteLine("This is {0},parameter={1},type={2}",
                typeof(CommonMethod).Name, iParameter.GetType().Name, iParameter);
        }

        /// <summary>
        /// 打印个string值
        /// </summary>
        /// <param name="sParameter"></param>
        public static void ShowString(string sParameter)
        {
            Console.WriteLine("This is {0},parameter={1},type={2}",
                typeof(CommonMethod).Name, sParameter.GetType().Name, sParameter);
        }

        /// <summary>
        /// 打印个DateTime值
        /// </summary>
        /// <param name="oParameter"></param>
        public static void ShowDateTime(DateTime dtParameter)
        {
            Console.WriteLine("This is {0},parameter={1},type={2}",
                typeof(CommonMethod).Name, dtParameter.GetType().Name, dtParameter);
        }
    }
}
```

**方法调用**
```csharp
using System;
using System.Collections.Generic;

namespace MyGeneric
{
    /// <summary>
    /// main方法调用
    /// </summary>
    class Program
    {
        static void Main(string[] args)
        {
            try
            {                
                int iValue = 123;
                string sValue = "456";
                DateTime dtValue = DateTime.Now;

                Console.WriteLine("***********************Common***********************");
                CommonMethod.ShowInt(iValue);
                CommonMethod.ShowString(sValue);
                CommonMethod.ShowDateTime(dtValue);
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.ReadKey();
        }
    }
}
```

**运行结果**
```textplain
***********************Common***********************
This is CommonMethod,parameter=Int32,type=123
This is CommonMethod,parameter=String,type=456
This is CommonMethod,parameter=DateTime,type=2021/7/25 19:30:52
```

**代码分析**

从上面的结果中我们可以看出这三个方法，除了传入的参数不同外，实现的功能都是一样的。在C#1.0版的时候，还没有泛型这个概念，那么怎么办呢。相信很多人会想到了OOP三大特性之一的继承，我们知道，C#语言中，object是所有类型的基类，将上面的代码进行以下优化。

## 2.2 用object类型参数，只需要写一个方法
**方法实现**
```csharp
using System;

namespace MyGeneric
{
    public class CommonMethod
    {
        /// <summary>
		/// 打印个object值
		/// </summary>
		/// <param name="oParameter"></param>
		public static void ShowObject(object oParameter)
		{
    		Console.WriteLine("This is {0},parameter={1},type={2}",
        		typeof(CommonMethod), oParameter.GetType().Name, oParameter);
		}
    }
}
```

**方法调用**
```csharp
using System;
using System.Collections.Generic;

namespace MyGeneric
{
    /// <summary>
    /// main方法调用
    /// </summary>
    class Program
    {
        static void Main(string[] args)
        {
            try
            {                
                int iValue = 123;
                string sValue = "456";
                DateTime dtValue = DateTime.Now;
                object oValue = "678";

                Console.WriteLine("***********************Object***********************");
                CommonMethod.ShowObject(oValue);
                CommonMethod.ShowObject(iValue);
                CommonMethod.ShowObject(sValue);
                CommonMethod.ShowObject(dtValue);
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.ReadKey();
        }
    }
}
```

**运行结果**
```textplain
***********************Object***********************
This is MyGeneric.CommonMethod,parameter=String,type=678
This is MyGeneric.CommonMethod,parameter=Int32,type=123
This is MyGeneric.CommonMethod,parameter=String,type=456
This is MyGeneric.CommonMethod,parameter=DateTime,type=2021/7/25 19:40:51
```

**代码分析**

从上面的结果中我们可以看出，使用Object类型达到了我们的要求，解决了代码的可复用。

object类型的，为什么可以传入int、string等类型呢？
- object类型是一切类型的父类。
- 通过继承，子类拥有父类的一切属性和行为，任何父类出现的地方，都可以用子类来代替。

但是上面object类型的方法又会带来另外一些问题
- 装箱和拆箱，会损耗程序的性能。
- 类型安全问题。

于是微软在C#2.0的时候推出了泛型，可以很好的解决上面的问题。

## 2.3. 泛型方法，只需写一个方法
**方法实现**
```csharp
using System;

namespace MyGeneric
{
    public class GenericMethod
    {
        /// <summary>
        /// <summary>
        /// 泛型方法：方法名称后面加上尖括号，里面是类型参数
        ///           类型参数实际上就是一个类型T声明，方法就可以用这个类型T了
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="tParameter"></param>
        public static void Show<T>(T tParameter)
        {
            Console.WriteLine("This is {0},parameter={1},type={2}",
               typeof(GenericMethod), tParameter.GetType().Name, tParameter);
        }
    }
}
```

**方法调用**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// main方法调用
    /// </summary>
    class Program
    {
        static void Main(string[] args)
        {
            try
            {                
                int iValue = 123;
                string sValue = "456";
                DateTime dtValue = DateTime.Now;
                object oValue = "678";

                Console.WriteLine("***********************Generic***********************");
                GenericMethod.Show<int>(iValue);//调用泛型，需要指定类型参数
                GenericMethod.Show(iValue);//如果可以从参数类型推断，可以省略类型参数---语法糖(编译器提供的功能)
                GenericMethod.Show<string>(sValue);
                //GenericMethod.Show<int>(sValue);//类型错了
                GenericMethod.Show<DateTime>(dtValue);
                GenericMethod.Show<object>(oValue);
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.ReadKey();
        }
    }
}
```

**运行结果**
```textplain
***********************Generic***********************
This is MyGeneric.GenericMethod,parameter=Int32,type=123
This is MyGeneric.GenericMethod,parameter=Int32,type=123
This is MyGeneric.GenericMethod,parameter=String,type=456
This is MyGeneric.GenericMethod,parameter=DateTime,type=2021/7/25 22:24:35
This is MyGeneric.GenericMethod,parameter=String,type=678
```

**代码分析**

在泛型类型或方法定义中，类型参数是在其实例化泛型类型的一个变量时，客户端指定的特定类型的占位符。 泛型类( GenericList<T>)无法按原样使用，因为它不是真正的类型；它更像是类型的蓝图。 若要使用 GenericList<T>，客户端代码必须通过指定尖括号内的类型参数来声明并实例化构造类型。 此特定类的类型参数可以是编译器可识别的任何类型。 可创建任意数量的构造类型实例，其中每个使用不同的类型参数。

# 3. 泛型运行原理
## 3.1 为什么泛型可以解决上面的问题呢？
泛型是延迟声明的：即定义的时候没有指定具体的参数类型，把参数类型的声明推迟到了调用的时候才指定参数类型。 延迟思想在程序架构设计的时候很受欢迎。例如：分布式缓存队列、EF的延迟加载等等。

## 3.2 泛型究竟是如何工作的呢？
控制台程序最终会编译成一个exe程序，exe被点击的时候，会经过JIT(即时编译器)的编译，最终生成二进制代码，才能被计算机执行。泛型加入到语法以后，VS自带的编译器又做了升级，升级之后编译时遇到泛型，会做特殊的处理：生成占位符。再次经过JIT编译的时候，会把上面编译生成的占位符替换成具体的数据类型。

**方法调用**
```csharp
using System;
using System.Collections.Generic;

namespace MyGeneric
{
    /// <summary>
    /// main方法调用
    /// </summary>
    class Program
    {
        static void Main(string[] args)
        {
            try
            { 
                Console.WriteLine(typeof(List<>));
                Console.WriteLine(typeof(Dictionary<,>));
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.ReadKey();
        }
    }
}
```

**运行结果**
```textplain
System.Collections.Generic.List`1[T]
System.Collections.Generic.Dictionary`2[TKey,TValue]
```

**代码分析**

从上面的截图中可以看出：泛型在编译之后会生成占位符。

注意：占位符需要在英文输入法状态下才能输入，只需要按一次波浪线(数字1左边的键位)的键位即可，不需要按Shift键。

## 3.3 泛型性能怎么样？
比较普通方法、Object参数类型的方法、泛型方法的性能。

**方法实现**

添加一个Monitor类，让三种方法执行同样的操作，比较用时长短：
```csharp
using System;
using System.Diagnostics;

namespace MyGeneric
{
    /// <summary>
    /// 性能对比
    /// </summary>
    public class Monitor
    {
        public static void Show()
        {
            Console.WriteLine("****************Monitor******************");
            {
                int iValue = 12345;
                long commonSecond = 0;
                long objectSecond = 0;
                long genericSecond = 0;

                {
                    Stopwatch watch = new Stopwatch();
                    watch.Start();
                    for (int i = 0; i < 100_000_000; i++)
                    {
                        ShowInt(iValue);
                    }
                    watch.Stop();
                    commonSecond = watch.ElapsedMilliseconds;
                }
                {
                    Stopwatch watch = new Stopwatch();
                    watch.Start();
                    for (int i = 0; i < 100_000_000; i++)
                    {
                        ShowObject(iValue);
                    }
                    watch.Stop();
                    objectSecond = watch.ElapsedMilliseconds;
                }
                {
                    Stopwatch watch = new Stopwatch();
                    watch.Start();
                    for (int i = 0; i < 100_000_000; i++)
                    {
                        Show<int>(iValue);
                    }
                    watch.Stop();
                    genericSecond = watch.ElapsedMilliseconds;
                }
                Console.WriteLine("commonSecond={0},objectSecond={1},genericSecond={2}"
                    , commonSecond, objectSecond, genericSecond);
            }
        }
        
        private static void ShowInt(int iParameter)
        {
            //do nothing
        }
        private static void ShowObject(object oParameter)
        {
            //do nothing
        }
        private static void Show<T>(T tParameter)
        {
            //do nothing
        }
    }
}
```

**方法调用**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// main方法调用
    /// </summary>
    class Program
    {
        static void Main(string[] args)
        {
            try
            { 
                Monitor.Show();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.ReadKey();
        }
    }
}
```

**运行结果**
```textplain
****************Monitor******************
commonSecond=325,objectSecond=644,genericSecond=347
```

**代码分析**

从结果中可以看出：泛型方法的性能最高，其次是普通方法，object方法的性能最低。

泛型的好处是速度快且支持不同类型。

# 4. 泛型应用范围
## 4.1 泛型方法
```csharp
using System;

namespace MyGeneric
{
    public class GenericMethod
    {
        /// <summary>
        /// 泛型方法：为了一个方法满足不同的类型的需求
        ///           一个方法完成多实体的查询
        ///           一个方法完成不同的类型的数据展示
        /// 多类型参数：不要关键字，不要类名称重复
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="tParameter"></param>
        public T Get<T, S, Model, Eleven, Null>(Eleven eleven)
        {
            GenericClass<int> genericClass = new GenericClass<int>();
            throw new Exception();
        }
    }
}
```

## 4.2 泛型类
**泛型类定义**
```csharp
namespace MyGeneric
{
    /// <summary>
    /// 泛型类
    /// </summary>
    /// <typeparam name="T"></typeparam>
    public class GenericClass<T>
    {
        public T _T;
    }
}
```

**泛型类使用**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// main方法调用
    /// </summary>
    class Program
    {
        static void Main(string[] args)
        {
            try
            { 
                // T是int类型
                GenericClass<int> genericInt = new GenericClass<int>();
                genericInt._T = 123;
                // T是string类型
                GenericClass<string> genericString = new GenericClass<string>();
                genericString._T = "123";
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.ReadKey();
        }
    }
}
```

## 4.3 泛型接口
```csharp
namespace MyGeneric
{
    /// <summary>
    /// 泛型接口
    /// </summary>
    /// <typeparam name="T"></typeparam>
    public interface IGenericInterface<T>
    {
        //泛型类型的返回值
        T GetT(T t);
    }
}
```

## 4.4 泛型委托
```csharp
public delegate void SayHi<T>(T t); //泛型委托
```

## 4.5 泛型类继承父类，实现接口
**如果子类不是泛型的，那么继承的时候必须指定具体类型**
```csharp
namespace MyGeneric
{
    /// <summary>
    /// 使用泛型的时候必须指定具体类型，这里的具体类型是int
    /// </summary>
    public class CommonClass : GenericClass<int>
    {
    }
}
```

**如果子类也是泛型的，那么继承的时候可以不指定具体类型**
```csharp
namespace MyGeneric
{
    /// <summary>
    /// 子类也是泛型的，继承的时候可以不指定具体类型
    /// </summary>
    /// <typeparam name="T"></typeparam>
    public class CommonClassChild<T> : GenericClass<T>
    {
    }
}
```

**如果子类不是泛型的，那么实现泛型接口的时候必须指定具体类型**
```csharp
namespace MyGeneric
{
    /// <summary>
    /// 必须指定具体类型
    /// </summary>
    public class Common : IGenericInterface<string>
    {
        public string GetT(string t)
        {
            throw new System.NotImplementedException();
        }
    }
}
```

**如果子类也是泛型的，那么实现泛型接口的时候可以不指定具体类型**
```csharp
namespace MyGeneric
{
    /// <summary>
    /// 可以不知道具体类型，但是子类也必须是泛型的
    /// </summary>
    /// <typeparam name="T"></typeparam>
    public class CommonChild<T> : IGenericInterface<T>
    {
        public T GetT(T t)
        {
            throw new NotImplementedException();
        }
    }
}
```

# 5. 泛型约束
## 5.1 为什么要有泛型约束？
因为有约束才有权利，自由主义的鼻祖洛克先生说过，有了法律，才有自由
**定义类，属性，方法**
```csharp
using System;

namespace MyGeneric
{
    public interface ISports
    {
        void Pingpang();
    }

    public interface IWork
    {
        void Work();
    }

    public class People
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public void Hi()
        { }
    }

    public class Chinese : People, ISports, IWork
    {
        public void Tradition()
        {
            Console.WriteLine("仁义礼智信，温良恭俭让");
        }
        public void SayHi()
        {
            Console.WriteLine("吃了么？");
        }

        public void Pingpang()
        {
            Console.WriteLine("打乒乓球...");
        }

        public void Work()
        {
            throw new NotImplementedException();
        }
    }

    public class Hubei : Chinese
    {
        public string Changjiang { get; set; }
        public void Majiang()
        {
            Console.WriteLine("打麻将啦。。");
        }
    }

    public class Japanese : ISports
    {
        public int Id { get; set; }
        public string Name { get; set; }


        public void Pingpang()
        {
            Console.WriteLine("打乒乓球...");
        }
    }
}
```

**定义方法**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// 泛型约束
    /// </summary>
    public class GenericConstraint
    {
        /// <summary>
        /// object参数
        /// </summary>
        /// <param name="oParameter"></param>
        public static void ShowObject(object oParameter)
        {
            Console.WriteLine("This is {0},parameter={1},type={2}",
                typeof(GenericConstraint), oParameter.GetType().Name, oParameter);
            People people = (People)oParameter;
            Console.WriteLine($"{people.Id}  {people.Name}");
        }
    }
}
```

**调用方法**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// main方法调用
    /// </summary>
    class Program
    {
        static void Main(string[] args)
        {
            try
            { 
                People people = new People()
                {
                    Id = 123,
                    Name = "Jon"
                };
                Chinese chinese = new Chinese()
                {
                    Id = 234,
                    Name = "一生为你"
                };
                Hubei hubei = new Hubei()
                {
                    Id = 345,
                    Name = "木头"
                };
                Japanese japanese = new Japanese()
                {
                    Id = 456,
                    Name = "苍老师"
                };

                GenericConstraint.ShowObject(people);
                GenericConstraint.ShowObject(chinese);
                GenericConstraint.ShowObject(hubei);
                //没有约束，任何类型都能传递进来，所以可能不安全
                GenericConstraint.ShowObject(japanese);
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.ReadKey();
        }
    }
}
```

**运行结果**
```textplain
This is MyGeneric.GenericConstraint,parameter=People,type=MyGeneric.People
123  Jon
This is MyGeneric.GenericConstraint,parameter=Chinese,type=MyGeneric.Chinese
234  一生为你
This is MyGeneric.GenericConstraint,parameter=Hubei,type=MyGeneric.Hubei
345  木头
This is MyGeneric.GenericConstraint,parameter=Japanese,type=MyGeneric.Japanese
无法将类型为“MyGeneric.Japanese”的对象强制转换为类型“MyGeneric.People”。
```

**代码分析**

可以看出程序报错了，因为Japanese没有继承自People，这里类型转换的时候失败了。这样会造成类型不安全的问题。那么怎么解决类型不安全的问题呢？那就是使用泛型约束。

所谓的泛型约束，实际上就是约束的类型T。使T必须遵循一定的规则。比如T必须继承自某个类，或者T必须实现某个接口等等。那么怎么给泛型指定约束？其实也很简单，只需要where关键字，加上约束的条件。

**添加泛型约束后代码实现**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// main方法调用
    /// </summary>
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                People people = new People()
                {
                    Id = 123,
                    Name = "Jon"
                };
                Chinese chinese = new Chinese()
                {
                    Id = 234,
                    Name = "一生为你"
                };
                Hubei hubei = new Hubei()
                {
                    Id = 345,
                    Name = "木头"
                };
                Japanese japanese = new Japanese()
                {
                    Id = 456,
                    Name = "苍老师"
                };

                GenericConstraint.ShowP<People>(people);
                GenericConstraint.ShowP<Chinese>(chinese);
                GenericConstraint.ShowP<Hubei>(hubei);
                //japanese不是people或者people的子类，所以编译直接报错
                //GenericConstraint.ShowP<Japanese>(japanese);
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.ReadKey();
        }
    }
}
```

**添加泛型约束后运行结果**
```textplain
This is MyGeneric.GenericConstraint,parameter=People,type=MyGeneric.People
123  Jon
This is MyGeneric.GenericConstraint,parameter=Chinese,type=MyGeneric.Chinese
234  一生为你
This is MyGeneric.GenericConstraint,parameter=Hubei,type=MyGeneric.Hubei
345  木头
```

**添加泛型约束后代码分析**

代码直接通不过编译，增强代码的安全性

## 5.2 五种泛型约束

| 约束 | 说明 |
| --- | --- |
| `T：结构` | 类型参数必须是值类型 |
| `T：类` | 类型参数必须是引用类型；这一点也适用于任何类、接口、委托或数组类型。 |
| `T：new()` | 类型参数必须具有无参数的公共构造函数。 当与其他约束一起使用时，new() 约束必须最后指定。 |
| `T：<基类名>` | 类型参数必须是指定的基类或派生自指定的基类。 |
| `T：<接口名称>` | 类型参数必须是指定的接口或实现指定的接口。 可以指定多个接口约束。 约束接口也可以是泛型的。 |

### 5.2.1 基类约束
**代码实现**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// 泛型约束
    /// </summary>
    public class GenericConstraint
    {
        /// <summary>
        /// 基类约束
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="tParameter"></param>
        public static void ShowP<T>(T tParameter) where T : People
        {
            Console.WriteLine("This is {0},parameter={1},type={2}",
               typeof(GenericConstraint), tParameter.GetType().Name, tParameter);

            Console.WriteLine($"{tParameter.Id}  {tParameter.Name}");
            tParameter.Hi();
        }
    }
}
```

**代码说明**

基类约束保证T必须是类或者子类

基类约束时，基类不能是密封类，即不能是sealed类。sealed类表示该类不能被继承，在这里用作约束就无任何意义，因为sealed类没有子类。

### 5.2.2 接口约束
**代码实现**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// 泛型约束
    /// </summary>
    public class GenericConstraint
    {
        /// <summary>
        /// 接口约束
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="tParameter"></param>
        public static void ShowPI<T>(T tParameter) where T : ISports
        {
            Console.WriteLine("This is {0},parameter={1},type={2}",
               typeof(GenericConstraint), tParameter.GetType().Name, tParameter);

            tParameter.Pingpang();
        }
    }
}
```

**代码说明**

接口约束保证T必须实现接口

### 5.2.3 引用类型约束 class
**代码实现**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// 泛型约束
    /// </summary>
    public class GenericConstraint
    {
        /// <summary>
        /// 引用类型约束
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="t"></param>
        /// <returns></returns>
        public static T Get<T>(T t) where T : class
        {
            return t;
        }
    }
}
```

**代码说明**

引用类型约束保证T一定是引用类型的。

### 5.2.4 值类型约束 struct
**代码实现**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// 泛型约束
    /// </summary>
    public class GenericConstraint
    {
        /// <summary>
        /// 值类型类型约束
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="t"></param>
        /// <returns></returns>
        public static T GetStruct<T>(T t) where T : struct
        {
            return t;
        }
    }
}
```

**代码说明**

值类型约束保证T一定是值类型的。

### 5.2.5 无参数构造函数约束 new()
**代码实现**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// 泛型约束
    /// </summary>
    public class GenericConstraint
    {
        /// <summary>
        /// 值类型类型约束
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="t"></param>
        /// <returns></returns>
        public static T GetNew<T>(T t) where T : new()
        {
            return t;
        }
    }
}
```

**代码说明**

无参数构造函数约束保证T必须有无参数构造函数

## 5.3 泛型约束可以同时约束多个
**代码实现**
```csharp
using System;

namespace MyGeneric
{
    /// <summary>
    /// 泛型约束
    /// </summary>
    public class GenericConstraint
    {
        /// <summary>
        /// 泛型约束也可以同时约束多个
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="tParameter"></param>
        public static void Show<T>(T tParameter) where T : People, ISports, IWork, new()
        {
            Console.WriteLine("This is {0},parameter={1},type={2}",
               typeof(GenericConstraint), tParameter.GetType().Name, tParameter);

            Console.WriteLine($"{tParameter.Id}  {tParameter.Name}");
            tParameter.Hi();
        }
    }
}
```

**代码说明**

有多个泛型约束时，new()约束一定是在最后。

# 6. 泛型的协变和逆变
## 6.1 泛型的协变和逆变是什么？
协变和逆变是在.NET 4.0的时候出现的，只能放在接口或者委托的泛型参数前面，out 协变covariant，用来修饰返回值；in：逆变contravariant，用来修饰传入参数。

## 6.2 为什么要用泛型的协变？
**定义一个Bird类， 然后再定义一个Sparrow类继承自Bird类**
```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace MyGeneric
{
    public class Bird
    {
        public int Id { get; set; }
    }
    public class Sparrow : Bird
    {
        public string Name { get; set; }
    }
}
```

**代码调用**
```csharp
Bird bird1 = new Bird();
Bird bird2 = new Sparrow();
Sparrow sparrow1 = new Sparrow();
List<Bird> birdList1 = new List<Bird>();
List<Sparrow> sparrowList1 = new List<Sparrow>();
```

**代码分析**

下面的一句代码是不是正确的呢？
```csharp
List<Bird> sparrowList1 = new List<Sparrow>();
```

可能有人会认为是正确的：因为一只Sparrow属于Bird，那么一群Sparrow也应该属于一群Bird啊。但是实际上这样声明是错误的：因为List和List之间没有父子关系。

## 6.3 泛型协变定义
**协变代码实现**
```csharp
// 协变
IEnumerable<Bird> birdList1 = new List<Bird>();
IEnumerable<Bird> birdList2 = new List<Sparrow>();
```

**F12查看IEnumerable定义：**
```csharp
#region 程序集 mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089
// C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\mscorlib.dll
// Decompiled with ICSharpCode.Decompiler 6.1.0.5902
#endregion

using System.Runtime.CompilerServices;

namespace System.Collections.Generic
{
    [TypeDependency("System.SZArrayHelper")]
    [__DynamicallyInvokable]
    public interface IEnumerable<out T> : IEnumerable
    {
        [__DynamicallyInvokable]
        new IEnumerator<T> GetEnumerator();
    }
}
#if false // 反编译日志
缓存中的 9 项
#endif
```

**代码分析**

可以看到，在泛型接口的T前面有一个out关键字修饰，而且T只能是返回值类型，不能作为参数类型，这就是协变。使用了协变以后，左边声明的是基类，右边可以声明基类或者基类的子类。

## 6.4 泛型协变用在委托上
**协变除了可以用在接口上面，也可以用在委托上面：**
```csharp
//委托协变
Func<Bird> func = new Func<Sparrow>(() => null);
```

## 6.5 自定义泛型协变
**定义协变**
```csharp
/// <summary>
/// out 协变 只能是返回结果， T在类里面就不能做方法参数，做参数会报错
/// </summary>
/// <typeparam name="T"></typeparam>
public interface ICustomerListOut<out T>
{
    T Get();
}

public class CustomerListOut<T> : ICustomerListOut<T>
{
    public T Get()
    {
        return default(T);
    }
}
```

**使用协变**
```csharp
//自定义协变
ICustomerListOut<Bird> customerList1 = new CustomerListOut<Bird>();
ICustomerListOut<Bird> customerList2 = new CustomerListOut<Sparrow>();
```

## 6.6 自定义泛型逆变
**定义泛型逆变**
```csharp
/// <summary>
/// in 逆变
/// </summary>
/// <typeparam name="T"></typeparam>
public interface ICustomerListIn<in T>
{
    void Show(T t);
}

public class CustomerListIn<T> : ICustomerListIn<T>
{
    public void Show(T t)
    {

    }
}
```

**使用自定义逆变**
```csharp
//自定义逆变
ICustomerListIn<Sparrow> customerList2 = new CustomerListIn<Sparrow>();
ICustomerListIn<Sparrow> customerList1 = new CustomerListIn<Bird>();
```

**代码说明**

在泛型接口的T前面有一个In关键字修饰，而且T只能方法参数，不能作为返回值类型，这就是逆变。

## 6.7 协变和逆变同时使用
**代码实现**
```csharp
/// <summary>
/// inT  逆变
/// outT 协变
/// </summary>
/// <typeparam name="inT"></typeparam>
/// <typeparam name="outT"></typeparam>
public interface IMyList<in inT, out outT>
{
    void Show(inT t);
    outT Get();
    outT Do(inT t);
}

public class MyList<T1, T2> : IMyList<T1, T2>
{
    public void Show(T1 t)
    {
        Console.WriteLine(t.GetType().Name);
    }

    public T2 Get()
    {
        Console.WriteLine(typeof(T2).Name);
        return default(T2);
    }

    public T2 Do(T1 t)
    {
        Console.WriteLine(t.GetType().Name);
        Console.WriteLine(typeof(T2).Name);
        return default(T2);
    }
}
```

**代码调用**
```csharp
IMyList<Sparrow, Bird> myList1 = new MyList<Sparrow, Bird>();
IMyList<Sparrow, Bird> myList2 = new MyList<Sparrow, Sparrow>(); //协变
IMyList<Sparrow, Bird> myList3 = new MyList<Bird, Bird>(); //逆变
IMyList<Sparrow, Bird> myList4 = new MyList<Bird, Sparrow>(); //协变+逆变
```

# 7. 泛型缓存
## 7.1 常见字典缓存实现
**代码实现**
```csharp
/// <summary>
/// 字典缓存：静态属性常驻内存
/// </summary>
public class DictionaryCache
{
    private static Dictionary<Type, string> _TypeTimeDictionary = null;
    static DictionaryCache()
    {
        Console.WriteLine("This is DictionaryCache 静态构造函数");
        _TypeTimeDictionary = new Dictionary<Type, string>();
    }
    public static string GetCache<T>()
    {
        Type type = typeof(Type);
        if (!_TypeTimeDictionary.ContainsKey(type))
        {
            _TypeTimeDictionary[type] = string.Format("{0}_{1}", typeof(T).FullName, DateTime.Now.ToString("yyyyMMddHHmmss.fff"));
        }
        return _TypeTimeDictionary[type];
    }
}
```

**代码说明**

类中的静态类型无论实例化多少次，在内存中只会有一个。静态构造函数只会执行一次。

## 7.2 泛型缓存实现
**代码实现**
```csharp
/// <summary>
/// 每个不同的T，都会生成一份不同的副本
/// 适合不同类型，需要缓存一份数据的场景，效率高
/// </summary>
/// <typeparam name="T"></typeparam>
public class GenericCache<T>
{
    static GenericCache()
    {
        Console.WriteLine("This is GenericCache 静态构造函数");
        _TypeTime = string.Format("{0}_{1}", typeof(T).FullName, DateTime.Now.ToString("yyyyMMddHHmmss.fff"));
    }

    private static string _TypeTime = "";

    public static string GetCache()
    {
        return _TypeTime;
    }
}
```

**然后新建一个测试类，用来测试GenericCache类的执行顺序**
```csharp
/// <summary>
/// 泛型缓存
/// </summary>
public class GenericCacheTest
{
    public static void Show()
    {
        for (int i = 0; i < 5; i++)
        {
            Console.WriteLine(GenericCache<int>.GetCache());
            Thread.Sleep(10);
            Console.WriteLine(GenericCache<long>.GetCache());
            Thread.Sleep(10);
            Console.WriteLine(GenericCache<DateTime>.GetCache());
            Thread.Sleep(10);
            Console.WriteLine(GenericCache<string>.GetCache());
            Thread.Sleep(10);
            Console.WriteLine(GenericCache<GenericCacheTest>.GetCache());
            Thread.Sleep(10);
        }
    }
}
```

**调用方法**
```csharp
GenericCacheTest.Show();
```

**运行结果**
```textplain
*******************GenericCache********************
This is GenericCache 静态构造函数
System.Int32_20210213230031.439
This is GenericCache 静态构造函数
System.Int64_20210213230031.463
This is GenericCache 静态构造函数
System.DateTime_20210213230031.479
This is GenericCache 静态构造函数
System.String_20210213230031.495
This is GenericCache 静态构造函数
MyGeneric.GenericCacheTest_20210213230031.511
System.Int32_20210213230031.439
System.Int64_20210213230031.463
System.DateTime_20210213230031.479
System.String_20210213230031.495
MyGeneric.GenericCacheTest_20210213230031.511
System.Int32_20210213230031.439
System.Int64_20210213230031.463
System.DateTime_20210213230031.479
System.String_20210213230031.495
MyGeneric.GenericCacheTest_20210213230031.511
System.Int32_20210213230031.439
System.Int64_20210213230031.463
System.DateTime_20210213230031.479
System.String_20210213230031.495
MyGeneric.GenericCacheTest_20210213230031.511
System.Int32_20210213230031.439
System.Int64_20210213230031.463
System.DateTime_20210213230031.479
System.String_20210213230031.495
MyGeneric.GenericCacheTest_20210213230031.511
```

**代码分析**

从上面的截图中可以看出，泛型会为不同的类型都创建一个副本，所以静态构造函数会执行5次。 而且每次静态属性的值都是一样的。利用泛型的这一特性，可以实现缓存。

注意：只能为不同的类型缓存一次。泛型缓存比字典缓存效率高。但是泛型缓存不能主动释放。
