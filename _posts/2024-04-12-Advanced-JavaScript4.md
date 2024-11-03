---
layout: post
title: "JavaScript高级 四、高阶技巧"
author: "Kai"
header-style: text
tags:
  - Javascript
---

转自：https://juejin.cn/post/7238869983622168632

# 1. 深浅拷贝
- 首先浅拷贝和深拷贝只针对引用类型

## 1.1 浅拷贝
- 浅拷贝：拷贝对象的属性的值（**简单类型**存的值就是**值本身**，**引用类型**存的值是**对象的堆地址**），所以如果拷贝的对象值中有**引用类型属性**，拷贝后的新对象属性和源对象属性指向同一个对地址，**修改**此指向的对象会**相互影响**。
- 常见方法：
  - 拷贝对象：`Object.assgin()` 或者 展开运算符`{...obj}`
  - 拷贝数组：`Array.prototype.concat()` 或者 `[...arr]`
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>浅拷贝</title>
    </head>

    <body>
        <script>
            const user = {
                uname: 'pink',
                age: 18,
                family: {
                    baby: '小pink'
                }
            }

            // 浅拷贝
            const o1 = {
                ...user
            }
            o1.age = 20 //对象属性值类型修改不影响
            console.log(o1.age) //20
            console.log(user.age) //18

            // 浅拷贝
            const o2 = {}
            Object.assign(o2, user)
            o2.age = 20
            o2.family.baby = '老pink' //对象属性引用类型修改会相互影响
            console.log(o2.family.baby) //老pink
            console.log(user.family.baby) //老pink
        </script>
    </body>

    </html>
    ```

## 1.2 深拷贝
- 深拷贝：拷贝对象属性的时候，遇到引用类型就创建一个新对象，然后递归，直至所有字段全部拷贝完成。
- **常见方法**：
  - 递归函数实现深拷贝
    - 如果一个函数在内部可以调用其本身，那么这个函数就是递归函数
    - 由于递归很容易发生“栈溢出”错误（stack overflow），所以必须要加退出条件 return
  - `lodash.cloneDeep`实现深拷贝
  - `JSON.stringify`实现深拷贝

- **递归函数实现深拷贝：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>递归函数实现深拷贝</title>
    </head>

    <body>
        <script>
            const obj = {
                uname: 'pink',
                age: 18,
                hobby: ['乒乓球', '足球'],
                family: {
                    baby: '小pink'
                }
            }
            const o = {}

            // 拷贝函数
            function deepCopy(newObj, oldObj) {
                for (let k in oldObj) {
                    // 处理数组
                    if (oldObj[k] instanceof Array) {
                        newObj[k] = []
                        deepCopy(newObj[k], oldObj[k])
                        // 处理对象
                    } else if (oldObj[k] instanceof Object) {
                        newObj[k] = {}
                        deepCopy(newObj[k], oldObj[k])
                        //值类型
                    } else {
                        newObj[k] = oldObj[k]
                    }
                }
            }

            // 函数调用  两个参数 o 新对象  obj 旧对象
            deepCopy(o, obj)
            o.age = 20
            o.hobby[0] = '篮球'
            o.family.baby = '老pink'
            console.log(o.age) //20
            console.log(o.hobby[0]) //篮球
            console.log(o.family.baby) //老pink
            console.log(obj.age) //18
            console.log(obj.hobby[0]) //乒乓球
            console.log(obj.family.baby) //小pink
        </script>
    </body>

    </html>
    ```

- `lodash.cloneDeep`实现深拷贝：
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>lodash.cloneDeep实现深拷贝</title>
    </head>

    <body>
        <!-- 先引用 -->
        <script src="./lodash.min.js"></script>
        <script>
            const obj = {
                uname: 'pink',
                age: 18,
                hobby: ['乒乓球', '足球'],
                family: {
                    baby: '小pink'
                }
            }
            const o = _.cloneDeep(obj)
            o.age = 20
            o.hobby[0] = '篮球'
            o.family.baby = '老pink'
            console.log(o.age) //20
            console.log(o.hobby[0]) //篮球
            console.log(o.family.baby) //老pink
            console.log(obj.age) //18
            console.log(obj.hobby[0]) //乒乓球
            console.log(obj.family.baby) //小pink
        </script>
    </body>

    </html>
    ```

- `JSON.stringify`实现深拷贝：
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>JSON.stringify实现深拷贝</title>
    </head>

    <body>
        <script>
            const obj = {
                uname: 'pink',
                age: 18,
                hobby: ['乒乓球', '足球'],
                family: {
                    baby: '小pink'
                }
            }
            // 把对象转换为 JSON 字符串    
            const o = JSON.parse(JSON.stringify(obj))
            o.age = 20
            o.hobby[0] = '篮球'
            o.family.baby = '老pink'
            console.log(o.age) //20
            console.log(o.hobby[0]) //篮球
            console.log(o.family.baby) //老pink
            console.log(obj.age) //18
            console.log(obj.hobby[0]) //乒乓球
            console.log(obj.family.baby) //小pink
        </script>
    </body>

    </html>
    ```

# 2. 异常处理
- 异常处理是指预估代码执行过程中可能发生的错误，然后最大程度的避免错误的发生导致整个程序无法继续运行

## 2.1 throw 抛异常
- throw **抛出异常信息**，程序也**会终止执行**
- throw 后面跟的是错误提示信息
- Error 对象配合 throw 使用，能够设置更详细的错误信息

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>throw抛出异常</title>
    </head>

    <body>
        <script>
            function fn(x, y) {
                if (!x || !y) {
                    // throw '没有参数传递进来'
                    throw new Error('没有参数传递过来')
                }

                return x + y
            }
            console.log(fn())
        </script>
    </body>

    </html>
    ```
    ![20241103101539](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103101539.png)

## 2.2 try / catch 捕获异常
- try...catch 用于捕获错误信息
- 将预估可能发生错误的代码写在 try 代码段中
- 如果 try 代码段中出现错误后，会执行 catch 代码段，并截获到错误信息
- finally 不管是否有错误，都会执行

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>try-catch捕获异常</title>
    </head>

    <body>
        <p>123</p>
        <script>
            function fn() {
                try {
                    // 可能发送错误的代码 要写到 try
                    const p = document.querySelector('.p')
                    p.style.color = 'red'
                } catch (err) {
                    // 拦截错误，提示浏览器提供的错误信息，但是不中断程序的执行
                    console.log(err.message)
                } finally {
                    // 不管你程序对不对，一定会执行的代码
                    console.log('不管你程序对不对，一定会执行的代码')
                }
                console.log(11)
            }
            fn()
        </script>
    </body>

    </html>
    ```
    ![20241103101644](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103101644.png)

## 2.3 debugger
- 可以在代码里面添加debugger，用来断点调试代码
    ![20241103101705](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103101705.png)

# 3. 处理this
## 3.1 this指向
- 普通函数this指向
  - 谁调用 this 的值指向谁
  - 普通函数没有明确调用者时 this 值为 window，严格模式下没有调用者时 this 的值为 undefined
- 箭头函数this指向
- 箭头函数中的并不存在 this，箭头函数中的this是绑定的最近作用域中的this，向外层作用域中一层一层查找this，直到有this的定义
- 不适用情况：构造函数，原型函数，字面量对象中函数，dom事件函数等
- 适用情况：需要使用上层this的地方

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>普通函数的this指向</title>
    </head>

    <body>
        <button>点击</button>
        <script>
            // 普通函数：谁调用我，this就指向谁
            console.log(this) // window
            function fn() {
                console.log(this) // window    
            }
            window.fn()
            window.setTimeout(function () {
                console.log(this) // window 
            }, 1000)
            document.querySelector('button').addEventListener('click', function () {
                console.log(this) // 指向 button
            })
            const obj = {
                sayHi: function () {
                    console.log(this) // 指向 obj
                }
            }
            obj.sayHi()
        </script>
    </body>

    </html>
    ```

## 3.2 改变this
- JavaScript 中还允许指定函数中 this 的指向，有 3 个方法可以动态指定普通函数中 this 的指向
- `call()`
  - 使用 call方法 **调用函数**，同时**指定**被调用函数中 **this** 的值
  - 语法：`fun.call(thisArg, arg1, arg2, ...)`
  - thisArg：在 fun 函数运行时指定的 this 值
  - arg1，arg2：传递的其他参数
  - 返回值就是函数的返回值，因为它就是调用函数
- `apply()`
  - 使用 apply方法调用函数，同时指定被调用函数中 this 的值
  - 语法：`fun.apply(thisArg, [argsArray])`
  - thisArg：在fun函数运行时指定的 this 值
  - argsArray：传递的值，**必须包含在数组里面**
  - 返回值就是函数的返回值，因为它就是调用函数
  - **因此 apply 主要跟数组有关系，比如使用 `Math.max()` 求数组的最大值**
- `bind()`
  - `bind()` 方法**不会调用函数**。**指定**被调用函数中 **this** 的值，返回新函数
  - 语法：`fun.bind(thisArg, arg1, arg2, ...)`
  - thisArg：在 fun 函数运行时指定的 this 值
  - arg1，arg2：传递的其他参数
  - 返回由指定的 this 值和初始化参数改造的 **原函数拷贝（新函数）**
  - **因此只改变 this 指向，不调用函数时，使用 bind，比如改变定时器内部的this指向**

- **call案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>call</title>
    </head>

    <body>
        <script>
            const obj = {
                uname: 'pink'
            }

            function fn(x, y) {
                console.log(this) // window
                console.log(x + y)
            }

            // 1. 调用函数  
            // 2. 改变this指向obj,原来是window调用指向window
            // 3. 返回值就是函数的返回值
            fn.call(obj, 1, 2)
        </script>
    </body>

    </html>
    ```

- **apply案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>apply</title>
    </head>

    <body>
        <script>
            const obj = {
                age: 18
            }

            function fn(x, y) {
                console.log(this)
                console.log(x + y)
            }
            // 1. 调用函数
            // 2. 改变this指向
            // 3. 返回值就是函数的返回值
            fn.apply(obj, [1, 2])

            // 使用场景：求数组最大值最小值
            const arr = [100, 44, 77]
            const max = Math.max.apply(Math, arr)
            const min = Math.min.apply(null, arr)
            console.log(max, min)
        </script>
    </body>

    </html>
    ```

- **bind案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>bind</title>
    </head>

    <body>
        <button>发送短信</button>
        <script>
            const obj = {
                age: 18
            }

            function fn() {
                console.log(this)
            }

            // 1. bind不会调用函数 
            // 2. 能改变this指向
            // 3. 返回值是个函数，但是这个函数里面的this是更改过的obj
            const fun = fn.bind(obj)
            fun()

            // 需求:有一个按钮，点击里面就禁用，2秒钟之后开启
            document.querySelector('button').addEventListener('click', function () {
                // 禁用按钮
                this.disabled = true
                window.setTimeout(function () {
                    // 在这个普通函数里面，我们要this由原来的window 改为 btn
                    this.disabled = false
                }.bind(this), 2000) // 这里的this 和 btn 一样
            })
        </script>
    </body>

    </html>
    ```

# 4. 性能优化
## 4.1 防抖
- 所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会**重新计算**函数执行时间
- 使用场景：搜索框输入，设定每次输入完毕n秒后发送请求，如果期间还有输入，则重新计算时间

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>防抖</title>
        <style>
            .box {
                width: 500px;
                height: 500px;
                background-color: #ccc;
                color: #fff;
                text-align: center;
                font-size: 100px;
            }
        </style>
    </head>

    <body>
        <div class="box"></div>
        <script>
            const box = document.querySelector('.box')
            let i = 1

            //数值加1
            function mouseMove() {
                box.innerHTML = ++i
            }

            // 防抖函数
            function debounce(fn, t) {
                let timeId
                return function () {
                    // 如果有定时器就清除
                    if (timeId) clearTimeout(timeId)

                    // 开启定时器
                    timeId = setTimeout(function () {
                        fn()
                    }, t)
                }
            }
            //鼠标移动触发函数
            box.addEventListener('mousemove', debounce(mouseMove, 1000))
        </script>
    </body>

    </html>
    ```

## 4.2 节流
- 所谓节流，就是指连续触发事件但是在 n 秒中只执行一次函数
- 使用场景： 鼠标移动，页面尺寸发生变化，滚动条滚动等开销比较大的情况下

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>节流</title>
        <style>
            .box {
                width: 500px;
                height: 500px;
                background-color: #ccc;
                color: #fff;
                text-align: center;
                font-size: 100px;
            }
        </style>
    </head>

    <body>
        <div class="box"></div>
        <script>
            const box = document.querySelector('.box')
            let i = 1

            //数值加1
            function mouseMove() {
                box.innerHTML = ++i
            }

            // 节流函数
            function throttle(fn, t) {
                // 起始时间
                let startTime = 0
                return function () {
                    // 得到当前的时间
                    let now = Date.now()

                    // 判断如果大于等于 500 调用函数
                    if (now - startTime >= t) {
                        // 调用函数
                        fn()

                        // 起始的时间 = 现在的时间
                        startTime = now
                    }
                }
            }
            box.addEventListener('mousemove', throttle(mouseMove, 1000))
        </script>
    </body>

    </html>
    ```

## 4.3 lodash节流和防抖
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>lodash节流和防抖</title>
    <style>
        .box {
            width: 500px;
            height: 500px;
            background-color: #ccc;
            color: #fff;
            text-align: center;
            font-size: 100px;
        }
    </style>
</head>

<body>
    <div class="box"></div>
    <script src="./lodash.min.js"></script>
    <script>
        const box = document.querySelector('.box')
        let i = 1

        //数值加1
        function mouseMove() {
            box.innerHTML = ++i
        }

        // lodash 节流
        // box.addEventListener('mousemove', _.throttle(mouseMove, 500))
        // lodash 防抖
        box.addEventListener('mousemove', _.debounce(mouseMove, 500))
    </script>
</body>

</html>
```
