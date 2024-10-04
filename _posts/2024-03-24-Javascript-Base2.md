---
layout: post
title: "JavaScript基础二、变量"
author: "Kai"
header-style: text
tags:
  - Javascript
---

转自：https://juejin.cn/post/7207304028253700153

# 1. 变量概念和作用
## 1.1 字面量
- 在计算机科学中，字面量（literal）是在计算机中描述 事/物，比如：
  - 我们工资是1000，1000是数字字面量
  - '前端学习'是字符串字面量
- 还有接下来我们学的 [] 数组字面量 {} 对象字面量 等等

## 1.2 变量
- 白话：变量就是一个装东西的盒子。
- 通俗：变量是计算机中用来 **存储数据** 的“容器”，它可以让计算机变得有记忆。
- 注意：变量不是数据本身，它们仅仅是一个用于存储数值的容器。可以理解为是一个个用来装东西的纸箱子。
    ![20241004223806](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004223806.png)

# 2. 变量基本使用
## 2.1 声明变量
- 要想使用变量，首先需要创建变量（也称为声明变量或者定义变量）
- 语法：`let 变量名`
  - 声明变量有两部分构成：声明关键字、变量名（标识符）
  - let 即声明关键字，所谓**关键字**是在JavaScript中**有特殊意义的词汇**，比如let、var、function、if、else、switch、case、break等，还有一个是**保留字**，目前在JavaScript中没意义，但**未来可能会具有特殊意义的词汇**，比如int、short、long、char等。
- 举例：`let age`
  - 我们声明了一个age变量
  - age 即变量的名称，也叫标识符

## 2.2 变量赋值
- 定义了一个变量后，你就能够初始化它（赋值）。在变量名之后跟上一个“=”，然后是数值。
- 注意：是通过变量名来获得变量里面的数据
- 简单点，也可以声明变量的时候直接完成赋值操作，这种操作也称为变量**初始化**。

    ![20241004223953](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004223953.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>变量的使用</title>
    </head>

    <body>
        <script>
            // 1. 声明一个年龄变量
            let age
            // 2. 赋值
            age = 18
            console.log(age)
            // 3. 声明的同时直接赋值  变量的初始化
            let age2 = 18

            // 小案例
            let num = 20
            let uname = 'pink老师'
            console.log(num)
            console.log(uname)
        </script>
    </body>

    </html>
    ```

## 2.3 更新变量
- 变量赋值后，还可以通过简单地给它一个不同的值来更新它。
- 注意： **let 不允许多次声明一个变量**。
    ![20241004224042](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004224042.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>变量的使用更新</title>
    </head>

    <body>
        <script>
            // 1 声明的同时直接赋值  变量的初始化
            // let age = 18
            // age = 19
            // // let age = 19
            // console.log(age)
            // 2. 声明多个变量    
            // let age = 18, uname = '迪丽热巴'
            // console.log(age, uname)
        </script>
    </body>

    </html>
    ```

## 2.4 声明多个变量
- **语法**：多个变量中间用逗号隔开。`let age=18, uname='pink'`
- **说明**：看上去代码长度更短，但并**不推荐**这样。为了更好的可读性，请一行只声明一个变量。
    ![20241004224144](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004224144.png)

## 2.5 输入用户名案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>输入用户名案例</title>
</head>

<body>
    <script>
        // 输出用户名案例
        // 1. 用户输入
        // prompt('请输入姓名')
        // 2. 内部处理保存数据
        let uname = prompt('请输入姓名')
        // 3. 打印输出
        document.write(uname)
    </script>
</body>

</html>
```

![20241004224209](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004224209.png)

![20241004224223](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004224223.png)

## 2.6 交换变量的值案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>交换变量的值案例</title>
</head>

<body>
    <script>
        // let if = 10
        let num1 = 'pink老师'
        let num2 = '戚薇'
        let temp
            // 都是把右边给左边
        temp = num1
        num1 = num2
        num2 = temp
        console.log(num1, num2)
    </script>
</body>

</html>
```

# 3. 变量的本质
- **内存**：计算机中存储数据的地方，相当于一个空间
- **变量本质**：是程序在内存中申请的一块用来存放数据的小空间
    ![20241004224305](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004224305.png)

# 4. 变量命名规则与规范
- **规则**：必须遵守，不遵守报错 (法律层面)
  - 不能用关键字
  - 关键字：有特殊含义的字符，JavaScript 内置的一些英语词汇。例如：let、var、if、for等
  - 只能用下划线、字母、数字、$组成，且数字不能开头
  - **字母严格区分大小写**，如 Age 和 age 是不同的变量
- **规范**：建议，不遵守不会报错，但不符合业内通识 （道德层面）
  - 起名要有意义
  - 遵守小驼峰命名法：第一个单词首字母小写，后面每个单词首字母大写。例：userName

    ![20241004224402](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004224402.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>变量的命名规范</title>
    </head>

    <body>
        <script>
            // let if = 10
            let num1$_ = 11
                // let nav-bar = 11
                // let 1num = 10
            //严格区分大小写
            let pink = '老师'
            let Pink = '演员'
            console.log(pink, Pink)
        </script>
    </body>

    </html>
    ```

# 5. let和var的区别
- var 在现代开发中一般不再使用它，只是我们可能在老版程序中看到它。
- let 是为了解决 var 的一些问题而出现的，以后声明变量我们统一使用 let。
- var 声明的一些问题
  - 可以先使用再声明 (不合理)
  - var 声明过的变量可以重复声明(不合理)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>let和var的区别</title>
    </head>

    <body>
        <script>
            // var可以重复声明，后面声明的覆盖前面的
            var num1
            var num1 = 10
            var num1= 20
            console.log(num1)
            
            // let不能重复声明，直接编译不通过
            // let num
            // let num = 20
            // let num = 10
            // console.log(num)
        </script>
    </body>

    </html>
    ```

# 6. strict模式
- JavaScript在设计之初，并不强制要求申明变量，如果一个变量**没有申明就被使用**，那么该变量就自动被声明为**全局变量**。
- 在同一个页面的不同的JavaScript文件中，如果都不声明，将造成变量污染。
- ECMA在后续规范中推出了strict模式，在strict模式下运行的JavaScript代码，强制要求申明变量，否则报错。启用strict模式的方法是在JavaScript代码的第一行写上：
    ```javascript
    'use strict';
    ```
- 这是一个字符串，不支持strict模式的浏览器会把它当做一个字符串语句执行，支持strict模式的浏览器将开启strict模式运行JavaScript。如果浏览器不报错，说明你的浏览器太古老了，需要尽快升级。
    ```javascript
    'use strict';
    // 如果浏览器支持strict模式，下面的代码将报ReferenceError错误:
    abc = 'Hello, world';
    console.log(abc);
    ```

# 7. 常量
- **概念**：使用 const 声明的变量称为“常量”。
- **使用场景**：当某个变量永远**不会改变**的时候，就可以使用 const 来声明，而不是let。
- **命名规范**：和变量一致
- **注意**：**常量不允许重新赋值，声明的时候必须赋值（初始化）**

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>常量</title>
    </head>

    <body>
        <script>
            // 1.声明常量，使用常量 
            const PI = 3.14
            console.log(PI)
            //不允许更改值
            //PI = 3.15
            // 2. 常量声明的时候必须赋值
            //const G
        </script>
    </body>

    </html>
    ```

# 8. 运算符
## 8.1 赋值运算符
- 赋值运算符：对变量进行赋值的运算符
  - 变量=值 ：将等号右边的值赋予给左边
  - 变量+=值：等价于 **变量=变量+值**
  - 变量-=值：等价于 **变量=变量-值**
  - 变量*=值：等价于 **变量=变量*值**
  - 变量/=值：等价于 **变量=变量/值**
  - 变量%=值：等价于 **变量=变量%值**

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>赋值运算符</title>
    </head>

    <body>
        <script>
            let num = 1
            // 采取赋值运算符
            // num = num + 1
            num += 1
            console.log(num)
        </script>
    </body>

    </html>
    ```

## 8.2 一元运算符
- JavaScript 的运算符可以根据所需表达式的个数，分为一元运算符、二元运算符、三元运算符
  - 二元运算符：`let num = 10 + 20`
  - 一元运算符：`let num = +10`
- 自增自减
  - 自增：**++**，让变量的值 **+1**
  - 自减：**--** ，让变量的值 **-1**
  - 使用场景：经常用于计数来使用。
- 自增
  - **前置自增：++num**，每执行1次，当前变量数值加1，作用相当于 `num += 1`
  - **后置自增：num++**，每执行1次，当前变量数值加1，作用相当于 `num += 1`
  - 前置自增和后置自增单独运算没有区别，一般开发中都是**独立使用后置自增**
- 后置自增后置自增计算顺序：
  - 前置自增：先自加再使用（记忆口诀：++在前 先加）
    ![20241004225221](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004225221.png)
  - 后置自增：先使用再自加（记忆口诀：++在后 后加）
    ![20241004225238](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004225238.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>一元运算符</title>
    </head>

    <body>
        <script>
            // 1. 前置自增
            let num1 = 1
            ++num1
            console.log(num1) //2

            // 2. 后置自增
            let num2 = 1
            num2++
            console.log(num2) //2

            //3. 自增参与计算
            let num3 = 1
            console.log(++num3 + 1) //3
            let num4 = 1
            console.log(num4++ + 1) //2

            //4. 自增复杂计算
            let i = 1
            console.log(i++ + ++i + i) //7
        </script>
    </body>

    </html>
    ```

## 8.3 比较运算符
- 使用场景：比较两个数据大小、是否相等
- 比较运算符：
  - **>**：左边是否大于右边
  - **<**：左边是否小于右边
  - **>=**：左边是否大于或等于右边
  - **<=**：左边是否小于或等于右边
  - **==**：**左右两边值是否相等**
  - **===**：**左右两边是否类型和值都相等**
  - **!==**：左右两边是否不全等
  - 比较结果为boolean类型，即只会得到 true 或 false
- 等号说明：
  - **=** 单等是赋值
  - **==** 是判断
  - **===** 是全等
  - **开发中判断是否相等，强烈推荐使用 ===**
- 字符串比较：比较的字符对应的**ASCII**码
  - 从左往右依次比较
  - 如果第一位一样再比较第二位，以此类推
- 数字比较
  - **NaN**不等于任何值，包括它本身，涉及到**NaN**都是**false**
  - 尽量不要比较小数，因为小数有精度问题
- 比较的**隐式转换**
  - 最终把数据隐式转换转成**number**类型再比较
  - 如果进行准确的比较我们更喜欢 **===** 或者 **!==**

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>比较运算符</title>
    </head>

    <body>
        <script>
            // 数字比较
            console.log(3 > 5) //false
            console.log(3 >= 3) //true
            console.log(2 == 2) //true
            // 比较运算符有隐式转换 
            // 把'2' 转换为 2 双等号只判断值
            console.log(2 == '2') // true  
            console.log(2 != '2') // false    
            // === 全等判断值和数据类型都一样才行
            // 以后判断是否相等 请用 ===  
            console.log(2 === '2') // false
            console.log(2 !== '2') // true  
            // NaN 不等于任何人，包括他自己
            console.log(NaN === NaN) // false
            
            //字符串比较
            console.log('a' < 'b') // true
            console.log('aa' < 'ab') // true
            console.log('aa' < 'aac') // true
        </script>
    </body>

    </html>
    ```

## 8.4 逻辑运算符
- **使用场景**：逻辑运算符用来解决多重条件判断。
- 举例：想判断一个变量 num 是否大于5且小于10
  - 错误写法： 5 < num < 10
  - 正确写法： num > 5 && num < 10
- 逻辑运算符：
    ![20241004225652](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004225652.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>逻辑运算符</title>
    </head>

    <body>
        <script>
            // 逻辑与 一假则假
            console.log(true && true) //true
            console.log(false && true) //false
            console.log(3 < 5 && 3 > 2) //true
            console.log(3 < 5 && 3 < 2) //false
            // 逻辑或 一真则真
            console.log(true || true)//true
            console.log(false || true)//true
            console.log(false || false)//false
            // 逻辑非  取反
            console.log(!true)//false
            console.log(!false)//true

            //数字比较逻辑判断
            let num = 6
            console.log(num > 5 && num < 10)//true
        </script>
    </body>

    </html>
    ```

## 8.5 逻辑中断
- 短路：只存在于 `&&` 和 `||` 中，当满足一定条件会让右边代码不执行
    ![20241004225731](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004225731.png)
- 原因：通过左边能得到整个式子的结果，因此没必要再判断右边
- 运算结果：无论 `&&` 还是 `||` ，运算结果都是最后被执行的表达式值，一般用在变量赋值
- 隐式转换：逻辑运算时，`''、0、undefined、null、false、NaN` 会隐士转换为false, 其余则为 true

**案例如下：**
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>逻辑中断</title>
</head>

<body>
    <script>
        // 左边能得到结果，不需要计算右边
        let age = 18
        console.log(false && age++) // age++ 不执行一假则假
        console.log(age) //18

        // 左边无法得到结果，需要计算右边
        console.log(false || age++)
        console.log(age) //19

        // 输出最后计算的表达式
        // console.log(11 && 22)  // 22 输出最后一个表达式
        // console.log(11 || 22)  // 11 输出第一个表达式

        //'''、0、undefined、null、false、NaN 转换为布尔值后都是false, 其余则为 true
        console.log('' || 20) //20
        console.log(0 || 20) //20
        console.log(undefined || 20) //20
        console.log(null || 20) //20
        console.log(false || 20) //20
        console.log(NaN || 20) // 20
    </script>
</body>

</html>
```

## 8.6 运算符优先级
逻辑非 > 逻辑与 > 逻辑或
![20241004230036](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004230036.png)

# 9. 常见错误
## 9.1 常量必须要赋值
![20241004230052](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004230052.png)

## 9.2 常量被重新赋值
![20241004230115](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004230115.png)

![20241004230124](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004230124.png)

## 9.3 变量未定义
![20241004230209](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004230209.png)

![20241004230215](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004230215.png)

分析：
- 提示 age变量没有定义过。
- 很可能 age 变量没有声明和赋值。
- 或者我们输出变量名和声明的变量不一致引起的（简单说写错变量名了）。

## 9.4 重复声明变量
![20241004230256](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004230256.png)

![20241004230303](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241004230303.png)

分析：
- 提示 “age”已经声明。
- 很大概率是因为重复声明了一个变量。
- 注意**let** 或者**const** 不允许多次声明同一个变量。
