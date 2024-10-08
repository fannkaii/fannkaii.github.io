---
layout: post
title: "C#高级--特性详解"
author: "Kai"
header-style: text
tags:
  - C#
---

转自：https://blog.csdn.net/liyou123456789/article/details/119314247

# 1. 特性是什么
## 1.1 特性定义
**特性（Attribute）** 是用于在运行时传递程序中各种元素（比如类、方法、结构、枚举、组件等）的行为信息的声明性标签。您可以通过使用特性向程序添加声明性信息。一个声明性标签是通过放置在它所应用的元素前面的方括号（[ ]）来描述的。

特性（Attribute）用于添加元数据，如编译器指令和注释、描述、方法、类等其他信息。.Net 框架提供了两种类型的特性：预定义特性和自定义特性。

## 1.2 特性的语法
特性（Attribute）的名称和值是在方括号内规定的，放置在它所应用的元素之前。positional_parameters 规定必需的信息，name_parameter 规定可选的信息。
```csharp
[attribute(positional_parameters, name_parameter = value, ...)]
element
```

## 1.3 特性和注释有什么区别
特性很厉害，加了特性之后，就有很厉害的功能

`[Obsolete]`编译时就有提示，影响了编译器`[Obsolete(“请不要使用这个了，请使用什么来代替”, true)]`甚至导致编译报错

`[Serializable]`对象就可以序列化，影响了程序运行
```csharp
using System;

namespace MyAttribute
{
    /// <summary>
    /// 这里是注释，除了让人看懂这里写的是什么，对运行没有任何影响
    /// </summary>
    ///[Obsolete("请不要使用这个了，请使用什么来代替")]//对编译都产生了影响，编译出现警告
    ///[Obsolete("请不要使用这个了，请使用什么来代替", true)]//对编译都产生了影响，编译报错不通过
    [Serializable]//可以序列化和反序列化
    public class Student
    {        
        public int Id { get; set; }
        
        public string Name { get; set; }
        
        public void Study()
        {
            Console.WriteLine($"这里是{this.Name}在学习");
        }
        
        public string Answer([Custom]string name)
        {
            return $"This is {name}";
        }
    }
}
```

特性无处不在：EF – MVC – WCF – WebService – UnitTest – IOC – AOP – SuperSocket

# 2. 特性声明和使用
## 2.1 什么是特性
特性其实就是一个类，直接或间接继承自Attribute
```csharp
#region 程序集 mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089
// C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\mscorlib.dll
// Decompiled with ICSharpCode.Decompiler 6.1.0.5902
#endregion

using System.Reflection;
using System.Runtime.InteropServices;

namespace System
{
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Enum | AttributeTargets.Delegate, Inherited = false)]
    [ComVisible(true)]
    public sealed class SerializableAttribute : Attribute
    {
        internal static Attribute GetCustomAttribute(RuntimeType type)
        {
            if ((type.Attributes & TypeAttributes.Serializable) != TypeAttributes.Serializable)
            {
                return null;
            }

            return new SerializableAttribute();
        }

        internal static bool IsDefined(RuntimeType type)
        {
            return type.IsSerializable;
        }
    }
}
#if false // 反编译日志
缓存中的 9 项
#endif
```

## 2.2 自定义一个特性
```csharp
using System;

namespace MyAttribute
{    
    public class CustomAttribute : Attribute
    {
        
    }
}
```

约定俗成用Attribute结尾，标记时就可以省略掉；可以用中括号包裹，然后标记到元素，其实就是调用构造函数；
```csharp
using System;

namespace MyAttribute
{
    [Custom]
    public class Student
    {
        [Custom]
        public int Id { get; set; }
        public string Name { get; set; }
        [Custom]
        public void Study()
        {
            Console.WriteLine($"这里是{this.Name}跟着Eleven老师学习");
        }
    }
}
```

## 2.3 AttributeUsage特性
直接在一个元素上添加多个相同的特性，会报错特性重复，需要在特性上面添加特性标记`[AttributeUsage(AttributeTargets.All, AllowMultiple = true)]`这样就可以给同一个元素添加多个相同的特性了
```csharp
using System;

namespace MyAttribute
{
    [AttributeUsage(AttributeTargets.All, AllowMultiple = true)]
    public class CustomAttribute : Attribute
    {
        public CustomAttribute()
        {
            Console.WriteLine($"{this.GetType().Name} 无参数构造函数执行");
        }
        public CustomAttribute(int id)
        {
            Console.WriteLine($"{this.GetType().Name} int参数构造函数执行");
            this._Id = id;
        }
        public CustomAttribute(string name)
        {
            Console.WriteLine($"{this.GetType().Name} string参数构造函数执行");
            this._Name = name;
        }
    }
}
```

多个相同的特性情况展示
```csharp
using System;

namespace MyAttribute
{    
    [Custom]
    [Custom()]    
    [Custom(0)]    
    public class Student
    {
        [Custom]
        public int Id { get; set; }
        public string Name { get; set; }
        [Custom]
        public void Study()
        {
            Console.WriteLine($"这里是{this.Name}跟着Eleven老师学习");
        }
       
        [Custom(0)]
        public string Answer([Custom]string name)
        {
            return $"This is {name}";
        }
    }
}
```

`AttributeUsage`特性，影响编译器运行，指定修饰的对象、能否重复修饰、修饰的特性子类是否生效，建议是明确约束用在哪些对象的
```csharp
[AttributeUsage(AttributeTargets.Method|AttributeTargets.Class|AttributeTargets.Property, AllowMultiple = true)]
```

## 2.4 特性可以指定属性和字段
```csharp
using System;

namespace MyAttribute
{

    [AttributeUsage(AttributeTargets.All, AllowMultiple = true, Inherited = true)]
    public class CustomAttribute : Attribute
    {
        public CustomAttribute()
        {
            Console.WriteLine($"{this.GetType().Name} 无参数构造函数执行");
        }
        public CustomAttribute(int id)
        {
            Console.WriteLine($"{this.GetType().Name} int参数构造函数执行");
            this._Id = id;
        }
        public CustomAttribute(string name)
        {
            Console.WriteLine($"{this.GetType().Name} string参数构造函数执行");
            this._Name = name;
        }

        private int _Id = 0;
        private string _Name = null;

        public string Remark;
        public string Description { get; set; }
    }
}
```

```csharp
using System;

namespace MyAttribute
{
    [Custom(Remark = "123")]
    [Custom(Remark = "123", Description = "456")]
    [Custom(0, Remark = "123")]
    [Custom(0, Remark = "123", Description = "456")]
    public class Student
    {        
        public int Id { get; set; }
        public string Name { get; set; }
        
        public void Study()
        {
            Console.WriteLine($"这里是{this.Name}跟着Eleven老师学习");
        }
    }
}
```

## 2.5 特性还可以修饰返回值和参数
```csharp
using System;

namespace MyAttribute
{
    public class Student
    {
        [return: Custom]
        public string Answer([Custom]string name)
        {
            return $"This is {name}";
        }
    }
}
```

## 2.6 多重修饰既可以中括号隔开，也可以一个中括号里面逗号隔开
```csharp
using System;

namespace MyAttribute
{
    [Custom]
    [Custom()]
    [Custom(Remark = "123")]
    [Custom(Remark = "123", Description = "456")]
    [Custom(0)]
    [Custom(0, Remark = "123")]
    [Custom(0, Remark = "123", Description = "456")]
    public class Student
    {
        [return: Custom, Custom,Custom(), Custom(0, Remark = "123", Description = "456")]        
        public string Answer(string name)
        {
            return $"This is {name}";
        }
    }
}
```

# 3. 特性工作原理
自定义的特性，好像毫无意义，那框架提供的特性究竟是怎么产生价值的呢，`[Obsolete]``[AttributeUsage]`影响了编译器，这属于系统内置，我们搞不了。

反编译之后，发现特性会在元素内部生成 `.custom`的东西，是这个东西我们C#访问不到，简直可以理解为，特性没有产生任何变化。但是框架究竟是怎么产生功能的呢？

可以通过反射，从类型，属性，方法都可以获取特性实例，要求先`IsDefined`检测再获取实例化。程序运行时可以找到特性，需要一个第三方`InvokeCenter`，在这里去主动检测并且使用特性，才能赋予它功能，只定义特性是没有用的。
```csharp
using System;

namespace MyAttribute
{
    public class InvokeCenter
    {
        public static void ManagerStudent<T>(T student) where T : Student
        {
            Console.WriteLine($"{student.Id}_{student.Name}");
            student.Study();
            student.Answer("123");

            Type type = student.GetType();
            if (type.IsDefined(typeof(CustomAttribute), true))
            {
                //type.GetCustomAttribute()
                object[] oAttributeArray = type.GetCustomAttributes(typeof(CustomAttribute), true);
                foreach (CustomAttribute attribute in oAttributeArray)
                {
                    attribute.Show();
                    //attribute.Description
                }

                foreach (var prop in type.GetProperties())
                {
                    if (prop.IsDefined(typeof(CustomAttribute), true))
                    {
                        object[] oAttributeArrayProp = prop.GetCustomAttributes(typeof(CustomAttribute), true);
                        foreach (CustomAttribute attribute in oAttributeArrayProp)
                        {
                            attribute.Show();
                        }
                    }
                }
                foreach (var method in type.GetMethods())
                {
                    if (method.IsDefined(typeof(CustomAttribute), true))
                    {
                        object[] oAttributeArrayMethod = method.GetCustomAttributes(typeof(CustomAttribute), true);
                        foreach (CustomAttribute attribute in oAttributeArrayMethod)
                        {
                            attribute.Show();
                        }
                    }
                }
            }
        }
    }
}
```

特性是在编译时确定的，构造函数/属性/字段，都不能用变量，所以，mvc5-filter是不能注入的，所以在core里面才提供了注入filter的方式

# 4. 特性应用案例
## 4.1 特性实现枚举展示描述信息
### 4.1.1 创建枚举类
```csharp
namespace MyAttribute.EnumExtend
{
    /// <summary>
    /// 用户状态
    /// </summary>
    public enum UserState
    {
        /// <summary>
        /// 正常状态
        /// </summary>
        [Remark("正常状态")]
        Normal = 0,
        /// <summary>
        /// 已冻结
        /// </summary>
        [Remark("已冻结")]
        Frozen = 1,
        /// <summary>
        /// 已删除
        /// </summary>
        [Remark("已删除")]
        Deleted = 2
    }
}
```

### 4.1.2 创建特性类
```csharp
using System;

namespace MyAttribute.EnumExtend
{
    /// <summary>
    /// Remark特性
    /// </summary>
    [AttributeUsage(AttributeTargets.Field)]
    public class RemarkAttribute : Attribute
    {
        public string Remark { get; private set; }
        public RemarkAttribute(string remark)
        {
            this.Remark = remark;
        }
    }
}
```

### 4.1.3 枚举扩展方法
```csharp
using System;
using System.Reflection;

namespace MyAttribute.EnumExtend
{
    public static class AttributeExtend
    {
        public static string GetRemark(this Enum value)
        {
            Type type = value.GetType();
            var field = type.GetField(value.ToString());
            if (field.IsDefined(typeof(RemarkAttribute), true))
            {
                RemarkAttribute attribute = (RemarkAttribute)field.GetCustomAttribute(typeof(RemarkAttribute), true);
                return attribute.Remark;
            }
            else
            {
                return value.ToString();
            }
        }
    }
}
```

### 4.1.4 枚举调用扩展方法
```csharp
using MyAttribute.EnumExtend;
using MyAttribute.ValidateExtend;
using System;

namespace MyAttribute
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
                #region 特性实现枚举展示描述信息
                {
                    UserState userState = UserState.Frozen;                    
                    string reamrk = userState.GetRemark();
                    //有了特性，文字其实是直接固化在枚举上面， 如果修改只用改这里
                    //1 数据展示--不想展示属性名字，而是用一个中文描述
                    //2 想指定哪个是主键 哪个是自增
                    //3 别名--数据库里面叫A 程序叫B，怎么映射起来
                }
                #endregion
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.Read();
        }
    }
}
```

## 4.2 特性实现数据验证
### 4.2.1 基类抽象特性
```csharp
using System;

namespace MyAttribute.ValidateExtend
{
    public abstract class AbstractValidateAttribute : Attribute
    {
        public abstract bool Validate(object oValue);
    }
}
```

### 4.2.2 子类特性实现 – 数字长度
```csharp
using System;

namespace MyAttribute.ValidateExtend
{
    [AttributeUsage(AttributeTargets.Property)]
    public class LongAttribute : AbstractValidateAttribute
    {
        private long _Min = 0;
        private long _Max = 0;
        public LongAttribute(long min, long max)
        {
            this._Min = min;
            this._Max = max;
        }

        public override bool Validate(object oValue)
        {
            return oValue != null
                && long.TryParse(oValue.ToString(), out long lValue)
                && lValue >= this._Min
                && lValue <= this._Max;
        }
    }
}
```

### 4.2.3 子类特性实现 – 可空
```csharp
namespace MyAttribute.ValidateExtend
{
    public class RequiredAttribute : AbstractValidateAttribute
    {
        public override bool Validate(object oValue)
        {
            return oValue != null
                && !string.IsNullOrWhiteSpace(oValue.ToString());
        }
    }
}
```

### 4.2.4 子类特性实现 – 字符串长度
```csharp
using System;

namespace MyAttribute.ValidateExtend
{
    [AttributeUsage(AttributeTargets.Property)]
    public class StringLengthAttribute : AbstractValidateAttribute
    {
        private int _Min = 0;
        private int _Max = 0;
        public StringLengthAttribute(int min, int max)
        {
            this._Min = min;
            this._Max = max;
        }

        public override bool Validate(object oValue)
        {
            return oValue != null
                && oValue.ToString().Length >= this._Min
                && oValue.ToString().Length <= this._Max;
        }
    }
}
```

### 4.2.5 泛型扩展方法
```csharp
using System;

namespace MyAttribute.ValidateExtend
{
    public static class AttributeExtend
    {
        public static bool Validate<T>(this T t)
        {
            Type type = t.GetType();
            foreach (var prop in type.GetProperties())
            {
                if (prop.IsDefined(typeof(AbstractValidateAttribute), true))
                {
                    object oValue = prop.GetValue(t);
                    foreach (AbstractValidateAttribute attribute in prop.GetCustomAttributes(typeof(AbstractValidateAttribute), true))
                    {
                        if (!attribute.Validate(oValue))
                            return false;
                    }
                }
            }
            return true;
        }
    }
}
```

### 4.2.6 常规类字段定义
```csharp
using System;

namespace MyAttribute.ValidateExtend
{
    public static class AttributeExtend
    {
        public static bool Validate<T>(this T t)
        {
            Type type = t.GetType();
            foreach (var prop in type.GetProperties())
            {
                if (prop.IsDefined(typeof(AbstractValidateAttribute), true))
                {
                    object oValue = prop.GetValue(t);
                    foreach (AbstractValidateAttribute attribute in prop.GetCustomAttributes(typeof(AbstractValidateAttribute), true))
                    {
                        if (!attribute.Validate(oValue))
                            return false;
                    }
                }
            }
            return tue;
        }
    }
}
```

### 4.2.7 类调用扩展方法验证字段
```csharp
using MyAttribute.EnumExtend;
using MyAttribute.ValidateExtend;
using System;

namespace MyAttribute
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
                #region 特性实现数据验证，并且可扩展
                {
                    //通过特性去提供额外行为
                    //数据验证--到处都需要验证
                    StudentVip student = new StudentVip()
                    {
                        Id = 123,
                        Name = "无为",
                        QQ = 729220650,
                        Salary = 1010000
                    };                    

                    if (student.Validate())
                    {
                        Console.WriteLine("特性校验成功");
                    }
                    //1 可以校验多个属性
                    //2 支持多重校验
                    //3 支持规则的随意扩展
                }
                #endregion
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.Read();
        }
    }
}
```
