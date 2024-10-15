---
layout: post
title: "Web APIs 六、正则表达式"
author: "Kai"
header-style: text
tags:
  - Browser
  - 正则表达式
---

转自：https://juejin.cn/post/7209960560666083365

# 1. 正则表达式
## 1.1 介绍
- 正则表达式（Regular Expression）是用于匹配字符串中字符组合的模式。在 JavaScript中，正则表达式也是对象，通常用来查找、替换那些符合正则表达式的文本。
- 正则表达式在 JavaScript中的使用场景：
  - 例如验证表单：用户名表单只能输入英文字母、数字或者下划线， 昵称输入框中可以输入中文(匹配)
  - 过滤掉页面内容中的一些敏感词(替换)，或从字符串中获取我们想要的特定部分(提取)等 。

## 1.2 语法
- 定义规则：`const 变量名 = /表达式/`
- 查找：`regobj.test(被检测的字符串)`，如果正则表达式与指定的字符串匹配 ，返回true，否则false
    ![20241015174742](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015174742.png)
- **检索（查找）符合规则的字符串**：`regobj.exec(被检测字符串)`，返回匹配到的数组，没有匹配返回null

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>正则表达式的使用</title>
    </head>

    <body>
        <script>
            const str = '我们在学习前端，希望学习前端能高薪毕业'
                // 正则表达式使用：
                // 1. 定义规则
            const reg = /前端/
                // 2. 是否匹配
                // console.log(reg.test(str))  // true
                // 3. exec()
            console.log(reg.exec(str)) // 返回数组
        </script>
    </body>

    </html>
    ```

## 1.3 元字符
- 元字符
  - **普通字符**：大多数的字符仅能够描述它们本身，这些字符称作普通字符，普通字符只能够匹配字符串中与它们相同的字符。
  - **元字符(特殊字符)**：是一些具有特殊含义的字符，可以极大提高了灵活性和强大的匹配功能。
    - 比如，规定用户只能输入英文26个英文字母，普通字符的话 `abcdefghijklm...`
    - 但是换成元字符写法：`[a-z]`
  - **参考文档**：
    - MDN：[developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions)
    - 正则测试工具: [tool.oschina.net/regex](https://tool.oschina.net/regex)
  - 元字符分类：
    - 边界符（表示位置，开头和结尾，必须用什么开头，用什么结尾）
    - 量词 （表示重复次数）
    - 字符类 （比如 `\d` 表示 0~9）
- **边界符**：正则表达式中的边界符（位置符）用来**提示字符所处的位置**，主要有两个字符，如果 `^` 和 `$` 在一起，表示必须是精确匹配。
    ![20241015175221](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015175221.png)
- **量词**：量词用来**设定某个模式出现的次数，逗号左右两侧千万不要出现空格**
    ![20241015175305](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015175305.png)
- **字符类**：
  - **`[]` 匹配字符集合**：后面的字符串只要包含 abc 中任意**一个字符**，都返回 true 。
  - **`[]` 里面加上 `-` 连字符**：表示一个范围
    - `[a-z]` 表示 a 到 z 26个英文字母都可以
    - `[a-zA-Z]` 表示大小写都可以
    - `[0-9]` 表示 0~9 的数字都可以
  - **`[]` 里面加上 `^` 取反符号**：表示不在括号范围
    - `[^a-z]` 匹配除了小写字母以外的字符
  - **`.` 匹配除换行符之外的任何单个字符**
  - **预定义**：**指的是 某些常见模式的简写方式**。

    ![20241015175616](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015175616.png)

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>元字符</title>
    </head>

    <body>
        <script>
            // 元字符分成三类：边界符，量词，字符类

            // 1. 边界符
            console.log('----------边界符--------')
            console.log(/^哈/.test('哈')) // true
            console.log(/^哈/.test('哈哈')) // true
            console.log(/^哈/.test('二哈')) // flase
            console.log(/^哈$/.test('哈')) // true  只有这种情况为true 否则全是false
            console.log(/^哈$/.test('哈哈')) // false
            console.log(/^哈$/.test('二哈')) // false

            // 2. 量词
            console.log('---------量词---------')
                // * 表示 >=0 次
            console.log('---------* 表示 >=0 次---------')
            console.log(/^哈$/.test('哈')) // true
            console.log(/^哈*$/.test('')) // true
            console.log(/^哈*$/.test('哈')) // true
            console.log(/^哈*$/.test('哈哈')) // true
            console.log(/^哈*$/.test('二哈很傻')) //  false
            console.log(/^哈*$/.test('哈很傻')) //  false
            console.log(/^哈*$/.test('哈很哈')) // false
            console.log('------------------')
                //  + 表示 >=1 次
            console.log('---------+ 表示 >=1 次---------')
            console.log(/^哈$/.test('哈')) // true
            console.log(/^哈+$/.test('')) // false
            console.log(/^哈+$/.test('哈')) // true
            console.log(/^哈+$/.test('哈哈')) // true
            console.log(/^哈+$/.test('二哈很傻')) //  false
            console.log(/^哈+$/.test('哈很傻')) //  false
            console.log(/^哈+$/.test('哈很哈')) // false
                // ? 表示  0 || 1 次
            console.log('---------? 表示  0 || 1 次---------')
            console.log(/^哈?$/.test('')) // true
            console.log(/^哈?$/.test('哈')) // true
            console.log(/^哈?$/.test('哈哈')) // true
            console.log(/^哈?$/.test('二哈很傻')) //  false
            console.log(/^哈?$/.test('哈很傻')) //  false
            console.log(/^哈?$/.test('哈很哈')) // false
                // {n} 表示写几，就必须出现几次
            console.log('---------{n} 表示写几，就必须出现几次---------')
            console.log(/^哈{4}$/.test('哈'))
            console.log(/^哈{4}$/.test('哈哈'))
            console.log(/^哈{4}$/.test('哈哈哈'))
            console.log(/^哈{4}$/.test('哈哈哈哈'))
            console.log(/^哈{4}$/.test('哈哈哈哈哈'))
            console.log(/^哈{4}$/.test('哈哈哈哈哈哈'))
                // {n,} 表示 >=n 次
            console.log('---------{n,} 表示 >=n 次---------')
            console.log(/^哈{4,}$/.test('哈'))
            console.log(/^哈{4,}$/.test('哈哈'))
            console.log(/^哈{4,}$/.test('哈哈哈'))
            console.log(/^哈{4,}$/.test('哈哈哈哈'))
            console.log(/^哈{4,}$/.test('哈哈哈哈哈'))
            console.log(/^哈{4,}$/.test('哈哈哈哈哈哈'))
                //  {n,m}  逗号左右两侧千万不能有空格，表示 >=n && <= m
            console.log('---------{n,m}  逗号左右两侧千万不能有空格，表示 >=n && <= m---------')
            console.log(/^哈{4,6}$/.test('哈'))
            console.log(/^哈{4,6}$/.test('哈哈'))
            console.log(/^哈{4,6}$/.test('哈哈哈'))
            console.log(/^哈{4,6}$/.test('哈哈哈哈'))
            console.log(/^哈{4,6}$/.test('哈哈哈哈哈'))
            console.log(/^哈{4,6}$/.test('哈哈哈哈哈哈'))
            console.log(/^哈{4,6}$/.test('哈哈哈哈哈哈哈'))
                // 字符类
            console.log('---------字符类---------')
                //[abc]  只选1个
            console.log('---------[abc]  只选1个---------')
            console.log(/^[abc]$/.test('a')) // true
            console.log(/^[abc]$/.test('b')) // true
            console.log(/^[abc]$/.test('c')) // true
            console.log(/^[abc]$/.test('ab')) // false
            console.log(/^[abc]{2}$/.test('ab')) // true
                // [a-z]  只选1个
            console.log('---------[a-z]  只选1个---------')
            console.log(/^[A-Z]$/.test('p')) // false
            console.log(/^[A-Z]$/.test('P')) // true
            console.log(/^[0-9]$/.test(2)) // true
            console.log(/^[a-zA-Z0-9]$/.test(2)) // true
            console.log(/^[a-zA-Z0-9]$/.test('p')) // true
            console.log(/^[a-zA-Z0-9]$/.test('P')) // true
        </script>
    </body>

    </html>
    ```

- **验证用户名案例：**
    ![20241015175743](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015175743.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>验证用户名案例</title>
        <style>
            span {
                display: inline-block;
                width: 250px;
                height: 30px;
                vertical-align: middle;
                line-height: 30px;
                padding-left: 15px;
            }
            
            .error {
                color: red;
                background: url(./images/error1.png) no-repeat left center;
            }
            
            .right {
                color: green;
                background: url(./images/right.png) no-repeat left center;
            }
        </style>
    </head>

    <body>
        <input type="text">
        <span></span>
        <script>
            // 1. 准备正则
            const reg = /^[a-zA-Z0-9-_]{6,16}$/
            const input = document.querySelector('input')
            const span = input.nextElementSibling
            input.addEventListener('blur', function() {
                //2. 验证
                if (reg.test(this.value)) {
                    span.innerHTML = '输入正确'
                    span.className = 'right'
                } else {
                    span.innerHTML = '请输入6~16位的英文数字下划线'
                    span.className = 'error'
                }
            })
        </script>
    </body>

    </html>
    ```

## 1.4 修饰符
- 修饰符约束正则执行的某些细节行为，如是否区分大小写、是否支持多行匹配等
- 语法：`/表达式/修饰符`
  - `i` 是单词 ignore 的缩写，正则匹配时字母不区分大小写
  - `g` 是单词 global 的缩写，匹配所有满足正则表达式的结果

    ![20241015175845](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015175845.png)
- **替换匹配的字符串**：`字符串.replace(/正则表达式/, '替换的文本')`
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>正则修饰符</title>
    </head>

    <body>
        <script>
            //修饰符
            console.log(/^java$/.test('java'))
            console.log(/^java$/i.test('JAVA'))
            console.log(/^java$/i.test('Java'))

            //替换匹配到的字符
            const str = 'java是一门编程语言， 学完JAVA工资很高'
            const re = str.replace(/java/ig, '前端')
            console.log(re) // 前端是一门编程语言， 学完前端工资很高
        </script>
    </body>

    </html>
    ```

- **过滤敏感词：**
    ![20241015175943](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015175943.png)
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>过滤敏感词</title>
    </head>

    <body>
        <textarea name="" id="" cols="30" rows="10"></textarea>
        <button>发布</button>
        <div></div>
        <script>
            const tx = document.querySelector('textarea')
            const btn = document.querySelector('button')
            const div = document.querySelector('div')
            btn.addEventListener('click', function() {
                // console.log(tx.value)
                div.innerHTML = tx.value.replace(/激情|基情/g, '**')
                tx.value = ''
            })
        </script>
    </body>

    </html>
    ```

# 2. 小兔鲜注册案例
## 2.1 模块业务分析
![20241015180023](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180023.png)

## 2.2 需求
![20241015180042](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180042.png)

![20241015180051](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180051.png)

![20241015180117](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180117.png)

## 2.3 代码
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>小兔鲜儿 - 新鲜 惠民 快捷!</title>
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="renderer" content="webkit">
    <link rel="shortcut icon" href="./favicon.ico">
    <link rel="stylesheet" href="./css/common.css">
    <link rel="stylesheet" href="./css/register.css">
    <link rel="stylesheet" href="https://at.alicdn.com/t/font_2143783_iq6z4ey5vu.css">
</head>

<body>
    <!-- 项部导航 -->
    <div class="xtx_topnav">
        <div class="wrapper">
            <!-- 顶部导航 -->
            <ul class="xtx_navs">
                <li>
                    <a href="./login.html">请先登录</a>
                </li>
                <li>
                    <a href="./register.html">免费注册</a>
                </li>
                <li>
                    <a href="./center-order.html">我的订单</a>
                </li>
                <li>
                    <a href="./center.html">会员中心</a>
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
                        <a href="./index.html">首页</a>
                    </li>
                    <li>
                        <a href="./category01.html">生鲜</a>
                    </li>
                    <li>
                        <a href="./category01.html">美食</a>
                    </li>
                    <li>
                        <a href="./category01.html">餐厨</a>
                    </li>
                    <li>
                        <a href="./category01.html">电器</a>
                    </li>
                    <li>
                        <a href="./category01.html">居家</a>
                    </li>
                    <li>
                        <a href="./category01.html">洗护</a>
                    </li>
                    <li>
                        <a href="./category01.html">孕婴</a>
                    </li>
                    <li>
                        <a href="./category01.html">服装</a>
                    </li>
                </ul>
            </div>
            <!-- 站内搜索 -->
            <div class="xtx_search clearfix">
                <!-- 购物车 -->
                <a href="./cart-none.html" class="xtx_search_cart sprites">
                    <i>2</i>
                </a>
                <!-- 搜索框 -->
                <div class="xtx_search_wrapper">
                    <input type="text" placeholder="搜一搜" onclick="location.href='./search.html'">
                </div>
            </div>
        </div>
    </div>
    <div class="xtx-wrapper">
        <div class="container">
            <!-- 卡片 -->
            <div class="xtx-card">
                <h3>新用户注册</h3>
                <form class="xtx-form">
                    <div data-prop="username" class="xtx-form-item">
                        <span class="iconfont icon-zhanghao"></span>
                        <input name="username" type="text" placeholder="设置用户名称">
                        <span class="msg"></span>
                    </div>
                    <div data-prop="phone" class="xtx-form-item">
                        <span class="iconfont icon-shouji"></span>
                        <input name="phone" type="text" placeholder="输入手机号码  ">
                        <span class="msg"></span>
                    </div>
                    <div data-prop="code" class="xtx-form-item">
                        <span class="iconfont icon-zhibiaozhushibiaozhu"></span>
                        <input name="code" type="text" placeholder="短信验证码">
                        <span class="msg"></span>
                        <a class="code" href="javascript:;">发送验证码</a>
                    </div>
                    <div data-prop="password" class="xtx-form-item">
                        <span class="iconfont icon-suo"></span>
                        <input name="password" type="password" placeholder="设置6至20位字母、数字和符号组合">
                        <span class="msg"></span>
                    </div>
                    <div data-prop="confirm" class="xtx-form-item">
                        <span class="iconfont icon-suo"></span>
                        <input name="confirm" type="password" placeholder="请再次输入上面密码">
                        <span class="msg"></span>
                    </div>
                    <div class="xtx-form-item pl50">
                        <i class="iconfont icon-queren"></i> 已阅读并同意
                        <i>《用户服务协议》</i>
                    </div>
                    <div class="xtx-form-item">
                        <button class="submit">下一步</button>
                        <!-- <a class="submit" href="javascript:;">下一步</a> -->
                    </div>
                </form>
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
    <script>
        (function() {
            // 1. 发送短信验证码模块
            const code = document.querySelector('.code')
            let flag = true // 通过一个变量来控制   节流阀 
                //  1.1 点击事件
            code.addEventListener('click', function() {
                if (flag) {
                    // 取反了，不能马上第二次点击
                    flag = false
                    let i = 5
                        // 点击完毕之后立马触发
                    code.innerHTML = `0${i}秒后重新获取`
                        // 开启定时器
                    let timerId = setInterval(function() {
                        i--
                        code.innerHTML = `0${i}秒后重新获取`
                        if (i === 0) {
                            // 清除定时器
                            clearInterval(timerId)
                                // 从新获取
                            code.innerHTML = `重新获取`
                                // 到时间了，可以开启 flag了
                            flag = true
                        }
                    }, 1000)
                }
            })
        })();


        // 2. 验证的是用户名
        // 2.1 获取用户名表单
        const username = document.querySelector('[name=username]')
            // 2.2 使用change事件  值发生变化的时候
        username.addEventListener('change', verifyName)
            // 2.3 封装verifyName函数
        function verifyName() {
            // console.log(11)
            const span = username.nextElementSibling
                // 2.4 定规则  用户名
            const reg = /^[a-zA-Z0-9-_]{6,10}$/
            if (!reg.test(username.value)) {
                // console.log(11)
                span.innerText = '输入不合法,请输入6~10位'
                return false
            }
            // 2.5 合法的 就清空span
            span.innerText = ''
            return true
        }



        // 3. 验证的是手机号
        // 2.1 获取手机表单
        const phone = document.querySelector('[name=phone]')
            // 2.2 使用change事件  值发生变化的时候
        phone.addEventListener('change', verifyPhone)
            // 2.3 verifyPhone
        function verifyPhone() {
            // console.log(11)
            const span = phone.nextElementSibling
                // 2.4 定规则  用户名
            const reg = /^1(3\d|4[5-9]|5[0-35-9]|6[567]|7[0-8]|8\d|9[0-35-9])\d{8}$/
            if (!reg.test(phone.value)) {
                // console.log(11)
                span.innerText = '输入不合法,请输入正确的11位手机号码'
                return false
            }
            // 2.5 合法的 就清空span
            span.innerText = ''
            return true
        }


        // 4. 验证的是验证码
        // 4.1 获取验证码表单
        const codeInput = document.querySelector('[name=code]')
            //4.2 使用change事件  值发生变化的时候
        codeInput.addEventListener('change', verifyCode)
            // 4.3 verifyPhone
        function verifyCode() {
            // console.log(11)
            const span = codeInput.nextElementSibling
                // 4.4 定规则  验证码
            const reg = /^\d{6}$/
            if (!reg.test(codeInput.value)) {
                // console.log(11)
                span.innerText = '输入不合法,6 位数字'
                return false
            }
            // 4.5 合法的 就清空span
            span.innerText = ''
            return true
        }

        // 5. 验证的是密码框
        // 5.1 获取密码表单
        const password = document.querySelector('[name=password]')
            //5.2 使用change事件  值发生变化的时候
        password.addEventListener('change', verifyPwd)
            // 5.3 verifyPhone
        function verifyPwd() {
            // console.log(11)
            const span = password.nextElementSibling
                // 5.4 定规则  密码
            const reg = /^[a-zA-Z0-9-_]{6,20}$/
            if (!reg.test(password.value)) {
                // console.log(11)
                span.innerText = '输入不合法,6~20位数字字母符号组成'
                return false
            }
            // 5.5 合法的 就清空span
            span.innerText = ''
            return true
        }



        // 6. 密码的再次验证
        // 6.1 获取再次验证表单
        const confirm = document.querySelector('[name=confirm]')
            //6.2 使用change事件  值发生变化的时候
        confirm.addEventListener('change', verifyConfirm)
            // 6.3 verifyPhone
        function verifyConfirm() {
            // console.log(11)
            const span = confirm.nextElementSibling
                // 6.4 当前表单的值不等于 密码框的值就是错误的
            if (confirm.value !== password.value) {
                // console.log(11)
                span.innerText = '两次密码输入不一致'
                return false
            }
            // 6.5 合法的 就清空span
            span.innerText = ''
            return true
        }

        // 7. 我同意
        const queren = document.querySelector('.icon-queren')
        queren.addEventListener('click', function() {
            // 切换类  原来有的就删掉，原来没有就添加
            this.classList.toggle('icon-queren2')
        })

        // 8. 提交模块
        const form = document.querySelector('form')
        form.addEventListener('submit', function(e) {
            // 判断是否勾选我同意模块 ，如果有 icon-queren2说明就勾选了，否则没勾选
            if (!queren.classList.contains('icon-queren2')) {
                alert('请勾选同意协议')
                    // 阻止提交
                e.preventDefault()
            }
            // 依次判断上面的每个框框 是否通过，只要有一个没有通过的就阻止
            // console.log(verifyName())
            if (!verifyName()) e.preventDefault()
            if (!verifyPhone()) e.preventDefault()
            if (!verifyCode()) e.preventDefault()
            if (!verifyPwd()) e.preventDefault()
            if (!verifyConfirm()) e.preventDefault()
        })
    </script>
</body>

</html>
```

# 3. 小兔鲜登录案例
## 3.1 需求
![20241015180205](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180205.png)

![20241015180214](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180214.png)

![20241015180224](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180224.png)

![20241015180232](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180232.png)

## 3.2 步骤
![20241015180250](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180250.png)

![20241015180300](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241015180300.png)

## 3.3 代码
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>小兔鲜儿 - 新鲜 惠民 快捷!</title>
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="renderer" content="webkit">
    <link rel="shortcut icon" href="../favicon.ico">
    <link rel="stylesheet" href="./css/common.css">
    <link rel="stylesheet" href="./css/login.css">
    <link rel="stylesheet" href="https://at.alicdn.com/t/font_2143783_iq6z4ey5vu.css">
</head>

<body>
    <!-- 登录头部 -->
    <div class="xtx-login-header">
        <h1 class="logo"></h1>
        <a class="home" href="./index.html">进入网站首页</a>
    </div>
    <!-- 登录内容 -->
    <div class="xtx-login-main">
        <div class="wrapper">
            <form action="" autocomplete="off">
                <div class="box">
                    <div class="tab-nav">
                        <a href="javascript:;" class="active" data-id="0">账户登录</a>
                        <a href="javascript:;" data-id="1">二维码登录</a>
                    </div>
                    <div class="tab-pane">
                        <div class="link">
                            <a href="javascript:;">手机验证码登录</a>
                        </div>
                        <div class="input">
                            <span class="iconfont icon-zhanghao"></span>
                            <input required type="text" placeholder="请输入用户名称/手机号码" name="username">
                        </div>
                        <div class="input">
                            <span class="iconfont icon-suo"></span>
                            <input required type="password" placeholder="请输入密码" name="password">
                        </div>
                        <div class="agree">
                            <label for="my-checkbox">
                <input type="checkbox" value="1" id="my-checkbox" class="remember" name="agree">
                <span class="iconfont icon-xuanze"></span>
              </label> 我已同意 <a href="javascript:;">《服务条款》</a href="javascript:;"> 和 <a>《服务条款》</a>
                        </div>
                        <div class="button clearfix">
                            <button type="submit" class="dl">登 录</button>
                            <!-- <a class="dl" href="./center.html">登 录</a> -->
                            <a class="fl" href="./forget.html">忘记密码？</a>
                            <a class="fr" href="./register.html">免费注册</a>
                        </div>
                    </div>
                    <div class="tab-pane" style="display: none;">
                        <img class="code" src="./images/code.png" alt="">
                    </div>
                </div>
            </form>
        </div>
    </div>
    <!-- 登录底部 -->
    <div class="xtx-login-footer">
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
    <script>
        // 1. tab栏切换  事件委托
        const tab_nav = document.querySelector('.tab-nav')
        const pane = document.querySelectorAll('.tab-pane')
            // 1.1 事件监听
        tab_nav.addEventListener('click', function(e) {
            if (e.target.tagName === 'A') {
                // 取消上一个active
                tab_nav.querySelector('.active').classList.remove('active')
                    // 当前元素添加active
                e.target.classList.add('active')

                // 先干掉所有人  for循环
                for (let i = 0; i < pane.length; i++) {
                    pane[i].style.display = 'none'
                }
                // 让对应序号的 大pane 显示 
                pane[e.target.dataset.id].style.display = 'block'
            }
        })

        // 点击提交模块
        const form = document.querySelector('form')
        const agree = document.querySelector('[name=agree]')
        const username = document.querySelector('[name=username]')
        form.addEventListener('submit', function(e) {
            e.preventDefault()
                // 判断是否勾选同意协议
            if (!agree.checked) {
                return alert('请勾选同意协议')
            }

            // 记录用户名到本地存储
            localStorage.setItem('xtx-uname', username.value)
                // 跳转到首页
            location.href = './index.html'
        })
    </script>
</body>

</html>
```
