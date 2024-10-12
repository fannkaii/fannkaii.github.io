---
layout: post
title: "Web APIs 四、DOM节点操作"
author: "Kai"
header-style: text
tags:
  - Browser
  - DOM
---

转自：https://juejin.cn/post/7209964630159867959

# 1. 节点操作
## 1.1 DOM 节点
- DOM节点：DOM树里每一个内容都称之为节点
- 节点类型：
  - **元素节点**：所有的标签 比如 body、 div、html 是根节点
  - 属性节点：所有的属性 比如 href
  - 文本节点：所有的文本
  - 其他

## 1.2 查找节点
- **节点关系**：**针对的找亲戚返回的都是对象**
  - 父节点
  - 子节点
  - 兄弟节点
- **父节点查找**：`子元素.parentNode`，返回最近一级的父节点 找不到返回为null
- **子节点查找**：
  - `父元素.childNodes`：获得所有子节点、包括文本节点（空格、换行）、注释节点等
  - `父元素.children`属性：仅获得所有元素节点，返回的还是一个**伪数组**
- **兄弟关系查找**：
  - `元素.nextElementSibling`： 下一个兄弟节点
  - `元素.previousElementSibling`：上一个兄弟节点

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>关闭广告</title>
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
        <div class="box">
            我是广告
            <div class="box1">X</div>
        </div>
        <div class="box">
            我是广告
            <div class="box1">X</div>
        </div>
        <script>
            // 1. 获取三个关闭按钮
            const closeBtn = document.querySelectorAll('.box1')
            for (let i = 0; i < closeBtn.length; i++) {
                closeBtn[i].addEventListener('click', function() {
                    // 关闭我的爸爸 所以只关闭当前的父元素
                    this.parentNode.style.display = 'none'
                })
            }
        </script>
    </body>

    </html>
    ```

- **子节点兄弟节点：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>子节点兄弟节点</title>
    </head>

    <body>
        <ul>
            <li>1</li>
            <li>2</li>
            <li>3</li>
            <li>4</li>
            <li>5</li>
        </ul>
        <script>
            const ul = document.querySelector('ul')  // ul
            console.log(ul.children)  // 得到伪数组  选择的是 亲儿子 
            
            const li2 = document.querySelector('ul li:nth-child(2)')
            console.log(li2.previousElementSibling) // 上一个兄弟
            console.log(li2.nextElementSibling) // 下一个兄弟
        </script>
    </body>

    </html>
    ```

## 1.3 增加节点
- 很多情况下，我们需要在页面中增加元素
  - 比如，点击发布按钮，可以新增一条信息
- 一般情况下，我们新增节点，按照如下操作：
  - 创建一个新的节点
  - 把创建的新的节点放入到指定的元素内部
- **创建节点**
  - 即创造出一个新的网页元素，再添加到网页
  - 创建元素节点方法：`document.createElement('标签名')`
- **追加节点**
  - 要想在界面看到，还得插入到某个父元素中
  - 插入到父元素的最后一个子元素：`父元素.appendChild(要插入的元素)`
  - 插入到父元素中某个子元素的前面：`父元素.insertBefore(要插入的元素，在哪个元素前面)`
- **克隆节点**：`元素.cloneNode(布尔值)`
  - 若为true，则代表克隆时会包含后代节点一起克隆
  - 若为false，则代表克隆时不包含后代节点，默认为false

- **增加节点**：
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>增加节点</title>
    </head>

    <body>
        <ul>
            <li>我是老大</li>
        </ul>
        <script>
            // 1. 创建节点
            const li = document.createElement('li')
            li.innerHTML = '我是li'

            // 2. 追加节点
            // ul.appendChild(li)//追加节点，作为最后一个子元素
            // insertBefore(插入的元素, 放到哪个元素的前面)
            const ul = document.querySelector('ul')
            ul.insertBefore(li, ul.children[0])
        </script>
    </body>

    </html>
    ```

- **学成在线首页：**
    ![20241012110802](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012110802.png)

    ![20241012110813](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012110813.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>学成在线首页</title>
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

                </ul>
            </div>
        </div>
        <script>
            // 1. 重构  
            let data = [
                {
                    src: 'images/course01.png',
                    title: 'Think PHP 5.0 博客系统实战项目演练',
                    num: 1125
                },
                {
                    src: 'images/course02.png',
                    title: 'Android 网络动态图片加载实战',
                    num: 357
                },
                {
                    src: 'images/course03.png',
                    title: 'Angular2 大前端商城实战项目演练',
                    num: 22250
                },
                {
                    src: 'images/course04.png',
                    title: 'Android APP 实战项目演练',
                    num: 389
                },
                {
                    src: 'images/course05.png',
                    title: 'UGUI 源码深度分析案例',
                    num: 124
                },
                {
                    src: 'images/course06.png',
                    title: 'Kami2首页界面切换效果实战演练',
                    num: 432
                },
                {
                    src: 'images/course07.png',
                    title: 'UNITY 从入门到精通实战案例',
                    num: 888
                },
                {
                    src: 'images/course08.png',
                    title: 'Cocos 深度学习你不会错过的实战',
                    num: 590
                },
            ]
            const ul = document.querySelector('.box-bd ul')
            // 1. 根据数据的个数，创建 对应的小li
            for (let i = 0; i < data.length; i++) {
                // 2. 创建新的小li
                const li = document.createElement('li')
                // 把内容给li
                li.innerHTML = `
                    <a href="#">
                        <img src=${data[i].src} alt="">
                        <h4>
                            ${data[i].title}
                        </h4>
                        <div class="info">
                            <span>高级</span> • <span>${data[i].num}</span>人在学习
                        </div>
                    </a>
                `
                // 3. ul追加小li
                ul.appendChild(li)
            }
        </script>
    </body>

    </html>
    ```

- **克隆节点：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>克隆节点</title>
    </head>

    <body>
        <ul>
            <li>1</li>
            <li>2</li>
            <li>3</li>
        </ul>
        <script>
            const ul = document.querySelector('ul')
                // 1 克隆节点  元素.cloneNode(true)
            const li1 = ul.children[0].cloneNode(true)
                // 2. 追加
            ul.appendChild(li1)
        </script>
    </body>

    </html>
    ```

## 1.4 删除节点
- 若一个节点在页面中已不需要时，可以删除它
- 在 JavaScript 原生DOM操作中，要删除元素必须通过父元素删除
- 语法：`父元素.removeChild(要删除的元素)`
- 注意：如不存在父子关系则删除不成功，删除节点和隐藏节点（display:none）是有区别的，隐藏节点还是存在的，但是删除，则从html中删除节点
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>删除节点</title>
    </head>

    <body>
        <ul>
            <li>没用了</li>
        </ul>
        <script>
            const ul = document.querySelector('ul')
                // 删除节点  父元素.removeChlid(子元素)
            ul.removeChild(ul.children[0])
        </script>
    </body>

    </html>
    ```

# 2. M端事件
- 移动端也有自己独特的地方。比如**触屏事件 touch**（也称触摸事件），Android 和 IOS 都有。
- touch 对象代表一个触摸点。触摸点可能是一根手指，也可能是一根触摸笔。触屏事件可响应用户手指（或触控笔）对屏幕或者触控板操作。
- 常见的触屏事件如下：
    ![20241012112431](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012112431.png)
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>m端事件</title>
        <style>
            div {
                width: 300px;
                height: 300px;
                background-color: pink;
            }
        </style>
    </head>

    <body>
        <div></div>
        <script>
            const div = document.querySelector('div')
                // 1. 触摸
            div.addEventListener('touchstart', function() {
                    console.log('开始摸我了')
                })
                // 2. 离开
            div.addEventListener('touchend', function() {
                    console.log('离开了')
                })
                // 3. 移动
            div.addEventListener('touchmove', function() {
                console.log('一直摸，移动')
            })
        </script>
    </body>

    </html>
    ```

# 3. JS插件
- 插件: 就是别人写好的一些代码,我们只需要复制对应的代码,就可以直接实现对应的效果
- 学习插件的基本过程
  - 熟悉官网,了解这个插件可以完成什么需求 [www.swiper.com.cn](https://www.swiper.com.cn/)
  - 看在线演示，找到需求的demo [www.swiper.com.cn/demo/index.html](https://www.swiper.com.cn/demo/index.html)
  - 查看基本使用流程 [www.swiper.com.cn/usage/index.html](https://www.swiper.com.cn/usage/index.html)
  - 查看APi文档，去配置自己的插件 [www.swiper.com.cn/usage/index.html](https://www.swiper.com.cn/usage/index.html)
  - 注意: 多个swiper同时使用的时候, 类名需要注意区分

    ![20241012112801](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012112801.png)

- 案例如下：
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>插件使用</title>
        <link rel="stylesheet" href="./css/swiper.min.css">
        <style>
            .box {
                position: relative;
                width: 800px;
                height: 300px;
                background-color: pink;
                margin: 100px auto;
            }
            
            html,
            body {
                position: relative;
                height: 100%;
            }
            
            body {
                background: #eee;
                font-family: Helvetica Neue, Helvetica, Arial, sans-serif;
                font-size: 14px;
                color: #000;
                margin: 0;
                padding: 0;
            }
            
            .swiper {
                overflow: hidden;
                width: 100%;
                height: 100%;
            }
            
            .swiper-slide {
                text-align: center;
                font-size: 18px;
                background: #fff;
                /* Center slide text vertically */
                display: -webkit-box;
                display: -ms-flexbox;
                display: -webkit-flex;
                display: flex;
                -webkit-box-pack: center;
                -ms-flex-pack: center;
                -webkit-justify-content: center;
                justify-content: center;
                -webkit-box-align: center;
                -ms-flex-align: center;
                -webkit-align-items: center;
                align-items: center;
            }
            
            .swiper-slide img {
                display: block;
                width: 100%;
                height: 100%;
                object-fit: cover;
            }
        </style>

    </head>

    <body>
        <div class="box">
            <!-- Swiper -->
            <div class="swiper mySwiper">
                <div class="swiper-wrapper">
                    <div class="swiper-slide">Slide 1</div>
                    <div class="swiper-slide">Slide 2</div>
                    <div class="swiper-slide">Slide 3</div>
                    <div class="swiper-slide">Slide 4</div>
                    <div class="swiper-slide">Slide 5</div>
                    <div class="swiper-slide">Slide 6</div>
                    <div class="swiper-slide">Slide 7</div>
                    <div class="swiper-slide">Slide 8</div>
                    <div class="swiper-slide">Slide 9</div>
                </div>
                <div class="swiper-pagination"></div>
            </div>
        </div>
        <script src="./js/swiper.min.js"></script>
        <script>
            var swiper = new Swiper(".mySwiper", {
                // 小圆点
                pagination: {
                    el: ".swiper-pagination",
                },
                // 自动播放
                autoplay: {
                    delay: 1000, //1秒切换一次
                    disableOnInteraction: false, // 鼠标点击 触摸之后，自动继续播放
                },
                // 可以键盘控制
                keyboard: {
                    enabled: true,
                    onlyInViewport: true,
                },
            })
        </script>
    </body>

    </html>
    ```

# 4. 综合案例
## 4.1 游乐园轮播图
![20241012112859](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012112859.png)
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>游乐园轮播图</title>
    <!-- 引入favicon图标 -->
    <link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
    <!-- 引入字体图标 -->
    <link rel="stylesheet" href="./lib/iconfont/iconfont.css">
    <!-- 引入index.css -->
    <link rel="stylesheet" href="./css/index.css">
    <link rel="stylesheet" href="./css/swiper.min.css">
    <style>
        html,
        body {
            position: relative;
            height: 100%;
        }
        
        body {
            background: #eee;
            font-family: Helvetica Neue, Helvetica, Arial, sans-serif;
            font-size: 14px;
            color: #000;
            margin: 0;
            padding: 0;
        }
        
        .swiper-container {
            width: 100%;
            height: 100%;
        }
        
        .swiper-slide {
            text-align: center;
            font-size: 18px;
            background: #fff;
            /* Center slide text vertically */
            display: -webkit-box;
            display: -ms-flexbox;
            display: -webkit-flex;
            display: flex;
            -webkit-box-pack: center;
            -ms-flex-pack: center;
            -webkit-justify-content: center;
            justify-content: center;
            -webkit-box-align: center;
            -ms-flex-align: center;
            -webkit-align-items: center;
            align-items: center;
        }
        
        .swiper-pagination-bullet {
            background: #fff;
            opacity: .8;
        }
        
        .swiper-pagination-bullet-active {
            background: pink;
        }
    </style>
</head>

<body>
    <!-- 页面主体部分 -->
    <div class="main">
        <!-- 轮播图模块 -->
        <div class="banner">
            <!-- 轮播图模块 -->
            <!-- Swiper -->
            <div class="swiper-container">
                <div class="swiper-wrapper">
                    <div class="swiper-slide">
                        <a href="#"><img src="./uploads/banner_1.png" alt=""></a>
                    </div>
                    <div class="swiper-slide">
                        <a href="#"><img src="./uploads/banner_1.png" alt=""></a>
                    </div>
                    <div class="swiper-slide">
                        <a href="#"><img src="./uploads/banner_1.png" alt=""></a>
                    </div>
                    <div class="swiper-slide">
                        <a href="#"><img src="./uploads/banner_1.png" alt=""></a>
                    </div>
                    <div class="swiper-slide">
                        <a href="#"><img src="./uploads/banner_1.png" alt=""></a>
                    </div>
                </div>
                <!-- Add Pagination -->
                <div class="swiper-pagination"></div>
            </div>
        </div>
        <!-- 标题模块 -->
        <div class="title">
            <h4>乐园活动</h4>
        </div>

        <!-- 活动项目模块 -->
        <div class="item">
            <!-- 图片模块 -->
            <div class="pic">
                <!-- 图片 -->
                <a href="#">
                    <img src="./uploads/item_2.png" alt="">
                </a>
                <!-- 进行模块 -->
                <div class="status">
                    进行中
                </div>
                <!-- 收藏 -->
                <div class="collect">
                    <i class="iconfont icon-shoucang1"></i>
                </div>
            </div>

            <!-- 信息模块 -->
            <div class="info">
                <h5>疯狂的红包 不一样的撕名牌 大型家庭亲子户外活动</h5>
                <div class="rest">
                    <span>
            <i class="iconfont icon-qizhi"></i>
            200人报名
          </span>
                    <span>
            <i class="iconfont icon-shizhong"></i>
            本周六开始</span>
                </div>
            </div>
        </div>

        <!-- 活动项目模块 -->
        <div class="item">
            <!-- 图片模块 -->
            <div class="pic">
                <!-- 图片 -->
                <a href="#">
                    <img src="./uploads/item_2.png" alt="">
                </a>
                <!-- 进行模块 -->
                <div class="status">
                    进行中
                </div>
                <!-- 收藏 -->
                <div class="collect">
                    <i class="iconfont icon-shoucang1"></i>
                </div>
            </div>

            <!-- 信息模块 -->
            <div class="info">
                <h5>疯狂的红包 不一样的撕名牌 大型家庭亲子户外活动</h5>
                <div class="rest">
                    <span>
            <i class="iconfont icon-qizhi"></i>
            200人报名
          </span>
                    <span>
            <i class="iconfont icon-shizhong"></i>
            本周六开始</span>
                </div>
            </div>
        </div>
        <!-- 活动项目模块 -->
        <div class="item">
            <!-- 图片模块 -->
            <div class="pic">
                <!-- 图片 -->
                <a href="#">
                    <img src="./uploads/item_2.png" alt="">
                </a>
                <!-- 进行模块 -->
                <div class="status">
                    进行中
                </div>
                <!-- 收藏 -->
                <div class="collect">
                    <i class="iconfont icon-shoucang1"></i>
                </div>
            </div>

            <!-- 信息模块 -->
            <div class="info">
                <h5>疯狂的红包 不一样的撕名牌 大型家庭亲子户外活动</h5>
                <div class="rest">
                    <span>
            <i class="iconfont icon-qizhi"></i>
            200人报名
          </span>
                    <span>
            <i class="iconfont icon-shizhong"></i>
            本周六开始</span>
                </div>
            </div>
        </div>
        <!-- 活动项目模块 -->
        <div class="item">
            <!-- 图片模块 -->
            <div class="pic">
                <!-- 图片 -->
                <a href="#">
                    <img src="./uploads/item_2.png" alt="">
                </a>
                <!-- 进行模块 -->
                <div class="status">
                    进行中
                </div>
                <!-- 收藏 -->
                <div class="collect">
                    <i class="iconfont icon-shoucang1"></i>
                </div>
            </div>

            <!-- 信息模块 -->
            <div class="info">
                <h5>疯狂的红包 不一样的撕名牌 大型家庭亲子户外活动</h5>
                <div class="rest">
                    <span>
            <i class="iconfont icon-qizhi"></i>
            200人报名
          </span>
                    <span>
            <i class="iconfont icon-shizhong"></i>
            本周六开始</span>
                </div>
            </div>
        </div>
    </div>
    <!-- 底部盒子 -->
    <footer class="toolbar">
        <a href="#" class="active">
            <i class="iconfont icon-index-copy"></i>
            <span>首页</span>
        </a>
        <a href="#">
            <i class="iconfont icon-youhuiquan"></i>
            <span>卡卷</span>
        </a>
        <a href="#">
            <i class="iconfont icon-index-copy"></i>
            <span>首页</span>
        </a>
    </footer>

    <!-- 引入js文件 -->
    <script src="./js/flexible.js"></script>
    <script src="./js/swiper.min.js"></script>
    <!-- Initialize Swiper -->
    <script>
        var swiper = new Swiper('.swiper-container', {
            pagination: {
                el: '.swiper-pagination',
            },
            autoplay: {
                delay: 3000, //3秒切换一次
            },
        });
    </script>
</body>

</html>
```

## 4.2 学生信息表案例
![20241012112942](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012112942.png)

![20241012112950](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012112950.png)

![20241012112959](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012112959.png)

![20241012113007](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012113007.png)

![20241012113015](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012113015.png)

![20241012113027](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012113027.png)

![20241012113036](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012113036.png)

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
    姓名：<input type="text" class="uname" name="uname" />
    年龄：<input type="text" class="age" name="age" />
    性别:
    <select name="gender" class="gender">
      <option value="男">男</option>
      <option value="女">女</option>
    </select>
    薪资：<input type="text" class="salary" name="salary" />
    就业城市：<select name="city" class="city">
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
    // 获取元素
    const uname = document.querySelector('.uname')
    const age = document.querySelector('.age')
    const gender = document.querySelector('.gender')
    const salary = document.querySelector('.salary')
    const city = document.querySelector('.city')
    const tbody = document.querySelector('tbody')
    // 获取所有带有name属性的 元素
    const items = document.querySelectorAll('[name]')
    // 声明一个空的数组， 增加和删除都是对这个数组进行操作
    const arr = []

    // 1. 录入模块
    // 1.1 表单提交事件
    const info = document.querySelector('.info')
    info.addEventListener('submit', function (e) {
      // 阻止默认行为  不跳转
      e.preventDefault()
      // console.log(11)

      // 这里进行表单验证  如果不通过，直接中断，不需要添加数据
      // 先遍历循环
      for (let i = 0; i < items.length; i++) {
        if (items[i].value === '') {
          return alert('输入内容不能为空')
        }
      }
      // 创建新的对象
      const obj = {
        stuId: arr.length + 1,
        uname: uname.value,
        age: age.value,
        gender: gender.value,
        salary: salary.value,
        city: city.value
      }
      // console.log(obj)
      // 追加给数组里面
      arr.push(obj)
      // console.log(arr)
      // 清空表单  重置 
      this.reset()
      // 调用渲染函数
      render()
    })


    // 2. 渲染函数 因为增加和删除都需要渲染
    function render() {
      // 先清空tbody 以前的行 ，把最新数组里面的数据渲染完毕 
      tbody.innerHTML = ''
      // 遍历arr数组
      for (let i = 0; i < arr.length; i++) {
        // 生成 tr 
        const tr = document.createElement('tr')
        tr.innerHTML = `
          <td>${arr[i].stuId}</td>
          <td>${arr[i].uname}</td>
          <td>${arr[i].age}</td>
          <td>${arr[i].gender}</td>
          <td>${arr[i].salary}</td>
          <td>${arr[i].city}</td>
          <td>
            <a href="javascript:" data-id=${i}>删除</a>
          </td>
        `
        // 追加元素  父元素.appendChild(子元素)
        tbody.appendChild(tr)
      }
    }


    // 3. 删除操作
    // 3.1 事件委托 tbody
    tbody.addEventListener('click', function (e) {
      if (e.target.tagName === 'A') {
        // 得到当前元素的自定义属性 data-id
        // console.log(e.target.dataset.id)
        // 删除arr 数组里面对应的数据
        arr.splice(e.target.dataset.id, 1)
        console.log(arr)
        // 从新渲染一次
        render()
      }
    })
  </script>

</body>

</html>
```

# 5. 浏览器界面渲染
## 5.1 浏览器是如何进行界面渲染的
- 解析（Parser）HTML，生成DOM树(DOM Tree)
- 同时解析（Parser） CSS，生成样式规则 (Style Rules)
- 根据DOM树和样式规则，生成渲染树(Render Tree)
- 进行布局 Layout(回流/重排):根据生成的渲染树，得到节点的几何信息（位置，大小）
- 进行绘制 Painting(重绘): 根据计算和获取的信息进行整个页面的绘制
- Display: 展示在页面上

    ![20241012120907](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241012120907.png)

## 5.2 重绘和回流
- **回流(重排)**：当 Render Tree 中部分或者全部元素的尺寸、结构、布局等发生改变时，浏览器就会重新渲染部分或全部文档的过程称为回流。
- **重绘**：节点(元素)的样式的改变并不影响它在文档流中的位置和文档布局时(比如：`color`、`background-color`、`outline`等), 称为重绘。
- **重绘不一定引起回流，而回流一定会引起重绘。**
- 会导致回流（重排）的操作：简单理解影响到布局了，就会有回流
  - 页面的首次刷新
  - 浏览器的窗口大小发生改变
  - 元素的大小或位置发生改变
  - 改变字体的大小
  - 内容的变化（如：input框的输入，图片的大小）
  - 激活css伪类 （如：`:hover`）
  - 脚本操作DOM（添加或者删除可见的DOM元素）
