---
layout: post
title: "Web APIs 二、DOM事件基础"
author: "Kai"
header-style: text
tags:
  - Browser
  - DOM
---

转自：https://juejin.cn/post/7209960560666034213

# 1. 事件监听
## 1.1 事件
- 事件是在编程时系统内发生的**动作**或者发生的事情
- 比如用户在网页上**单击**一个按钮

## 1.2 事件监听
- 就是让程序检测是否有事件产生，一旦有事件触发，就立即调用一个函数做出响应，也称为 绑定事件或者注册事件
- 比如鼠标经过显示下拉菜单，比如点击可以播放轮播图等等
- 语法：`元素对象.addEventListener('事件类型'，要执行的函数)`
- 事件监听三要素：
  - **事件源**：哪个dom元素被事件触发了，要获取dom元素
  - **事件类型**：用什么方式触发，比如鼠标单击 click、鼠标经过 mouseover 等
  - **事件调用的函数**：要做什么事
- 注意：
  - 事件类型要加引号
  - 函数是点击之后再去执行，每次点击都会执行一次

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>事件监听</title>
    </head>

    <body>
        <button>点击</button>
        <script>
            // 需求： 点击了按钮，弹出一个对话框
            // 1. 事件源   按钮  
            // 2. 事件类型 点击鼠标   click 字符串
            // 3. 事件处理程序 弹出对话框
            const btn = document.querySelector('button')
            btn.addEventListener('click', function() {
                alert('你早呀~')
            })
        </script>
    </body>

    </html>
    ```

    **广告点击关闭：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>广告点击关闭</title>
        <style>
            .box {
                position: relative;
                width: 1000px;
                height: 200px;
                background-color: pink;
                margin: 100px auto;
                text-align: center;
                font-size: 50px;
                line-height: 200px;
                font-weight: 700;
            }
            
            .box1 {
                position: absolute;
                right: 20px;
                top: 10px;
                width: 20px;
                height: 20px;
                background-color: skyblue;
                text-align: center;
                line-height: 20px;
                font-size: 16px;
                cursor: pointer;
            }
        </style>
    </head>

    <body>
        <div class="box">
            我是广告
            <div class="box1">X</div>
        </div>
        <script>
            // 1. 获取事件源
            const box1 = document.querySelector('.box1')
                //  关闭的是大盒子
            const box = document.querySelector('.box')
                // 2. 事件侦听
            box1.addEventListener('click', function() {
                box.style.display = 'none'
            })
        </script>
    </body>

    </html>
    ```

- **随机点名案例：**
    ![20241010220114](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010220114.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>随机点名案例</title>
        <style>
            * {
                margin: 0;
                padding: 0;
            }
            
            h2 {
                text-align: center;
            }
            
            .box {
                width: 600px;
                margin: 50px auto;
                display: flex;
                font-size: 25px;
                line-height: 40px;
            }
            
            .qs {
                width: 450px;
                height: 40px;
                color: red;
            }
            
            .btns {
                text-align: center;
            }
            
            .btns button {
                width: 120px;
                height: 35px;
                margin: 0 50px;
            }
        </style>
    </head>

    <body>
        <h2>随机点名</h2>
        <div class="box">
            <span>名字是：</span>
            <div class="qs">这里显示姓名</div>
        </div>
        <div class="btns">
            <button class="start">开始</button>
            <button class="end">结束</button>
        </div>

        <script>
            // 数据数组
            const arr = ['马超', '黄忠', '赵云', '关羽', '张飞']
                // 定时器的全局变量
            let timerId = 0
                // 随机号要全局变量
            let random = 0
                // 业务1.开始按钮模块
            const qs = document.querySelector('.qs')
                // 1.1 获取开始按钮对象
            const start = document.querySelector('.start')
                // 1.2 添加点击事件
            start.addEventListener('click', function() {
                timerId = setInterval(function() {
                        // 随机数
                        random = parseInt(Math.random() * arr.length)
                            // console.log(arr[random])
                        qs.innerHTML = arr[random]
                    }, 35)
                    // 如果数组里面只有一个值了，还需要抽取吗？  不需要  让两个按钮禁用就可以
                if (arr.length === 1) {
                    // start.disabled = true
                    // end.disabled = true
                    start.disabled = end.disabled = true
                }
            })

            // 2. 关闭按钮模块
            const end = document.querySelector('.end')
            end.addEventListener('click', function() {
                clearInterval(timerId)
                    // 结束了，可以删除掉当前抽取的那个数组元素
                arr.splice(random, 1)
                console.log(arr)
            })
        </script>
    </body>

    </html>
    ```

## 1.3 事件监听版本
- DOM L0：`事件源.on事件 = function() { }`
- DOM L2：`事件源.addEventListener(事件, 事件处理函数)`
- 区别：on方式会被覆盖，addEventListener方式可绑定多次，拥有事件更多特性，推荐使用
- 发展史：
  - DOM L0 ：是 DOM 的发展的第一个版本； L：level
  - DOM L1：DOM级别1 于1998年10月1日成为W3C推荐标准
  - DOM L2：使用addEventListener注册事件
  - DOM L3： DOM3级事件模块在DOM2级事件的基础上重新定义了这些事件，也添加了一些新事件类型

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>事件监听版本</title>
    </head>

    <body>
        <button>点击</button>
        <script>
            const btn = document.querySelector('button')

            // on 绑定的后面的覆盖前面的
            // btn.onclick = function () {
            //   alert(11)
            // }
            // btn.onclick = function () {
            //   alert(22)
            // }

            //addEventListener 绑定的都会绑上去
            btn.addEventListener('click', function() {
                alert(11)
            })
            btn.addEventListener('click', function() {
                alert(22)
            })
        </script>
    </body>

    </html>
    ```

# 2. 事件类型
![20241010220333](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010220333.png)

## 2.1 鼠标事件
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>鼠标事件</title>
    <style>
        div {
            width: 200px;
            height: 200px;
            background-color: pink;
        }
    </style>
</head>

<body>
    <div></div>
    <script>
        const div = document.querySelector('div')
            // 鼠标经过
        div.addEventListener('mouseenter', function() {
                console.log(`轻轻的我来了`)
            })
            // 鼠标离开
        div.addEventListener('mouseleave', function() {
            console.log(`轻轻的我走了`)
        })
    </script>
</body>

</html>
```

- **轮播图完整版：**
    ![20241010220419](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010220419.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>轮播图完整版</title>
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
            const data = [{
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
                // 获取元素
            const img = document.querySelector('.slider-wrapper img')
            const p = document.querySelector('.slider-footer p')
            const footer = document.querySelector('.slider-footer')
                // 1. 右按钮业务
                // 1.1 获取右侧按钮 
            const next = document.querySelector('.next')
            let i = 0 // 信号量 控制播放图片张数
                // 1.2 注册点击事件

            next.addEventListener('click', function() {
                // console.log(11)
                i++
                // 1.6判断条件  如果大于8 就复原为 0
                // if (i >= 8) {
                //   i = 0
                // }
                i = i >= data.length ? 0 : i
                    // 1.3 得到对应的对象
                    // console.log(data[i])
                    // 调用函数
                toggle()
            })

            // 2. 左侧按钮业务
            // 2.1 获取左侧按钮 
            const prev = document.querySelector('.prev')
                // 1.2 注册点击事件
            prev.addEventListener('click', function() {
                i--
                // 判断条件  如果小于0  则爬到最后一张图片索引号是 7
                // if (i < 0) {
                //   i = 7
                // }
                i = i < 0 ? data.length - 1 : i
                    // 1.3 得到对应的对象
                    // console.log(data[i])
                    // 调用函数
                toggle()
            })

            // 声明一个渲染的函数作为复用
            function toggle() {
                // 1.4 渲染对应的数据
                img.src = data[i].url
                p.innerHTML = data[i].title
                footer.style.backgroundColor = data[i].color
                    // 1.5 更换小圆点    先移除原来的类名， 当前li再添加 这个 类名
                document.querySelector('.slider-indicator .active').classList.remove('active')
                document.querySelector(`.slider-indicator li:nth-child(${i + 1})`).classList.add('active')
            }

            // 3. 自动播放模块
            let timerId = setInterval(function() {
                // 利用js自动调用点击事件  click()  一定加小括号调用函数
                next.click()
            }, 1000)


            // 4. 鼠标经过大盒子，停止定时器
            const slider = document.querySelector('.slider')
                // 注册事件
            slider.addEventListener('mouseenter', function() {
                // 停止定时器
                clearInterval(timerId)
            })

            // 5. 鼠标离开大盒子，开启定时器
            // 注册事件
            slider.addEventListener('mouseleave', function() {
                // 停止定时器
                if (timerId) clearInterval(timerId)
                    // 开启定时器
                timerId = setInterval(function() {
                    // 利用js自动调用点击事件  click()  一定加小括号调用函数
                    next.click()
                }, 1000)
            })
        </script>
    </body>

    </html>
    ```

## 2.2 焦点事件
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>焦点事件</title>
</head>

<body>
    <input type="text">
    <script>
        const input = document.querySelector('input')
        input.addEventListener('focus', function() {
            console.log('有焦点触发')
        })
        input.addEventListener('blur', function() {
            console.log('失去焦点触发')
        })
    </script>
</body>

</html>
```

- **小米搜索框：**
    ![20241010220520](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010220520.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>小米搜索框</title>
        <style>
            * {
                margin: 0;
                padding: 0;
                box-sizing: border-box;
            }
            
            ul {
                list-style: none;
            }
            
            .mi {
                position: relative;
                width: 223px;
                margin: 100px auto;
            }
            
            .mi input {
                width: 223px;
                height: 48px;
                padding: 0 10px;
                font-size: 14px;
                line-height: 48px;
                border: 1px solid #e0e0e0;
                outline: none;
            }
            
            .mi .search {
                border: 1px solid #ff6700;
            }
            
            .result-list {
                display: none;
                position: absolute;
                left: 0;
                top: 48px;
                width: 223px;
                border: 1px solid #ff6700;
                border-top: 0;
                background: #fff;
            }
            
            .result-list a {
                display: block;
                padding: 6px 15px;
                font-size: 12px;
                color: #424242;
                text-decoration: none;
            }
            
            .result-list a:hover {
                background-color: #eee;
            }
        </style>

    </head>

    <body>
        <div class="mi">
            <input type="search" placeholder="小米笔记本">
            <ul class="result-list">
                <li><a href="#">全部商品</a></li>
                <li><a href="#">小米11</a></li>
                <li><a href="#">小米10S</a></li>
                <li><a href="#">小米笔记本</a></li>
                <li><a href="#">小米手机</a></li>
                <li><a href="#">黑鲨4</a></li>
                <li><a href="#">空调</a></li>
            </ul>
        </div>
        <script>
            // 1. 获取元素
            const input = document.querySelector('[type=search]')
            const ul = document.querySelector('.result-list')
                // console.log(input)
                // 2. 监听事件 获得焦点
            input.addEventListener('focus', function() {
                    // ul显示
                    ul.style.display = 'block'
                        // 添加一个带有颜色边框的类名
                    input.classList.add('search')
                })
                // 3. 监听事件 失去焦点
            input.addEventListener('blur', function() {
                ul.style.display = 'none'
                input.classList.remove('search')
            })
        </script>
    </body>

    </html>
    ```

## 2.3 键盘事件
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>键盘事件</title>
</head>

<body>
    <input type="text">
    <script>
        const input = document.querySelector('input')
            //  键盘事件
        input.addEventListener('keydown', function() {
            console.log('键盘按下了')
        })
        input.addEventListener('keyup', function() {
            console.log('键盘谈起了')
        })
    </script>
</body>

</html>
```

## 2.4 文本事件
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>文本事件</title>
</head>

<body>
    <input type="text">
    <script>
        const input = document.querySelector('input')
        // 用户输入文本事件  input
        input.addEventListener('input', function() {
            console.log(input.value)
        })
    </script>
</body>

</html>
```

# 3. 事件对象
## 3.1 事件对象
- 事件对象
  - 也是个对象，这个对象里有事件触发时的相关信息
  - 例如：鼠标点击事件中，事件对象就存了鼠标点在哪个位置等信息
- 使用场景
  - 可以判断用户按下哪个键，比如按下回车键可以发布新闻
  - 可以判断鼠标点击了哪个元素，从而做相应的操作

## 3.2 如何获取
- 在事件绑定的回调函数的第一个参数就是事件对象
- 一般命名为event、ev、e
    ![20241010220714](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010220714.png)

## 3.3 事件对象常用属性
- type：获取当前的事件类型
- clientX/clientY：获取光标相对于浏览器可见窗口左上角的位置
- offsetX/offsetY：获取光标相对于当前DOM元素左上角的位置
- key：用户按下的键盘键的值，现在不提倡使用keyCode

## 3.4 案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>事件对象</title>
</head>

<body>
    <button>点击</button>
    <input type="text">
    <script>
        const btn = document.querySelector('button')
        btn.addEventListener('click', function(e) {
            console.log(e)
        })

        const input = document.querySelector('input')
        input.addEventListener('keyup', function(e) {
            if (e.key === 'Enter') {
                console.log('我按下了回车键')
            }
        })
    </script>
</body>

</html>
```

- **回车发布评论：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>回车发布评论</title>
        <style>
            .wrapper {
                min-width: 400px;
                max-width: 800px;
                display: flex;
                justify-content: flex-end;
            }
            
            .avatar {
                width: 48px;
                height: 48px;
                border-radius: 50%;
                overflow: hidden;
                background: url(./images/avatar.jpg) no-repeat center / cover;
                margin-right: 20px;
            }
            
            .wrapper textarea {
                outline: none;
                border-color: transparent;
                resize: none;
                background: #f5f5f5;
                border-radius: 4px;
                flex: 1;
                padding: 10px;
                transition: all 0.5s;
                height: 30px;
            }
            
            .wrapper textarea:focus {
                border-color: #e4e4e4;
                background: #fff;
                height: 50px;
            }
            
            .wrapper button {
                background: #00aeec;
                color: #fff;
                border: none;
                border-radius: 4px;
                margin-left: 10px;
                width: 70px;
                cursor: pointer;
            }
            
            .wrapper .total {
                margin-right: 80px;
                color: #999;
                margin-top: 5px;
                opacity: 0;
                transition: all 0.5s;
            }
            
            .list {
                min-width: 400px;
                max-width: 800px;
                display: flex;
            }
            
            .list .item {
                width: 100%;
                display: flex;
            }
            
            .list .item .info {
                flex: 1;
                border-bottom: 1px dashed #e4e4e4;
                padding-bottom: 10px;
            }
            
            .list .item p {
                margin: 0;
            }
            
            .list .item .name {
                color: #FB7299;
                font-size: 14px;
                font-weight: bold;
            }
            
            .list .item .text {
                color: #333;
                padding: 10px 0;
            }
            
            .list .item .time {
                color: #999;
                font-size: 12px;
            }
        </style>
    </head>

    <body>
        <div class="wrapper">
            <i class="avatar"></i>
            <textarea id="tx" placeholder="发一条友善的评论" rows="2" maxlength="200"></textarea>
            <button>发布</button>
        </div>
        <div class="wrapper">
            <span class="total">0/200字</span>
        </div>
        <div class="list">
            <div class="item" style="display: none;">
                <i class="avatar"></i>
                <div class="info">
                    <p class="name">清风徐来</p>
                    <p class="text">大家都辛苦啦，感谢各位大大的努力，能圆满完成真是太好了[笑哭][支持]</p>
                    <p class="time">2022-10-10 20:29:21</p>
                </div>
            </div>
        </div>
        <script>
            const tx = document.querySelector('#tx')
            const total = document.querySelector('.total')
            const item = document.querySelector('.item')
            const text = document.querySelector('.text')
                // 1. 当我们文本域获得了焦点，就让 total 显示出来
            tx.addEventListener('focus', function() {
                    total.style.opacity = 1
                })
                // 2. 当我们文本域失去了焦点，就让 total 隐藏出来
            tx.addEventListener('blur', function() {
                    total.style.opacity = 0
                })
                // 3. 检测用户输入
            tx.addEventListener('input', function() {
                // console.log(tx.value.length)  得到输入的长度
                total.innerHTML = `${tx.value.length}/200字`
            })

            // 4. 按下回车发布评论
            tx.addEventListener('keyup', function(e) {
                // 只有按下的是回车键，才会触发
                // console.log(e.key)
                if (e.key === 'Enter') {
                    // 如果用户输入的不为空就显示和打印
                    if (tx.value.trim()) {
                        // console.log(11)
                        item.style.display = 'block'
                            // console.log(tx.value)  // 用户输入的内容
                        text.innerHTML = tx.value
                    }
                    // 等我们按下回车，结束，清空文本域
                    tx.value = ''
                        // 按下回车之后，就要把 字符统计 复原
                    total.innerHTML = '0/200字'
                }

            })
        </script>
    </body>

    </html>
    ```

# 4. 环境对象
- **环境对象**：指的是函数内部特殊的**变量this**。
- 对象调用的方法，this就指向调用对象。
- 直接调用函数，其实相当于是 window.函数，所以this指向window对象。
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>环境对象</title>
    </head>

    <body>
        <button>点击</button>
        <script>
            // 每个函数里面都有this 环境对象  直接调用函数里面this指向的是window
            function fn() {
            console.log(this)
            }
            fn()

            //哪个对象调用就是哪个对象
            const btn = document.querySelector('button')
            btn.addEventListener('click', function() {
                // console.log(this)  // btn 对象
                // btn.style.color = 'red'
                this.style.color = 'red'
            })
        </script>
    </body>

    </html>
    ```

# 5. 回调函数
- 如果将函数 A 做为参数传递给函数 B 时，我们称函数 A 为**回调函数**
- 简单理解： 当一个函数当做参数来传递给另外一个函数的时候，这个函数就是**回调函数**
    ![20241010220934](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010220934.png)

# 6. 综合案例
## 6.1 tab栏切换
![20241010220956](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010220956.png)

![20241010221002](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010221002.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>tab栏切换</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        
        .tab {
            width: 590px;
            height: 340px;
            margin: 20px;
            border: 1px solid #e4e4e4;
        }
        
        .tab-nav {
            width: 100%;
            height: 60px;
            line-height: 60px;
            display: flex;
            justify-content: space-between;
        }
        
        .tab-nav h3 {
            font-size: 24px;
            font-weight: normal;
            margin-left: 20px;
        }
        
        .tab-nav ul {
            list-style: none;
            display: flex;
            justify-content: flex-end;
        }
        
        .tab-nav ul li {
            margin: 0 20px;
            font-size: 14px;
        }
        
        .tab-nav ul li a {
            text-decoration: none;
            border-bottom: 2px solid transparent;
            color: #333;
        }
        
        .tab-nav ul li a.active {
            border-color: #e1251b;
            color: #e1251b;
        }
        
        .tab-content {
            padding: 0 16px;
        }
        
        .tab-content .item {
            display: none;
        }
        
        .tab-content .item.active {
            display: block;
        }
    </style>
</head>

<body>
    <div class="tab">
        <div class="tab-nav">
            <h3>每日特价</h3>
            <ul>
                <li><a class="active" href="javascript:;">精选</a></li>
                <li><a href="javascript:;">美食</a></li>
                <li><a href="javascript:;">百货</a></li>
                <li><a href="javascript:;">个护</a></li>
                <li><a href="javascript:;">预告</a></li>
            </ul>
        </div>
        <div class="tab-content">
            <div class="item active"><img src="./images/tab00.png" alt="" /></div>
            <div class="item"><img src="./images/tab01.png" alt="" /></div>
            <div class="item"><img src="./images/tab02.png" alt="" /></div>
            <div class="item"><img src="./images/tab03.png" alt="" /></div>
            <div class="item"><img src="./images/tab04.png" alt="" /></div>
        </div>
    </div>
    <script>
        // 1. a 模块制作 要给 5个链接绑定鼠标经过事件
        // 1.1 获取 a 元素 
        const as = document.querySelectorAll('.tab-nav a')
            // console.log(as) 
        for (let i = 0; i < as.length; i++) {
            // console.log(as[i])
            // 要给 5个链接绑定鼠标经过事件
            as[i].addEventListener('mouseenter', function() {
                // console.log('鼠标经过')
                // 排他思想  
                // 干掉别人 移除类active
                document.querySelector('.tab-nav .active').classList.remove('active')
                    // 我登基 我添加类 active  this 当前的那个 a 
                this.classList.add('active')

                // 下面5个大盒子 一一对应  .item 
                // 干掉别人
                document.querySelector('.tab-content .active').classList.remove('active')
                    // 对应序号的那个 item 显示 添加 active 类
                document.querySelector(`.tab-content .item:nth-child(${i + 1})`).classList.add('active')

            })
        }
    </script>
</body>

</html>
```

## 6.2 全选按钮案例
![20241010221100](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010221100.png)

![20241010221106](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241010221106.png)

```html
<!DOCTYPE html>
<html>

<head lang="en">
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>全选按钮案例</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        
        table {
            border-collapse: collapse;
            border-spacing: 0;
            border: 1px solid #c0c0c0;
            width: 500px;
            margin: 100px auto;
            text-align: center;
        }
        
        th {
            background-color: #09c;
            font: bold 16px "微软雅黑";
            color: #fff;
            height: 24px;
        }
        
        td {
            border: 1px solid #d0d0d0;
            color: #404060;
            padding: 10px;
        }
        
        .allCheck {
            width: 80px;
        }
    </style>
</head>

<body>
    <table>
        <tr>
            <th class="allCheck">
                <input type="checkbox" name="" id="checkAll"> <span class="all">全选</span>
            </th>
            <th>商品</th>
            <th>商家</th>
            <th>价格</th>
        </tr>
        <tr>
            <td>
                <input type="checkbox" name="check" class="ck">
            </td>
            <td>小米手机</td>
            <td>小米</td>
            <td>￥1999</td>
        </tr>
        <tr>
            <td>
                <input type="checkbox" name="check" class="ck">
            </td>
            <td>小米净水器</td>
            <td>小米</td>
            <td>￥4999</td>
        </tr>
        <tr>
            <td>
                <input type="checkbox" name="check" class="ck">
            </td>
            <td>小米电视</td>
            <td>小米</td>
            <td>￥5999</td>
        </tr>
    </table>
    <script>
        // 1. 获取大复选框
        const checkAll = document.querySelector('#checkAll')
            // 2. 获取所有的小复选框
        const cks = document.querySelectorAll('.ck')
            // 3. 点击大复选框  注册事件
        checkAll.addEventListener('click', function() {
                // 得到当前大复选框的选中状态
                // console.log(checkAll.checked)  // 得到 是 true 或者是 false
                // 4. 遍历所有的小复选框 让小复选框的checked  =  大复选框的 checked
                for (let i = 0; i < cks.length; i++) {
                    cks[i].checked = this.checked
                }
            })
            // 5. 小复选框控制大复选框

        for (let i = 0; i < cks.length; i++) {
            // 5.1 给所有的小复选框添加点击事件
            cks[i].addEventListener('click', function() {
                // 判断选中的小复选框个数 是不是等于  总的小复选框个数
                // 一定要写到点击里面，因为每次要获得最新的个数
                // console.log(document.querySelectorAll('.ck:checked').length)
                // console.log(document.querySelectorAll('.ck:checked').length === cks.length)
                checkAll.checked = document.querySelectorAll('.ck:checked').length === cks.length
            })
        }
    </script>
</body>

</html>
```
