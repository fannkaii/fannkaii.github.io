---
layout: post
title: "C#高级--反射详解"
author: "Kai"
header-style: text
tags:
  - C#
---

转自：https://blog.csdn.net/liyou123456789/article/details/119548050

# 1. 反射是什么
## 1.1 C#编译运行过程
高级语言 -> 编译 -> dll/exe文件 -> CLR/JIT -> 机器码
![20240207100346](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240207100346.png)

## 1.2 原理解析
- **metadata**：元数据数据清单，记录了dll中包含了哪些东西,是一个描述。
- **IL**：中间语言，编译把高级语言编译后得到的C#中最真实的语言状态，面向对象语言。
- **反射**：来自于System.Reflection，是一个帮助类库，可以读取dll/exe中metadata，使用metadata创建对象。
- **Emit**：一种反射技术，可以动态创建dll/exe。
- **反编译工具**：ILSpy可以反编译dll/exe，查看对应的C#/IL代码。

# 2. 反射创建对象
## 2.1 动态读取dll
- **LoadFrom**：dll全名称，需要后缀
- **LoadFile**：全路径，需要dll后缀
- **Load**：dll名称不需要后缀

```csharp
//1、动态读取dll的三种方式
//（1）LoadFrom：dll全名称，需要后缀                        
Assembly assembly = Assembly.LoadFrom("Business.DB.SqlServer.dll");
//（2）LoadFile：全路径，需要dll后缀
//Assembly assembly1 = Assembly.LoadFile(@"dll文件全路径");
//（3）Load：dll名称 不需要后缀
//Assembly assembly2 = Assembly.Load("Business.DB.SqlServer");
```

## 2.2 获取类型
```csharp
//2、获取某一个具体的类型，参数需要是类的全名称
Type type1 = assembly.GetType("Business.DB.SqlServer.SqlServerHelper");
```

## 2.3 创建对象
- 直接传类型
- 重载方法，传dll的全名称
- 返回值是object类型，不能直接调用方法

```csharp
//3、创建对象
//（1）直接传类型
object? oInstance = Activator.CreateInstance(type1);
//（2）重载方法，传dll的全名称
//object? oInstanc1= Activator.CreateInstance("Business.DB.SqlServer.dll", "Business.DB.SqlServer.SqlServerHelper");
//a.oInstance.Query();//报错了：因为oInstance当做是一个object类型，object类型是没有Query方法；C#语言是一种强类型语言；编译时决定你是什么类型,以左边为准；不能调用是因为编译器不允许；实际类型一定是SqlServerHelper；
//b.如果使用dynamic 作为类型的声明，在调用的时候，没有限制；
//c.dynamic :动态类型：不是编译时决定类型，避开编译器的检查；运行时决定是什么类型
//d.dynamic dInstance = Activator.CreateInstance(type);
//e.dInstance.Query();
//f.dInstance.Get(); //报错了--因为SqlServerHelper没有Get方法
```

## 2.4 类型转换
```csharp
//4、类型转换
// SqlServerHelper helper = (SqlServerHelper)oInstance; //不建议这样转换--如果真实类型不一致--会报报错； 
IDBHelper helper = oInstance as IDBHelper;//如果类型一直，就转换，如果不一致；就返回null
```
## 2.5 调用方法
```csharp
//5、调用方法
helper.Query();
```

# 3. 反射创建对象封装
问题：反射创建对象代码很多。

- 其实除了dll的名称和类的全名称都是一样的代码，可以封装反射创建对象帮助类。
- 传入的参数都是字符串，可做成配置项，提高代码扩展性和灵活性，可以做到不修改代码而改变程序的功能。

## 3.1 反射创建对象封装
1. 创建SqlServerHelper的时候，没有依赖SqlServerHelper
2. 依赖的是两个字符串Business.DB.SqlServer.dll + Business.DB.SqlServer.SqlServerHelper，从配置文件读取。
3. 去掉个对细节的依赖的：依赖于抽象，不再依赖于细节；依赖倒置原则； 增强代码的稳定性；

```csharp
using Business.DB.Interface;
using Microsoft.Extensions.Configuration;
using System;
using System.Reflection;

namespace MyReflecttion
{
    public class SimpleFactory
    {
        //创建SqlServerHelper的时候，没有依赖SqlServerHelper
        //依赖的是两个字符串Business.DB.SqlServer.dll + Business.DB.SqlServer.SqlServerHelper
        //去掉个对细节的依赖的：依赖于抽象，不再依赖于细节；依赖倒置原则； 增强代码的稳定性；
        public static IDBHelper CreateInstance()
        {  
            string ReflictionConfig = CustomConfigManager.GetConfig("ReflictionConfig"); 
            //Business.DB.SqlServer.SqlServerHelper,Business.DB.SqlServer.dll 
            string typeName = ReflictionConfig.Split(',')[0];
            string dllName = ReflictionConfig.Split(',')[1];

            //Assembly assembly = Assembly.LoadFrom("Business.DB.SqlServer.dll"); 
            //Type type = assembly.GetType("Business.DB.SqlServer.SqlServerHelper");

            Assembly assembly = Assembly.LoadFrom(dllName); 
            Type type = assembly.GetType(typeName);

            object? oInstance = Activator.CreateInstance(type);
            IDBHelper helper = oInstance as IDBHelper; 
            return helper; 
        }
    }

    public static class CustomConfigManager
    {
        //Core 读取配置文件：appsettings
        //1.Microsoft.Extensions.Configuration；
        //2.Microsoft.Extensions.Configuration.Json 
        public static string GetConfig(string key)
        {
            var builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");  //默认读取  当前运行目录
            IConfigurationRoot configuration = builder.Build();
            string configValue = configuration.GetSection(key).Value;
            return configValue;
        }
    }
}
```

## 3.2 配置文件
```csharp
"ReflictionConfig": "Business.DB.Orcale.OrcaleHelper,Business.DB.Orcale.dll"
```

## 3.3 程序调用
如果公司来了一个新的技术经理：要将SqlServer换成MySql
1. 传统方式，必须要修改代码，然后必须要重新编译发布，步骤很多
2. 反射实现：断开了对普通类的依赖；依赖于配置文件+接口（抽象），做到了程序的可配置
3. 反射实现的步骤：按照接口约定实现一个Mysql帮助类库，Copy dll 文件到执行目录下，修改配置文件

```csharp
IDBHelper helper1 = SimpleFactory.CreateInstance();
helper1.Query();
```

# 4. 反射创建对象之破环单例
除了反射之外，没有其他的方法来调用私有化构造函数的；私有化的方法就只能从内部访问；元数据中只要有的，反射都可以给找出来； 完全不用关注权限问题；

## 4.1 单例类代码
```csharp
using System;

namespace MyReflecttion
{
    /// <summary>
    /// 单例模式：类，能保证在整个进程中只有一个实例
    /// </summary>
    public sealed class Singleton
    {
        private static Singleton _Singleton = null;
        /// <summary>
        /// 创建对象的时候执行
        /// </summary>
        private Singleton()
        {
            Console.WriteLine("Singleton被构造");
        }

        /// <summary>
        /// 被CLR 调用 整个进程中 执行且只执行一次
        /// </summary>
        static Singleton()
        {
            _Singleton = new Singleton();
        }

        public static Singleton GetInstance()
        {
            return _Singleton;
        }
    }
}
```

## 4.2 传统手艺创建单例类对象
```csharp
//1、传统手艺创建单例类对象
Console.WriteLine("********************传统单例***************************");
Singleton singleton1 = Singleton.GetInstance();
Singleton singleton2 = Singleton.GetInstance();
Singleton singleton3 = Singleton.GetInstance();
Singleton singleton4 = Singleton.GetInstance();
Console.WriteLine(object.ReferenceEquals(singleton1, singleton2));
Console.WriteLine(object.ReferenceEquals(singleton2, singleton3));
Console.WriteLine(object.ReferenceEquals(singleton1, singleton4));
Console.WriteLine("********************传统单例***************************");
```

## 4.3 反射方式创建单例类对象
```csharp
//2、反射方式创建单例类对象
Console.WriteLine("********************反射单例***************************");
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
Type type = assembly.GetType("MyReflecttion.Singleton");
Singleton singleton1 = (Singleton)Activator.CreateInstance(type, true);
Singleton singleton2 = (Singleton)Activator.CreateInstance(type, true);
Singleton singleton3 = (Singleton)Activator.CreateInstance(type, true);
Singleton singleton4 = (Singleton)Activator.CreateInstance(type, true);
Console.WriteLine(object.ReferenceEquals(singleton1, singleton2));
Console.WriteLine(object.ReferenceEquals(singleton2, singleton3));
Console.WriteLine(object.ReferenceEquals(singleton1, singleton4));
Console.WriteLine("********************反射单例***************************");
```

## 4.4 运行结果
```textplain
********************传统单例***************************
Singleton被构造
True
True
True
********************传统单例***************************
********************反射单例***************************
Singleton被构造
Singleton被构造
Singleton被构造
Singleton被构造
False
False
False
********************反射单例***************************
```

# 5. 反射创建对象详解
## 5.1 创建测试类
**普通类重载方法**
```csharp
using System;

namespace MyReflecttion
{
    /// <summary>
    /// 反射测试类
    /// </summary>
    public class ReflectionTest
    {
        #region Actor
        /// <summary>
        /// 无参构造函数
        /// </summary>
        public ReflectionTest()
        {
            Console.WriteLine($"这里是{this.GetType()} 无参数构造函数");
        }

        /// <summary>
        /// 带参数构造函数
        /// </summary>
        /// <param name="name"></param>
        public ReflectionTest(string name)
        {
            Console.WriteLine($"这里是{this.GetType()} 有参数构造函数");
        }

        public ReflectionTest(int id)
        {
            Console.WriteLine($"这里是{this.GetType()} 有参数构造函数");
        }

        public ReflectionTest(int id, string name)
        {
            Console.WriteLine($"这里是{this.GetType()} 有参数构造函数");
        }

        public ReflectionTest(string name,int id )
        {
            Console.WriteLine($"这里是{this.GetType()} 有参数构造函数");
        }
        #endregion

        #region Method
        /// <summary>
        /// 无参方法
        /// </summary>
        public void Show1()
        {
            Console.WriteLine($"这里是{this.GetType()}的Show1" );
        }

        /// <summary>
        /// 有参数方法
        /// </summary>
        /// <param name="id"></param>
        public void Show2(int id)
        {

            Console.WriteLine($"这里是{this.GetType()}的Show2");
        }

        /// <summary>
        /// 重载方法之一
        /// </summary>
        /// <param name="id"></param>
        /// <param name="name"></param>
        public void Show3(int id, string name)
        {
            Console.WriteLine($"这里是{this.GetType()}的Show3");
        }

        /// <summary>
        /// 重载方法之二
        /// </summary>
        /// <param name="name"></param>
        /// <param name="id"></param>
        public void Show3(string name, int id)
        {
            Console.WriteLine($"这里是{this.GetType()}的Show3_2");
        }

        /// <summary>
        /// 重载方法之三
        /// </summary>
        /// <param name="id"></param>
        public void Show3(int id)
        {

            Console.WriteLine($"这里是{this.GetType()}的Show3_3");
        }

        /// <summary>
        /// 重载方法之四
        /// </summary>
        /// <param name="name"></param>
        public void Show3(string name)
        {

            Console.WriteLine($"这里是{this.GetType()}的Show3_4");
        }

        /// <summary>
        /// 重载方法之五
        /// </summary>
        public void Show3()
        {
            Console.WriteLine($"这里是{this.GetType()}的Show3_1");
        }

        /// <summary>
        /// 私有方法
        /// </summary>
        /// <param name="name"></param>
        private void Show4(string name)  //肯定是可以的
        {
            Console.WriteLine($"这里是{this.GetType()}的Show4");
        }
        /// <summary>
        /// 静态方法
        /// </summary>
        /// <param name="name"></param>
        public static void Show5(string name)
        {
            Console.WriteLine($"这里是{typeof(ReflectionTest)}的Show5");
        }
        #endregion
    }
}
```

**泛型方法泛型类**
```csharp
using System;

namespace MyReflecttion
{

    public class GenericMethod
    {
        public void Show<T, W, X>(T t, W w, X x)
        {
            Console.WriteLine($"t.type={t.GetType().Name},w.type={ w.GetType().Name},x.type={x.GetType().Name}");
        }
    }


    public class GenericClass<T, W, X>
    {
        public void Show(T t, W w, X x)
        {
            Console.WriteLine($"t.type={t.GetType().Name},w.type={w.GetType().Name},x.type={x.GetType().Name}");
        }
    }
     
    public class GenericDouble<T>
    {
        public void Show<W, X>(T t, W w, X x)
        {
            Console.WriteLine($"t.type={t.GetType().Name},w.type={w.GetType().Name},x.type={x.GetType().Name}");
        }
    }
}
```

## 5.2 创建对象
### 5.2.1 调用无参数构造函数的
```csharp
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
Type type = assembly.GetType("MyReflecttion.ReflectionTest");
object noParaObject = Activator.CreateInstance(type);
```

### 5.2.2 调用有参数构造函数的
需要传递一个object类型的数组作为参数，参数按照从昨往右严格匹配，如果没有匹配的报异常
```csharp
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
Type type = assembly.GetType("MyReflecttion.ReflectionTest");
object paraObject = Activator.CreateInstance(type, new object[] { 123 });
object paraObject1 = Activator.CreateInstance(type, new object[] { "三三" });
object paraObject2 = Activator.CreateInstance(type, new object[] { 234, "四四" });
object paraObject3 = Activator.CreateInstance(type, new object[] { "五五", 456 });
```

# 6. 反射调用方法详解
获取方法MethodInfo，执行MethodInfo 的Invoke方法，传递方法所在的类的实例对象+参数

## 6.1 调用无参数的方法
```csharp
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
Type type = assembly.GetType("MyReflecttion.ReflectionTest");
object oInstance = Activator.CreateInstance(type);
MethodInfo show1 = type.GetMethod("Show1");
show1.Invoke(oInstance, new object[] { });
show1.Invoke(oInstance, new object[0]);
show1.Invoke(oInstance, null);
```

## 6.2 调用有参数的方法
需要通过方法参数类型类区别方法，传递参数，严格匹配参数类型
```csharp
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
Type type = assembly.GetType("MyReflecttion.ReflectionTest");
object oInstance = Activator.CreateInstance(type);
MethodInfo show2 = type.GetMethod("Show2");
show2.Invoke(oInstance, new object[] { 123 });
MethodInfo show31 = type.GetMethod("Show3", new Type[] { typeof(string), typeof(int) });
show31.Invoke(oInstance, new object[] { "一一一", 234 });
MethodInfo show32 = type.GetMethod("Show3", new Type[] { typeof(int) });
show32.Invoke(oInstance, new object[] { 345 });
MethodInfo show33 = type.GetMethod("Show3", new Type[] { typeof(string) });
show33.Invoke(oInstance, new object[] { "二二二" });
MethodInfo show34 = type.GetMethod("Show3", new Type[0]);
show34.Invoke(oInstance, null);
```

## 6.3 调用私有方法
在获取方法的时候，加上参数`BindingFlags.NonPublic | BindingFlags.Instance`
```csharp
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
Type type = assembly.GetType("MyReflecttion.ReflectionTest");
object oInstance = Activator.CreateInstance(type);
MethodInfo show4 = type.GetMethod("Show4", BindingFlags.NonPublic | BindingFlags.Instance);
show4.Invoke(oInstance, new object[] { "String" });
```

## 6.4 调用静态方法
不需要创建对象也可以调用
```csharp
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
Type type = assembly.GetType("MyReflecttion.ReflectionTest");
MethodInfo show5 = type.GetMethod("Show5");
show5.Invoke(null, new object[] { "String" });
```

## 6.5 调用普通类的泛型方法
获取到泛型方法后需要先确定类型
```csharp
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
Type type = assembly.GetType("MyReflecttion.GenericMethod");
object oInstance = Activator.CreateInstance(type);
MethodInfo show = type.GetMethod("Show");
//获取到泛型方法后需要先确定类型
MethodInfo genericshow = show.MakeGenericMethod(new Type[] { typeof(int), typeof(string), typeof(DateTime) });
genericshow.Invoke(oInstance, new object[] { 123, "三三三", DateTime.Now });
```

## 6.6 调用泛型类的普通方法
```csharp
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
//泛型类的类型需要在类后面加占位符
Type type = assembly.GetType("MyReflecttion.GenericClass`3");
//泛型类获取到类型后需要先确定类型
Type generType = type.MakeGenericType(new Type[] { typeof(int), typeof(string), typeof(DateTime) });
object oInstance = Activator.CreateInstance(generType);
MethodInfo show = generType.GetMethod("Show");
show.Invoke(oInstance, new object[] { 123, "四四四", DateTime.Now });
```

## 6.7 调用泛型类的泛型方法
```csharp
Assembly assembly = Assembly.LoadFrom("MyReflecttion.dll");
//泛型类的类型需要在类后面加占位符
Type type = assembly.GetType("MyReflecttion.GenericDouble`1");
//泛型类获取到类型后需要先确定类型
Type generType = type.MakeGenericType(new Type[] { typeof(int) });
object oInstance = Activator.CreateInstance(generType);
MethodInfo show = generType.GetMethod("Show");
//获取到泛型方法后需要先确定类型
MethodInfo genericMethod = show.MakeGenericMethod(new Type[] { typeof(string), typeof(DateTime) });
genericMethod.Invoke(oInstance, new object[] { 123, "五五五", DateTime.Now });
```

# 7. 反射操作属性字段
- 普通方法调用属性字段简单快捷，反射操作麻烦点。
- 类增加一个字段呢，普通方法调用需要修改代码，重新编译发布，代码不稳定，反射赋值没啥优势，反射取值不需要修改代码，代码就更加稳定。
- `type.GetProperties()`获取属性，`type.GetFields()`获取字段。

## 7.1 创建测试类
```csharp
using System;

namespace Business.DB.Model
{
    /// <summary>
    /// 实体---属性是不能保存数据，只有字段才能保存数据
    /// </summary>
    public class People
    {
        public People()
        {
            Console.WriteLine("{0}被创建", this.GetType().FullName);
        }
         
        public int Id { get; set; }//带有Get Set 方法的叫做属性

        public string Name { get; set; }

        public int Age { get; set; }
         
        public string Description;//字段
    }
}
```

## 7.2 传统方式赋值取值
```csharp
//传统手艺赋值取值
Console.WriteLine("***********传统手艺赋值取值*************");
People people = new People();
people.Id = 134;
people.Name = "WWWW";
people.Age = 25;
people.Description = "XXXXX";
Console.WriteLine($"people.Id={people.Id}");
Console.WriteLine($"people.Name={people.Name}");
Console.WriteLine($"people.Age={people.Age}");
Console.WriteLine($"people.Description={people.Description}");
```

## 7.3 反射方式赋值取值
```csharp
//反射方式赋值取值
Console.WriteLine("***********反射方式赋值取值*************");
Type type = typeof(People);
object pObject = Activator.CreateInstance(type);
foreach (var prop in type.GetProperties())
{
    if (prop.Name.Equals("Id"))
    {
        prop.SetValue(pObject, 134);
    }
    else if (prop.Name.Equals("Name"))
    {
        prop.SetValue(pObject, "WWWW");
    }
    else if (prop.Name.Equals("Age"))
    {
        prop.SetValue(pObject, 25);
    }                            
}                        
foreach (var prop in type.GetProperties())
{
    Console.WriteLine($"people.{prop.Name}={prop.GetValue(pObject)}");
}
```

## 7.4 运行结果
```textplain
***********传统手艺赋值取值*************
Business.DB.Model.People被创建
people.Id=134
people.Name=WWWW
people.Age=25
people.Description=XXXX
***********反射方式赋值取值*************
Business.DB.Model.People被创建
people.Id=134
people.Name=WWWW
people.Age=25
```

# 8. 反射的局限/性能问题
## 8.1 性能对比代码实现
```csharp
using Business.DB.Interface;
using Business.DB.SqlServer;
using System;
using System.Diagnostics;
using System.Reflection;

namespace MyReflecttion
{
    public class Monitor
    {
        public static void Show()
        {
            Console.WriteLine("*******************Monitor*******************");
            long commonTime = 0;
            long reflectionTime = 0;
            {
                Stopwatch watch = new Stopwatch();
                watch.Start();
                for (int i = 0; i < 1000_000; i++) //1000000000
                {
                    IDBHelper iDBHelper = new SqlServerHelper();
                    iDBHelper.Query();
                }
                watch.Stop();
                commonTime = watch.ElapsedMilliseconds;
            }
            {
                Stopwatch watch = new Stopwatch();
                watch.Start();
                //优化代码，加载dll放到循环外面
                Assembly assembly = Assembly.Load("Business.DB.SqlServer");//1 动态加载
                Type dbHelperType = assembly.GetType("Business.DB.SqlServer.SqlServerHelper");//2 获取类型
                for (int i = 0; i < 1000_000; i++)
                {
                    //创建对象+方法调用
                    object oDBHelper = Activator.CreateInstance(dbHelperType);//3 创建对象
                    IDBHelper dbHelper = (IDBHelper)oDBHelper;//4 接口强制转换
                    dbHelper.Query();//5 方法调用
                }
                watch.Stop();
                reflectionTime = watch.ElapsedMilliseconds;
            }

            Console.WriteLine($"commonTime={commonTime} reflectionTime={reflectionTime}");
        }
    }
}
```

## 8.2 运行结果
- 测试用例：普通方式循环100000次，创建对象+方法调用：16毫秒
    反射方式循环100000次，加载dll+创建对象+方法调用：6300毫秒
- 加载dll放到循环外面，创建对象+方法调用放循环里面，泛型方法：57毫秒

```textplain
*******************Monitor*******************
commonTime=16 reflectionTime=57
```

## 8.3 使用反射的建议
反射确实有性能问题，但是差别没有那么大，在需要的地方可以放心使用

# 9. 反射实现ORM框架
ORM：对象关系映射，通过对象查询数据库数据

## 9.1 创建测试类，继承基类
```csharp
using System;

namespace Business.DB.Model
{
    public class SysCompany : BaseModel
    { 
        public string Name { get; set; }
        public DateTime CreateTime { get; set; }
        public int CreatorId { get; set; }
        public int? LastModifierId { get; set; }
        public DateTime? LastModifyTime { get; set; }
    }
}
```

```csharp
namespace Business.DB.Model
{
    /// <summary>
    /// 数据库basemodel
    /// </summary>
    public class BaseModel
    {
        public int Id { set; get; }
    }
}
```

## 9.2 使用泛型方法来兼容不同的对象查询

## 9.3 使用反射获取字段

## 9.4 使用反射来对结果赋值
```csharp
using Business.DB.Interface;
using Business.DB.Model;
using System;
using System.Data.SqlClient;

namespace Business.DB.SqlServer
{
    public class SqlServerHelper : IDBHelper
    {

        //Nuget:System.Data.SqlClient

        private string ConnectionString = "Data Source=XXX; Database=YYY; User ID=sa; Password=ZZZ; MultipleActiveResultSets=True";

        public SqlServerHelper()
        {
           //Console.WriteLine($"{this.GetType().Name}被构造");
        }
        

        public void Query()
        {
            //Console.WriteLine($"{this.GetType().Name}.Query");
        }

        /// <summary> 
        /// 泛型方法适配查询不同对象数据
        /// </summary>
        public T Find<T>(int id) where T : BaseModel
        {
            //（1）反射创建对象
            Type type = typeof(T);
            object oReulst = Activator.CreateInstance(type);

            //（2）连接数据库,数据库链接字符串ConnectionString 
            using (SqlConnection connection = new SqlConnection(ConnectionString))
            {
                //（3）准备SqlConnection，使用数据库链接字符串
                connection.Open();
                
                //（4）准备sql，通过泛型缓存缓存sql
                string sql = ConstantSqlString<T>.GetFindSql(id);
                //（5）准备SqlCommand
                //（6）通过SqlCommand对象执行Sql语句
                SqlCommand sqlCommand = new SqlCommand(sql, connection);
                //（7）开始获取数据
                SqlDataReader reader = sqlCommand.ExecuteReader();
                if (reader.Read())
                {
                    foreach (var prop in type.GetProperties())
                    {
                        //（8）反射赋值
                        prop.SetValue(oReulst, reader[prop.Name] is DBNull ? null : reader[prop.Name]);
                    }
                }
            }
            return (T)oReulst;
        }
    }
}
```

## 9.5 使用泛型缓存来缓存sql
```csharp
using System;
using System.Linq;

namespace Business.DB.SqlServer
{
    public class ConstantSqlString<T>
    {
        private static string FindSql = null;

        static ConstantSqlString()
        {
            Type type = typeof(T);
            FindSql = $"Select {string.Join(',', type.GetProperties().Select(c => $"[{c.Name}]").ToList())} from {type.Name} where id=";
        }

        /// <summary>
        /// GetSql语句
        /// </summary>
        /// <param name="id"></param>
        /// <returns></returns>
        public static string GetFindSql(int id)
        {
            return $"{FindSql}{id}";
        }
    }
}
```

## 9.6 反射多种应用场景
- **IOC容器**：反射+ 配置文件+ 工厂
- **MVC框架**：反射调用类型方法
- **ORM**：反射+泛型+Ado.Net
- **AOP**：在方法的前面后面添加处理内容

# 10. 反射的Emit的技术
- 在运行时去动态的生成dll、exe包括dll内部的方法、属性、字段等内容
- 偏向于底层，学习成本比较高 除非是非常复杂的业务逻辑，一般情况，用的比较少；

## 10.1 程序集内部结构
![20240207110207](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240207110207.png)

## 10.2 动态创建程序过程
一般的，使用反射发出（reflection emit）可能会是这样子的步骤
1. 创建一个新的程序集
    程序集是动态的存在于内存中或把它们保存到磁盘上
2. 在程序集内部，创建一个模块
3. 在模块内部，创建一个类型
4. 给类型添加属性和方法
5. 产生属性和方法内部的代码

## 10.3 参照类
```csharp
public class MyDynamicType
{
    /// <summary>
    /// 字段
    /// </summary>
    public int NumberField = 0;       

    /// <summary>
    /// int类型参数构造函数
    /// </summary>
    /// <param name="numberField"></param>
    public MyDynamicType(int numberField)
    {
        this.NumberField = numberField;
    }

    /// <summary>
    /// 无参数方法
    /// </summary>
    public void ConsoleMethod()
    {
        Console.WriteLine("方法输出的内容");
    }

    /// <summary>
    /// 有参数方法
    /// </summary>
    /// <param name="para"></param>
    /// <returns></returns>
    public int MyMethod(int para)
    {
        return 2 * para;
    }
}
```

## 10.4 emit实现参照类
```csharp
public class ReflectionEmit
{
    //一般的，使用反射发出（reflection emit）可能会是这样子的步骤
    //（1）创建一个新的程序集
    //程序集是动态的存在于内存中或把它们保存到磁盘上
    //（2）在程序集内部，创建一个模块
    //（3）在模块内部，创建一个类型
    //（4）给类型添加属性和方法
    //（5）产生属性和方法内部的代码
    public static void Show()
    {

        //（1）创建一个新的程序集
        AssemblyBuilder assemblyBuilder = AssemblyBuilder.DefineDynamicAssembly(new AssemblyName("DynamicAssemblyExample"), AssemblyBuilderAccess.RunAndCollect);

        //（2）在程序集内部，创建一个模块
        ModuleBuilder modulebuilder = assemblyBuilder.DefineDynamicModule("MyModal");

        //（3）在模块内部，创建一个类型
        TypeBuilder typebuilder = modulebuilder.DefineType("MyDynamicType", TypeAttributes.Public);

        //（4）给类型添加属性和方法
        // 在Type中生成字段
        FieldBuilder fieldBuilder = typebuilder.DefineField("NumberField", typeof(int), FieldAttributes.Public);

        #region 定义一个接受整数参数的构造函数
        //（4）给类型添加属性和方法
        Type[] parameterTypes = { typeof(int) };
        ConstructorBuilder ctor1 = typebuilder.DefineConstructor(MethodAttributes.Public, CallingConventions.Standard, parameterTypes);

        //（5）产生属性和方法内部的代码
        //中间语言的生成者
        ILGenerator ctor1IL = ctor1.GetILGenerator();
        //对于构造函数，参数0是对新 
        //实例。在调用base之前将其推到堆栈上 
        //类构造函数。指定的默认构造函数
        //类型（Type.EmptyTypes）到GetConstructor。
        ctor1IL.Emit(OpCodes.Ldarg_0);
        ctor1IL.Emit(OpCodes.Call, typeof(object).GetConstructor(Type.EmptyTypes));
        //在推送参数之前，先将实例推送到堆栈上 
        //将被分配给私有字段m\u编号。 
        ctor1IL.Emit(OpCodes.Ldarg_0);
        ctor1IL.Emit(OpCodes.Ldarg_1);
        ctor1IL.Emit(OpCodes.Stfld, fieldBuilder);
        ctor1IL.Emit(OpCodes.Ret);//写IL最后一定要Ret

        //测试代码
        {
            //Type type1 = typebuilder.CreateType();
            //object oInstacne = Activator.CreateInstance(type1, new object[] { 123456 });
            //FieldInfo fieldInfo = type1.GetField("NumberField");
            //object numberFieldResult = fieldInfo.GetValue(oInstacne);
        }
        #endregion

        #region 定义一个无参数方法
        //（4）给类型添加属性和方法
        MethodBuilder consoleMethod = typebuilder.DefineMethod("ConsoleMethod", MethodAttributes.Public | MethodAttributes.Static, null, null);

        //（5）产生属性和方法内部的代码
        ILGenerator consoleMethodIL = consoleMethod.GetILGenerator();
        consoleMethodIL.Emit(OpCodes.Ldstr, "方法输出的内容");
        consoleMethodIL.Emit(OpCodes.Call, typeof(Console).GetMethod("WriteLine", new Type[] { typeof(string) }));
        consoleMethodIL.Emit(OpCodes.Ret); 

        //测试代码
        {
            //Type type1 = typebuilder.CreateType();
            //object oInstacne = Activator.CreateInstance(type1, new object[] { 123456 });
            //MethodInfo myMethod = type1.GetMethod("ConsoleMethod");
            //object oResult = myMethod.Invoke(oInstacne, null);
        }
        #endregion

        #region 定义一个有参数方法
        //（4）给类型添加属性和方法
        MethodBuilder AddMethod = typebuilder.DefineMethod("MyMethod", MethodAttributes.Public | MethodAttributes.Static, typeof(int), new Type[] { typeof(int), typeof(int) });

        //（5）产生属性和方法内部的代码
        ILGenerator AddMethodIL = AddMethod.GetILGenerator();
        AddMethodIL.Emit(OpCodes.Ldarg_0);
        AddMethodIL.Emit(OpCodes.Ldarg_1);
        AddMethodIL.Emit(OpCodes.Add_Ovf_Un);
        AddMethodIL.Emit(OpCodes.Ret);

        //测试代码
        {                
            //Type type1 = typebuilder.CreateType();
            //object oInstacne = Activator.CreateInstance(type1, new object[] { 123456 });
            //MethodInfo myMethod = type1.GetMethod("MyMethod");
            //object oResult = myMethod.Invoke(oInstacne, new object[] { 12, 34 });
        }
        #endregion
    }
}
```
