---
layout: post
title: "JavaScript基础六、函数"
author: "Kai"
header-style: text
tags:
  - Javascript
---

转自：https://juejin.cn/post/7207304028253896761

# 1. 函数作用
- **函数**：**function**，是被设计为执行特定任务的代码块
- 函数可以把具有相同或相似逻辑的代码“包裹”起来，通过函数调用执行这些被“包裹”的代码逻辑，这么做的优势是有利于**精简代码方便复用**。
- 比如我们前面使用的 `alert()`、`prompt()` 和 `console.log()` 都是一些 js **函数**，只不过已经封装好了，我们直接使用。

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>体验函数99乘法表</title>
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
            // 声明函数
            function sheet99() {
                // 1. 外层循环控制行数
                for (let i = 1; i <= 9; i++) {
                    // 2. 里层循环控制列数
                    for (let j = 1; j <= i; j++) {
                        document.write(`<span>${j} X ${i} = ${i * j}</span>`)
                    }
                    // 换行
                    document.write('<br>')
                }
            }
            // 调用 代码复用
            sheet99()
            sheet99()
        </script>
    </body>

    </html>
    ```
    ![20241008180023](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180023.png)

# 2. 函数基本使用
## 2.1 函数声明
![20241008180053](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180053.png)

## 2.2 函数命名规范
- 和变量命名基本一致
- 尽量小驼峰式命名法
- 前缀应该为动词
- 命名建议：常用动词约定

    ![20241008180119](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180119.png)

## 2.3 函数调用
- 声明（定义）的函数必须调用才会真正被执行，使用 () 调用函数

    ![20241008180144](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180144.png)

## 2.4 函数体
- 函数体是函数的构成部分，它负责将相同或相似代码“包裹”起来，直到函数调用时函数体内的代码才会被执行。函数的功能代码都要写在函数体当中。

    ![20241008180208](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180208.png)

## 2.5 案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>函数的使用</title>
</head>

<body>
    <script>
        // 1. 函数的声明   
        function sayHi() {
            console.log('hi~~~')
        }
        // 2. 函数调用   函数不调用，自己不执行
        sayHi()
    </script>
</body>

</html>
```

# 3. 函数传参
## 3.1 参数作用
- 参数的重要性：没有参数的函数功能局限非常大，有参数的函数可以极大提高函数的灵活性

## 3.2 声明语法
- 传入数据列表
- 多个数据用逗号隔开

    ![20241008180300](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180300.png)

## 3.3 调用语法
- 调用函数时，需要传入几个数据就写几个，用逗号隔开

    ![20241008180319](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180319.png)

## 3.4 形参和实参
- 形参：声明函数时写在函数名右边小括号里的叫形参（形式上的参数）
- 实参：调用函数时写在函数名右边小括号里的叫实参（实际上的参数）
- 形参可以理解为是在这个函数内声明变量，实参可以理解为是给这个变量赋值
- 形参和实参个数可以不一致，开发中尽量保持一致

    ![20241008180351](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180351.png)

## 3.5 arguments
- 它只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数。即使函数不定义任何参数，还是可以拿到参数的值。

## 3.6 rest
- 参数个数不确定的时候，我们可以用到rest参数（ES6），rest参数只能写在最后，前面用 ... 标识，从运行结果可知，传入的参数先绑定确定的参数，多余的参数以数组形式交给变量 rest 。

## 3.7 函数参数案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>函数参数</title>
</head>

<body>
    <script>
        // 求和 起始数到结束数的和
        // 函数定义时的参数-形参  形式上的参数  
        function getSum(start, end) {
            let sum = 0
            for (let i = start; i <= end; i++) {
                sum += i
            }
            console.log(sum)
        }
        getSum(1, 50) //调用的小括号里面传入参数-实参 实际的参数
        getSum(100, 200)

        //利用arguments获得调用者传入的所有参数
        function abs() {
            if (arguments.length === 0) {
                return 0
            }
            var x = arguments[0]
            return x >= 0 ? x : -x
        }
        console.log(abs()) // 0
        console.log(abs(10)) // 10
        console.log(abs(-9)) // 9

        //rest参数
        function foo(a, b, ...rest) {
            console.log('a = ' + a);
            console.log('b = ' + b);
            console.log(rest);
        }
        foo(1, 2, 3, 4, 5);
        // 结果:
        // a = 1
        // b = 2
        // Array [ 3, 4, 5 ]
        foo(1);
        // 结果:
        // a = 1
        // b = undefined
        // Array []
    </script>
</body>

</html>
```

## 3.8 参数默认值
- 形参可以看做变量，但是如果一个变量不给值，默认**undefined**
- 我们可以改进下，用户不输入实参，可以给 **形参默认值**，可以默认为 0，这样程序更严谨。
- **默认值只会在缺少实参参数传递时才被执行，所以有参数会优先执行传递过来的实参**

    ![20241008180511](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180511.png)

## 3.9 参数默认值案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>函数参数-默认参数</title>
</head>

<body>
    <script>
        // 求和 起始数到结束数的和
        // 函数定义时的参数-形参  形式上的参数  
        function getSum(start=0, end=0) {
            let sum = 0
            for (let i = start; i <= end; i++) {
                sum += i
            }
            console.log(sum)
        }
        getSum(1, 50) //调用的小括号里面传入参数-实参 实际的参数
        getSum(100, 200)
        getSum()
    </script>
</body>

</html>
```

# 4. 函数返回值
## 4.1 函数返回值
- 有返回值函数的概念：当调用某个函数，这个函数会返回一个结果出来，这就是有**返回值**的函数
- 其实我们前面已经接触了很多的函数具备返回值：
    ![20241008180600](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180600.png)
- 当然有些函数，则没有返回值
    ![20241008180615](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180615.png)
- 所以要根据**需求**，来设定需不需要返回值

## 4.2 return关键字
- 当函数需要返回数据出去时，用**return**关键字
- **return** 后面代码不会再被执行，会立即结束当前函数，所以 **return** 后面的数据不要换行写。
- 函数可以没有 **return**，这种情况函数默认返回值为 **undefined**。
- **break**一般用在循环中结束循环，**return**是结束整个函数。

    ![20241008180722](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180722.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>函数的返回值</title>
    </head>

    <body>
        <script>
            // // 函数的返回值
            function fn() {
                return 20 // return 的值返回给调用者
            }
            // let re = fn()
            // console.log(re)
            console.log(fn()) //20

            // 求和函数的写法
            function getTotalPrice(x, y) {
                return x + y
                    // return 后面的代码不会被执行
            }
            let sum = getTotalPrice(1, 2)
            console.log(sum)

            //不return返回值默认是undefined
            function fn2() {

            }
            let re2 = fn2()
            console.log(re2) // undefined
        </script>
    </body>

    </html>
    ```

    **求最大值最小值函数：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>求最大值最小值函数</title>

    </head>

    <body>
    <div></div>
    <script>
        // 求任意数组的最大值和最小值，并且返回
        function getArrValue(arr = []) {
        // (1)先准备一个max变量存放数组的第一个值
        let max = arr[0]
        let min = arr[0]  // 最小值
        // (2) 遍历比较
        for (let i = 1; i < arr.length; i++) {
            // 最大值
            if (max < arr[i]) {
            max = arr[i]
            }
            // 最小值
            if (min > arr[i]) {
            min = arr[i]
            }
        }
        // (3) 返回值  返回的是数组
        return [max, min]
        }
        let newArr = getArrValue([11, 3, 55, 7, 29])
        console.log(`数组的最大值是: ${newArr[0]}`)
        console.log(`数组的最小值是: ${newArr[1]}`)
    </script>
    </body>

    </html>
    ```

# 5. 函数细节补充
## 5.1 同名覆盖
- 两个相同的函数后面的会覆盖前面的函数

## 5.2 形参实参个数不一致
- 在Javascript中 实参的个数和形参的个数可以不一致
  - 如果实参不够 会自动填上undefined
  - 如果实参过多 那么多余的实参会被忽略 (函数内部有一个arguments,里面装着所有的实参)

## 5.3 案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>函数细节</title>
</head>

<body>
    <script>
        // 1. 函数名相同， 后面覆盖前面
        function fn() {
            console.log(1)
        }
        function fn() {
            console.log(2)
        }
        fn() //2

        // 2. 参数不匹配
        function fn2(a, b) {
            console.log(a + b)
        }
        // (1). 实参多余形参   剩余的实参不参与运算
        fn2(1, 2, 3) //3
            // (2). 实参少于形参   剩余的实参不参与运算
        fn2(1) // 1 + undefined  = NaN
    </script>
</body>

</html>
```

# 6. 作用域
## 6.1 作用域
- 作用域：代码的作用范围。
- 全局作用域和局部作用域：
  - **全局作用域**：作用于所有代码环境。
  - **局部作用域**：作用于函数内的代码环境，和函数有关系，所以也称为函数作用域。

    ![20241008180938](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008180938.png)

- 作用域原理
  - JavaScript默认有一个全局对象 **window** ，全局作用域的变量实际上是被绑定到 **window** 的一个属性。因为window对象是全局唯一的，所以全局变量使用可以省略**window**对象名。
  - 任何变量（函数也视为变量），如果没有在当前函数作用域中找到，就会继续往上查找，最后如果在全局作用域中也没有找到，则报 **ReferenceError** 错误。
  - 不同的JavaScript文件如果使用了相同的全局变量，都会造成命名冲突，可以把变量和函数绑定到一个全局变量中来减少冲突，许多著名的JavaScript库如jQuery等都是这么干的。
  - 只要是代码，就至少有一个作用域。

    ![20241008181023](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008181023.png)

- 全局变量和局部变量：
  - 根据作用域的不同，变量分为全局变量和局部变量。
  - **全局变量**：任何区域都可以访问和修改。
  - **局部变量**：只能在当前函数内部访问和修改。

    ![20241008181055](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241008181055.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>作用域</title>
    </head>

    <body>
        <script>
            let num = 10 // 1. 全局变量
            console.log(num)

            function fn() {
                console.log(num)
            }
            fn()//10

            // 2. 局部变量
            function fun() {
                let str = 'pink'
            }
            console.log(str) // 错误
        </script>
    </body>

    </html>
    ```

## 6.2 作用域特殊情况
- 如果函数内部，变量没有声明直接赋值，会被当全局变量看，但是**强烈不推荐**
- 形参可以看做是函数的局部变量，外部无法访问
- **变量提升**：JavaScript的函数定义有个特点，它会先扫描整个函数体的语句，把所有申明的变量“提升”到函数顶部，但是赋值不会提升，所以先使用后声明不会报错，但是变量值是**undefined**，为了避免出现问题，**建议先声明再使用变量**。

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>变量的特殊情况</title>
    </head>

    <body>
        <script>
            let num = 20

            function fn() {
                num = 10 // 全局变量来看 强烈不允许 修改了全局变量
            }
            fn() //10
            console.log(num)

            // 形参可以看做是函数的局部变量，外部无法访问
            function fn2(x, y) {
                console.log(x)
            }
            fn2(1, 2)
            console.log(x) // 错误的

            //变量提升
            function foo() {
                var x = 'Hello, ' + y
                console.log(x)
                var y = 'Bob'  
            }
            foo()//Hello, undefined
        </script>
    </body>

    </html>
    ```

## 6.3 变量的访问原则
- 先局部后全局：**在能够访问到的情况下先局部，局部没有在找全局**
- 就近原则：如果函数中还有函数，那么在这个作用域中就又可以诞生一个作用域，哪个作用域更近先访问哪个作用域。

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>变量访问原则</title>
    </head>

    <body>
        <script>
            //局部变量优于全局变量
            function f1() {
                let num = 123

                function f2() {
                    console.log(num)
                }
                f2()
            }
            let num = 456
            f1() //123

            //局部变量就近原则
            function f3() {
                let num1 = 123

                function f4() {
                    let num1 = 0
                    console.log(num1)
                }
                f4()
            }
            let num1 = 456
            f3() //0

            //局部变量就近原则  复杂
            let a = 1

            function fn1() {
                let a = 2
                let b = '22'
                fn2()

                function fn2() {
                    let a = 3
                    fn3()

                    function fn3() {
                        let a = 4
                        console.log(a) //a的值 ?
                        console.log(b) //b的值 ?
                    }
                }
            }
            fn1() //4  22
        </script>
    </body>

    </html>
    ```

# 7. 匿名函数
## 7.1 函数分类
- 具名函数：有函数名
- 匿名函数：没函数名

![20241009092557](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241009092557.png)

## 7.2 匿名函数使用方式
- **函数表达式**：将匿名函数赋值给一个变量，并且通过变量名称进行调用。
    ![20241009092624](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241009092624.png)
- **立即执行函数**：可以避免全局变量之间的污染
  - 注意： 多个立即执行函数要用 ; 隔开，要不然会报错

    ![20241009092655](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241009092655.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>立即执行函数</title>
    </head>

    <body>
        <script>
            // 1. 第一种写法
            (function(x, y) {
                console.log(x + y)
                let num = 10
                let arr = []
            })(1, 2)
            
            // 2. 第二种写法
            (function(x, y) {
                let arr = []
                console.log(x + y)
            }(1, 3))
        </script>
    </body>

    </html>
    ```

# 8. this
- 绑定到对象上的函数称为方法，和普通函数也没啥区别，但是它在内部使用了一个 this 关键字。
- **this**指向视情况而定
  - 如果是对象调用方法，**this**指向**调用的对象**。
  - 单独调用方法，**this**指向**window**

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>this</title>
    </head>

    <body>
        <script>
            function getAge() {
                var y = new Date().getFullYear()
                return y - this.birth
            }
            let xiaoming = {
                name: '小明',
                birth: 1990,
                age: getAge
            }
            xiaoming.age() // 25, 正常结果  this指向xiaoming
            getAge() // NaN  this指向window
        </script>
    </body>

    </html>
    ```

# 9. 时间转换案例
![20241009092923](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241009092923.png)
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>转换时间案例</title>
</head>

<body>
    <script>
        // 1.用户输入
        let second = +prompt('请输入秒数:')
        // 2.封装函数
        function getTime(t) {
            // 3. 转换
            // 小时：  h = parseInt(总秒数 / 60 / 60 % 24)
            // 分钟：  m = parseInt(总秒数 / 60 % 60)
            // 秒数: s = parseInt(总秒数 % 60) 
            let h = parseInt(t / 60 / 60 % 24)
            let m = parseInt(t / 60 % 60)
            let s = parseInt(t % 60)
            h = h < 10 ? '0' + h : h
            m = m < 10 ? '0' + m : m
            s = s < 10 ? '0' + s : s
            return `转换完毕之后是${h}小时${m}分${s}秒`
        }
        let str = getTime(second)
        document.write(str)
    </script>
</body>

</html>
```
