---
layout: post
title: "Web APIs 一、DOM获取元素"
author: "Kai"
header-style: text
tags:
  - Browser
  - DOM
---

转自：https://juejin.cn/post/7209960560665985061

# 1. 基本认知
## 1.1 作用和分类
- 作用: 就是使用 JS 去操作 html 和浏览器
- 分类：DOM (文档对象模型)、BOM（浏览器对象模型）
  ![20241010163555](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010163555.png)

## 1.2 DOM是什么
- DOM（Document Object Model——文档对象模型）是用来呈现以及与任意 HTML 或 XML文档交互的API
- 白话文：DOM是浏览器提供的一套专门用来 **操作网页内容** 的功能
- DOM作用：开发网页内容特效和实现用户交互

## 1.3 DOM树
- 将 HTML 文档以树状结构直观的表现出来，我们称之为文档树或 DOM 树
- 作用：文档树直观的体现了标签与标签之间的关系
  ![20241010163652](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010163652.png)

## 1.4 DOM对象
- DOM对象：浏览器根据html标签生成的 JS对象
  - 所有的标签属性都可以在这个对象上面找到
  - 修改这个对象的属性会自动映射到标签身上
- DOM的核心思想： 把网页内容当做对象来处理
- document 对象：
  - 是 DOM 里提供的一个**对象**
  - 它提供的属性和方法都是**用来访问和操作网页内容的**
    - 例：`document.write()`
  - 网页所有内容都在document里面

  ![20241010163813](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010163813.png)

  **案例如下：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>DOM对象</title>
  </head>

  <body>
      <div>123</div>
      <script>
          const div = document.querySelector('div')
          console.dir(div) // dom 对象
      </script>
  </body>

  </html>
  ```

# 2. 获取DOM对象
## 2.1 根据CSS选择器来获取DOM元素
- 选择匹配的第一个元素：`document.querySelector('css选择器')`
  - **参数**：包含一个或多个有效的CSS选择器 **字符串**
  - **返回值**：CSS选择器匹配的第一个**HTMLElement**对象。如果没有匹配到，则返回null。
- 选择匹配的多个元素：`document.querySelectorAll('css选择器')`
  - **参数**：包含一个或多个有效的CSS选择器 **字符串**
  - **返回值**：CSS选择器匹配的**NodeList 对象集合**
  - **伪数组**：得到的是一个伪数组，有长度有索引，但是没有 `pop()` `push()` 等数组方法

  **案例如下：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>获取DOM元素</title>
      <style>
          .box {
              width: 200px;
              height: 200px;
          }
      </style>
  </head>

  <body>
      <div class="box">123</div>
      <div class="box">abc</div>
      <p id="nav">导航栏</p>
      <ul class="nav">
          <li>测试1</li>
          <li>测试2</li>
          <li>测试3</li>
      </ul>
      <script>
          // 1. 获取匹配的第一个元素
          const box = document.querySelector('div')
          console.log(box)

          const box2 = document.querySelector('.box')
          console.log(box2)

          const nav = document.querySelector('#nav')
          console.log(nav)

          const li = document.querySelector('ul li:first-child')
          console.log(li)

          // 2. 获取匹配的全部元素
          const lis = document.querySelectorAll('ul li')
          console.log(lis)

          const lis2 = document.querySelectorAll('.nav li')
          console.log(lis2)
          for (let i = 0; i < lis2.length; i++) {
              console.log(lis2[i]) // 每一个小li对象
          }

          const p = document.querySelectorAll('#nav')
          console.log(p)
      </script>
  </body>

  </html>
  ```

## 2.2 其他获取DOM元素方法
![20241010164120](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010164120.png)

# 3. 操作元素内容
## 3.1 `元素.innerText` 属性
- 将文本内容 添加/更新 到任意标签位置
- 显示纯文本，不解析标签

  ![20241010164159](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010164159.png)

## 3.2 元素.innerHTML 属性
- 将文本内容 添加/更新 到任意标签位置
- 会解析标签，多标签建议使用模板字符

  ![20241010164228](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010164228.png)

## 3.3 案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>修改对象内容</title>
</head>

<body>
    <div class="box">我是文字的内容</div>
    <script>
        // 1. 获取元素
        const box = document.querySelector('.box')
        // 2. 修改文字内容  对象.innerText 属性
        console.log(box.innerText) // 获取文字内容
        box.innerText = '我是一个盒子' // 修改文字内容
        box.innerText = '<strong>我是一个盒子</strong>' // 不解析标签

        // 3. innerHTML 解析标签
        console.log(box.innerHTML)
        box.innerHTML = '<strong>我要更换</strong>'
    </script>
</body>

</html>
```

**随机抽奖案例：**
![20241010164309](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010164309.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>随机抽奖案例</title>
    <style>
        .wrapper {
            width: 840px;
            height: 420px;
            background: url(./images/bg01.jpg) no-repeat center / cover;
            padding: 100px 250px;
            box-sizing: border-box;
        }
    </style>
</head>

<body>
    <div class="wrapper">
        <strong>年会抽奖</strong>
        <h1>一等奖：<span id="one">???</span></h1>
        <h3>二等奖：<span id="two">???</span></h3>
        <h5>三等奖：<span id="three">???</span></h5>
    </div>
    <script>
        // 1.声明数组
        const personArr = ['周杰伦', '刘德华', '周星驰', 'Pink老师', '张学友']
            // 2. 先做一等奖
            // 2.1 随机数 数组的下标
        const random = Math.floor(Math.random() * personArr.length)
            // console.log(personArr[random])
            // 2.2 获取one 元素 
        const one = document.querySelector('#one')
            // 2.3 把名字给 one
        one.innerHTML = personArr[random]
            // 2.4 删除数组这个名字
        personArr.splice(random, 1)
            // console.log(personArr)


        // 3. 二等奖
        // 2.1 随机数 数组的下标
        const random2 = Math.floor(Math.random() * personArr.length)
            // console.log(personArr[random])
            // 2.2 获取one 元素 
        const two = document.querySelector('#two')
            // 2.3 把名字给 one
        two.innerHTML = personArr[random2]
            // 2.4 删除数组这个名字
        personArr.splice(random2, 1)

        // 4. 三等奖
        // 2.1 随机数 数组的下标
        const random3 = Math.floor(Math.random() * personArr.length)
            // console.log(personArr[random])
            // 2.2 获取one 元素 
        const three = document.querySelector('#three')
            // 2.3 把名字给 one
        three.innerHTML = personArr[random3]
            // 2.4 删除数组这个名字
        personArr.splice(random3, 1)
    </script>
</body>

</html>
```
![20241010164332](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010164332.png)

# 4. 操作元素属性
## 4.1 操作元素常用属性
- 还可以通过 JS 设置/修改 标签元素属性，比如通过 src更换 图片
- 最常见的属性比如： href、title、src 等
- 语法：`对象.属性 = 值`

  **案例如下：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>修改元素常见的属性</title>
  </head>

  <body>
      <img src="./images/1.webp" alt="">
      <script>
          // 1. 获取图片元素
          const img = document.querySelector('img')
              // 2. 修改图片对象的属性   对象.属性 = 值
          img.src = './images/2.webp'
          img.title = 'pink老师的艺术照'
      </script>
  </body>

  </html>
  ```

  **随机显示图片：**
  ![20241010164455](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010164455.png)
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>随机显示图片</title>
  </head>

  <body>
      <img src="./images/1.webp" alt="">
      <script>
          // 取到 N ~ M 的随机整数
          function getRandom(N, M) {
              return Math.floor(Math.random() * (M - N + 1)) + N
          }
          // 1. 获取图片对象
          const img = document.querySelector('img')
              // 2. 随机得到序号
          const random = getRandom(1, 6)
              // 3. 更换路径
          img.src = `./images/${random}.webp`
      </script>
  </body>

  </html>
  ```

## 4.2 操作元素样式属性
- 还可以通过 JS 设置/修改标签元素的样式属性。
  - 比如通过 轮播图小圆点自动更换颜色样式
  - 点击按钮可以滚动图片，这是移动的图片的位置 left 等等
- **通过 style 属性操作CSS**：`元素.style.样式属性 = 值`

  **通过style属性修改样式：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>通过style属性修改样式</title>
      <style>
          .box {
              width: 200px;
              height: 200px;
              background-color: pink;
          }
      </style>
  </head>

  <body>
      <div class="box"></div>
      <script>
          // 1. 获取元素
          const box = document.querySelector('.box')
              //2. 修改样式属性 对象.style.样式属性 = '值'  别忘了跟单位
          box.style.width = '300px'
              // 多组单词的采取 小驼峰命名法
          box.style.backgroundColor = 'hotpink'
          box.style.border = '2px solid blue'
          box.style.borderTop = '2px solid red'
      </script>
  </body>

  </html>
  ```

  **随机更换背景图片：**
  ![20241010164643](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010164643.png)
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>随机更换背景图片</title>
      <style>
          body {
              background: url(./images/desktop_1.jpg) no-repeat top center/cover;
          }
      </style>
  </head>

  <body>
      <script>
          // 取到 N ~ M 的随机整数
          function getRandom(N, M) {
              return Math.floor(Math.random() * (M - N + 1)) + N
          }
          // 随机数
          const random = getRandom(1, 10)
          document.body.style.backgroundImage = `url(./images/desktop_${random}.jpg)`
      </script>
  </body>

  </html>
  ```

- **操作类名(className) 操作CSS**：`元素.className = 类名`
  - 如果修改的样式比较多，直接通过style属性修改比较繁琐，我们可以通过借助于css类名的形式。
  - 注意：由于class是关键字, 所以使用className去代替，className是使用新值换旧值, 如果需要添加一个类,需要保留之前的类名

  **通过类名修改样式：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>通过类名修改样式</title>
      <style>
          div {
              width: 200px;
              height: 200px;
              background-color: pink;
          }
          
          .nav {
              color: red;
          }
          
          .box {
              width: 300px;
              height: 300px;
              background-color: skyblue;
              margin: 100px auto;
              padding: 10px;
              border: 1px solid #000;
          }
      </style>
  </head>

  <body>
      <div class="nav">123</div>
      <script>
          // 1. 获取元素
          const div = document.querySelector('div')
              // 2.添加类名  class 是个关键字 我们用 className
          div.className = 'nav box'
      </script>
  </body>

  </html>
  ```

- **通过 classList 操作类控制CSS**：
  - 为了解决className 容易覆盖以前的类名，我们可以通过classList方式追加和删除类名
  - 追加一个类：`元素.classList.add('类名')`
  - 移除一个类：`元素.classList.remove('类名')`
  - 切换一个类：`元素.classList.toggle('类名')`

  **通过classList修改样式：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>通过classList修改样式</title>
      <style>
          .box {
              width: 200px;
              height: 200px;
              color: #333;
          }
          
          .active {
              color: red;
              background-color: pink;
          }
      </style>
  </head>

  <body>
      <div class="box active">文字</div>
      <script>
          // 通过classList添加
          // 1. 获取元素
          const box = document.querySelector('.box')
              // 2. 修改样式
              // 2.1 追加类 add() 类名不加点，并且是字符串
              // box.classList.add('active')
              // 2.2 删除类  remove() 类名不加点，并且是字符串
              // box.classList.remove('box')
              // 2.3 切换类  toggle()  有就删掉，没有就加上
          box.classList.toggle('active')
      </script>
  </body>

  </html>
  ```

  **随机轮播图：**
  ![20241010165029](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010165029.png)
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>随机轮播图</title>
      <style>
          * {
              box-sizing: border-box;
          }
          
          .slider {
              width: 560px;
              height: 400px;
              overflow: hidden;
          }
          
          .slider-wrapper {
              width: 100%;
              height: 320px;
          }
          
          .slider-wrapper img {
              width: 100%;
              height: 100%;
              display: block;
          }
          
          .slider-footer {
              height: 80px;
              background-color: rgb(100, 67, 68);
              padding: 12px 12px 0 12px;
              position: relative;
          }
          
          .slider-footer .toggle {
              position: absolute;
              right: 0;
              top: 12px;
              display: flex;
          }
          
          .slider-footer .toggle button {
              margin-right: 12px;
              width: 28px;
              height: 28px;
              appearance: none;
              border: none;
              background: rgba(255, 255, 255, 0.1);
              color: #fff;
              border-radius: 4px;
              cursor: pointer;
          }
          
          .slider-footer .toggle button:hover {
              background: rgba(255, 255, 255, 0.2);
          }
          
          .slider-footer p {
              margin: 0;
              color: #fff;
              font-size: 18px;
              margin-bottom: 10px;
          }
          
          .slider-indicator {
              margin: 0;
              padding: 0;
              list-style: none;
              display: flex;
              align-items: center;
          }
          
          .slider-indicator li {
              width: 8px;
              height: 8px;
              margin: 4px;
              border-radius: 50%;
              background: #fff;
              opacity: 0.4;
              cursor: pointer;
          }
          
          .slider-indicator li.active {
              width: 12px;
              height: 12px;
              opacity: 1;
          }
      </style>
  </head>

  <body>
      <div class="slider">
          <div class="slider-wrapper">
              <img src="./images/slider01.jpg" alt="" />
          </div>
          <div class="slider-footer">
              <p>对人类来说会不会太超前了？</p>
              <ul class="slider-indicator">
                  <li></li>
                  <li></li>
                  <li></li>
                  <li></li>
                  <li></li>
                  <li></li>
                  <li></li>
                  <li></li>
              </ul>
              <div class="toggle">
                  <button class="prev">&lt;</button>
                  <button class="next">&gt;</button>
              </div>
          </div>
      </div>
      <script>
          // const arr = [1, 3]
          // arr[0]
          // 1. 初始数据
          const sliderData = [{
              url: './images/slider01.jpg',
              title: '对人类来说会不会太超前了？',
              color: 'rgb(100, 67, 68)'
          }, {
              url: './images/slider02.jpg',
              title: '开启剑与雪的黑暗传说！',
              color: 'rgb(43, 35, 26)'
          }, {
              url: './images/slider03.jpg',
              title: '真正的jo厨出现了！',
              color: 'rgb(36, 31, 33)'
          }, {
              url: './images/slider04.jpg',
              title: '李玉刚：让世界通过B站看到东方大国文化',
              color: 'rgb(139, 98, 66)'
          }, {
              url: './images/slider05.jpg',
              title: '快来分享你的寒假日常吧~',
              color: 'rgb(67, 90, 92)'
          }, {
              url: './images/slider06.jpg',
              title: '哔哩哔哩小年YEAH',
              color: 'rgb(166, 131, 143)'
          }, {
              url: './images/slider07.jpg',
              title: '一站式解决你的电脑配置问题！！！',
              color: 'rgb(53, 29, 25)'
          }, {
              url: './images/slider08.jpg',
              title: '谁不想和小猫咪贴贴呢！',
              color: 'rgb(99, 72, 114)'
          }, ]

          // 1. 需要一个随机数 
          const random = parseInt(Math.random() * sliderData.length)
              // console.log(sliderData[random])
              // 2. 把对应的数据渲染到标签里面
              // 2.1 获取图片
          const img = document.querySelector('.slider-wrapper img')
              // 2.2. 修改图片路径  =  对象.url
          img.src = sliderData[random].url
              // 3. 把p里面的文字内容更换
              // 3.1 获取p
          const p = document.querySelector('.slider-footer p')
              // 3.2修改p
          p.innerHTML = sliderData[random].title
              // 4. 修改背景颜色
          const footer = document.querySelector('.slider-footer')
          footer.style.backgroundColor = sliderData[random].color
              // 5. 小圆点
          const li = document.querySelector(`.slider-indicator li:nth-child(${random + 1})`)
              // 让当前这个小li 添加 active这个类
          li.classList.add('active')
      </script>
  </body>

  </html>
  ```

## 4.3 操作表单元素属性
- 表单很多场景也需要修改属性，比如点击眼睛，可以看到密码，本质是把表单类型转换为文本框
- 正常的有属性有取值的跟其他的标签属性没有任何区别
  - 获取: `DOM对象.属性名`
  - 设置: `DOM对象.属性名 = 新值`
- 获取设置表单元素的值不是用的`innertHTML`，用的是`value`
- 有些表单属性，添加就有效果，移除就没有效果，一律使用布尔值表示如果为true 代表添加了该属性，如果是false代表移除了该属性，比如： disabled、checked、selected

  **案例如下：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>修改表单属性</title>
  </head>

  <body>
      <input type="text" value="电脑">
      <input type="checkbox" name="" id="">
      <script>
          // 1 获取元素
          const uname = document.querySelector('input[type=text]')
              // 2. 获取值  获取表单里面的值 用的  表单.value
          console.log(uname.value) // 电脑
          console.log(uname.innerHTML) //innertHTML 得不到表单的内容
              // 3. 设置表单的值
          uname.value = '我要买电脑'
          console.log(uname.type)
          uname.type = 'password'

          // 1. 获取
          const ipt = document.querySelector('input[type=checkbox]')
              // console.log(ipt.checked)  // false   只接受布尔值
          ipt.checked = true
              // ipt.checked = 'true'  // 会选中，不提倡  有隐式转换

          // 1.获取
          const button = document.querySelector('button')
              // console.log(button.disabled)  // 默认false 不禁用
          button.disabled = true // 禁用按钮
      </script>
  </body>

  </html>
  ```

## 4.4 自定义属性
- **标准属性**: 标签天生自带的属性，比如class id title等，可以直接使用点语法操作比如： disabled、checked、selected
- **自定义属性**：
  - 在html5中推出来了专门的data-自定义属性
  - 在标签上一律以data-开头
  - 在DOM对象上一律以dataset对象方式获取

  **案例如下：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>自定义属性</title>
  </head>

  <body>
      <div data-id="1" data-spm="不知道">1</div>
      <div data-id="2">2</div>
      <div data-id="3">3</div>
      <div data-id="4">4</div>
      <div data-id="5">5</div>
      <script>
          const one = document.querySelector('div')
          console.log(one.dataset.id) // 1
          console.log(one.dataset.spm) // 不知道
      </script>
  </body>

  </html>
  ```

# 5. 定时器函数
## 5.1 开启定时器
- 语法：`setInterval(函数，间隔时间)`
- 作用：每隔一段时间调用这个函数
- 注意：
  - 函数名字**不需要加括号**
  - 间隔时间单位是**毫秒**
  - **定时器返回的是一个id数字**

## 5.2 关闭定时器
![20241010165409](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010165409.png)

## 5.3 案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>定时器函数</title>
</head>

<body>
    <script>
        //具名函数，开启定时器，关闭定时器
        function fn() {
            console.log('一秒执行一次')
        }
        // setInterval(函数名, 间隔时间)  函数名不要加小括号
        let n = setInterval(fn, 1000)
        console.log(n)
            // 关闭定时器
        clearInterval(n)

        //匿名函数，开启定时器
        let m = setInterval(function() {
            console.log('一秒执行一次')
        }, 1000)
        clearInterval(m)
    </script>
</body>

</html>
```

**用户注册倒计时：**
![20241010165459](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010165459.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>用户注册倒计时</title>
</head>

<body>
    <textarea name="" id="" cols="30" rows="10">
        用户注册协议
        欢迎注册成为京东用户！在您注册过程中，您需要完成我们的注册流程并通过点击同意的形式在线签署以下协议，请您务必仔细阅读、充分理解协议中的条款内容后再点击同意（尤其是以粗体或下划线标识的条款，因为这些条款可能会明确您应履行的义务或对您的权利有所限制）。
        【请您注意】如果您不同意以下协议全部或任何条款约定，请您停止注册。您停止注册后将仅可以浏览我们的商品信息但无法享受我们的产品或服务。如您按照注册流程提示填写信息，阅读并点击同意上述协议且完成全部注册流程后，即表示您已充分阅读、理解并接受协议的全部内容，并表明您同意我们可以依据协议内容来处理您的个人信息，并同意我们将您的订单信息共享给为完成此订单所必须的第三方合作方（详情查看
    </textarea>
    <br>
    <button class="btn" disabled>我已经阅读用户协议(5)</button>
    <script>
        // 1. 获取元素
        const btn = document.querySelector('.btn')
            // console.log(btn.innerHTML)  butto按钮特殊用innerHTML
            // 2. 倒计时
        let i = 60
            // 2.1 开启定时器
        let n = setInterval(function() {
            i--
            btn.innerHTML = `我已经阅读用户协议(${i})`
            if (i === 0) {
                clearInterval(n) // 关闭定时器
                    // 定时器停了，我就可以开按钮
                btn.disabled = false
                btn.innerHTML = '同意'
            }
        }, 1000)
    </script>
</body>

</html>
```

**下班倒计时：**
![20241010165550](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010165550.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>下班倒计时</title>
    <style>
        .countdown {
            width: 240px;
            height: 305px;
            text-align: center;
            line-height: 1;
            color: #fff;
            background-color: brown;
            /* background-size: 240px; */
            /* float: left; */
            overflow: hidden;
        }
        
        .countdown .next {
            font-size: 16px;
            margin: 25px 0 14px;
        }
        
        .countdown .title {
            font-size: 33px;
        }
        
        .countdown .tips {
            margin-top: 80px;
            font-size: 23px;
        }
        
        .countdown small {
            font-size: 17px;
        }
        
        .countdown .clock {
            width: 142px;
            margin: 18px auto 0;
            overflow: hidden;
        }
        
        .countdown .clock span,
        .countdown .clock i {
            display: block;
            text-align: center;
            line-height: 34px;
            font-size: 23px;
            float: left;
        }
        
        .countdown .clock span {
            width: 34px;
            height: 34px;
            border-radius: 2px;
            background-color: #303430;
        }
        
        .countdown .clock i {
            width: 20px;
            font-style: normal;
        }
    </style>
</head>

<body>
    <div class="countdown">
        <p class="next">今天是9999年9月9日</p>
        <p class="title">下班倒计时</p>
        <p class="clock">
            <span id="hour">00</span>
            <i>:</i>
            <span id="minutes">25</span>
            <i>:</i>
            <span id="scond">20</span>
        </p>
        <p class="tips">17:30:00下班</p>
    </div>
    <script>
        // 随机颜色函数
        // 1. 自定义一个随机颜色函数
        function getRandomColor(flag = true) {
            if (flag) {
                // 3. 如果是true 则返回 #ffffff
                let str = '#'
                let arr = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f']
                    // 利用for循环随机抽6次 累加到 str里面
                for (let i = 1; i <= 6; i++) {
                    // 每次要随机从数组里面抽取一个  
                    // random 是数组的索引号 是随机的
                    let random = Math.floor(Math.random() * arr.length)
                        // str = str + arr[random]
                    str += arr[random]
                }
                return str

            } else {
                // 4. 否则是 false 则返回 rgb(255,255,255)
                let r = Math.floor(Math.random() * 256) // 55
                let g = Math.floor(Math.random() * 256) // 89
                let b = Math.floor(Math.random() * 256) // 255
                return `rgb(${r},${g},${b})`
            }
        }

        //获取今天日期
        function getMyDate() {
            const date = new Date()
            let h = date.getHours()
            let m = date.getMinutes()
            let s = date.getSeconds()
            h = h < 10 ? '0' + h : h
            m = m < 10 ? '0' + m : m
            s = s < 10 ? '0' + s : s
            return `今天是: ${date.getFullYear()}年${date.getMonth() + 1}月${date.getDate()}号`
        }

        // 页面刷新随机得到颜色
        const countdown = document.querySelector('.countdown')
        countdown.style.backgroundColor = getRandomColor()
            //绑定日期
        const next = document.querySelector('.next')
        next.innerText = getMyDate()

        // 函数封装 getCountTime
        function getCountTime() {
            // 1. 得到当前的时间戳
            const now = +new Date()
                // 2. 得到将来的时间戳
            const datenow = new Date()
            const str = `${datenow.getFullYear()}-${datenow.getMonth()+1}-${datenow.getDate()} 17:30:00`
            const last = +new Date(str)
                // console.log(now, last)
                // 3. 得到剩余的时间戳 count  记得转换为 秒数
            const count = (last - now) / 1000
                // console.log(count)
                // 4. 转换为时分秒
                // h = parseInt(总秒数 / 60 / 60 % 24)   //   计算小时
                // m = parseInt(总秒数 / 60 % 60);     //   计算分数
                // s = parseInt(总秒数 % 60);   
                // let d = parseInt(count / 60 / 60 / 24)               //   计算当前秒数
            let h = parseInt(count / 60 / 60 % 24)
            h = h < 10 ? '0' + h : h
            let m = parseInt(count / 60 % 60)
            m = m < 10 ? '0' + m : m
            let s = parseInt(count % 60)
            s = s < 10 ? '0' + s : s
            console.log(h, m, s)

            //  5. 把时分秒写到对应的盒子里面
            document.querySelector('#hour').innerHTML = h
            document.querySelector('#minutes').innerHTML = m
            document.querySelector('#scond').innerHTML = s
        }
        // 先调用一次
        getCountTime()

        // 开启定时器
        setInterval(getCountTime, 1000)
    </script>
</body>

</html>
```

# 6. 综合案例
![20241010165631](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010165631.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>轮播图定时版</title>
    <style>
        * {
            box-sizing: border-box;
        }
        
        .slider {
            width: 560px;
            height: 400px;
            overflow: hidden;
        }
        
        .slider-wrapper {
            width: 100%;
            height: 320px;
        }
        
        .slider-wrapper img {
            width: 100%;
            height: 100%;
            display: block;
        }
        
        .slider-footer {
            height: 80px;
            background-color: rgb(100, 67, 68);
            padding: 12px 12px 0 12px;
            position: relative;
        }
        
        .slider-footer .toggle {
            position: absolute;
            right: 0;
            top: 12px;
            display: flex;
        }
        
        .slider-footer .toggle button {
            margin-right: 12px;
            width: 28px;
            height: 28px;
            appearance: none;
            border: none;
            background: rgba(255, 255, 255, 0.1);
            color: #fff;
            border-radius: 4px;
            cursor: pointer;
        }
        
        .slider-footer .toggle button:hover {
            background: rgba(255, 255, 255, 0.2);
        }
        
        .slider-footer p {
            margin: 0;
            color: #fff;
            font-size: 18px;
            margin-bottom: 10px;
        }
        
        .slider-indicator {
            margin: 0;
            padding: 0;
            list-style: none;
            display: flex;
            align-items: center;
        }
        
        .slider-indicator li {
            width: 8px;
            height: 8px;
            margin: 4px;
            border-radius: 50%;
            background: #fff;
            opacity: 0.4;
            cursor: pointer;
        }
        
        .slider-indicator li.active {
            width: 12px;
            height: 12px;
            opacity: 1;
        }
    </style>
</head>

<body>
    <div class="slider">
        <div class="slider-wrapper">
            <img src="./images/slider01.jpg" alt="" />
        </div>
        <div class="slider-footer">
            <p>对人类来说会不会太超前了？</p>
            <ul class="slider-indicator">
                <li class="active"></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
            </ul>
            <div class="toggle">
                <button class="prev">&lt;</button>
                <button class="next">&gt;</button>
            </div>
        </div>
    </div>
    <script>
        // 1. 初始数据
        const sliderData = [{
                url: './images/slider01.jpg',
                title: '对人类来说会不会太超前了？',
                color: 'rgb(100, 67, 68)'
            }, {
                url: './images/slider02.jpg',
                title: '开启剑与雪的黑暗传说！',
                color: 'rgb(43, 35, 26)'
            }, {
                url: './images/slider03.jpg',
                title: '真正的jo厨出现了！',
                color: 'rgb(36, 31, 33)'
            }, {
                url: './images/slider04.jpg',
                title: '李玉刚：让世界通过B站看到东方大国文化',
                color: 'rgb(139, 98, 66)'
            }, {
                url: './images/slider05.jpg',
                title: '快来分享你的寒假日常吧~',
                color: 'rgb(67, 90, 92)'
            }, {
                url: './images/slider06.jpg',
                title: '哔哩哔哩小年YEAH',
                color: 'rgb(166, 131, 143)'
            }, {
                url: './images/slider07.jpg',
                title: '一站式解决你的电脑配置问题！！！',
                color: 'rgb(53, 29, 25)'
            }, {
                url: './images/slider08.jpg',
                title: '谁不想和小猫咪贴贴呢！',
                color: 'rgb(99, 72, 114)'
            }, ]
            // 1. 获取元素 
        const img = document.querySelector('.slider-wrapper img')
        const p = document.querySelector('.slider-footer p')
        let i = 0 // 信号量 控制图片的张数
            // 2. 开启定时器
            // console.log(sliderData[i])  拿到对应的对象啦
        setInterval(function() {
            i++
            // 无缝衔接位置  一共八张图片，到了最后一张就是 8， 数组的长度就是 8
            if (i >= sliderData.length) {
                i = 0
            }
            // console.log(i)
            // console.log(sliderData[i])
            // 更换图片路径  
            img.src = sliderData[i].url
                // 把字写到 p里面
            p.innerHTML = sliderData[i].title
                // 小圆点
                // 先删除以前的active
            document.querySelector('.slider-indicator .active').classList.remove('active')
                // 只让当前li添加active
            document.querySelector(`.slider-indicator li:nth-child(${i + 1})`).classList.add('active')
        }, 1000)
    </script>
</body>

</html>
```
