---
layout: post
title: "Web APIs 三、DOM事件进阶"
author: "Kai"
header-style: text
tags:
  - Browser
  - DOM
---

转自：https://juejin.cn/post/7209964630159818807

# 1. 事件流
## 1.1 事件流和两个阶段说明
- **事件流**指的是事件完整执行过程中的流动路径
- 假设页面里有个div，当触发事件时，会经历两个阶段，分别是捕获阶段、冒泡阶段
- 简单来说：捕获阶段是 从父到子 冒泡阶段是从子到父
- **实际工作都是使用事件冒泡为主**
    ![20241011221402](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011221402.png)

## 1.2 事件捕获
- **事件捕获概念**：从DOM的根元素开始去执行对应的事件 (从外到里)
- 事件捕获需要写对应代码才能看到效果
- 语法：`DOM.addEventListener(事件类型, 事件处理函数, 是否使用捕获机制)`
- addEventListener第三个参数传入 `true` 代表是捕获阶段触发（很少使用）
- 若传入`false`代表冒泡阶段触发，默认就是`false`
- 若是用 L0 事件监听，则只有冒泡阶段，没有捕获

## 1.3 事件冒泡
- **事件冒泡概念**: 当一个元素的事件被触发时，同样的事件将会在该元素的所有祖先元素中依次被触发。这一过程被称为事件冒泡
- 简单理解：当一个元素触发事件后，会依次向上调用所有父级元素的 **同名事件**
- 事件冒泡是默认存在的，L2事件监听第三个参数是 `false`，或者默认都是冒泡
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>事件流</title>
        <style>
            .father {
                width: 500px;
                height: 500px;
                background-color: pink;
            }
            
            .son {
                width: 200px;
                height: 200px;
                background-color: purple;
            }
        </style>
    </head>

    <body>
        <div class="father">
            <div class="son"></div>
        </div>
        <script>
            const fa = document.querySelector('.father')
            const son = document.querySelector('.son')

            document.addEventListener('click', function() {
                alert('我是爷爷')
            }, true)
            fa.addEventListener('click', function() {
                alert('我是爸爸')
            }, true)
            son.addEventListener('click', function() {
                alert('我是儿子')
            }, true)
        </script>
    </body>

    </html>
    ```

## 1.4 阻止冒泡、默认行为
- **问题**：因为默认就有冒泡模式的存在，所以容易导致事件影响到父级元素
- **需求**：若想把事件就限制在当前元素内，就需要阻止事件冒泡
- **前提**：阻止事件冒泡需要拿到事件对象
- `事件对象.stopPropagation()`：此方法可以阻断事件流动传播，不光在冒泡阶段有效，捕获阶段也有效
- `e.preventDefault()`：**我们某些情况下需要**阻止默认行为的发生，比如 阻止 链接的跳转，表单域跳转
- **阻止冒泡：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>阻止冒泡</title>
        <style>
            .father {
                width: 500px;
                height: 500px;
                background-color: pink;
            }
            
            .son {
                width: 200px;
                height: 200px;
                background-color: purple;
            }
        </style>
    </head>

    <body>
        <div class="father">
            <div class="son"></div>
        </div>
        <script>
            const fa = document.querySelector('.father')
            const son = document.querySelector('.son')
            document.addEventListener('click', function() {
                alert('我是爷爷')
            })
            fa.addEventListener('click', function() {
                alert('我是爸爸')
            })
            son.addEventListener('click', function(e) {
                alert('我是儿子')
                    // 阻止流动传播  事件对象.stopPropagation()
                e.stopPropagation()
            })
        </script>
    </body>

    </html>
    ```

- **阻止默认行为：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>阻止默认行为</title>
    </head>

    <body>
        <form action="http://www.itcast.cn">
            <input type="submit" value="免费注册">
        </form>
        <a href="http://www.baidu.com">百度一下</a>
        <script>
            const form = document.querySelector('form')
            form.addEventListener('submit', function(e) {
                // 阻止默认行为  提交
                e.preventDefault()
            })

            const a = document.querySelector('a')
            a.addEventListener('click', function(e) {
                e.preventDefault()
            })
        </script>
    </body>

    </html>
    ```

## 1.5 解绑事件
- **on事件方式**：直接使用**null覆盖**就可以实现事件的解绑
- **addEventListener方式**：必须使用`removeEventListener(事件类型, 事件处理函数, [获取捕获或者冒泡阶段])`。
- **注意**：**匿名函数无法被解绑**
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>事件解绑</title>
    </head>

    <body>
        <button>点击</button>
        <script>
            //L0 事件移除解绑 
            const btn = document.querySelector('button')
            btn.onclick = function() {
                alert('点击了1')
            }
            btn.onclick = null

            // L2 事件移除解绑
            function fn() {
                alert('点击了2')
            }
            btn.addEventListener('click', fn)
            btn.removeEventListener('click', fn)
        </script>
    </body>

    </html>
    ```

## 1.6 其他说明
- 鼠标经过事件的区别
  - mouseover 和 mouseout 会有冒泡效果
  - mouseenter 和 mouseleave 没有冒泡效果 (推荐)
- 两种注册事件的区别
  - 传统on注册（L0）
    - 同一个对象，后面注册的事件会覆盖前面注册(同一个事件)
    - 直接使用null覆盖偶就可以实现事件的解绑
    - 都是冒泡阶段执行的
  - 事件监听注册（L2）
    - 语法: addEventListener(事件类型, 事件处理函数, 是否使用捕获)
    - 后面注册的事件不会覆盖前面注册的事件(同一个事件)
    - 可以通过第三个参数去确定是在冒泡或者捕获阶段执行
    - 必须使用removeEventListener(事件类型, 事件处理函数, 获取捕获或者冒泡阶段)
    - 匿名函数无法被解绑
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>mouseover和mouseenter的区别</title>
        <style>
            .dad {
                width: 400px;
                height: 400px;
                background-color: pink;
            }
            
            .baby {
                width: 200px;
                height: 200px;
                background-color: purple;
            }
        </style>
    </head>

    <body>
        <div class="dad">
            <div class="baby"></div>
        </div>
        <script>
            const dad = document.querySelector('.dad')
            const baby = document.querySelector('.baby')
                //有冒泡效果
            dad.addEventListener('mouseover', function() {
                console.log('鼠标经过1')
            })
            dad.addEventListener('mouseout', function() {
                    console.log('鼠标离开1')
                })
                //没有冒泡效果推荐
            dad.addEventListener('mouseenter', function() {
                console.log('鼠标经过2')
            })
            dad.addEventListener('mouseleave', function() {
                console.log('鼠标离开2')
            })
        </script>
    </body>

    </html>
    ```

# 2. 事件委托
- 事件委托：利用事件流的特征解决一些开发需求的知识技巧
- 优点：减少注册次数，可以提高程序性能
- 原理：事件委托其实是利用事件冒泡的特点，只给父元素注册事件，当我们触发子元素的时候，会冒泡到父元素身上，从而触发父元素的事件
- 实现：`事件对象.target.tagName` 可以获得真正触发事件的元素
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>事件委托</title>
    </head>

    <body>
        <ul>
            <li>第1个孩子</li>
            <li>第2个孩子</li>
            <li>第3个孩子</li>
            <li>第4个孩子</li>
            <li>第5个孩子</li>
            <p>我不需要变色</p>
        </ul>
        <script>
            // 点击每个小li 当前li 文字变为红色,p不变色
            // 按照事件委托的方式  委托给父级，事件写到父级身上
            // 1. 获得父元素
            const ul = document.querySelector('ul')
            ul.addEventListener('click', function(e) {
                // 我的需求，我们只要点击li才会有效果
                if (e.target.tagName === 'LI') {
                    e.target.style.color = 'red'
                }
            })
        </script>
    </body>

    </html>
    ```
- **tab栏切换改造：**
    ![20241011223823](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011223823.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>tab栏切换改造</title>
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
                    <li><a class="active" href="javascript:;" data-id="0">精选</a></li>
                    <li><a href="javascript:;" data-id="1">美食</a></li>
                    <li><a href="javascript:;" data-id="2">百货</a></li>
                    <li><a href="javascript:;" data-id="3">个护</a></li>
                    <li><a href="javascript:;" data-id="4">预告</a></li>
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
            // 采取事件委托的形式 tab栏切换
            // 1. 获取 ul 父元素 因为 ul只有一个
            const ul = document.querySelector('.tab-nav ul')
                // 获取 5个 item 
            const items = document.querySelectorAll('.tab-content .item')
                // 2. 添加事件
            ul.addEventListener('click', function(e) {
                // console.log(e.target)  // e.target是我们点击的对象
                // 我们只有点击了 a 才会 进行 添加类和删除类操作 
                // console.log(e.target.tagName)  // e.target.tagName 点击那个对象的 标签名
                if (e.target.tagName === 'A') {
                    // console.log('我选的是a')
                    // 排他思想 ，先移除原来的active  
                    document.querySelector('.tab-nav .active').classList.remove('active')
                        //当前元素添加 active  是 e.target
                        // this 指向ul 不能用this 
                    e.target.classList.add('active')

                    // 下面大盒子模块
                    // console.log(e.target.dataset.id)
                    const i = +e.target.dataset.id
                        // 排他思想 ，先移除原来的active 
                    document.querySelector('.tab-content .active').classList.remove('active')
                        // 对应的大盒子 添加 active 
                        // document.querySelector(`.tab-content .item:nth-child(${i + 1})`).classList.add('active')
                    items[i].classList.add('active')
                }
            })
        </script>
    </body>

    </html>
    ```

# 3. 其他事件
## 3.1 页面加载事件
- 加载外部资源（如图片、外联CSS和JavaScript等）加载完毕时触发的事件
- 使用场景：
  - 有些时候需要等页面资源全部处理完了做一些事情
  - 老代码喜欢把 script 写在 head 中，这时候直接找 dom 元素找不到
- **load事件**：监听页面所有资源加载完毕，可以给 window 添加 load 事件，不光可以监听整个页面资源加载完毕，也可以针对某个资源绑定load事件
- **DOMContentLoaded事件**：当初始的HTML文档被完全加载和解析完成之后， 事件被触发，而无需等待样式表、图像等完全加载
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>页面加载事件</title>
        <script>
            // 等待页面所有资源加载完毕，就回去执行回调函数
            window.addEventListener('load', function() {
                console.log('页面所有资源加载完毕')
                const btn = document.querySelector('button')
                btn.addEventListener('click', function() {
                    alert(11)
                })
            })

            // 等待HTML文档被完全加载和解析完成，就回去执行回调函数
            document.addEventListener('DOMContentLoaded', function() {
                console.log('HTML文档被完全加载和解析完成')
            })
        </script>
    </head>

    <body>
        <button>点击</button>
    </body>

    </html>
    ```

## 3.2 元素滚动事件
- **scroll事件**：监听某个元素滚动时触发，直接给某个元素加即可。
  - 使用场景：很多网页需要检测用户把页面滚动到某个区域后做一些处理， 比如固定导航栏，比如返回顶部
    ![20241011224011](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224011.png)
- **scrollLeft 和 scrollTop（属性）**
  - 获取元素内容往左、往上滚出去看不到的距离，返回的是数值，没有单位，这两个值是可**读写**的
  - **document.documentElement.scrollTop**：开发中，我们经常检测页面滚动的距离
    ![20241011224118](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224118.png)

    ![20241011224125](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224125.png)

    ![20241011224132](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224132.png)

- `scrollTo()`：可把内容滚动到指定的坐标
    ![20241011224154](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224154.png)
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>页面滚动事件</title>
        <style>
            body {
                padding-top: 100px;
                height: 1000px;
            }
            
            div {
                display: none;
                margin: 100px;
                overflow: scroll;
                width: 200px;
                height: 200px;
                border: 1px solid #000;
            }
        </style>
    </head>

    <body>
        <div>
            我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字 我里面有很多很多的文字
        </div>
        <script>
            const div = document.querySelector('div')
                // 页面滚动事件
            window.addEventListener('scroll', function() {
                // 获取html元素写法
                // document.documentElement
                // 获取html元素滚动的距离
                // 得到的是数字型，不带单位
                // document.documentElement.scrollTop

                const n = document.documentElement.scrollTop
                if (n >= 100) {
                    div.style.display = 'block'
                } else {
                    div.style.display = 'none'
                }
            })
        </script>
    </body>

    </html>
    ```

## 3.3 页面尺寸事件
- **resize事件**：会在窗口尺寸改变的时候触发事件，例如检测屏幕宽度
    ![20241011224245](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224245.png)

    ![20241011224252](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224252.png)

- **clientWidth 和 clientHeight**：获取元素的可见部分宽高（不包含边框，margin，滚动条等）
    ![20241011224324](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224324.png)

    ![20241011224336](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224336.png)

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>页面尺寸事件</title>
        <style>
            div {
                display: inline-block;
                /* width: 200px; */
                height: 200px;
                background-color: pink;
                padding: 10px;
                border: 20px solid red;
            }
        </style>
    </head>

    <body>
        <div>123123123123123123123123123123123123123</div>
        <script>
            const div = document.querySelector('div')
            console.log(div.clientWidth)
                // resize 浏览器窗口大小发生变化的时候触发的事件
            window.addEventListener('resize', function() {
                console.log(1)
            })
        </script>
    </body>

    </html>
    ```

# 4. 元素尺寸与位置
## 4.1 使用场景
- 前面案例滚动多少距离，都是我们自己算的，最好是页面滚动到某个元素，就可以做某些事。
- 简单说，就是通过js的方式，得到元素在页面中的位置
- 这样我们可以做，页面滚动到这个位置，就可以做某些操作，省去计算了

## 4.2 获取宽高
- **offsetWidth 和 offsetHeight**：
  - 获取元素的自身宽高、包含元素自身设置的宽高、padding、border。
  - 获取出来的是数值，方便计算
  - 注意: 获取的是可视宽高, 如果盒子是隐藏的，获取的结果是0

## 4.3 获取位置
- **offsetLeft 和 offsetTop**：获取元素距离自己定位父级元素的左、上距离，注意是**只读属性**
    ![20241011224516](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224516.png)
  - **案例如下：**
      ```html
      <!DOCTYPE html>
      <html lang="en">

      <head>
          <meta charset="UTF-8">
          <meta http-equiv="X-UA-Compatible" content="IE=edge">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>offsetLeft</title>
          <style>
              div {
                  position: relative;
                  width: 200px;
                  height: 200px;
                  background-color: pink;
                  margin: 100px;
              }
              
              p {
                  width: 100px;
                  height: 100px;
                  background-color: purple;
                  margin: 50px;
              }
          </style>
      </head>

      <body>
          <div>
              <p></p>
          </div>
          <script>
              const div = document.querySelector('div')
              const p = document.querySelector('p')
                  // 检测盒子的位置  最近一级带有定位的祖先元素
              console.log(p.offsetLeft)
          </script>
      </body>

      </html>
      ```

- `element.getBoundingClientRect()`：方法返回元素的大小及其相对于视口的位置
  - **案例如下：**
      ```html
      <!DOCTYPE html>
      <html lang="en">

      <head>
          <meta charset="UTF-8">
          <meta http-equiv="X-UA-Compatible" content="IE=edge">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>获取元素大小</title>
          <style>
              body {
                  height: 2000px;
              }
              
              div {
                  width: 200px;
                  height: 200px;
                  background-color: pink;
                  margin: 100px;
              }
          </style>
      </head>

      <body>
          <div></div>
          <script>
              const div = document.querySelector('div')
              console.log(div.getBoundingClientRect())
          </script>
      </body>

      </html>
      ```
      ![20241011224714](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224714.png)

## 4.4 总结
![20241011224736](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224736.png)

## 4.5 仿京东固定头部案例
![20241011224752](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224752.png)
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>仿京东固定头部</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        .content {
            overflow: hidden;
            width: 1000px;
            height: 3000px;
            background-color: pink;
            margin: 0 auto;
        }
        
        .backtop {
            display: none;
            width: 50px;
            left: 50%;
            margin: 0 0 0 505px;
            position: fixed;
            bottom: 60px;
            z-index: 100;
        }
        
        .backtop a {
            height: 50px;
            width: 50px;
            background: url(./images/bg2.png) 0 -600px no-repeat;
            opacity: 0.35;
            overflow: hidden;
            display: block;
            text-indent: -999em;
            cursor: pointer;
        }
        
        .header {
            position: fixed;
            top: -80px;
            left: 0;
            width: 100%;
            height: 80px;
            background-color: purple;
            text-align: center;
            color: #fff;
            line-height: 80px;
            font-size: 30px;
            transition: all .3s;
        }
        
        .sk {
            width: 300px;
            height: 300px;
            background-color: skyblue;
            margin-top: 500px;
        }
    </style>
</head>

<body>
    <div class="header">我是顶部导航栏</div>
    <div class="content">
        <div class="sk">秒杀模块</div>
    </div>
    <div class="backtop">
        <img src="./images/close2.png" alt="">
        <a href="javascript:;"></a>
    </div>
    <script>
        const sk = document.querySelector('.sk')
        const header = document.querySelector('.header')
            // 1. 页面滚动事件
        window.addEventListener('scroll', function() {
            // 当页面滚动到 秒杀模块的时候，就改变 头部的 top值
            // 页面被卷去的头部 >=  秒杀模块的位置 offsetTop
            const n = document.documentElement.scrollTop
            header.style.top = n >= sk.offsetTop ? 0 : '-80px'
        })
    </script>
</body>

</html>
```

## 4.6 点击小滑块移动效果案例
![20241011224834](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224834.png)
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>bilibili-干杯~~~</title>
    <!-- 引入favicon图标 -->
    <link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
    <!-- 引入css文件 -->
    <link rel="stylesheet" href="./css/index.css">
    <!-- 引入字体图标文件 -->
    <link rel="stylesheet" href="./fonts/iconfont.css">
</head>

<body>
    <!-- 头部模块 -->
    <header class="suspension">
        <div class="m-navbar">
            <!-- logo -->
            <a href="#" class="logo">
                <i class="iconfont Navbar_logo"></i>
            </a>
            <!-- 右侧 -->
            <div class="right">
                <a href="#" class="search">
                    <i class="iconfont ic_search_tab"></i>
                </a>
                <a href="#" class="face">
                    <img src="./images/login.png" alt="">
                </a>
                <div class="app-btn">
                    <img src="./images/download.png" alt="">
                </div>
            </div>
        </div>
        <div class="channel-menu">
            <div class="tabs">
                <!-- 很宽的盒子 -->
                <div class="tabs-list">
                    <a href="#">首页</a>
                    <a href="#">动画</a>
                    <a href="#">番剧</a>
                    <a href="#">果蔬</a>
                    <a href="#">音乐</a>
                    <a href="#">舞蹈</a>
                    <a href="#">鬼畜</a>
                    <a href="#">吹鬼</a>
                    <!-- 红色线 -->
                    <div class="line"></div>
                </div>
            </div>
            <div class="after">
                <i class="iconfont general_pulldown_s"></i>
            </div>
        </div>
    </header>

    <!-- 主体部分 -->
    <div class="m-home">
        <div class="video-list">
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下电视？
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
            <a href="#" class="video-item">
                <div class="card">
                    <img src="./images/dog.jpg@480w_270h_1c" alt="">
                    <!-- 播放量 -->
                    <div class="count">
                        <span>
              <i class="iconfont icon_shipin_bofangshu"></i>
              播放量
            </span>
                        <span>
              <i class="iconfont icon_shipin_danmushu"></i>
              评论数
            </span>
                    </div>
                </div>
                <p class="title ellipsis-2">
                    315晚会能不能曝光下智能电视？N重广告、套娃会员、操作反人类，当代年轻人是怎么被智能电视逼疯的？【商业B面&牛顿】
                </p>
            </a>
        </div>
    </div>

    <!-- 底部模块 -->
    <footer class="app">
        <div class="btn-app">
            <i class="iconfont Navbar_logo"></i> 打开App，看你感兴趣的视频
        </div>
    </footer>
    <script>
        // 1. 事件委托的方法 获取父元素 tabs-list
        const list = document.querySelector('.tabs-list')
        const line = document.querySelector('.line')
            // 2. 注册点击事件
        list.addEventListener('click', function(e) {
            // 只有点击了A 才有触发效果
            if (e.target.tagName === 'A') {
                // console.log(11)
                // 当前元素是谁 ？  e.target
                // 得到当前点击元素的位置
                // console.log(e.target.offsetLeft)
                // line.style.transform = 'translateX(100px)'
                // 把我们点击的a链接盒子的位置  然后移动
                line.style.transform = `translateX(${e.target.offsetLeft}px)`
            }
        })
    </script>
</body>

</html>
```

# 5. 电梯导航案例
![20241011224909](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224909.png)

![20241011224917](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241011224917.png)
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>小兔鲜儿 - 新鲜 惠民 快捷!</title>
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="renderer" content="webkit">
    <link rel="stylesheet" href="./css/common.css">
    <link rel="stylesheet" href="./css/index.css">
</head>

<body>
    <!-- 项部导航 -->
    <div class="xtx_topnav">
        <div class="wrapper">
            <!-- 顶部导航 -->
            <ul class="xtx_navs">
                <li>
                    <a href="javascript:;">请先登录</a>
                </li>
                <li>
                    <a href="javascript:;">免费注册</a>
                </li>
                <li>
                    <a href="javascript:;">我的订单</a>
                </li>
                <li>
                    <a href="javascript:;">会员中心</a>
                </li>
                <li>
                    <a href="javascript:;">帮助中心</a>
                </li>
                <li>
                    <a href="javascript:;">在线客服</a>
                </li>
                <li>
                    <a href="javascript:;">
                        <i class="mobile sprites"></i> 手机版
                    </a>
                </li>
            </ul>
        </div>
    </div>
    <!-- 头部 -->
    <div class="xtx_header">
        <div class="wrapper">
            <!-- 网站Logo -->
            <h1 class="xtx_logo"><a href="/">小兔鲜儿</a></h1>
            <!-- 主导航 -->
            <div class="xtx_navs">
                <ul class="clearfix">
                    <li>
                        <a href="javascript:;">首页</a>
                    </li>
                    <li>
                        <a href="javascript:;">生鲜</a>
                    </li>
                    <li>
                        <a href="javascript:;">美食</a>
                    </li>
                    <li>
                        <a href="javascript:;">餐厨</a>
                    </li>
                    <li>
                        <a href="javascript:;">电器</a>
                    </li>
                    <li>
                        <a href="javascript:;">居家</a>
                    </li>
                    <li>
                        <a href="javascript:;">洗护</a>
                    </li>
                    <li>
                        <a href="javascript:;">孕婴</a>
                    </li>
                    <li>
                        <a href="javascript:;">服装</a>
                    </li>
                </ul>
            </div>
            <!-- 站内搜索 -->
            <div class="xtx_search clearfix">
                <!-- 购物车 -->
                <a href="javascript:;" class="xtx_search_cart sprites">
                    <i>2</i>
                </a>
                <!-- 搜索框 -->
                <div class="xtx_search_wrapper">
                    <input type="text" placeholder="搜一搜">
                </div>
            </div>
        </div>
    </div>
    <!-- 分类及焦点图 -->
    <div class="xtx_entry">
        <div class="wrapper">
            <!-- 分类 -->
            <div class="xtx_category">
                <!-- 顶级分类 -->
                <ul class="xtx_category_super">
                    <li>
                        <a href="javascript:;">
              生鲜
              <small>水果</small>
              <small>蔬菜</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                    <li class="active">
                        <a href="javascript:;">
              美食
              <small>面点</small>
              <small>干果</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                    <li>
                        <a href="javascript:;">
              电器
              <small>数码产品</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                    <li>
                        <a href="javascript:;">
              居家
              <small>床品</small>
              <small>四件套</small>
              <small>被枕</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                    <li>
                        <a href="javascript:;">
              洗护
              <small>洗发洗护</small>
              <small>美妆</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                    <li>
                        <a href="javascript:;">
              孕婴
              <small>奶粉</small>
              <small>玩具</small>
              <small>辅食</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                    <li>
                        <a href="javascript:;">
              餐橱
              <small>数码产品</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                    <li>
                        <a href="javascript:;">
              服饰
              <small>女装</small>
              <small>男装</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                    <li>
                        <a href="javascript:;">
              杂货
              <small>户外</small>
              <small>图书</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                    <li>
                        <a href="javascript:;">
              品牌
              <small>品牌制造</small>
            </a>
                        <i class="sprites"></i>
                    </li>
                </ul>
                <!-- 子分类 -->
                <div class="xtx_category_subset">

                </div>
            </div>
            <!-- 焦点图 -->
            <div class="xtx_banner">

                <ul>
                    <li>
                        <a href="javascript:;">
                            <img src="./uploads/banner_1.png" alt="">
                        </a>
                    </li>
                </ul>
                <!-- 切换按钮  -->
                <a href="javascript:;" class="prev sprites"></a>
                <a href="javascript:;" class="next sprites"></a>
                <!-- 指示器 -->
                <div class="indicator">
                    <span></span>
                    <span></span>
                    <span class="active"></span>
                    <span></span>
                    <span></span>
                </div>

            </div>
        </div>
    </div>
    <!-- 新鲜好物 -->
    <div class="xtx_goods_new xtx_panel">
        <div class="wrapper">
            <!-- 面板头部 -->
            <div class="xtx_panel_header">
                <h3>新鲜好物<small>新鲜出炉 品质靠谱</small></h3>
                <a href="javascript:;" class="more">
          查看全部<i class="sprites"></i>
        </a>
            </div>
            <!-- 商品列表 -->
            <div class="xtx_panel_goods_1">
                <a href="javascript:;">
                    <img src="./uploads/new_goods_1.jpg" alt="">
                    <span class="name">睿米无线吸尘器F8</span>
                    <span class="price"><small>￥</small>899</span>
                </a>
                <a href="javascript:;">
                    <img src="./uploads/new_goods_2.jpg" alt="">
                    <span class="name">智能环绕3D空调</span>
                    <span class="price"><small>￥</small>1299</span>
                </a>
                <a href="javascript:;">
                    <img src="./uploads/new_goods_3.jpg" alt="">
                    <span class="name">广东软软糯米煲仔饭</span>
                    <span class="price"><small>￥</small>129</span>
                </a>
                <a href="javascript:;">
                    <img src="./uploads/new_goods_4.jpg" alt="">
                    <span class="name">罗西机械智能手表</span>
                    <span class="price"><small>￥</small>3399</span>
                </a>
            </div>
        </div>
    </div>

    <!-- 人气推荐 -->
    <div class="xtx_goods_popular xtx_panel">
        <div class="wrapper">
            <!-- 面板头部 -->
            <div class="xtx_panel_header">
                <h3>人气推荐<small>人气爆款 不容错过</small></h3>
            </div>
            <!-- 商品列表 -->
            <div class="xtx_panel_goods_1">
                <a href="javascript:;">
                    <img src="./uploads/popular_1.jpg">
                    <span class="title">特惠推荐</span>
                    <span class="alt">我猜得到 你的需要</span>
                </a>
                <a href="./index-hot.html">
                    <img src="./uploads/popular_2.jpg">
                    <span class="title">爆款推荐</span>
                    <span class="alt">人气好物推荐</span>
                </a>
                <a href="./index-one.html">
                    <img src="./uploads/popular_3.jpg">
                    <span class="title">场景使用一站买全</span>
                    <span class="alt">编辑精心整理推荐</span>
                </a>
                <a href="javascript:;">
                    <img src="./uploads/popular_4.jpg">
                    <span class="title">领券中心</span>
                    <span class="alt">发现更多超值优惠券</span>
                </a>
            </div>
        </div>
    </div>

    <!-- 热门品牌 -->
    <div class="xtx_goods_brand xtx_panel">
        <div class="wrapper">
            <div class="xtx_panel_header">
                <h3>热门品牌<small>国际经典 品质保证</small></h3>
                <div class="page-bar">
                    <a href="javascript:;" class="prev sprites"></a>
                    <a href="javascript:;" class="next sprites active"></a>
                </div>
            </div>
            <!-- 商品列表 -->
            <div class="xtx_goods">
                <ul>
                    <li>
                        <a href="./index-brand.html">
                            <img src="./uploads/brand_goods_1.jpg" alt="">
                        </a>
                        <a href="./brand-list.html">
                            <img src="./uploads/brand_goods_2.jpg" alt="">
                        </a>
                        <a href="javascript:;">
                            <img src="./uploads/brand_goods_3.jpg" alt="">
                        </a>
                        <a href="javascript:;">
                            <img src="./uploads/brand_goods_4.jpg" alt="">
                        </a>
                        <a href="javascript:;">
                            <img src="./uploads/brand_goods_5.jpg" alt="">
                        </a>
                    </li>
                </ul>
            </div>
        </div>
    </div>

    <!-- 分类商品 -->
    <div class="xtx_goods_category xtx_panel">
        <div class="wrapper">
            <!-- 生鲜 -->
            <div class="xtx_panel_header">
                <h3>生鲜</h3>
                <a href="javascript:;" class="more">
          查看全部<i class="sprites"></i>
        </a>
                <div class="tabs-bar">
                    <a href="javascript:;" class="active">水果</a>
                    <a href="javascript:;">蔬菜</a>
                    <a href="javascript:;">肉禽蛋</a>
                    <a href="javascript:;">裤装</a>
                    <a href="javascript:;">衬衫</a>
                    <a href="javascript:;">内衣</a>
                </div>
            </div>
            <!-- 商品列表 -->
            <div class="xtx_panel_goods_2">
                <ul class="clearfix">
                    <li>
                        <a href="javascript:;">
                            <img src="./uploads/fresh_goods_cover.jpg" alt="">
                        </a>
                        <div class="label">
                            <span>生鲜馆</span>
                            <span>全场3件8折</span>
                        </div>
                    </li>
                    <li>
                        <!-- 商品图片 -->
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/fresh_goods_1.jpg" alt="">
                            </div>
                        </a>
                        <!-- 商品信息 -->
                        <div class="meta">
                            <p class="name">美威 智利原味三文鱼排 240g/袋 4片装</p>
                            <p class="flag">海鲜年货</p>
                            <p class="price"><small>￥</small>59</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/fresh_goods_2.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">红功夫 麻辣小龙虾1.5kg 4-6钱/25-32只</p>
                            <p class="flag">火锅食材</p>
                            <p class="price"><small>￥</small>71.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/fresh_goods_3.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">三都港 冷冻无公害黄花鱼 700g 2条 袋装</p>
                            <p class="flag">海鲜水产</p>
                            <p class="price"><small>￥</small>49.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/fresh_goods_4.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">渔公码头 大连鲜食入味 即食海参 辽参刺参 调味海</p>
                            <p class="flag">海鲜年货</p>
                            <p class="price"><small>￥</small>899</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/fresh_goods_5.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">越南进口白心火龙果4个 装 标准果 单果400-550g </p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>29</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/fresh_goods_6.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">广西沃柑 柑橘1.5kg</p>
                            <p class="flag">新鲜水果</p>
                            <p class="price"><small>￥</small>59</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/fresh_goods_7.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">进口 牛油果 6个装 单果重约130-180g</p>
                            <p class="flag">新鲜水果</p>
                            <p class="price"><small>￥</small>39.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/fresh_goods_8.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">泰国进口山竹5A级 500g </p>
                            <p class="flag">新鲜水果</p>
                            <p class="price"><small>￥</small>29.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                </ul>
            </div>

            <!-- 服饰 -->
            <div class="xtx_panel_header">
                <h3>服饰</h3>
                <a href="javascript:;" class="more">
          查看全部<i class="sprites"></i>
        </a>
                <div class="tabs-bar">
                    <a href="javascript:;" class="active">行李箱</a>
                    <a href="javascript:;">男士包袋</a>
                    <a href="javascript:;">女士包袋</a>
                    <a href="javascript:;">钱包及小提袋</a>
                    <a href="javascript:;">男鞋</a>
                    <a href="javascript:;">女鞋</a>
                    <a href="javascript:;">拖鞋</a>
                </div>
            </div>
            <!-- 商品列表 -->
            <div class="xtx_panel_goods_2">
                <ul class="clearfix">
                    <li>
                        <a href="javascript:;">
                            <img src="./uploads/clothes_goods_cover.jpg" alt="">
                        </a>
                        <div class="label">
                            <span>服饰馆</span>
                            <span>3折狂欢</span>
                        </div>
                    </li>
                    <li>
                        <!-- 商品图片 -->
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/clothes_goods_1.jpg" alt="">
                            </div>
                        </a>
                        <!-- 商品信息 -->
                        <div class="meta">
                            <p class="name">人本秋季厚底帆布鞋 韩版低帮增高学生</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>55</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/clothes_goods_2.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">迪士尼真皮针扣表带宽度 14-16mm规格双色压纹 女表带</p>
                            <p class="flag">海鲜年货</p>
                            <p class="price"><small>￥</small>20.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/clothes_goods_3.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">三都港 冷冻无公害黄花鱼 700g 2条 袋装</p>
                            <p class="flag">海鲜水产</p>
                            <p class="price"><small>￥</small>209</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/clothes_goods_4.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">ONLY夏季新款高腰宽松 七分阔腿裙裤休闲裤</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>274.5</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/clothes_goods_5.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">拉夫劳伦t恤男正品 </p>
                            <p class="flag">圆领短袖</p>
                            <p class="price"><small>￥</small>99</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/clothes_goods_6.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">李宁跑步鞋男鞋空气 弧2018春季款</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>79</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/clothes_goods_7.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">Dickies男鞋2020春季 英伦休闲工装鞋低帮</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>179</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/clothes_goods_8.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">北极绒春夏季纯棉背心 男士修身纯色打底</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>69</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                </ul>
            </div>

            <!-- 餐厨 -->
            <div class="xtx_panel_header">
                <h3>餐厨</h3>
                <a href="javascript:;" class="more">
          查看全部<i class="sprites"></i>
        </a>
                <div class="tabs-bar">
                    <a href="javascript:;" class="active">水果</a>
                    <a href="javascript:;">蔬菜</a>
                    <a href="javascript:;">肉禽蛋</a>
                    <a href="javascript:;">裤装</a>
                    <a href="javascript:;">衬衫</a>
                    <a href="javascript:;">内衣</a>
                </div>
            </div>
            <!-- 商品列表 -->
            <div class="xtx_panel_goods_2">
                <ul class="clearfix">
                    <li>
                        <a href="javascript:;">
                            <img src="./uploads/kitchen_goods_cover.jpg" alt="">
                        </a>
                        <div class="label">
                            <span>餐厨馆</span>
                            <span>大额优惠<br>等你来拿</span>
                        </div>
                    </li>
                    <li>
                        <!-- 商品图片 -->
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/kitchen_goods_1.jpg" alt="">
                            </div>
                        </a>
                        <!-- 商品信息 -->
                        <div class="meta">
                            <p class="name">创意可爱不锈钢便携餐具 套装筷子便携三件套</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>5.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/kitchen_goods_2.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">三金西瓜霜竹炭牙刷软毛 成人家用家庭装</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>20.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/kitchen_goods_3.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">朴（TOPOTO）大卫免手 洗平板拖把拓扑懒人木地 板刮刮乐桶拖布</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>129</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/kitchen_goods_4.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">ONLY夏季新款高腰宽松 七分阔腿裙裤休闲裤</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>274.5</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/kitchen_goods_5.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">金纺不伤手柔顺剂 妈妈的选择</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>29</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/kitchen_goods_6.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">洁成绵柔抹布洗碗巾超 值5片装 洗锅刷碗</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>10.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/kitchen_goods_7.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">大卫双驱动旋转拖把桶 免手洗拓扑拖布地拖墩布 </p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>159</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/kitchen_goods_8.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">斧头牌（AXE）去污地板 清洁剂2L 柠檬清香 </p>
                            <p class="flag">海鲜年货</p>
                            <p class="price"><small>￥</small>22.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                </ul>
            </div>

            <!-- 居家 -->
            <div class="xtx_panel_header">
                <h3>居家</h3>
                <a href="javascript:;" class="more">
          查看全部<i class="sprites"></i>
        </a>
                <div class="tabs-bar">
                    <a href="javascript:;" class="active">咖啡具</a>
                    <a href="javascript:;">水具酒具</a>
                    <a href="javascript:;">锅具</a>
                    <a href="javascript:;">餐具</a>
                    <a href="javascript:;">功能厨具</a>
                    <a href="javascript:;">茶具</a>
                    <a href="javascript:;">清洁保鲜</a>
                </div>
            </div>
            <!-- 商品列表 -->
            <div class="xtx_panel_goods_2">
                <ul class="clearfix">
                    <li>
                        <a href="javascript:;">
                            <img src="./uploads/home_goods_cover.jpg" alt="">
                        </a>
                        <div class="label">
                            <span>居家馆</span>
                            <span>全场满减</span>
                        </div>
                    </li>
                    <li>
                        <!-- 商品图片 -->
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/home_goods_1.jpg" alt="">
                            </div>
                        </a>
                        <!-- 商品信息 -->
                        <div class="meta">
                            <p class="name">菜鸟异常专用链接 非请 勿拍</p>
                            <p class="flag">海鲜年货</p>
                            <p class="price"><small>￥</small>8999</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/home_goods_2.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">【中盐软水盐】汉斯希 尔家用软水机适配</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>65</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/home_goods_3.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">云米净水壶家用直饮台式 净水机渗透过滤自来水</p>
                            <p class="flag">海鲜年货</p>
                            <p class="price"><small>￥</small>129</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/home_goods_4.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">ztk恒温调奶器配件玻璃壶 炖盅</p>
                            <p class="flag">海鲜年货</p>
                            <p class="price"><small>￥</small>129</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/home_goods_5.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">荞麦枕头单人枕芯双人 护颈椎枕头芯</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>29</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/home_goods_6.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">Bear/小熊 LLJ-B04G1 家用多功能切碎机 电动</p>
                            <p class="flag">料理机</p>
                            <p class="price"><small>￥</small>10.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/home_goods_7.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">荣事达薄饼机春饼春卷皮 家用博饼机电饼铛 </p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>159</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <div class="img-box">
                                <img src="./uploads/home_goods_8.jpg" alt="">
                            </div>
                        </a>
                        <div class="meta">
                            <p class="name">美式双人实木床 红实木 显档次</p>
                            <p class="flag"></p>
                            <p class="price"><small>￥</small>22.9</p>
                        </div>
                        <!-- 其它 -->
                        <div class="extra">
                            <a href="javascript:;">
                                <span>找相似</span>
                                <span>发现现多宝贝&gt;</span>
                            </a>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>

    <!-- 最新主题 -->
    <div class="xtx_goods_topic xtx_panel">
        <div class="wrapper">
            <div class="xtx_panel_header">
                <h3>最新专题</h3>
                <a href="javascript:;" class="more">
          查看全部<i class="sprites"></i>
        </a>
            </div>
            <div class="xtx_topic">
                <ul class="clearfix">
                    <li>
                        <a href="javascript:;">
                            <img src="./uploads/topic_goods_1.jpg" alt="">
                            <div class="meta">
                                <p class="title">
                                    吃这些美食才不算辜负自己
                                    <small>餐厨起居洗护好物</small>
                                </p>
                                <span class="price"><small>￥</small>29.9起</span>
                            </div>
                        </a>
                        <div class="social">
                            <span class="like">
                <i class="sprites"></i>1220
              </span>
                            <span class="view">
                <i class="sprites"></i>1800
              </span>

                            <span class="reply">
                <i class="sprites"></i>1220
              </span>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <img src="./uploads/topic_goods_2.jpg" alt="">
                            <div class="meta">
                                <p class="title">
                                    吃这些美食才不算辜负自己
                                    <small>餐厨起居洗护好物</small>
                                </p>
                                <span class="price"><small>￥</small>29.9起</span>
                            </div>
                        </a>
                        <div class="social">
                            <span class="liked">
                <i class="sprites"></i>1220
              </span>
                            <span class="view">
                <i class="sprites"></i>1800
              </span>

                            <span class="reply">
                <i class="sprites"></i>1220
              </span>
                        </div>
                    </li>
                    <li>
                        <a href="javascript:;">
                            <img src="./uploads/topic_goods_3.jpg" alt="">
                            <div class="meta">
                                <p class="title">
                                    吃这些美食才不算辜负自己
                                    <small>餐厨起居洗护好物</small>
                                </p>
                                <span class="price"><small>￥</small>29.9起</span>
                            </div>
                        </a>
                        <div class="social">
                            <span class="like">
                <i class="sprites"></i>1220
              </span>
                            <span class="view">
                <i class="sprites"></i>1800
              </span>

                            <span class="reply">
                <i class="sprites"></i>1220
              </span>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- 公共底部 -->
    <div class="xtx_footer clearfix">
        <div class="wrapper">
            <!-- 联系我们 -->
            <div class="contact clearfix">
                <dl>
                    <dt>客户服务</dt>
                    <dd class="chat">在线客服</dd>
                    <dd class="feedback">问题反馈</dd>
                </dl>
                <dl>
                    <dt>关注我们</dt>
                    <dd class="weixin">公众号</dd>
                    <dd class="weibo">微博</dd>
                </dl>
                <dl>
                    <dt>下载APP</dt>
                    <dd class="qrcode">
                        <img src="./uploads/qrcode.jpg">
                    </dd>
                    <dd class="download">
                        <span>扫描二维码</span>
                        <span>立马下载APP</span>
                        <a href="javascript:;">下载页面</a>
                    </dd>
                </dl>
                <dl>
                    <dt>服务热线</dt>
                    <dd class="hotline">
                        400-0000-000
                        <small>周一至周日 8:00-18:00</small>
                    </dd>
                </dl>
            </div>
        </div>
        <!-- 其它 -->
        <div class="extra">
            <div class="wrapper">
                <!-- 口号 -->
                <div class="slogan">
                    <a href="javascript:;" class="price">价格亲民</a>
                    <a href="javascript:;" class="express">物流快捷</a>
                    <a href="javascript:;" class="quality">品质新鲜</a>
                </div>
                <!-- 版权信息 -->
                <div class="copyright">
                    <p>
                        <a href="javascript:;">关于我们</a>
                        <a href="javascript:;">帮助中心</a>
                        <a href="javascript:;">售后服务</a>
                        <a href="javascript:;">配送与验收</a>
                        <a href="javascript:;">商务合作</a>
                        <a href="javascript:;">搜索推荐</a>
                        <a href="javascript:;">友情链接</a>
                    </p>
                    <p>CopyRight &copy; 小兔鲜儿</p>
                </div>
            </div>
        </div>
    </div>
    <!-- 电梯 -->
    <div class="xtx-elevator">
        <ul class="xtx-elevator-list">
            <li><a href="javascript:;" data-name="new">新鲜好物</a></li>
            <li><a href="javascript:;" data-name="popular">人气推荐</a></li>
            <li><a href="javascript:;" data-name="brand">热门品牌</a></li>
            <li><a href="javascript:;" data-name="topic">最新专题</a></li>
            <li><a href="javascript:;" id="backTop"><i class="sprites"></i>顶部</a></li>
        </ul>
    </div>
    <script>
        // 第一大模块，页面滑动可以显示和隐藏
        (function() {
            // 获取元素
            const entry = document.querySelector('.xtx_entry')
            const elevator = document.querySelector('.xtx-elevator')
                // 1. 当页面滚动大于 300像素，就显示 电梯导航
                // 2. 给页面添加滚动事件
            window.addEventListener('scroll', function() {
                // 被卷去的头部大于 300 
                const n = document.documentElement.scrollTop
                    // if (n >= 300) {
                    //   elevator.style.opacity = 1
                    // } else {
                    //   elevator.style.opacity = 0
                    // }
                    // 简写
                elevator.style.opacity = n >= entry.offsetTop ? 1 : 0
            })

            // 点击返回页面顶部
            const backTop = document.querySelector('#backTop')
            backTop.addEventListener('click', function() {
                // 可读写
                // document.documentElement.scrollTop = 0
                // window.scrollTo(x, y)
                window.scrollTo(0, 0)
            })
        })();

        // 第二第三都放到另外一个执行函数里面
        (function() {
            // 2. 点击页面可以滑动 
            const list = document.querySelector('.xtx-elevator-list')
            list.addEventListener('click', function(e) {
                // console.log(11)
                if (e.target.tagName === 'A' && e.target.dataset.name) {
                    // 排他思想  
                    // 先移除原来的类active 
                    // 先获取这个active的对象
                    const old = document.querySelector('.xtx-elevator-list .active')
                        // console.log(old)
                        // 判断 如果原来有active类的对象，就移除类，如果开始就没有对象，就不删除，所以不报错
                    if (old) old.classList.remove('active')
                        // 当前元素添加 active 
                    e.target.classList.add('active')
                        // 获得自定义属性  new   topic 
                        // console.log(e.target.dataset.name)
                        // 根据小盒子的自定义属性值 去选择 对应的大盒子
                        // console.log(document.querySelector(`.xtx_goods_${e.target.dataset.name}`).offsetTop)
                        // 获得对应大盒子的 offsetTop
                    const top = document.querySelector(`.xtx_goods_${e.target.dataset.name}`).offsetTop
                        // 让页面滚动到对应的位置
                    document.documentElement.scrollTop = top

                }
            })


            // 3. 页面滚动，可以根据大盒子选 小盒子 添加 active 类
            window.addEventListener('scroll', function() {
                //  3.1  先移除类 
                // 先获取这个active的对象
                const old = document.querySelector('.xtx-elevator-list .active')
                    // console.log(old)
                    // 判断 如果原来有active类的对象，就移除类，如果开始就没有对象，就不删除，所以不报错
                if (old) old.classList.remove('active')
                    // 3.2 判断页面当前滑动的位置，选择小盒子

                // 获取4个大盒子
                const news = document.querySelector('.xtx_goods_new')
                const popular = document.querySelector('.xtx_goods_popular')
                const brand = document.querySelector('.xtx_goods_brand')
                const topic = document.querySelector('.xtx_goods_topic')
                const n = document.documentElement.scrollTop
                if (n >= news.offsetTop && n < popular.offsetTop) {
                    // 选择第一个小盒子
                    document.querySelector('[data-name=new]').classList.add('active')
                } else if (n >= popular.offsetTop && n < brand.offsetTop) {
                    document.querySelector('[data-name=popular]').classList.add('active')
                } else if (n >= brand.offsetTop && n < topic.offsetTop) {
                    document.querySelector('[data-name=brand]').classList.add('active')
                } else if (n >= topic.offsetTop) {
                    document.querySelector('[data-name=topic]').classList.add('active')
                }
            })

        })();

        // let n = 10
        // n = 20
    </script>
</body>

</html>
```
