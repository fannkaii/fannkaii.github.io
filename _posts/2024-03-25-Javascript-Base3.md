---
layout: post
title: "JavaScript基础三、数据类型"
author: "Kai"
header-style: text
tags:
  - Javascript
---

转自：https://juejin.cn/post/7207314488243208249

# 1. 概念和作用
## 1.1 数据分类
- 计算机世界中的万事万物都是数据。
- 计算机程序可以处理大量的数据，为什么要给数据分类？
  - 更加充分和高效的利用内存
  - 也更加方便程序员的使用数据

## 1.2 JS 数据分为两大类
- 基本数据类型：又叫做**简单数据类型**或者**值类型**，在存储时变量中存储的是值本身，因此叫做值类型。**string**，**number**，**boolean**，**undefined**，**null**。
- 引用数据类型：又叫做**复杂数据类型**，在存储时变量中存储的仅仅是地址（引用），因此叫做引用数据类型。**通过 new 关键字创建的对象（系统对象、自定义对象），如 Object、Array、Date等。**
    ![20241005175533](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005175533.png)

# 2. 基本数据类型
## 2.1 数字类型（Number）
- 即我们数学中学习到的数字，可以是整数、小数、正数、负数。
- JavaScript 中的正数、负数、小数等 统一称为 数字类型。
- 注意事项
  - **JS 是弱数据类型，变量到底属于那种类型，只有赋值之后，我们才能确认**
  - **Java是强数据类型 例如 `int a = 3` 必须是整数**
- 数字可以有很多操作，经常和**算术运算符**一起。**算术运算符**主要包括：
- **+**：求和
- **-**：求差
- <strong>*</strong>：求积
- **/**：求商
- **%**：取模（取余数），开发中经常作为某个数字是否被整除
- 同时使用多个运算符编写程序时，会按着某种顺序先后执行，我们称为优先级。JavaScript中 优先级越高越先被执行，**优先级相同时从左向右执行**。
  - 乘、除、取余优先级相同
  - 加、减优先级相同
  - 乘、除、取余优先级大于加、减
  - 使用 () 可以提升优先级
  - 总结： 先乘除后加减，有括号先算括号里面的
- **NaN** 代表一个计算错误。它是一个不正确的或者一个未定义的数学操作所得到的结果，**NaN** 是粘性的。任何对 **NaN** 的操作都会返回 **NaN**。

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>基本数据类型-数字型</title>
    </head>

    <body>
        <script>
            // js 弱数据类型的语言 只有当我们赋值了，才知道是什么数据类型
            // let num = 'pink'
            // let num = 10.11
            // console.log(num)
            // java 强数据类型的语言    int num = 10
            console.log(1 + 1) // 2
            console.log(1 * 1) // 1
            console.log(1 / 1) // 1
            console.log(4 % 2) // 0
            console.log(5 % 3) // 2 
            console.log(3 % 5) // 3 

            //错误的计算返回NaN
            console.log('pink老师' - 2) //NaN
            console.log(NaN - 2) //NaN
            console.log(NaN + 2) //NaN
            console.log(NaN / 2) //NaN
            console.log(NaN === NaN) //false
        </script>
    </body>

    </html>
    ```
    
    **计算圆面积案例：**
    ![20241005175738](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005175738.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>计算圆的面积</title>
    </head>

    <body>
        <script>
            // 1. 页面弹出输入框
            let r = prompt('请输入圆的半径：')
                // 2. 计算圆的面积（内部处理）
            let re = 3.14 * r * r
                // 3. 页面输出
            document.write(re)
        </script>
    </body>

    </html>
    ```

## 2.2 字符串类型（string）
- 字符串：通过单引号（`''`） 、双引号（`""`）或反引号(``) 包裹的数据都叫字符串，推荐使用单引号。
- 字符串嵌套：单引号/双引号可以互相嵌套，但是不以自已嵌套自已（口诀：外双内单，或者外单内双）
- 字符串转义：必要时可以使用转义符 `\`，输出单引号或双引号
- 字符串拼接：`+`运算符可以实现字符串的拼接。（口诀：数字相加，字符相连）
- 操作字符串：获取字符串字符可以用`str[下标]`，下标索引从0开始，**字符串是不可变的**，如果对字符串的某个索引赋值，不会有任何错误，也没有任何效果。
- 字符串转大小写：`str.toUpperCase`、`str.toLowerCase`
- 获取字符串的索引位置：`str1.indexOf(str2)`，找出str2在str1中的位置
- 截取字符串：`str.substring(start，end)` start开始，end结束，不包含end；`str.substring(start)` start开始到结束。

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>字符串类型</title>
    </head>

    <body>
        <script>
            // 字符串
            let uname = '小明' //使用单引号
            let gender = "男" //使用双引号
            let goods = `小米` //使用反引号
            let tel = '13681113456' //看上去是数字，但是引号包裹了就是字符事
            let str = '' //这种情况叫空字符串

            //字符串嵌套
            console.log('pink老师讲课非常有"基情"')
            console.log("pink老师讲课非常有'基情'")
            console.log('pink老师讲课非常有\'基情\'')

            // 字符串拼接
            // console.log(1 + 1)//2
            // console.log('pink' + '老师')//pink老师

            let age = 25
                // document.write('我今年' + 19)
                // document.write('我今年' + age)
            document.write('我今年' + age + '岁了')

            //获取字符串字符
            let s = 'Hello, world!'
            console.log(s[0]) // 'H'
            console.log(s[6]) // ' '
            console.log(s[7]) // 'w'
            console.log(s[12]) // '!'
            console.log(s[13]) // undefined 超出范围的索引不会报错，但一律返回undefined

            //字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果
            let s1 = 'Test'
            s1[0] = 'X'
            console.log(s1) // s仍然为'Test'

            //把一个字符串全部变为大写
            let s2 = 'Hello'
            console.log(s2.toUpperCase()) // 返回'HELLO'

            //把一个字符串全部变为小写
            let s3 = 'Hello'
            console.log(s3.toLowerCase()) // 返回'hello'

            //搜索指定字符串出现的位置
            let s4 = 'hello, world'
            console.log(s4.indexOf('world')) // 返回7
            console.log(s4.indexOf('World')) // 没有找到指定的子串，返回-1

            let s5 = 'hello, world'
            console.log(s5.substring(0, 5)) // 从索引0开始到5（不包括5），返回'hello'
            console.log(s5.substring(7)) // 从索引7开始到结束，返回'world'
        </script>
    </body>

    </html>
    ```

- 模板字符串
  - **场景**：用于拼接字符串和变量
  - **语法**：外面用反引号(``)，里面用`${变量名}`

    **案例如下：**
    ![20241005180355](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005180355.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>模板字符串</title>
    </head>

    <body>
        <script>
            // let age = 20
            // 模板字符串 外面用`` 里面 ${变量名}
            // document.write(`我今年${age}岁了`)
            
            //输入
            let uname = prompt('请输入您的姓名:')
            let age = prompt('请输入您的年龄:')
            // 输出
            document.write(`大家好，我叫${uname}， 今年${age}岁了`)
        </script>
    </body>

    </html>
    ```

## 2.3 布尔类型（boolean）
- 表示肯定或否定时在计算机中对应的是布尔类型数据。
- 它有两个固定的值 true 和 false，表示肯定的数据用 true（真），表示否定的数据用 false（假）。

## 2.4 未定义类型（undefined）
- 未定义是比较特殊的类型，只有一个值 **undefined**。
- 只声明变量，不赋值，变量的值为 **undefined**。
- **使用场景**：我们开发中经常声明一个变量，等待传送过来的数据。我们可以通过检测这个变量是不是**undefined**，就判断用户是否有数据传递过来。

## 2.5 null（空类型）
- JavaScript 中的 null 仅仅是一个代表“无”、“空”或“值未知”的特殊值。
- 把 null 作为尚未创建的对象。
- **使用场景**：将来有个变量里面存放的是一个对象，但是对象还没创建好，可以先给个null。
- **null 和 undefined 区别**：
  - undefined 表示没有赋值
  - null 表示赋值了，但是内容为空

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>其他三种类型</title>
    </head>

    <body>
        <script>
            // 1. true  false 是布尔型字面量  
            console.log(3 > 4)
            let isCool = false
            console.log(isCool)

            // 2. 未定义类型 弱数据类型 声明一个变量未赋值就是 undefined
            let num
            console.log(num)

            // 3. null 空的
            let obj = null
            console.log(obj)

            // undefined null 计算有区别
            console.log(undefined + 1) // NaN
            console.log(null + 1) // 1
        </script>
    </body>

    </html>
    ```

# 3. 引用数据类型-对象
## 3.1 对象是什么
- 对象（object）：JavaScript里的一种**数据类型**
- 可以理解为是一种**无序**的数据集合， 注意数组是有序的数据集合
- 用来详细的描述某个事物，比如人信息：
  - 如果用多个变量保存则比较散，用对象比较统一
  - 静态特征 (姓名, 年龄, 身高, 性别, 爱好) => 可以使用数字, 字符串, 数组, 布尔类型等表示
  - 动态行为 (点名, 唱, 跳, rap) => 使用函数表示

## 3.2 对象使用
- 对象声明：实际开发中，我们多用花括号。 **{} 是对象字面量**
    ![20241005180641](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005180641.png)
- 对象组成
  - 属性：信息或叫特征（名词）。 比如 手机尺寸、颜色、重量等…
  - 方法：功能或叫行为（动词）。 比如 手机打电话、发短信、玩游戏…

    ![20241005180714](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005180714.png)

- 对象属性
  - 属性都是成 对出现的，包括属性名和值，它们之间使用英文 : 分隔
  - 多个属性方法之间使用英文 , 分隔
  - 属性就是依附在对象上的变量（外面是变量，对象内是属性）
  - 属性名可以使用 `""` 或 `''`，**一般情况下省略**，除非名称遇到特殊符号如空格、中横线等
  - 判断属性或者方法是否在对象中，使用**in**，继承的方法或者属性也算，如果要判断一个属性是否是 对象自身拥有的，而不是继承得到的，可以用 `hasOwnProperty()` 方法

    ![20241005180804](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005180804.png)

- 对象本质是无序的数据集合, 操作数据无非就是 **增 删 改 查** 语法：
  - 属性-查：`对象名.属性`或者`对象名['属性']`（多词属性或 - 等属性，点操作就不能用了）
  - 属性-改：`对象名.属性 = 新值`
  - 属性-增：`对象名.新属性 = 新值`
  - 属性-删 （了解）：`delete 对象名.属性`
  - **改** 和 **增** 语法一样，判断标准就是对象有没有这个属性，**没有就是新增，有就是改**

    ![20241005180929](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005180929.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>对象的使用</title>
    </head>

    <body>
        <script>
            // 1.声明对象
            let pink = {
                uname: 'pink老师',
                age: 18,
                gender: '女'
            }
            console.log(pink.uname)
            console.log(typeof pink)

            // 改
            pink.gender = '男'
            console.log(pink)

            // 增
            pink.hobby = '足球'
            console.log(pink)

            // 删 (了解)
            delete pink.age
            console.log(pink)

            // 声明对象
            let obj = {
                    'goods-name': '小米10青春版',
                    num: '100012816024',
                    weight: '0.55kg',
                    address: '中国大陆'
                }
                //多词属性或 - 等属性，点操作就不能用了
                //console.log(obj.goods - name)
                //可以用查的另外一种方式,对象名['属性名']
            console.log(obj['goods-name'])

            //要检测对象是否拥有某一属性
            let xiaoming = {
                name: '小明',
                birth: 1990,
                school: 'No.1 Middle School',
                height: 1.70,
                weight: 65,
                score: null
            }
            console.log('name' in xiaoming) // true
            console.log('grade' in xiaoming) // false

            //object继承到的方法也存在
            console.log('toString' in xiaoming) // true

            //判断是否是自身拥有的属性或者方法
            console.log(xiaoming.hasOwnProperty('name')) // true
            console.log(xiaoming.hasOwnProperty('toString')) // false
        </script>
    </body>

    </html>
    ```

- 对象方法
  - 方法是由方法名和函数两部分构成，它们之间使用 `:` 分隔
  - 多个属性方法之间使用英文 `,` 分隔
  - 方法是依附在对象中的函数
  - 方法名可以使用 `""` 或 `''`，一般情况下省略，除非名称遇到特殊符号如空格、中横线等

    ![20241005181021](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005181021.png)

- 对象方法调用：可以使用 `.` 调用对象中函数，我称之为方法调用。
    ![20241005181053](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005181053.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>对象的方法</title>
    </head>

    <body>
    <script>
        let obj = {
        uname: '刘德华',
        // 方法
        song: function (x, y) {
            console.log(x + y)
        },
        dance: function () { }
        }
        // 方法调用 对象名.方法名
        obj.song(1, 2)
    </script>
    </body>

    </html>
    ```

## 3.3 内置对象-Math
- **内置对象**：JavaScript内部提供的对象，包含各种属性和方法给开发者调用
- **Math对象**：JavaScript提供的一个“数学”对象，提供了一系列做数学运算的方法
  - random：生成0-1之间的随机数（包含0不包括1）
  - ceil：向上取整
  - floor：向下取整
  - max：找最大数
  - min：找最小数
  - pow：幂运算
  - abs：绝对值

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>内置对象Math</title>
    </head>

    <body>
        <script>
            // 属性
            console.log(Math.PI)
                // 方法
                // ceil 天花板  向上取整
            console.log(Math.ceil(1.1)) // 2 
            console.log(Math.ceil(1.5)) // 2 
            console.log(Math.ceil(1.9)) // 2 
                // floor 地板  向下取整
            console.log(Math.floor(1.1)) // 1
            console.log(Math.floor(1.5)) // 1
            console.log(Math.floor(1.9)) // 1
            console.log(Math.floor('12px')) // NaN
                // 四舍五入 round
            console.log(Math.round(1.1)) // 1
            console.log(Math.round(1.49)) // 1
            console.log(Math.round(1.5)) // 2
            console.log(Math.round(1.9)) // 2
            console.log(Math.round(-1.1)) // -1 
            console.log(Math.round(-1.5)) // -1
            console.log(Math.round(-1.51)) // -2

            //最大值
            console.log(Math.max(1, 2, 3, 4, 5)) //5
                //最小值
            console.log(Math.min(1, 2, 3, 4, 5)) //1

            //绝对值
            console.log(Math.abs(-1)) //1
        </script>
    </body>

    </html>
    ```

- `Math.random()`：随机数函数， 返回一个0 - 1之间，并且包括0不包括1的随机小数 [0, 1）
  - 生成0-10的随机数：`Math.floor(Math.random() * (10 + 1))`
  - 生成5-10的随机数：`Math.floor(Math.random() * (5 + 1)) + 5`
  - 生成N-M之间的随机数：`Math.floor(Math.random() * (M - N + 1)) + N`

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>随机数函数</title>
    </head>

    <body>
    <script>
        // 左闭右开 取到0~1中间一个随机小数[0,1)
        console.log(Math.random())

        // 0~ 10 之间的整数
        console.log(Math.floor(Math.random() * 11))
        
        //随机取数组元素
        let arr = ['red', 'green', 'blue']
        let random = Math.floor(Math.random() * arr.length)
        console.log(arr[random])
        
        // 取到 N ~ M 的随机整数
        function getRandom(N, M) {
        return Math.floor(Math.random() * (M - N + 1)) + N
        }
        console.log(getRandom(4, 8))
    </script>
    </body>

    </html>
    ```

    **随机点名：**
    ![20241005181344](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005181344.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>随机点名</title>
    </head>

    <body>
    <script>
        let arr = ['赵云', '黄忠', '关羽', '张飞', '马超', '刘备', '曹操']
        // 1. 得到一个随机数， 作为数组的索引号， 这个随机数 0~6
        let random = Math.floor(Math.random() * arr.length)
        // 2. 页面输出数组里面的元素 
        document.write(arr[random])
        // 3. splice(起始位置(下标), 删除几个元素)
        arr.splice(random, 1)
        console.log(arr)
    </script>
    </body>

    </html>
    ```

## 3.4 内置对象-Date
- Date 对象
  - 作用：Date 对象用来表示日期和时间。
  - 注意：当前时间是浏览器从本机操作系统获取的时间，所以不一定准确，因为用户可以把当前时间设定为任何值。
  - Date 对象表示的时间总是按浏览器所在时区显示的，不过我们既可以显示本地时间，也可以显示调整后的UTC时间。
- 实例化
  - 获得当前时间：`const date=new Date()`
  - 获得指定时间：`const date=new Date('2008-8-8')`
- 常用方法
    ![20241005181527](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005181527.png)
- 时间戳
- **使用场景**：如果计算倒计时效果，前面方法无法直接计算，需要借助于时间戳完成
- **时间戳**：是指1970年01月01日00时00分00秒起至现在的毫秒数，它是一种特殊的计量时间的方式
- 常用计算方法
  - `将来的时间戳 - 现在的时间戳 = 剩余时间毫秒数`，比如 `将来时间戳2000ms - 现在时间戳 1000ms = 1000ms`
  - 剩余时间毫秒数 转换为 剩余时间的 年月日时分秒 就是 倒计时时间，1000ms 转换为就是 0小时0分1秒
- **三种方式获取时间戳**：
  - `date.getTime()`
  - `+new Date()`
  - `Date.now()`

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>内置对象Date</title>
    </head>

    <body>
        <script>
            // 实例化new 
            // 1. 得到当前时间 
            const date = new Date()
            console.log(date)
                // 2. 指定时间
            const date1 = new Date('2022-5-1 08:30:00')
            console.log(date1)

            //常用方法
            let date2 = new Date()
            console.log(date2) // Wed Jun 24 2015 19:49:22 GMT+0800 (CST)
            console.log(date2.getFullYear()) // 2023, 年份
            console.log(date2.getMonth()) // 2, 3月份，注意月份范围是0~11，5表示六月
            console.log(date2.getDate()) // 6, 表示6号
            console.log(date2.getDay()) // 1, 表示星期一
            console.log(date2.getHours()) // 13, 24小时制
            console.log(date2.getMinutes()) // 31, 分钟
            console.log(date2.getSeconds()) // 8, 秒
            console.log(date2.getMilliseconds()) // 414, 毫秒数
            console.log(date2.getTime()) // 1678080668414, 以number形式表示的时间戳

            //UTC时间
            let date3 = new Date()
            console.log(date3.toLocaleString()) //2023/3/6 13:31:08，显示的字符串与操作系统设定的格式有关
            console.log(date3.toUTCString()) // Mon, 06 Mar 2023 05:31:08 GMT，UTC时间，与本地时间相差8小时

            //时间格式化
            const date4 = new Date()
            let h = date4.getHours()
            let m = date4.getMinutes()
            let s = date4.getSeconds()
            h = h < 10 ? '0' + h : h
            m = m < 10 ? '0' + m : m
            s = s < 10 ? '0' + s : s
            console.log(`今天是: ${date4.getFullYear()}年${date4.getMonth() + 1}月${date4.getDate()}号 ${h}:${m}:${s}`)

            //时间戳
            // 1. getTime()
            const date5 = new Date()
            console.log(date5.getTime())
                // 2. +new Date()
            console.log(+new Date())
                // 3. Date.now()
            console.log(Date.now())
        </script>
    </body>

    </html>
    ```

## 3.5 内置对象-JSON
- JSON(JavaScript Object Notation, JS 对象标记)
  - 一种轻量级的数据交换格式，目前使用特别广泛。
  - 采用完全独立于编程语言的**文本格式**来存储和表示数据。
  - 简洁和清晰的层次结构，易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。
  - **JSON 是 JavaScript 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个字符串。**
- JSON和JavaScript对象互转：
  - JSON字符串转换为JavaScript 对象：`JSON.parse()`
  - JavaScript 对象转换为JSON字符串：`JSON.stringify()`

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>内置对象JSON</title>
    </head>

    <body>
        <script>
            //编写一个js的对象
            let user = {
                name: "张三",
                age: 17,
                sex: "男"
            };
            //将js对象转换成json字符串
            var str = JSON.stringify(user)
            console.log(str)//{"name":"张三","age":17,"sex":"男"}

            //将json字符串转换为js对象
            var user2 = JSON.parse(str)
            console.log(user2.age, user2.name, user2.sex)//17 '张三' '男'
        </script>
    </body>

    </html>
    ```

# 4. 数据内存分配
## 4.1 堆栈空间分配区别
- 栈（操作系统）：由操作系统自动分配释放存放函数的参数值、局部变量的值等。其操作方式类似于数据结构中的栈；**简单数据类型存放到栈里面**
- 堆（操作系统）：存储复杂类型(对象)，一般由程序员分配释放，若程序员不释放，由垃圾回收机制回收。**引用数据类型存放到堆里面**
    ![20241005181929](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005181929.png)

## 4.2 简单类型的内存分配
- 值类型（简单数据类型）： string ，number，boolean，undefined，null
- 值类型变量的数据直接存放在变量（栈空间）中
    ![20241005181950](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005181950.png)

## 4.3 复杂类型的内存分配
- 引用类型（复杂数据类型）：通过 new 关键字创建的对象（系统对象、自定义对象），如 Object、Array、Date等
- 引用类型变量（栈空间）里存放的是地址，真正的对象实例存放在堆空间中
    ![20241005182017](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005182017.png)

## 4.4 案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>数据类型存储</title>
</head>

<body>
  <script>
    let num1 = 10
    let num2 = num1
    num2 = 20
    console.log(num1) //10 值类型不变

    let obj1 = {
      age: 18
    }
    let obj2 = obj1
    // 修改属性
    obj2.age = 20
    console.log(obj1.age)//20 引用类型属性被修改
  </script>
</body>

</html>
```

# 5. 数据类型检测
## 5.1 控制台输出颜色
- 控制台语句经常用于测试结果来使用。
- 可以看出数字型和布尔型颜色为蓝色，字符串和undefined颜色为灰色
    ![20241005182058](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005182058.png)

## 5.2 通过 typeof关键字
- `typeof` 运算符可以返回被检测的数据类型。它支持两种语法形式：
- 作为运算符：`typeof x`（常用的写法）
- 函数形式：`typeof(x)`
- 换言之，有括号和没有括号，得到的结果是一样的，所以我们直接使用**运算符**的写法更简单。

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>检测数据类型</title>
    </head>

    <body>
        <script>
            let num = 10
            console.log(typeof num) //number
            let str = 'pink'
            console.log(typeof str) //string
            let str1 = '10'
            console.log(typeof str1) //string
            let flag = false
            console.log(typeof flag) //boolean
            let un
            console.log(typeof un) //undefined
            let obj = null
            console.log(typeof obj) //object
        </script>
    </body>

    </html>
    ```

# 6. 数据类型转换
## 6.1 类型转换作用
- JavaScript是弱数据类型，JavaScript也不知道变量到底属于那种数据类型，只有赋值了才清楚。
- 使用**表单、prompt**获取过来的数据**默认是字符串类型**的，此时就不能直接简单的进行加法运算。
- 此时需要转换变量的数据类型，通俗来说，就是**把一种数据类型的变量转换成我们需要的数据类型**。

## 6.2 隐式转换
- 某些运算符被执行时，系统内部自动将数据类型进行转换，这种转换称为隐式转换。
- **规则**：
  - `+`号两边只要有一个是字符串，都会把另外一个转成字符串
  - **除了`+`以外**的算术运算符 比如 `- * /` 等都会把数据转成数字类型
  - `null` 经过数字转换之后会变为 `0`
  - `undefined` 经过数字转换之后会变为 `NaN`
- **缺点**：转换类型不明确，靠经验才能总结
- **小技巧**：
  - **`+`号作为正号解析可以转换成数字型**
  - **任何数据和字符串相加结果都是字符串**

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>隐式转换</title>
    </head>

    <body>
        <script>
            console.log(11 + 11) //22
            console.log('11' + 11) //1111
            console.log(11 - 11) //0
            console.log('11' - 11) //0
            console.log(1 * 1) //1
            console.log('1' * 1) //1
            console.log(typeof '123') //string
            console.log(typeof + '123') //number
            console.log(+'11' + 11) //22
            console.log(null + 1) //1
            console.log(undefined + 1) //NaN
            console.log(NaN + 1) //NaN
        </script>
    </body>

    </html>
    ```

    **求和案例：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>求和案例</title>
    </head>

    <body>
        <script>
            // 1. 用户输入   prompt 得到是字符串类型 隐士转换为 数字型
            let num1 = +prompt('请输入第一个数字：')
            let num2 = +prompt('请输入第二个数字：')
            // 2. 输出
            alert(`两个数相加的和是:${num1 + num2}`)
        </script>
    </body>

    </html>
    ```

## 6.3 显式转换
- 为了避免因隐式转换带来的问题，通常根逻辑需要对数据进行显示转换。
- **转换为数字型**
  - `Number(数据)`：如果数据内容里有非数字，转换失败时结果为 **NaN（Not a Number）**即不是一个数字，**NaN**也是**number**类型的数据，代表非数字。
  - `parseInt(数据)`：只保留整数。
  - `parseFloat(数据)`：可以保留小数。
- **转换为字符型**
  - `String(数据)`
  - `变量.toString(进制)`
- **转换为布尔型**
  - `Boolean(内容)`
  - **`''`、0、undefined、null、false、NaN 转换为布尔值后都是false, 其余则为 true**

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>显示转换</title>
    </head>

    <body>
        <script>
            let str = '123'
            console.log(Number(str)) //123
            console.log(Number('pink')) //NaN

            console.log(parseInt('12px')) //12
            console.log(parseInt('12.34px')) //12
            console.log(parseInt('12.94px')) //12
            console.log(parseInt('abc12.94px')) //NaN

            console.log(parseFloat('12px')) //12
            console.log(parseFloat('12.34px')) //12.34
            console.log(parseFloat('12.94px')) //12.94
            console.log(parseFloat('abc12.94px')) //NaN
        </script>
    </body>

    </html>
    ```

    **转换为布尔型：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>转换为布尔型</title>
    </head>

    <body>
        <script>
            console.log(Boolean('pink')) //true
            console.log(Boolean('')) //false
            console.log(Boolean(0)) //false
            console.log(Boolean(90)) //true
            console.log(Boolean(-1)) //true
            console.log(Boolean(undefined)) //false
            console.log(Boolean(null)) //false
            console.log(Boolean(NaN)) //false
        </script>
    </body>

    </html>
    ```

## 6.4 常见错误
![20241005182804](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005182804.png)
分析：
- 因为prompt 获取过来的是字符型，所以会出现字符相加的问题
- prompt 如果出现相加记得要转为数字型，可以 利用 `+` 最简单

![20241005182832](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005182832.png)

# 7. 综合案例
![20241005182849](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005182849.png)
代码如下：
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>综合案例</title>
    <style>
        h2 {
            text-align: center;
        }
        
        table {
            /* 合并相邻边框 */
            border-collapse: collapse;
            height: 80px;
            margin: 0 auto;
            text-align: center;
        }
        
        th {
            padding: 5px 30px;
        }
        
        table,
        th,
        td {
            border: 1px solid #000;
        }
    </style>
</head>

<body>
    <h2>订单确认</h2>
    <script>
        // 1 用户输入
        let price = +prompt('请输入商品价格：')
        let num = +prompt('请输入商品数量：')
        let address = prompt('请输入收获地址：')
        // 2.计算总额
        let total = price * num
        // 3.页面打印渲染 模板字符串
        document.write(`
      <table>
          <tr>
            <th>商品名称</th>
            <th>商品价格</th>
            <th>商品数量</th>
            <th>总价</th>
            <th>收货地址</th>
          </tr>
          <tr>
            <td>小米青春版PLUS</td>
            <td>${price}元</td>
            <td>${num}</td>
            <td>${total}元</td>
            <td>${address}</td>
          </tr>
        </table>
    `)
    </script>
</body>

</html>
```
