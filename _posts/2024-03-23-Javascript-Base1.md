---
layout: post
title: "JavaScript基础一、简介"
author: "Kai"
header-style: text
tags:
  - Javascript
---

转自：https://juejin.cn/post/7207314488243077177

# 1. 简介
## 1.1 JavaScript是什么
是一种运行在**客户端（浏览器）**的编程语言，实现人机交互效果。

## 1.2 JavaScript做什么
- 网页特效 (监听用户的一些行为让网页作出对应的反馈)
- 表单验证 (针对表单数据的合法性进行判断)
- 数据交互 (获取后台的数据, 渲染到前端)
- 服务端编程 (node.js)

    ![20241003222126](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241003222126.png)

## 1.3 JavaScript的组成
- **ECMAScript**：规定了js基础语法核心知识。比如：变量、分支语句、循环语句、对象等等，ECMAScript是一种语言标准，而JavaScript是对ECMAScript标准的一种实现。
- **Web APIs**:
  - DOM 操作文档，比如对页面元素进行移动、大小、添加删除等操作
  - BOM 操作浏览器，比如页面弹窗，检测窗口宽度、存储数据到浏览器等等

    ![20241003222216](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241003222216.png)

## 1.4 ECMAScript版本
- JavaScript历史
  - 在上个世纪的1995年，当时的网景公司正凭借其Navigator浏览器成为Web时代开启时最著名的第一代互联网公司。
  - 由于网景公司希望能在静态HTML页面上添加一些动态效果，于是叫Brendan Eich这哥们在两周之内设计出了JavaScript语言。
  - 为什么起名叫JavaScript？原因是当时Java语言非常红火，所以网景公司希望借Java的名气来推广。
- ECMAScript
  - 因为网景开发了JavaScript，一年后微软又模仿JavaScript开发了JScript，为了让JavaScript成为全球标准，几个公司联合ECMA（European Computer Manufacturers Association）组织定制了JavaScript语言的标准，被称为ECMAScript标准。
  - 所以简单说来就是，ECMAScript是一种语言标准，而JavaScript是网景公司对ECMAScript标准的一种实现。
  - 不过大多数时候，如果你遇到ECMAScript这个词，简单把它当成JavaScript就行了。
- JavaScript版本
  - 由于JavaScript的标准ECMAScript在不断发展，不停的迭代新的版本，所以，讲到JavaScript的版本，实际上就是说它实现了ECMAScript标准的哪个版本。
  - 2015年6月17日，ECMAScript 6发布正式版本，即ECMAScript 2015。ES6是继ES5之后的一次主要改进，语言规范由ES5.1时代的245页扩充至600页。ECMAScript 6之后，Ecma国际意在更频繁地发布包含小规模增量更新的新版本，新版本将按照ECMAScript+年份的形式发布。
  - 你在写JavaScript的时候，要照顾一下老用户，不能一上来就用ES6标准写，否则，老用户的浏览器是无法运行新版本的JavaScript代码的。

## 1.5 参考网站
- 权威网站： [MDN](https://developer.mozilla.org/zh-CN/)
- JavaScript权威网站： [developer.mozilla.org/zh-CN/docs/Web/JavaScript](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)

# 2. 初体验
## 2.1 体验js案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>体验js</title>
    <style>
        .pink {
            background-color: pink;
        }
    </style>
</head>

<body>
    <button class="pink">按钮1</button>
    <button>按钮2</button>
    <button>按钮3</button>
    <button>按钮4</button>
    <script>
        let bts = document.querySelectorAll('button')
        for (let i = 0; i < bts.length; i++) {
            bts[i].addEventListener('click', function() {
                document.querySelector('.pink').className = ''
                this.className = 'pink'
            })
        }
    </script>
</body>

</html>
```
点击按钮，按钮变色
![20241003222440](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241003222440.png)

## 2.2 注释
- 单行注释
  - **符号**：//
  - **作用**：//右边这一行的代码会被忽略
  - **快捷键**：ctrl + /
- 块注释
  - **符号**：/* */
  - **作用**：在/* 和 */ 之间的所有内容都会被忽略
  - **快捷键**：shift + alt + A

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>js注释</title>
    </head>

    <body>
        <script>
            //单行注释
            //右边这一行的代码会被忽略
            //快捷键：ctrl + /

            //块注释
            /* 
            之间的所有内容都会被忽略
            快捷键：shift + alt + A 
            */
        </script>
    </body>

    </html>
    ```

## 2.3 结束符
- **作用**： 使用英文的 ; 代表语句结束
- **实际情况**： 实际开发中，可写可不写, 浏览器(JavaScript 引擎) 可以自动推断语句的结束位置
- **现状**： 在实际开发中，越来越多的人主张，书写 JavaScript 代码时省略结束符
- **约定**：**为了风格统一，结束符要么每句都写，要么每句都不写（按照团队要求）**

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>js结束符</title>
    </head>

    <body>
        <script>
            //要么都写分号，要么都不写
            //alert('你好');
            //alert('你好2');

            alert('你好')
            alert('你好2')
        </script>
    </body>

    </html>
    ```

## 2.4 script标签位置
![20241003222737](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241003222737.png)
- 内部 JavaScript
  - 直接写在**html**文件里，写在`</body>`上面，用**script**标签包住
  - 注意事项：文件加载顺序
    - 浏览器会按照代码在文件中的顺序加载 HTML。
    - 如果先加载的JS再加载需要JS操作的HTML，那么它可能由于 HTML 尚未被加载而失效。
    - 因此，将 JavaScript 代码放在 HTML页面的底部附近通常是最好的策略。
- 外部 JavaScript
  - 代码写在以**.js**结尾的文件里，可以使代码更加有序，更易于复用，且没有了脚本的混合，HTML 也会更加易读。
  - **语法**：通过**script**标签，引入到**html**页面中。
  - 注意事项：**script**标签中间写的代码会被忽略！
- 内联 JavaScript
  - 代码写在标签内部
  - 此处作为了解即可，但是后面vue框架会用这种模式

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>js书写位置</title>
    </head>

    <body>
        <!-- 内部js -->
        <script>
            // 页面弹出警示框
            alert('你好，js~')
        </script>

        <!-- 外部js -->
        <script src="./js/my.js">
            // 中间不要写内容
        </script>

        <!-- 内联js -->
        <button onclick="alert('你好，js')">点击我</button>
    </body>

    </html>
    ```

# 3. 输入和输出
## 3.1 语法是什么
- 人和计算机的规则约定，我们要按照这个规则写代码，计算机才能看懂
- 输出和输入也可理解为人和计算机的交互，用户通过键盘、鼠标等向计算机输入信息，计算机处理后再展示结果给用户，这便是一次输入和输出的过程。

## 3.2 输出语法
- **向body内输出内容**：`document.write('页面打印')`
  - **注意：**如果输出的内容写的是标签，也会被解析成网页元素
- **页面弹出警告对话框**：`alert('弹出内容')`
- **控制台输出，程序员调试用**：`console.log('控制台打印')`

## 3.3 输入语法
**语法**：`prompt('请输入您的姓名：')`
**作用**：显示一个对话框，对话框中包含一条文字信息，用来提示用户输入文字

## 3.4 案例
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>输入和输出语法</title>
</head>

<body>

    <script>
        // 1. 文档输出内容
        document.write('我是div标签')
        document.write('<h1>我是标题</h1>')
        // 2. 弹框提示
        alert('我是弹框')
        // 3. 控制台打印输出 给 程序员
        console.log('看看对不对')
        console.log('日志')
        // 4. 输入语句
        prompt('请输入您的年龄：')
    </script>
</body>

</html>
```

**JavaScript 代码执行顺序：**
- 按HTML文档流顺序执行JavaScript代码
- alert() 和 prompt() 它们会跳过页面渲染先被执行（目前作为了解，后期讲解详细执行过程）

    ![20241003223207](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241003223207.png)
    ![20241003223252](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241003223252.png)
    ![20241003223301](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241003223301.png)
