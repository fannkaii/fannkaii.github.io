---
layout: post
title: "Web APIs 五、BOM操作浏览器"
author: "Kai"
header-style: text
tags:
  - Browser
  - BOM
---

转自：https://juejin.cn/post/7209967260898164794

# 1. Window对象
## 1.1 BOM(浏览器对象模型)
- BOM(Browser Object Model) 是浏览器对象模型
- window对象是一个全局对象，也可以说是JavaScript中的顶级对象
- 像`document`、`alert()`、`console.log()`这些都是window的属性，基本BOM的属性和方法都是window的。
- 所有通过var定义在全局作用域中的变量、函数都会变成window对象的属性和方法
- window对象下的属性和方法调用的时候可以省略window

    ![20241015165709](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015165709.png)

- 案例如下：
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>BOM</title>
    </head>

    <body>
        <script>
            // document是window对象的对象属性
            console.log(document === window.document)
            
            // fn是window的方法
            function fn() {
                console.log(11)
            }
            window.fn()

            // num是window对象的属性
            var num = 10
            console.log(window.num)
        </script>
    </body>

    </html>
    ```

## 1.2 JS执行机制
- **单线程和多线程**
  - JavaScript 语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。
  - 这是因为 Javascript 这门脚本语言诞生的使命所致——JavaScript 是为处理页面中用户的交互，以及操作DOM 而诞生的。比如我们对某个 DOM 元素进行添加和删除操作，不能同时进行。 应该先进行添加，之后再删除。
  - 单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。这样所导致的问题是：如果 JS 执行的时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞的感觉。
  - 为了解决这个问题，利用多核 CPU 的计算能力，HTML5 提出 Web Worker 标准，允许 JavaScript 脚本创建多个线程。于是，JS 中出现了同步和异步。
- **同步和异步**
  - **同步**：前一个任务结束后再执行后一个任务，程序的执行顺序与任务的排列顺序是一致的、同步的。比如做饭的同步做法：我们要烧水煮饭，等水开了（10分钟之后），再去切菜，炒菜。
  - **异步**：你在做一件事情时，因为这件事情会花费很长时间，在做这件事的同时，你还可以去处理其他事情。比如做饭的异步做法，我们在烧水的同时，利用这10分钟，去切菜，炒菜。
  - **他们的本质区别**：**这条流水线上各个流程的执行顺序不同。**
- **同步任务和异步任务**
  - **同步任务**：同步任务都在主线程上执行，形成一个**执行栈**。
  - **异步任务**：JS 的异步是通过回调函数实现的。一般而言，异步任务有三种类型:
    1. 普通事件，如 click、resize 等
    2. 资源加载，如 load、error 等
    3. 定时器，包括 setInterval、setTimeout 等
  - 异步任务相关添加到**任务队列**中（任务队列也称为消息队列）。

- **JS执行机制**：由于主线程不断的重复获得任务、执行任务、再获取任务、再执行，所以这种机制被称为事件循环（event loop）。
  - 先执行执行栈中的同步任务。
  - 异步任务放入任务队列中。
  - 一旦执行栈中的所有同步任务执行完毕，系统就会按次序读取任务队列中的异步任务，于是被读取的异步任务结束等待状态，进入执行栈，开始执行。

    ![20241015173031](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173031.png)

    ![20241015173043](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173043.png)

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>JS执行机制</title>
    </head>

    <body>
        <script>
            console.log(1)
            console.log(2)
            setTimeout(function() {
                console.log(3)
            }, 0)
            console.log(4)
            //结果 1 2 4 3
        </script>
    </body>

    </html>
    ```

## 1.3 location对象
- location 的数据类型是对象，它拆分并保存了 URL 地址的各个组成部分
- **常用属性和方法**：
  - **href** 属性获取完整的 URL 地址，对其赋值时用于地址的跳转
  - **search** 属性获取地址中携带的参数，符号 ？后面部分
  - **hash** 属性获取地址中的啥希值，符号 # 后面部分，后期vue路由的铺垫，经常用于不刷新页面，显示不同页面，比如 网易云音乐
  - **reload** 方法用来刷新当前页面，传入参数 true 时表示强制刷新

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>location对象</title>
    </head>

    <body>
        <button class="reload">刷新</button>

        <script>
            // location
            // console.log(window.location)
            // console.log(location)
            // console.log(location.href)        

            // 1. href 经常用href 利用js的方法去跳转页面
            // location.href = 'http://www.baidu.com'

            // 2.强制刷新
            const reload = document.querySelector('.reload')
            reload.addEventListener('click', function() {
                // f5 刷新页面
                // location.reload()
                // 强制刷新  ctrl+f5
                location.reload(true)
            })
        </script>
    </body>

    </html>
    ```

- **5秒钟之后自动跳转页面：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>5秒钟之后自动跳转页面</title>
        <style>
            span {
                color: red;
            }
        </style>
    </head>

    <body>
        <a href="https://www.baidu.com">支付成功<span>5</span>秒钟之后跳转到首页</a>
        <script>
            // 1. 获取元素
            const a = document.querySelector('a')
                // 2.开启定时器
                // 3. 声明倒计时变量
            let num = 5
            let timerId = setInterval(function() {
                num--
                a.innerHTML = `支付成功<span>${num}</span>秒钟之后跳转到首页`
                    // 如果num === 0 则停止定时器，并且完成跳转功能
                if (num === 0) {
                    clearInterval(timerId)
                        // 4. 跳转  location.href
                    location.href = 'https://www.baidu.com'
                }
            }, 1000)
        </script>
    </body>

    </html>
    ```

## 1.4 navigator对象
- navigator的数据类型是对象，该对象下记录了浏览器自身的相关信息
- **常用属性和方法**：
  - 通过 userAgent 检测浏览器的版本及平台
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>自动跳转到移动端</title>
        <script>
            // 检测 userAgent（浏览器信息）
            !(function() {
                const userAgent = navigator.userAgent
                    // 验证是否为Android或iPhone
                const android = userAgent.match(/(Android);?[\s\/]+([\d.]+)?/)
                const iphone = userAgent.match(/(iPhone\sOS)\s([\d_]+)/)
                    // 如果是Android或iPhone，则跳转至移动站点
                if (android || iphone) {
                    location.href = 'http://m.jd.com'
                }
            })()
        </script>
    </head>

    <body>
        这是pc端的页面
    </body>

    </html>
    ```

## 1.5 histroy对象
- history的数据类型是对象，主要管理历史记录，该对象与浏览器地址栏的操作相对应，如前进、后退、历史记录等
- **常用属性和方法**：
    ![20241015173342](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173342.png)
- history对象一般在实际开发中比较少用，但是会在一些OA办公系统中见到。
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>history对象</title>
    </head>

    <body>
        <button>后退</button>
        <button>前进</button>
        <script>
            const back = document.querySelector('button:first-child')
            const forward = back.nextElementSibling
            back.addEventListener('click', function() {
                // 后退一步
                // history.back()
                history.go(-1)
            })
            forward.addEventListener('click', function() {
                // 前进一步
                // history.forward()
                history.go(1)
            })
        </script>
    </body>

    </html>
    ```

# 2. 本地存储
## 2.1 本地存储介绍
- 以前我们页面写的数据一刷新页面就没有了，是不是？
- 随着互联网的快速发展，基于网页的应用越来越普遍，同时也变的越来越复杂，为了满足各种各样的需求，会经常性在本地存储大量的数据，HTML5规范提出了相关解决方案。
  1. 数据存储在用户浏览器中
  2. 设置、读取方便、甚至页面刷新不丢失数据
  3. 容量较大，sessionStorage和localStorage约 5M 左右

## 2.2 本地存储分类
- **作用**: 可以将数据永久存储在本地(用户的电脑), 除非手动删除，否则关闭页面也会存在
- **特性**：
  - 可以多窗口（页面）共享（同一浏览器可以共享）
  - 以键值对的形式存储使用
- **语法**：
  - **存储数据**：`localStorage.setItem(key, value)`，存在更新，不存在新增
  - **获取数据**：`localStorage.getItem(key)`
  - **删除数据**：`localStorage.removeItem(key)`

- **浏览器查看本地数据**：
    ![20241015173558](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173558.png)

- **特性**：
  - 生命周期为关闭浏览器窗口
  - 在同一个窗口(页面)下数据可以共享
  - 以键值对的形式存储使用
  - 用法跟localStorage 基本相同

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>localStorage存储</title>
    </head>

    <body>
        <script>
            // 1. 要存储一个名字  'uname'， 'pink老师'
            // localStorage.setItem('键'，'值')
            localStorage.setItem('uname', 'pink老师')
                // 2. 获取方式  都加引号
            console.log(localStorage.getItem('uname'))
                // 3. 删除本地存储  只删除名字
                // localStorage.removeItem('uname')
                // 4. 改  如果原来有这个键，则是改，如果没有这个键是增
            localStorage.setItem('uname', 'red老师')

            // 我要存一个年龄
            // 2. 本地存储只能存储字符串数据类型
            localStorage.setItem('age', 18)
            console.log(localStorage.getItem('age'))
        </script>
    </body>

    </html>
    ```

## 2.3 存取复杂数据类型
- **存储对象**
  - **问题**：本地只能存储字符串，无法存储复杂数据类型。
  - **解决**：需要将复杂数据类型转换成JSON字符串，再存储到本地
  - **语法**：`JSON.stringify(复杂数据类型)`

    ![20241015173731](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173731.png)

    ![20241015173753](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173753.png)

    ![20241015173807](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173807.png)

- **取出对象**
  - **问题**：因为本地存储里面取出来的是字符串，不是对象，无法直接使用
  - **解决**：把取出来的字符串转换为对象
  - **语法**：`JSON.parse(JSON字符串)`

    ![20241015173907](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173907.png)

    ![20241015173922](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173922.png)

    ![20241015173933](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015173933.png)

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>存储复杂数据类型</title>
    </head>

    <body>
        <script>
            const obj = {
                    uname: 'pink老师',
                    age: 18,
                    gender: '女'
                }
                // 存储 复杂数据类型  无法直接使用
                // localStorage.setItem('obj', obj)  [object object]
                // console.log(localStorage.getItem('obj'))//[object object]

            // 1.复杂数据类型存储必须转换为 JSON字符串存储
            localStorage.setItem('obj', JSON.stringify(obj))
                // JSON 对象  属性和值有引号，而是引号统一是双引号
                // {"uname":"pink老师","age":18,"gender":"女"}
                // 取出来的是字符串不是对象，需要转换
                // console.log(typeof localStorage.getItem('obj'))
                // 2. 把JSON字符串转换为 对象
            const str = localStorage.getItem('obj') // {"uname":"pink老师","age":18,"gender":"女"}
            console.log(JSON.parse(str))
        </script>
    </body>

    </html>
    ```

# 3. 综合案例
## 3.1 需求
![20241015174020](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174020.png)

## 3.2 模块分析
![20241015174035](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174035.png)

## 3.3 思路分析
![20241015174049](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174049.png)

## 3.4 步骤
![20241015174105](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174105.png)

![20241015174113](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174113.png)

![20241015174123](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174123.png)

![20241015174135](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174135.png)

![20241015174147](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174147.png)

![20241015174215](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174215.png)

![20241015174224](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174224.png)

![20241015174255](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174255.png)

## 3.5 代码
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>学生信息管理</title>
    <link rel="stylesheet" href="css/index.css" />
</head>

<body>
    <h1>新增学员</h1>
    <form class="info" autocomplete="off">
        姓名：<input type="text" class="uname" name="uname" /> 年龄：
        <input type="text" class="age" name="age" /> 性别:
        <select name="gender" class="gender">
      <option value="男">男</option>
      <option value="女">女</option>
    </select> 薪资：
        <input type="text" class="salary" name="salary" /> 就业城市：
        <select name="city" class="city">
      <option value="北京">北京</option>
      <option value="上海">上海</option>
      <option value="广州">广州</option>
      <option value="深圳">深圳</option>
      <option value="曹县">曹县</option>
    </select>
        <button class="add">录入</button>
    </form>

    <h1>就业榜</h1>
    <table>
        <thead>
            <tr>
                <th>学号</th>
                <th>姓名</th>
                <th>年龄</th>
                <th>性别</th>
                <th>薪资</th>
                <th>就业城市</th>
                <th>操作</th>
            </tr>
        </thead>
        <tbody>
            <!-- 
        <tr>
          <td>1001</td>
          <td>欧阳霸天</td>
          <td>19</td>
          <td>男</td>
          <td>15000</td>
          <td>上海</td>
          <td>
            <a href="javascript:">删除</a>
          </td>
        </tr> 
        -->
        </tbody>
    </table>
    <script>
        // 参考数据
        // const initData = [
        //   {
        //     stuId: 1001,
        //     uname: '欧阳霸天',
        //     age: 19,
        //     gender: '男',
        //     salary: '20000',
        //     city: '上海',
        //   }
        // ]

        // 1. 读取本地存储的数据   student-data  本地存储的命名
        const data = localStorage.getItem('student-data')
            // console.log(data)
            // 2. 如果有就返回对象，没有就声明一个空的数组  arr 一会渲染的时候用的
        const arr = data ? JSON.parse(data) : []

        // console.log(arr)
        // 获取 tbody
        const tbody = document.querySelector('tbody')
            // 3. 渲染模块函数
        function render() {
            // 遍历数组 arr，有几个对象就生成几个 tr，然后追击给tbody
            // map 返回的是个数组  [tr, tr]
            const trArr = arr.map(function(item, i) {
                    // console.log(item)
                    // console.log(item.uname)  // 欧阳霸天
                    return `
        <tr>
          <td>${item.stuId}</td>
          <td>${item.uname}</td>
          <td>${item.age}</td>
          <td>${item.gender}</td>
          <td>${item.salary}</td>
          <td>${item.city}</td>
          <td>
            <a href="javascript:" data-id=${i}>删除</a>
          </td>
        </tr> 
        `
                })
                // console.log(trArr)
                // 追加给tbody
                // 因为 trArr 是个数组， 我们不要数组，我们要的是 tr的字符串 join()
            tbody.innerHTML = trArr.join('')
        }
        render()


        // 4. 录入模块
        const info = document.querySelector('.info')
            // 获取表单form 里面带有 name属性的元素
        const items = info.querySelectorAll('[name]')
            // console.log(items)
        info.addEventListener('submit', function(e) {
            // 阻止提交
            e.preventDefault()
                // 声明空的对象
            const obj = {}
                // obj.stuId = arr.length + 1
                // 加入有2条数据   2 
            obj.stuId = arr.length ? arr[arr.length - 1].stuId + 1 : 1
                // 非空判断
            for (let i = 0; i < items.length; i++) {
                // console.log(items) // 数组里面包含 5个表单  name
                // console.log(items[i]) //  每一个表单 对象
                // console.log(items[i].name) //  
                // item 是每一个表单
                const item = items[i]
                if (items[i].value === '') {
                    return alert('输入内容不能为空')
                }
                // console.log(item.name)  uname  age gender
                // obj[item.name]   === obj.uname  obj.age 
                obj[item.name] = item.value
            }
            // console.log(obj)
            // 追加给数组
            arr.push(obj)
                //  把数组 arr 存储到本地存储里面
            localStorage.setItem('student-data', JSON.stringify(arr))
                // 渲染页面
            render()
                // 重置表单
            this.reset()
        })


        // 5. 删除模块
        tbody.addEventListener('click', function(e) {
            if (e.target.tagName === 'A') {
                // alert(1)
                // console.log(e.target.dataset.id)
                // 删除数组对应的这个数据
                arr.splice(e.target.dataset.id, 1)
                    // 写入本地存储
                localStorage.setItem('student-data', JSON.stringify(arr))
                    // 重新渲染
                render()
            }
        })
    </script>
</body>

</html>
```
