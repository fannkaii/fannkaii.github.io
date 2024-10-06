---
layout: post
title: "JavaScript基础五、语句"
author: "Kai"
header-style: text
tags:
  - Javascript
---

转自：https://juejin.cn/post/7207314488243290169

# 1. 表达式和语句
## 1.1 表达式和语句
- 表达式：可以被求值的代码，JavaScript 引擎会将其计算出一个结果。
- 语句：一段可以执行的代码。
- 表达式和语句区别：
  - **表达式**：**因为表达式可被求值，所以它可以写在赋值语句的右侧。**
  - **语句**：**而语句不一定有值，所以比如 alert() for和break 等语句就不能被用于赋值。**

## 1.2 三大流程控制语句
- 顺序结构：从上往下**顺序**执行代码
- 分支结构：根据条件**选择性**执行代码
  - If分支语句
  - 三元运算符
  - switch 语句
- 循环结构：**重复**执行代码
  - while循环
  - for循环

    ![20241006100958](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006100958.png)

# 2. 分支语句
## 2.1 if分支语句
- if分支语句有三种使用：单分支、双分支、多分支
- 单分支使用语法：
  - 括号内的条件为true时，进入大括号里执行代码
  - 小括号内的结果若不是布尔类型时，会发生隐式转换转为布尔类型
  - 如果大括号只有一个语句，大括号可以省略，但是，俺们不提倡这么做

    ![20241006101051](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101051.png)

- **if单分支语句案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>if单分支语句</title>
    </head>

    <body>
    <script>
        // 单分支语句
        if (false) {
        console.log('执行语句1')
        }
        if (3 > 5) {
        console.log('执行语句2')
        }
        if (2 === '2') {
        console.log('执行语句3')
        }
        
        //1. 除了0 所有的数字都为真
        if (1) {
        console.log('执行语句4')
        }
        if (0) {
        console.log('执行语句5')
        }
        //2.除了 '' 所有的字符串都为真 true
        if ('pink老师') {
        console.log('执行语句6')
        }
        if ('') {
        console.log('执行语句7')
        }

        //输入单分支判断
        // 1. 用户输入
        let score = +prompt('请输入成绩')
        // 2. 进行判断输出
        if (score >= 700) {
        alert('恭喜高考成功')
        }
    </script>
    </body>

    </html>
    ```

- 双分支使用语法：
    ![20241006101209](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101209.png)

- **if双分支语句案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>if双分支语句</title>
    </head>

    <body>
        <script>
            // 1. 用户输入
            let uname = prompt('请输入用户名:')
            let pwd = prompt('请输入密码:')
                // 2. 判断输出
            if (uname === 'pink' && pwd === '123456') {
                alert('恭喜登录成功')
            } else {
                alert('用户名或者密码错误')
            }
        </script>
    </body>

    </html>
    ```

- **闰年案例如下：**
    ![20241006101256](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101256.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>闰年案例</title>
    </head>

    <body>
        <script>
            // 1. 用户输入
            let year = +prompt('请输入年份')
                // 2. 判断输出
            if (year % 4 === 0 && year % 100 !== 0 || year % 400 === 0) {
                alert(`${year}年是闰年`)
            } else {
                alert(`${year}年是平年`)
            }
        </script>
    </body>

    </html>
    ```

- 多分支使用语法：适合于有多个结果的时候， 比如学习成绩可以分为： 优 良 中 差
  - 先判断条件1，若满足条件1就执行代码1，其他不执行
  - 若不满足则向下判断条件2，满足条件2执行代码2，其他不执行
  - 若依然不满足继续往下判断，依次类推
  - 若以上条件都不满足，执行else里的代码n
  - 注：可以写N个条件，但这里演示只写2个

    ![20241006101347](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101347.png)

- **判断成绩案例案例如下：**
    ![20241006101435](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101435.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>判断成绩案例</title>
    </head>

    <body>
        <script>
            // 1. 用户输入
            let score = +prompt('请输入成绩：')
                // 2. 判断输出
            if (score >= 90) {
                alert('成绩优秀，宝贝，你是我的骄傲')
            } else if (score >= 70) {
                alert('成绩良好，宝贝，你要加油哦~~')
            } else if (score >= 60) {
                alert('成绩及格，宝贝，你很危险~')
            } else {
                alert('成绩不及格，宝贝，我不想和你说话，我只想用鞭子和你说话~')
            }
        </script>
    </body>

    </html>
    ```

## 2.2 三元运算符
- **使用场景**： 是比 if 双分支更简单的写法，能够满足简单双分支的需求
- **符号**：`?` 与 `:` 配合使用
- **语法**：
    ![20241006101545](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101545.png)
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>三元运算符</title>
    </head>

    <body>
    <script>
        // 三元运算符
        // 条件 ? 代码1 : 代码2
        
        // if双分支实现
        // if (3 < 5) {
        //   alert('真的')
        // } else {
        //   alert('假的')
        // }

        // 三元运算符
        3 < 5 ? alert('真的')  : alert('假的')
    </script>
    </body>

    </html>
    ```
- **求最大值案例：**
    ![20241006101635](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101635.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>求最大值案例</title>
    </head>

    <body>
    <script>
        // 1. 用户输入
        let num1 = +prompt('请您输入第一个数：')
        let num2 = +prompt('请您输入第二个数：')
        // 2. 判断输出-三元运算符
        num1 > num2 ? alert(`最大值是: ${num1}`) : alert(`最大值是: ${num2}`)
    </script>
    </body>

    </html>
    ```

- **数字补0案例：**
    ![20241006101711](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101711.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数字补0案例</title>
    </head>

    <body>
        <script>
            // 1. 用户输入 
            let num = prompt('请您输入一个数字:')
                // 2. 判断输出- 小于10才补0
            num = num < 10 ? 0 + num : num
            alert(num)
        </script>
    </body>

    </html>
    ```

## 2.3 switch语句
- 找到跟小括号里数据**全等**的case值，并执行里面对应的代码
- 若没有全等 `===` 的则执行default里的代码
- 例：数据若跟值2全等，则执行代码2
    ![20241006101806](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101806.png)
- 注意事项：
  - `switch case`语句一般用于等值判断,不适合于区间判断，if适合区间判断
  - `switch case`一般需要配合break关键字使用 没有break会造成case穿透，也就是从匹配到的值一直往下执行直到break或者结束。

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>switch选择语句</title>
    </head>

    <body>
        <script>
            switch (2) {
                case 1:
                    console.log('您选择的是1')
                    break // 退出switch
                case 2:
                    console.log('您选择的是2')
                    break // 退出switch
                case 3:
                    console.log('您选择的是3')
                    break // 退出switch
                default:
                    console.log('没有符合条件的')
            }
        </script>
    </body>

    </html>
    ```

- **简单计算器案例：**
    ![20241006101913](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006101913.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>简单计算器案例</title>
    </head>

    <body>
        <script>
            // 1.用户输入  2个数字 +  操作符号  + - *  / 
            let num1 = +prompt('请您输入第一个数字:')
            let num2 = +prompt('请您输入第二个数字:')
            let sp = prompt('请您输入 + - * / 其中一个:')
                // 2. 判断输出
            switch (sp) {
                case '+':
                    alert(`两个数的加法操作是${num1 + num2}`)
                    break
                case '-':
                    alert(`两个数的减法操作是${num1 - num2}`)
                    break
                case '*':
                    alert(`两个数的乘法操作是${num1 * num2}`)
                    break
                case '/':
                    alert(`两个数的除法操作是${num1 / num2}`)
                    break
                default:
                    alert(`你干啥咧，请输入+-*/`)
            }
        </script>
    </body>

    </html>
    ```

# 3. 循环语句
## 3.1 断点调试
- 作用：学习时可以帮助更好的理解代码运行，工作时可以更快找到bug
- 浏览器打开调试界面
  - 按F12打开开发者工具
  - 点到sources一栏
  - 选择代码文件
- 断点：在某句代码上加的标记就叫断点，当程序执行到这句有标记的代码时会暂停下来
    ![20241006102128](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006102128.png)

## 3.2 while循环
- **while循环**： 就是在满足条件期间，重复执行某些代码。
- while 循环基本语法
  - 跟if语句很像，都要满足小括号里的条件为true才会进入 循环体 执行代码
  - while大括号里代码执行完毕后不会跳出，而是继续回到小括号里判断条件是否满足，若满足又执行大括号里的代码，然后再回到小括号判断条件，直到括号内条件不满足，即跳出

    ![20241006102209](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006102209.png)
- while 循环三要素：循环的本质就是以某个变量为起始值，然后不断产生变化量，慢慢靠近终止条件的过程。
  - 变量起始值
  - 终止条件（没有终止条件，循环会一直执行，造成死循环）
  - 变量变化量（用自增或者自减）

    ![20241006102238](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006102238.png)

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>while循环</title>
    </head>

    <body>
        <script>
            // // 1. 变量的起始值
            // let i = 1
            // // 2. 终止条件
            // while (i <= 3) {
            //   document.write('我要循环三次 <br>')
            //   // 3. 变量的变化量
            //   i++
            // }
            
            // 1. 变量的起始值
            let end = +prompt('请输入次数:')
            let i = 1
                // 2. 终止条件
            while (i <= end) {
                document.write(`我要循环${end}次 <br>`)
                    // 3. 变量的变化量
                i++
            }
        </script>
    </body>

    </html>
    ```
- **while循环练习：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>while循环练习</title>
    </head>

    <body>
        <script>
            // 1. 页面输出1~100
            // let i = 1
            // while (i <= 100) {
            //   document.write(`这是第${i}个数<br>`)
            //   i++
            // }

            // 2. 页面输出1~100 累加和
            // let i = 1  // 变量的起始值
            // let sum = 0  // 累加和变量
            // while (i <= 100) {
            //   // 把i 累加到 sum 里面
            //   // sum = sum + i
            //   sum += i
            //   i++
            // }
            // console.log(sum) // 5050

            // 3. 页面输出1~100 偶数和
            let i = 1
            let sum = 0
            while (i <= 100) {
                // 筛选偶数 只有偶数才累加
                if (i % 2 === 0) {
                    sum = sum + i
                }
                // 每次循环都要自加
                i++
            }
            console.log(sum)
        </script>
    </body>

    </html>
    ```

## 3.3 for循环
- **作用**：重复执行代码
- **好处**：把声明起始值、循环条件、变化值写到一起，让人一目了然，它是最常使用的循环形式
    ![20241006102624](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006102624.png)
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>for循环的基本使用</title>
    </head>

    <body>
    <script>
        // 利用for循环输出
        for (let i = 1; i <= 3; i++) {
        document.write('for循环输出<br>')
        }
    </script>
    </body>

    </html>
    ```
- **for循环练习：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>for循环练习</title>
    </head>

    <body>
    <script>
        // 1. 输出1~100岁
        for (let i = 1; i <= 100; i++) {
        document.write(`今年我${i}岁了 <br>`)
        }

        // 2. 求1~100之间的偶数和
        let sum = 0
        for (let i = 1; i <= 100; i++) {
        if (i % 2 === 0) {
            // 把i加到sum里面去
            // sum = sum + i
            sum += i
        }
        }
        document.write(`1~100之间的偶数和是: ${sum}`)

        // 3. 页面打印5个小星星
        for (let i = 1; i <= 5; i++) {
        document.write('★')
        }

        // 4. 打印数组
        let arr = ['刘德华', '刘晓强', '刘晓庆', '刘若英', '刘热巴', 'pink老师']    
        // 必须从0开始，因为数组索引号从0开始  arr.length = 6
        // 遍历数组：从第一个循环到最后一个
        for (let i = 0; i < arr.length; i++) {
        console.log(arr[i])
        }

        //超出索引
        let arr1 = []
        console.log(arr1)
        console.log(arr1[0]) // undefined
        console.log(arr1[1]) // undefined
    </script>
    </body>

    </html>
    ```

- for循环嵌套：一个循环里再套一个循环，一般用在for循环里
    ![20241006102727](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006102727.png)
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>for循环的嵌套</title>
    </head>

    <body>
    <script>
        // 外层循环打印 第 n 天
        for (let i = 1; i <= 3; i++) {
        document.write(`第${i}天<br>`)
        // 里层循环打印 第几个单词
        for (let j = 1; j <= 5; j++) {
            document.write(`记住了第${j}个单词<br>`)
        }
        }
    </script>
    </body>

    </html>
    ```

- **打印五行五列小星星：**
    ![20241006102808](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006102808.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>打印五行五列小星星</title>
    </head>

    <body>
    <script>
        // // 外层循环打印行数
        // for (let i = 1; i <= 5; i++) {
        //   // 里层循环打印几个星星
        //   for (let j = 1; j <= 5; j++) {
        //     document.write('☆')
        //   }
        //   // 进行换行显示
        //   document.write('<br>')
        // }

        let row = +prompt('请输入行数:')
        let col = +prompt('请输入列数:')
        // 外层循环打印行数
        for (let i = 1; i <= row; i++) {
        // 里层循环打印几个星星
        for (let j = 1; j <= col; j++) {
            document.write('☆')
        }
        // 进行换行显示
        document.write('<br>')
        }
    </script>
    </body>

    </html>
    ```

- **打印直角三角形：**
    ![20241006102835](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006102835.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>打印直角三角形</title>
    </head>

    <body>
    <script>
        // 1. 外层循环控制行数
        for (let i = 1; i <= 5; i++) {
        // 2. 里层循环控制列数（几个星星）
        for (let j = 1; j <= i; j++) {
            document.write('◆')
        }
        // 换行
        document.write('<br>')
        }
    </script>
    </body>

    </html>
    ```

- **99乘法表：**
    ![20241006102901](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006102901.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>99乘法表</title>
        <style>
            span {
                display: inline-block;
                width: 100px;
                padding: 5px 10px;
                border: 1px solid pink;
                margin: 2px;
                border-radius: 5px;
                box-shadow: 2px 2px 2px rgba(255, 192, 203, .4);
                background-color: rgba(255, 192, 203, .1);
                text-align: center;
                color: hotpink;
            }
        </style>
    </head>

    <body>
        <script>
            // 1. 外层循环控制行数
            for (let i = 1; i <= 9; i++) {
                // 2. 里层循环控制列数
                for (let j = 1; j <= i; j++) {
                    document.write(`<span>${j} X ${i} = ${i * j}</span>`)
                }
                // 换行
                document.write('<br>')
            }
        </script>
    </body>

    </html>
    ```

## 3.4 循环退出
- 循环结束：
  - break：退出循环
  - continue：结束本次循环，继续下次循环
- 区别：
  - continue 退出本次循环，一般用于排除或者跳过某一个选项的时候, 可以使用continue
  - break 退出整个循环，一般用于结果已经得到, 后续的循环不需要的时候可以使用
- for和while
  - `while(true)` 来构造“无限”循环，需要使用break退出循环。
  - `for(;;)` 也可以来构造“无限”循环，同样需要使用break退出循环。

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>while退出循环</title>
    </head>

    <body>
        <script>
            let i = 1
            while (i <= 5) {
            console.log(i)
            if (i === 3) {
                break  // 退出循环
            }
            i++
            }

            let j = 1
            while (j <= 5) {
                if (j === 3) {
                    j++
                    continue //继续下一次循环
                }
                console.log(j)
                j++
            }
        </script>
    </body>

    </html>
    ```

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>for退出循环</title>
    </head>

    <body>
        <script>
            for (let i = 1; i <= 5; i++) {
            if (i === 3) {
                continue  // 退出本次循环，本次循环中 continue下面的语句不在执行
            }
            console.log(i)
            }

            for (let i = 1; i <= 5; i++) {
            if (i === 3) {
                break  // 退出整个循环 结束循环
            }
            console.log(i)
            }

            // 无限循环
            for (;;) {
                console.log(11)
            }
        </script>
    </body>

    </html>
    ```

## 3.5 for遍历数组
- **数组大于10筛选案例：**
    ![20241006103157](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103157.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数组大于10筛选案例</title>
    </head>

    <body>
        <script>
            let arr = [2, 0, 6, 1, 77, 9, 54, 3, 78, 7]
            // 1. 声明新的空的数组
            let newArr = []
            // 2. 遍历旧数组
            for (let i = 0; i < arr.length; i++) {
                if (arr[i] >= 10) {
                    // 3. 满足条件 追加给新的数组
                    newArr.push(arr[i])
                }
            }
            // 4. 输出新的数组
            console.log(newArr)
        </script>
    </body>

    </html>
    ```

- **数组去0案例：**
    ![20241006103223](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103223.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>数组去0案例</title>
    </head>

    <body>
    <script>
        let arr = [2, 0, 6, 1, 77, 0, 52, 0, 25, 7]
        // 1. 声明一个新的数组
        let newArr = []
        // 2. 遍历筛选
        for (let i = 0; i < arr.length; i++) {
        if (arr[i] !== 0) {
            newArr.push(arr[i])
        }
        }
        // 输出新数组
        console.log(newArr)
    </script>
    </body>

    </html>
    ```

- **数组求和以及平均值：**
    ![20241006103254](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103254.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数组求和以及平均值</title>
    </head>

    <body>
        <script>
            let arr = [2, 6, 1, 7, 4]
            // 1. 求和的变量 sum
            let sum = 0
            // 2.遍历累加
            for (let i = 0; i < arr.length; i++) {
            sum += arr[i]
            }
            console.log(`数组的和的结果是: ${sum}`)
            // 3. 平均值  和 / arr.length  = 4
            console.log(`数组的平均值结果是: ${sum / arr.length}`)
        </script>
    </body>

    </html>
    ```

- **数组最大值最小值：**
    ![20241006103320](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103320.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数组最大值最小值</title>
    </head>

    <body>
        <script>
            let arr = [2, 6, 1, 7, 400, 55, 88, 100]
            // max 里面要存的是最大值
            let max = arr[0]
            // min 要存放的是最小值
            let min = arr[0]
            // 遍历数组
            for (let i = 1; i < arr.length; i++) {
                // 如果max 比 数组元素里面的值小，我们就需要把这元素赋值给 max
                // if (max < arr[i]) max = arr[i]
                max < arr[i] ? max = arr[i] : max
                // 如果min 比 数组元素大， 我们就需要把数组元素给min
                // if (min > arr[i]) {
                //   min = arr[i]
                // }
                min > arr[i] ? min = arr[i] : min
            }
            // 输出
            console.log(`最大值是: ${max}`)
            console.log(`最小值是: ${min}`)
        </script>
    </body>

    </html>
    ```

- **冒泡排序：**
    ![20241006103349](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103349.png)

    ![20241006103356](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103356.png)

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>冒泡排序</title>
    </head>

    <body>
    <script>
        // 冒泡排序
        let arr = [2, 4, 3, 5, 1]
        for (let i = 0; i < arr.length - 1; i++) {
        for (let j = 0; j < arr.length - i - 1; j++) {
            // 开始交换 但是前提 第一个数大于第二个数才交换
            if (arr[j] > arr[j + 1]) {
            // 交换2个变量
            let temp = arr[j]
            arr[j] = arr[j + 1]
            arr[j + 1] = temp
            }
        }
        }
    </script>
    </body>

    </html>
    ```

- **生成柱形图案例：**
    ![20241006103420](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103420.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>生成柱形图案例</title>
        <style>
            * {
                margin: 0;
                padding: 0;
            }
            
            .box {
                display: flex;
                width: 700px;
                height: 300px;
                border-left: 1px solid pink;
                border-bottom: 1px solid pink;
                margin: 50px auto;
                justify-content: space-around;
                align-items: flex-end;
                text-align: center;
            }
            
            .box>div {
                display: flex;
                width: 50px;
                background-color: pink;
                flex-direction: column;
                justify-content: space-between;
            }
            
            .box div span {
                margin-top: -20px;
            }
            
            .box div h4 {
                margin-bottom: -35px;
                width: 70px;
                margin-left: -10px;
            }
        </style>
    </head>

    <body>
        <script>        
            // 声明一个新的数组
            let arr = []
            for (let i = 1; i <= 4; i++) {
                // 四次弹框采集数据
                // let num = prompt(`请输入第${i}季度的数据:`)
                // arr.push(num)
                arr.push(prompt(`请输入第${i}季度的数据:`))
            }
            // 盒子开头
            document.write(` <div class="box">`)

            // 盒子中间 利用循环的形式  跟数组有关系
            for (let i = 0; i < arr.length; i++) {
                document.write(`
                <div style="height: ${arr[i]}px;">
                    <span>${arr[i]}</span>
                    <h4>第${i + 1}季度</h4>
                </div>          
                `)
            }
            // 盒子结尾
            document.write(` </div>`)
        </script>
    </body>

    </html>
    ```
    ![20241006103454](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103454.png)


## 3.6 for遍历对象
- for 遍历对象的问题：
  - 对象没有像数组一样的`length`属性,所以无法确定长度
  - 对象里面是无序的键值对, 没有规律. 不像数组里面有规律的下标
- for 遍历对象的方法
  - 一般不用这种方式遍历数组、主要是用来遍历对象
  - `for in`语法中的 `k` 是一个变量, 在循环的过程中依次代表对象的属性名
  - 由于 `k` 是变量, 所以必须使用 `[ ]` 语法解析
  - 一定记住： `k` 是获得对象的**属性名**， `对象名[k]` 是获得 **属性值**

    ![20241006103635](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103635.png)

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>遍历对象</title>
    </head>

    <body>
    <script>
        // for in 我们不推荐遍历数组
        // let arr = ['pink', 'red', 'blue']
        // for (let k in arr) {
        //   console.log(k)  // 数组的下标 索引号  但是是字符串 '0'
        //   console.log(arr[k])  // arr[k]
        // }

        // 1. 创建对象
        let obj = {
        uname: 'pink老师',
        age: 18,
        gender: '男'
        }
        // 2. 遍历对象
        for (let k in obj) {
        console.log(k) // 属性名  'uname'   'age'
        console.log(obj[k])  // 输出属性值  obj[k]
        }
    </script>
    </body>

    </html>
    ```

- **遍历数组对象：**
    ![20241006103718](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103718.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>遍历数组对象</title>
    </head>

    <body>
    <script>
        let students = [
        { name: '小明', age: 18, gender: '男', hometown: '河北省' },
        { name: '小红', age: 19, gender: '女', hometown: '河南省' },
        { name: '小刚', age: 17, gender: '男', hometown: '山西省' },
        { name: '小丽', age: 18, gender: '女', hometown: '山东省' }
        ]
        for (let i = 0; i < students.length; i++) {
        // console.log(i)  // 下标索引号
        // console.log(students[i]) // 每个对象
        console.log(students[i].name)
        console.log(students[i].hometown)
        }
    </script>
    </body>

    </html>
    ```

- **渲染学生信息表：**
    ![20241006103758](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103758.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>渲染学生信息表</title>
        <style>
            table {
                width: 600px;
                text-align: center;
            }

            table,
            th,
            td {
                border: 1px solid #ccc;
                border-collapse: collapse;
            }

            caption {
                font-size: 18px;
                margin-bottom: 10px;
                font-weight: 700;
            }

            tr {
                height: 40px;
                cursor: pointer;
            }

            table tr:nth-child(1) {
                background-color: #ddd;
            }

            table tr:not(:first-child):hover {
                background-color: #eee;
            }
        </style>
    </head>

    <body>
        <h2>学生信息</h2>
        <p>将数据渲染到页面中...</p>

        <table>
            <caption>学生列表</caption>
            <tr>
                <th>序号</th>
                <th>姓名</th>
                <th>年龄</th>
                <th>性别</th>
                <th>家乡</th>
            </tr>
            <!-- script写到这里 -->
            <script>
                // 1. 数据准备
                let students = [
                    { name: '小明', age: 18, gender: '男', hometown: '河北省' },
                    { name: '小红', age: 19, gender: '女', hometown: '河南省' },
                    { name: '小刚', age: 17, gender: '男', hometown: '山西省' },
                    { name: '小丽', age: 18, gender: '女', hometown: '山东省' },
                    { name: '晓强', age: 16, gender: '女', hometown: '蓝翔技校' }
                ]
                // 2. 渲染页面
                for (let i = 0; i < students.length; i++) {
                    document.write(`
                    <tr>
                        <td>${i + 1}</td>
                        <td>${students[i].name}</td>
                        <td>${students[i].age}</td>
                        <td>${students[i].gender}</td>
                        <td>${students[i].hometown}</td>
                    </tr>
                    `)
                }
            </script>
        </table>

    </body>


    </html>
    ```

- **学成在线精品推荐：**
    ![20241006103830](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103830.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>学成在线精品推荐</title>
        <link rel="stylesheet" href="./css/style.css">
    </head>

    <body>
        <!-- 4. box核心内容区域开始 -->
        <div class="box w">
            <div class="box-hd">
                <h3>精品推荐</h3>
                <a href="#">查看全部</a>
            </div>
            <div class="box-bd">
                <ul class="clearfix">
                    <!-- <li>
                        <a href="#">
                            <img src="images/course01.png" alt="">
                            <h4>
                                Think PHP 5.0 博客系统实战项目演练
                            </h4>
                            <div class="info">
                                <span>高级</span> • <span>1125</span>人在学习
                            </div>
                        </a>
                    </li> -->
                    <script>
                        let data = [{
                            src: 'images/course01.png',
                            title: 'Think PHP 5.0 博客系统实战项目演练',
                            num: 1125
                        }, {
                            src: 'images/course02.png',
                            title: 'Android 网络动态图片加载实战',
                            num: 357
                        }, {
                            src: 'images/course03.png',
                            title: 'Angular2大前端商城实战项目演练',
                            num: 22250
                        }, {
                            src: 'images/course04.png',
                            title: 'AndroidAPP实战项目演练',
                            num: 389
                        }, {
                            src: 'images/course05.png',
                            title: 'UGUI源码深度分析案例',
                            num: 124
                        }, {
                            src: 'images/course06.png',
                            title: 'Kami2首页界面切换效果实战演练',
                            num: 432
                        }, {
                            src: 'images/course07.png',
                            title: 'UNITY 从入门到精通实战案例',
                            num: 888
                        }, {
                            src: 'images/course08.png',
                            title: 'Cocos 深度学习你不会错过的实战',
                            num: 590
                        }, {
                            src: 'images/course04.png',
                            title: '自动添加的模块',
                            num: 1000
                        }]

                        for (let i = 0; i < data.length; i++) {
                            document.write(`
                            <li>
                                <a href="#">
                                    <img src=${data[i].src} title="${data[i].title}">
                                    <h4>
                                    ${data[i].title}
                                    </h4>
                                    <div class="info">
                                        <span>高级</span> • <span>${data[i].num}</span>人在学习
                                    </div>
                                </a>
                            </li>
                        `)
                        }
                    </script>
                </ul>
            </div>
        </div>

    </body>

    </html>
    ```

# 4. 综合案例
![20241006103908](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241006103908.png)
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ATM取款机综合案例</title>
</head>

<body>
  <script>
    // 1. 开始循环 输入框写到 循环里面
    // 3. 准备一个总的金额
    let money = 100
    while (true) {
      let re = +prompt(`
        请您选择操作：
        1.存钱
        2.取钱
        3.查看余额
        4.退出
        `)
      // 2. 如果用户输入的 4 则退出循环， break  写到if 里面，没有写到switch里面 因为4需要break退出循环
      if (re === 4) {
        break
      }
      // 4. 根据输入做操作
      switch (re) {
        case 1:
          // 存钱
          let cun = +prompt('请输入存款金额')
          money = money + cun
          break
        case 2:
          // 存钱
          let qu = +prompt('请输入取款金额')
          money = money - qu
          break
        case 3:
          // 存钱
          alert(`您的银行卡余额是${money}`)
          break
      }
    }
  </script>
</body>

</html>
```
