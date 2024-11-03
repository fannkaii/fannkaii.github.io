---
layout: post
title: "前后端交互 一、服务器概念与初识Ajax"
author: "Kai"
header-style: text
tags:
  - Javascript
  - Ajax
---

转自：https://juejin.cn/post/7242284648020361275

# 1. 上网的过程
- 网络相关知识请参考 [网络详解](https://blog.csdn.net/liyou123456789/article/details/122731144)
- HTTP相关知识请参考 [HTTP详解](https://blog.csdn.net/liyou123456789/article/details/127138103)

## 1.1 客户端与服务器
- **上网的目的**：通过互联网**获取和消费资源**
    ![20241103110353](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103110353.png)

- **服务器**：上网过程中，负责**存放和对外提供资源**的电脑，叫做**服务器**。
    ![20241103110426](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103110426.png)

- **客户端**：上网过程中，**获取和消费资源**的电脑，叫做**客户端**。
    ![20241103110445](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103110445.png)

## 1.2 URL地址
- URL地址的概念
  - URL（全称是UniformResourceLocator）中文叫**统一资源定位符**，用于**标识互联网上每个资源的唯一存放位置**。**浏览器只有通过URL地址**，才能正确定位资源的存放位置，从而**成功访问到对应的资源**。
  - 常见的URL举例：
    - www.baidu.com
    - www.taobao.com
    - blog.csdn.net/liyou123456789
- URL地址的组成部分
  - URL由三部分组成：资源类型、存放资源的主机域名、资源文件名。
  - 也可认为由4部分组成：协议、主机、端口、路径
  - URL的一般语法格式为：`protocol://hostname[:port]/path/[:parameters][?query]#fragment`
  - 比如：blog.csdn.net/liyou123456789
    - 协议：https
    - 主机：blog.csdn.net
    - 端口：默认端口
    - 路径：liyou123456789

## 1.3 浏览器访问网页
- **浏览器（客户端）与服务器的通信过程**
  - 客户端与服务器之间的通信过程，分为 **请求 – 处理 – 响应** 三个步骤。
  - 网页中的每一个资源，都是通过 **请求 – 处理 – 响应** 的方式从服务器获取回来的。

    ![20241103111658](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103111658.png)

- **基于浏览器的开发者工具分析通信过程**
  - 打开 Chrome 浏览器
  - Ctrl+Shift+I 打开 Chrome 的开发者工具
  - 切换到 Network 面板
  - 请求数据
  - 分析客户端与服务器的通信过程

    ![20241103111734](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103111734.png)

## 1.4 资源的种类
- **网页中常见的资源**
  ![20241103222535](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103222535.png)

- **数据也是资源**
  - 网页中的数据，也是服务器对外提供的一种资源。
  - 数据是网页的灵魂，HTML是网页的骨架，CSS是网页的颜值，Javascript是网页的行为，这些皆为数据
- **网页中如何请求数据**
  - 数据，也是服务器对外提供的一种资源。只要是资源，必然要通过 **请求 – 处理 – 响应** 的方式进行获取。
  - 如果要在网页中请求服务器上的数据资源，则需要用到 **XMLHttpRequest** 对象。**XMLHttpRequest**（简称 xhr）是浏览器提供的 js 成员，**用于请求服务器上的数据资源**。
- **资源的请求方式**
  - 客户端请求服务器时，**请求的方式**有很多种，最常见的是 **get** 和 **post** 请求。
  - **get请求**通常用于**获取服务端资源**（向服务器要资源），例如：根据 URL 地址，从服务器获取 HTML 文件、css 文件、js文件、图片文件、数据资源等
  - **post请求**通常用于**向服务器提交数据**（往服务器发送资源），例如：登录时向服务器提交的登录信息、注册时向服务器提交的注册信息、添加用户时向服务器提交的用户信息等各种数据提交操作

# 2. 初识Ajax
## 2.1 Ajax是什么
- **Ajax** 的全称是 Asynchronous Javascript And XML（异步 JavaScript 和 XML）。
- **Ajax**：在网页中利用 **XMLHttpRequest** 对象和服务器进行数据交互的方式。
- **Ajax** 技术可以在**不刷新整个页面**的情况下，通过异步请求来获取服务器数据并**更新部分页面内容**。这种异步请求将 XML 或 JSON 数据格式发送到服务器，然后在页面上使用 JavaScript 更新数据，并进行相应 UI 的更新。

- **示例代码：**
  ```javascript
  // 创建一个 XMLHttpRequest 对象
  let xhr = new XMLHttpRequest();

  // 指定请求方法和 URL
  xhr.open('GET', '/user', true);

  // 指定回调函数
  xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
      // 处理请求得到的数据
      console.log(xhr.responseText);
    }
  };

  // 发送 HTTP 请求
  xhr.send();
  ```

## 2.2 Ajax作用
- Ajax能让我们轻松实现网页与服务器之间的数据交互。
  ![20241103222852](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103222852.png)

## 2.3 Ajax应用场景
- 用户名检测：注册用户时，通过 ajax 的形式，动态检测用户名是否被占用
- 搜索提示：当输入搜索关键字时，通过 ajax 的形式，动态加载搜索提示列表
- 数据分页显示：当点击页码值的时候，通过 ajax 的形式，根据页码值动态刷新表格的数据
- 数据的增删改查：数据的添加、删除、修改、查询操作，都需要通过 ajax 的形式，来实现数据的交互

## 2.4 jQuery中的Ajax
- 浏览器中提供的 **XMLHttpRequest 用法比较复杂**，且不同的浏览器有兼容性问题，所以 **jQuery** 对 **XMLHttpRequest** 进行了封装，提供了一系列 Ajax 相关的函数，极大地**降低了Ajax的使用难度**。
- jQuery 中发起 Ajax 请求最常用的三个方法如下： `$.get()` `$.post()` `$.ajax()`
- `$.get()`
  - 说明：jQuery 中 `$.get()` 功能单一，专门用来发起 get 请求，从而将服务器上的资源请求到客户端来进行使用。
  - 语法：`$.get(url, [data], [callback])`
  - 参数说明：
    ![20241103223027](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103223027.png)

    - **发起不带参数的GET请求：**
      ```html
      <!DOCTYPE html>
      <html lang="en">

      <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <meta http-equiv="X-UA-Compatible" content="ie=edge">
          <title>Document</title>
          <script src="./lib/jquery.js"></script>
      </head>

      <body>
          <button id="btnGET">发起不带参数的GET请求</button>

          <script>
              $(function () {
                  $('#btnGET').on('click', function () {
                      $.get('http://xxx.yyy.zzz/api/getbooks', function (res) {
                          console.log(res)
                      })
                  })
              })
          </script>
      </body>

      </html>
      ```

    - **发起单参数的GET请求：**
      ```html
      <!DOCTYPE html>
      <html lang="en">

      <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <meta http-equiv="X-UA-Compatible" content="ie=edge">
          <title>Document</title>
          <script src="./lib/jquery.js"></script>
      </head>

      <body>
          <button id="btnGETINFO">发起单参数的GET请求</button>

          <script>
              $(function () {
                  $('#btnGETINFO').on('click', function () {
                      $.get('http://xxx.yyy.zzz/api/getbooks', {
                          id: 1
                      }, function (res) {
                          console.log(res)
                      })
                  })
              })
          </script>
      </body>

      </html>
      ```

- `$.post()`
  - 说明：jQuery 中 `$.post()` 功能单一，专门用来发起 post 请求，从而向服务器提交数据。
  - 语法：`$.post(url, [data], [callback])`
  - 参数说明：
    ![20241103223226](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103223226.png)

    - **发起POST请求：**
      ```html
      <!DOCTYPE html>
      <html lang="en">

      <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <meta http-equiv="X-UA-Compatible" content="ie=edge">
          <title>Document</title>
          <script src="./lib/jquery.js"></script>
      </head>

      <body>
          <button id="btnPOST">发起POST请求</button>

          <script>
              $(function () {
                  $('#btnPOST').on('click', function () {
                      $.post('http://xxx.yyy.zzz/api/addbook', {
                          bookname: '水浒传',
                          author: '施耐庵',
                          publisher: '天津图书出版社'
                      }, function (res) {
                          console.log(res)
                      })
                  })
              })
          </script>
      </body>

      </html>
      ```

- `$.ajax()`
  - 说明：相比于 `$.get()` 和 `$.post()` 函数，jQuery 中提供的 `$.ajax()` 函数，是一个功能比较综合的函数，它允许我们对 Ajax 请求进行更详细的配置。
    ```javascript
    $.ajax({
      type: '', // 请求的方式，例如 GET 或 POST
      url: '',  // 请求的 URL 地址
      data: {},// 这次请求要携带的数据
      success: function (res) { } // 请求成功之后的回调函数
    })

    // 使用 $.ajax() 发起 GET 请求时
    // 只需要将 type 属性的值设置为 'GET' 即可
    $.ajax({
      type: 'GET', // 请求的方式
      url: 'http://xxx.yyy.zzz/api/getbooks',  // 请求的 URL 地址
      data: { id: 1 },// 这次请求要携带的数据
      success: function (res) { // 请求成功之后的回调函数
        console.log(res)
      }
    })

    // 使用 $.ajax() 发起 POST 请求时
    // 只需要将 type 属性的值设置为 'POST' 即可
    $.ajax({
      type: 'POST', // 请求的方式
      url: 'http://xxx.yyy.zzz/api/addbook',  // 请求的 URL 地址
      data: { // 要提交给服务器的数据
        bookname: '水浒传',
        author: '施耐庵',
        publisher: '上海图书出版社'
      },
      success: function (res) { // 请求成功之后的回调函数
        console.log(res)
      }
    })
    ```
  - **发起GET请求：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="./lib/jquery.js"></script>
    </head>

    <body>
        <button id="btnGET">发起GET请求</button>

        <script>
            $(function () {
                $('#btnGET').on('click', function () {
                    $.ajax({
                        type: 'GET',
                        url: 'http://xxx.yyy.zzz/api/getbooks',
                        data: {
                            id: 1
                        },
                        success: function (res) {
                            console.log(res)
                        }
                    })
                })
            })
        </script>
    </body>

    </html>
    ```

  - **发起POST请求：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="./lib/jquery.js"></script>
    </head>

    <body>
        <button id="btnPOST">发起POST请求</button>

        <script>
            $(function () {
                $('#btnPOST').on('click', function () {
                    $.ajax({
                        type: 'POST',
                        url: 'http://xxx.yyy.zzz/api/addbook',
                        data: {
                            bookname: '史记',
                            author: '司马迁',
                            publisher: '上海图书出版社'
                        },
                        success: function (res) {
                            console.log(res)
                        }
                    })
                })
            })
        </script>
    </body>

    </html>
    ```

# 3. 接口
## 3.1 接口的概念
- 使用 Ajax 请求数据时，被请求的 URL 地址，就叫做数据接口（简称接口）。
- 每个接口必须有明确的请求方式（GET或者POST）。

## 3.2 接口的请求过程
- 通过GET方式请求接口的过程
  ![20241103223523](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103223523.png)
- 通过POST方式请求接口的过程
  ![20241103223537](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103223537.png)

## 3.3 接口测试工具PostMan
- 为了验证接口能否被正常被访问，我们常常需要使用接口测试工具，来检测，比较常用的工具是PostMan。
- PostMan下载地址：[www.postman.com/downloads](https://www.postman.com/downloads/)，下载之后安装即可
- PostMan界面的组成部分，从上到下，从左到右，分别是：
  - 菜单栏
  - 工具栏
  - 左侧历史记录与集合面板
  - 请求页签
  - 请求地址区域
  - 请求参数区域
  - 响应结果区域
  - 状态栏

  ![20241103223701](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103223701.png)

## 3.4 使用PostMan测试GET接口
- 选择请求的方式
- 填写请求的URL地址
- 填写请求的参数
- 点击 Send 按钮发起 GET 请求
- 查看服务器响应的结果

  ![20241103223734](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103223734.png)

## 3.5 使用PostMan测试POST接口
- 选择请求的方式
- 填写请求的URL地址
- 选择 Body 面板并**勾选数据格式**
- 填写要发送到服务器的数据
- 点击 Send 按钮发起 POST 请求
- 查看服务器响应的结果

  ![20241103223806](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103223806.png)

## 3.6 接口文档
- 接口文档，顾名思义就是**接口的说明文档**。好的接口文档包含了**接口URL**，**参数**以及**输出内容**的说明。
- 接口文档包括但不限于以下6项内容：
  - **接口名称**：用来标识各个接口的简单说明，如**登录接口，获取图书列表接口**等。
  - **接口URL**：接口的调用地址。
  - **调用方式**：接口的调用方式，如 **GET** 或 **POST**。
  - **参数格式**：接口需要传递的参数，每个参数必须包含**参数名称、参数类型、是否必选、参数说明**这4项内容。
  - **响应格式**：接口的返回值的详细描述，一般包含**数据名称、数据类型、说明**3项内容。
  - **返回示例（可选）**：通过对象的形式，例举服务器返回数据的结构。

  ![20241103224002](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103224002.png)

  ![20241103224011](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103224011.png)

  ![20241103224020](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103224020.png)

# 4. 案例 - 图书管理
## 4.1 渲染UI结构
![20241103224042](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103224042.png)

## 4.2 用到的库和插件
- 用到的 css 库 bootstrap.css
- 用到的 javascript 库 jquery.js
- 用到的 vs code 插件 Bootstrap 3 Snippets

## 4.3 渲染图书列表（核心代码）
```javascript
function getBookList() {
  // 1. 发起 ajax 请求获取图书列表数据
  $.get('http://xxx.yyy.zzz/api/getbooks', function (res) {
    // 2. 获取列表数据是否成功
    if (res.status !== 200) return alert('获取图书列表失败！')
    // 3. 渲染页面结构
    var rows = []
    $.each(res.data, function (i, item) { // 4. 循环拼接字符串
      rows.push('<tr><td>' + item.id + '</td><td>' + item.bookname + '</td><td>' + item.author + '</td><td>' + item.publisher + '</td><td><a href="javascript:;">删除</a></td></tr>')
    })
    $('#bookBody').empty().append(rows.join('')) // 5. 渲染表格结构
  })
}
```

## 4.4 删除图书（核心代码）
```javascript
// 1. 为按钮绑定点击事件处理函数
$('tbody').on('click', '.del', function () {
  // 2. 获取要删除的图书的 Id
  var id = $(this).attr('data-id')
  $.ajax({ // 3. 发起 ajax 请求，根据 id 删除对应的图书
    type: 'GET',
    url: 'http://xxx.yyy.zzz/api/delbook',
    data: { id: id },
    success: function (res) {
      if (res.status !== 200) return alert('删除图书失败！')
      getBookList() // 4. 删除成功后，重新加载图书列表
    }
  })
})
```

## 4.5 添加图书（核心代码）
```javascript
// 1. 检测内容是否为空
var bookname = $('#bookname').val()
var author = $('#author').val()
var publisher = $('#publisher').val()
if (bookname === '' || author === '' || publisher === '') {
  return alert('请完整填写图书信息！')
}
// 2. 发起 ajax 请求，添加图书信息
$.post(
  'http://xxx.yyy.zzz/api/addbook',
  { bookname: bookname, author: author, publisher: publisher },
  function (res) {
    // 3. 判断是否添加成功
    if (res.status !== 201) return alert('添加图书失败！')
    getBookList() // 4. 添加成功后，刷新图书列表
    $('input:text').val('') // 5. 清空文本框内容
  }
)
```

## 4.6 完整代码
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" href="./lib/bootstrap.css" />
    <script src="./lib/jquery.js"></script>
</head>

<body style="padding: 15px;">
    <!-- 添加图书的Panel面板 -->
    <div class="panel panel-primary">
        <div class="panel-heading">
            <h3 class="panel-title">添加新图书</h3>
        </div>
        <div class="panel-body form-inline">

            <div class="input-group">
                <div class="input-group-addon">书名</div>
                <input type="text" class="form-control" id="iptBookname" placeholder="请输入书名">
            </div>

            <div class="input-group">
                <div class="input-group-addon">作者</div>
                <input type="text" class="form-control" id="iptAuthor" placeholder="请输入作者">
            </div>

            <div class="input-group">
                <div class="input-group-addon">出版社</div>
                <input type="text" class="form-control" id="iptPublisher" placeholder="请输入出版社">
            </div>

            <button id="btnAdd" class="btn btn-primary">添加</button>

        </div>
    </div>


    <!-- 图书的表格 -->
    <table class="table table-bordered table-hover">
        <thead>
            <tr>
                <th>Id</th>
                <th>书名</th>
                <th>作者</th>
                <th>出版社</th>
                <th>操作</th>
            </tr>
        </thead>
        <tbody id="tb"></tbody>
    </table>

    <script>
        $(function () {
            // 获取图书列表数据
            function getBookList() {
                $.get('http://xxx.yyy.zzz/api/getbooks', function (res) {
                    if (res.status !== 200) return alert('获取数据失败！')

                    var rows = []
                    $.each(res.data, function (i, item) {
                        rows.push('<tr><td>' + item.id + '</td><td>' + item.bookname + '</td><td>' + item.author + '</td><td>' + item.publisher + '</td><td><a href="javascript:;" class="del" data-id="' + item.id + '">删除</a></td></tr>')
                    })
                    $('#tb').empty().append(rows.join(''))
                })
            }

            getBookList()

            // 通过代理的方式为动态添加的元素绑定点击事件
            $('tbody').on('click', '.del', function () {
                var id = $(this).attr('data-id')
                $.get('http://xxx.yyy.zzz/api/delbook', {
                    id: id
                }, function (res) {
                    if (res.status !== 200) return alert('删除图书失败！')
                    getBookList()
                })
            })

            $('#btnAdd').on('click', function () {
                var bookname = $('#iptBookname').val().trim()
                var author = $('#iptAuthor').val().trim()
                var publisher = $('#iptPublisher').val().trim()
                if (bookname.length <= 0 || author.length <= 0 || publisher.length <= 0) {
                    return alert('请填写完整的图书信息！')
                }

                $.post('http://xxx.yyy.zzz/api/addbook', {
                    bookname: bookname,
                    author: author,
                    publisher: publisher
                }, function (res) {
                    if (res.status !== 201) return alert('添加图书失败！')
                    getBookList()
                    $('#iptBookname').val('')
                    $('#iptAuthor').val('')
                    $('#iptPublisher').val('')
                })
            })
        })
    </script>

</body>

</html>
```

# 5. 案例 – 聊天机器人
## 5.1 演示案例要完成的效果
- 实现步骤：
  - 梳理案例的代码结构
  - 将用户输入的内容渲染到聊天窗口
  - 发起请求获取聊天消息
  - 将机器人的聊天内容转为语音
  - 通过 `<audio>` 播放语音
  - 使用回车键发送消息

  ![20241103224304](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103224304.png)

## 5.2 梳理案例的代码结构
- 梳理页面的 UI 布局
- 将业务代码抽离到 chat.js 中
- 了解 `resetui()` 函数的作用

## 5.3 将用户输入的内容渲染到聊天窗口
```javascript
// 为发送按钮绑定点击事件处理函数
$('#btnSend').on('click', function () {
  var text = $('#ipt').val().trim() // 获取用户输入的内容
  if (text.length <= 0) { // 判断用户输入的内容是否为空
    return $('#ipt').val('')
  }
  // 将用户输入的内容显示到聊天窗口中
  $('#talk_list').append('<li class="right_word"><img src="img/person02.png" /> <span>' + text + '</span></li>')
  resetui() // 重置滚动条的位置
  $('#ipt').val('') // 清空输入框的内容

  // TODO: 发起请求，获取聊天消息
})
```

## 5.4 发起请求获取聊天消息
```javascript
function getMsg(text) {
  $.ajax({
    method: 'GET',
    url: 'http://xxx.yyy.zzz/api/robot',
    data: {
      spoken: text
    },
    success: function (res) {
      if (res.message === 'success') {
        var msg = res.data.info.text
        $('#talk_list').append('<li class="left_word"><img src="img/person01.png" /> <span>' + msg + '</span></li>')
        resetui()
        // TODO: 发起请求，将机器人的聊天消息转为语音格式
      }
    }
  })
}
```

## 5.5 将机器人的聊天内容转为语音
```javascript
function getVoice(text) {
  $.ajax({
    method: 'GET',
    url: 'http://xxx.yyy.zzz/api/synthesize',
    data: {
      text: text
    },
    success: function (res) {
      // 如果请求成功，则 res.voiceUrl 是服务器返回的音频 URL 地址
      if (res.status === 200) {
        $('#voice').attr('src', res.voiceUrl)
      }
    }
  })
}
```

## 5.6 通过 `<audio>` 播放语音
```html
<!-- 音频播放语音内容 -->
<audio src="" id="voice" autoplay style="display: none;"></audio>
```

## 5.7 使用回车发送消息
```javascript
// 让文本输入框响应回车事件后，提交消息
$('#ipt').on('keyup', function (e) {
  // e.keyCode 可以获取到当前按键的编码
  if (e.keyCode === 13) {
    // 调用按钮元素的 click 函数，可以通过编程的形式触发按钮的点击事件
    $('#btnSend').click()
  }
})
```

## 5.8 完整代码
- **scroll.js**
  ```javascript
  $(function () {
    var $main = $('.main');
    var $list = $('.talk_list');
    var $drager = $('.drager');
    var $mainh = $main.outerHeight(false);
    var $listh = $list.outerHeight(false);

    var $rate = $mainh / $listh;
    var $dragh = $mainh * $rate;
    var $top = 0;
    $drager.css({ 'height': $dragh });

    $drager.draggable({
      containment: "parent",
      drag: function (ev, ui) {
        $top = ui.position.top;
        $list.css({ 'top': -$top / $rate });
      }
    });

    $(window).resize(function () {
      resetui();
    });

    //var timer = null;
    var flag = false;

    $main.mousewheel(function (ev, delta) {

      // 向上滚动正值，向下滚动负值
      if (flag) {
        return;
      }

      flag = true;

      setTimeout(function () {
        flag = false;
      }, 300);

      if ($listh <= $mainh) {
        return;
      } else {
        if (delta > 0) {
          $top = $top - 60;
          if ($top < 0) {
            $top = 0;
          }
          $drager.animate({ 'top': $top }, 200);
          $list.animate({ 'top': -$top / $rate }, 200);
        } else {
          $top = $top + 60;
          if ($top > ($mainh - $dragh)) {
            $top = parseInt($mainh - $dragh);
          }
          $drager.animate({ 'top': $top }, 200);
          $list.animate({ 'top': -parseInt($top / $rate) }, 200);
        }
      }

      //},300);        
    });
    if ($listh <= $mainh) {
      $('.drag_bar').hide();
      $('.drager').hide();
    }

    function resetui() {
      $mainh = $main.outerHeight(false);
      $listh = $list.outerHeight(false);
      $rate = $mainh / $listh;
      $dragh = $mainh * $rate;
      $drager.css({ 'height': $dragh });

      if ($listh <= $mainh) {
        $('.drag_bar').hide();
        $drager.hide();
        $list.css({ 'top': 0 });
      } else {
        $('.drag_bar').show();
        $drager.show();
        $drager.css({ 'top': $mainh - $dragh });
        $list.css({ 'top': -($listh - $mainh) });
      }
    }

    window.resetui = resetui;
  })
  ```

- **chat.js**
  ```javascript
  $(function () {
    // 初始化右侧滚动条
    // 这个方法定义在scroll.js中
    resetui()

    // 为发送按钮绑定鼠标点击事件
    $('#btnSend').on('click', function () {
      var text = $('#ipt')
        .val()
        .trim()
      if (text.length <= 0) {
        return $('#ipt').val('')
      }
      // 如果用户输入了聊天内容，则将聊天内容追加到页面上显示
      $('#talk_list').append('<li class="right_word"><img src="img/person02.png" /> <span>' + text + '</span></li>')
      $('#ipt').val('')
      // 重置滚动条的位置
      resetui()
      // 发起请求，获取聊天内容
      getMsg(text)
    })

    // 获取聊天机器人发送回来的消息
    function getMsg(text) {
      $.ajax({
        method: 'GET',
        url: ' http://xxx.yyy.zzz/api/robot',
        data: {
          spoken: text
        },
        success: function (res) {
          // console.log(res)
          if (res.message === 'success') {
            // 接收聊天消息
            var msg = res.data.info.text
            $('#talk_list').append('<li class="left_word"><img src="img/person01.png" /> <span>' + msg + '</span></li>')
            // 重置滚动条的位置
            resetui()
            // 调用 getVoice 函数，把文本转化为语音
            getVoice(msg)
          }
        }
      })
    }

    // 把文字转化为语音进行播放
    function getVoice(text) {
      $.ajax({
        method: 'GET',
        url: ' http://xxx.yyy.zzz/api/synthesize',
        data: {
          text: text
        },
        success: function (res) {
          // console.log(res)
          if (res.status === 200) {
            // 播放语音
            $('#voice').attr('src', res.voiceUrl)
          }
        }
      })
    }

    // 为文本框绑定 keyup 事件
    $('#ipt').on('keyup', function (e) {
      // console.log(e.keyCode)
      if (e.keyCode === 13) {
        // console.log('用户弹起了回车键')
        $('#btnSend').click()
      }
    })
  })
  ```

- **index.html**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <meta http-equiv="X-UA-Compatible" content="ie=edge" />
      <link rel="stylesheet" href="css/reset.css" />
      <link rel="stylesheet" href="css/main.css" />
      <script type="text/javascript" src="js/jquery-1.12.4.min.js"></script>
      <script type="text/javascript" src="js/jquery-ui.min.js"></script>
      <script type="text/javascript" src="js/jquery.mousewheel.js"></script>
      <title>聊天机器人</title>
  </head>

  <body>
      <div class="wrap">
          <!-- 头部 Header 区域 -->
          <div class="header">
              <h3>小思同学</h3>
              <img src="img/person01.png" alt="icon" />
          </div>
          <!-- 中间 聊天内容区域 -->
          <div class="main">
              <ul class="talk_list" style="top: 0px;" id="talk_list">
                  <li class="left_word">
                      <img src="img/person01.png" /> <span>嗨，最近想我没有？</span>
                  </li>
                  <!-- <li class="right_word">
              <img src="img/person02.png" /> <span>你好哦</span>
            </li> -->
              </ul>
              <div class="drag_bar" style="display: none;">
                  <div class="drager ui-draggable ui-draggable-handle" style="display: none; height: 412.628px;"></div>
              </div>
          </div>
          <!-- 底部 消息编辑区域 -->
          <div class="footer">
              <img src="img/person02.png" alt="icon" />
              <input type="text" placeholder="说的什么吧..." class="input_txt" id="ipt" />
              <input type="button" value="发 送" class="input_sub" id="btnSend" />
          </div>
      </div>
      <!-- 注意：只要为 audio 指定了新的 src 属性，而且指定了 autoplay，那么，语音就会自动播放 -->
      <audio src="" id="voice" autoplay style="display: none;"></audio>
      <script type="text/javascript" src="js/scroll.js"></script>
      <script src="./js/chat.js"></script>
  </body>

  </html>
  ```
