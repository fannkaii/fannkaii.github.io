---
layout: post
title: "前后端交互 二、form表单与模板引擎"
author: "Kai"
header-style: text
tags:
  - Javascript
  - 模板引擎
---

转自：https://juejin.cn/post/7242258285159940152
{% raw %}
# 1. form表单的基本使用
- HTML相关知识请参考 [HTML入门](https://blog.csdn.net/liyou123456789/article/details/128244744)

## 1.1 表单是什么
- 表单在网页中主要负责数据采集功能。HTML中的`<form>`标签，就是用于采集用户输入的信息的，并通过`<form>`标签的提交操作，把采集到的信息提交到服务器端进行处理。
- 表单由三个基本部分组成：表单标签（form），表单域（input），表单按钮（button）。
  ![20241103225834](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241103225834.png)

## 1.2 `<form>`标签的属性
- `<form>`标签用来采集数据，`<form>`标签的属性则是用来规定如何把采集到的数据发送到服务器。

  | 属性 | 值 | 描述 |
  | --- | --- | --- |
  | action | URL地址 | 规定当提交表单时，向何处发送表单数据 |
  | method | get或post | 规定以何种方式把表单数据提交到 action URL |
  | enctype | `application/x-www-form-urlencoded`，`multipart/form-data`，`text/plain` | 规定在发送表单数据之前如何对其进行编码 |
  | target | `_blank`，`_self`，`_parent`，`_top`，`framename` | 规定在何处打开 action URL |
  | name | 表单名称 | 区分页面中的多个表单 |

- action
  - action 属性用来规定当提交表单时，向何处发送表单数据。
  - action 属性的值应该是后端提供的一个 URL 地址，这个 URL 地址专门负责接收表单提交过来的数据。
  - 当表单在未指定 action 属性值的情况下，action 的默认值为当前页面的 URL 地址。
  - 注意：当提交表单后，页面会立即跳转到 action 属性指定的 URL 地址
- target
  - target 属性用来规定在何处打开 action URL。
  - 它的可选值有5个，默认情况下，target 的值是 _self，表示在当前页面中打开 action URL。

    | 值 | 描述 |
    | --- | --- |
    | _blank | 在新窗口中打开。 |
    | _self | 默认。在相同的框架中打开。 |
    | _parent | 在父框架集中打开。（很少用） |
    | _top | 在整个窗口中打开。（很少用） |
    | framename | 在指定的框架中打开。（很少用） |

- method
  - method 属性用来规定以何种方式把表单数据提交到 action URL。
  - 它的可选值有两个，分别是 get 和 post。
  - **默认值**为 **get**，表示通过URL地址的形式，把表单数据提交到 action URL。
  - get 方式适合用来提交**少量的、简单的**数据。
  - post 方式适合用来提交**大量的、复杂的**、或包含**文件上传**的数据。
  - 在实际开发中，post 提交方式用的最多，很少用 get。例如登录、注册、添加数据等表单操作，都需要使用 post 方式来提交表单。

- enctype
  - enctype 属性用来规定在发送表单数据之前如何对数据进行编码。
  - **默认值**为 application/x-www-form-urlencoded，表示在发送前编码所有的字符。
  - 在涉及到**文件上传**的操作时，必须将 enctype 的值设置为 **multipart/form-data**，不涉及到文件上传操作，则直接默认即可！

    | 值 | 描述 |
    | --- | --- |
    | application/x-www-form-urlencoded | 在发送前编码所有字符（默认） |
    | multipart/form-data | 不对字符编码。在使用包含文件上传控件的表单时，必须使用该值。 |
    | text/plain | 空格转换为 “+” 加号，但不对特殊字符编码。（很少用） |

## 1.3 表单的同步提交及缺点
- 什么是表单的同步提交：通过点击 submit 按钮，触发表单提交的操作，从而使页面跳转到 action URL 的行为，叫做表单的同步提交。
- 表单同步提交的缺点：
  - **整个页面会跳转到** action URL 所指向的地址，用户体验很差。
  - 页面之前的**状态和数据会丢失**。
- 如何解决同步提交的缺点：**表单只负责采集数据，Ajax 负责将数据提交到服务器。**

# 2. 通过Ajax提交表单数据
## 2.1 监听表单提交事件
- 在 jQuery 中，可以使用如下两种方式，监听到表单的提交事件：
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

      <form action="/login" id="f1">
          <input type="text" name="username" />
          <input type="password" name="password" />
          <button type="submit">提交</button>
      </form>

      <script>
          $(function () {
              // 第一种方式
              $('#f1').submit(function () {
                  alert('监听到了表单的提交事件')
              })

              // 第二种方式
              $('#f1').on('submit', function () {
                  alert('监听到了表单的提交事件2')
              })
          })
      </script>

  </body>

  </html>
  ```

## 2.2 阻止表单默认提交行为
- 当监听到表单的提交事件以后，可以调用事件对象的 event.preventDefault() 函数，来阻止表单的提交和页面的跳转，示例代码如下：
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

      <form action="/login" id="f1">
          <input type="text" name="username" />
          <input type="password" name="password" />
          <button type="submit">提交</button>
      </form>

      <script>
          $(function () {
              // 第一种方式
              $('#f1').submit(function (e) {
                  alert('监听到了表单的提交事件')
                  e.preventDefault()
              })

              // 第二种方式
              $('#f1').on('submit', function (e) {
                  alert('监听到了表单的提交事件2')
                  e.preventDefault()
              })
          })
      </script>

  </body>

  </html>
  ```

## 2.3 快速获取表单中的数据
- 为了简化表单中数据的获取操作，jQuery 提供了 `serialize()` 函数
- 语法格式：`$(selector).serialize()`
- `serialize()` 函数的好处：可以一次性获取到表单中的所有的数据。
- 在使用 `serialize()` 函数快速获取表单数据时，必须为每个表单元素添加 name 属性！

  ```html
  <form id="form1">
      <input type="text" name="username" />
      <input type="password" name="password" />
      <button type="submit">提交</button>
  </form>
  ```

  ```javascript
  $('#form1').serialize()
  // 调用的结果：
  // username=用户名的值&password=密码的值
  ```

# 3. 案例 - 评论列表
## 3.1 渲染UI结构
![20241106203600](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241106203600.png)

## 3.2 获取评论列表
```javascript
function getCmtList() {
    $.get('http://xxx.yyy.zzz/api/cmtlist', function (res) {
        if (res.status !== 200) {
            return alert('获取评论列表失败！')
        }
        var rows = []
        $.each(res.data, function (i, item) { // 循环拼接字符串
            rows.push('<li class="list-group-item">' + item.content + '<span class="badge cmt-date">评论时间：' + item.time + '</span><span class="badge cmt-person">评论人：' + item.username + '</span></li>')
        })
        $('#cmt-list').empty().append(rows.join('')) // 渲染列表的UI结构
    })
}
```

## 3.3 发表评论
```javascript
$('#formAddCmt').submit(function (e) {
    e.preventDefault() // 阻止表单的默认提交行为
    // 快速得到表单中的数据
    var data = $(this).serialize()
    $.post('http://xxx.yyy.zzz/api/addcmt', data, function (res) {
        if (res.status !== 201) {
            return alert('发表评论失败！')
        }
        // 刷新评论列表
        getCmtList()
        // 快速清空表单内容
        $('#formAddCmt')[0].reset()
    })
})
```

## 3.4 完整代码
- **cmt.js**
  ```javascript
  function getCommentList() {
      $.ajax({
          method: 'GET',
          url: 'http://xxx.yyy.zzz/api/cmtlist',
          success: function (res) {
              if (res.status !== 200) return alert('获取评论列表失败！')
              var rows = []
              $.each(res.data, function (i, item) {
                  var str = '<li class="list-group-item"><span class="badge" style="background-color: #F0AD4E;">评论时间：' + item.time + '</span><span class="badge" style="background-color: #5BC0DE;">评论人：' + item.username + '</span>' + item.content + '</li>'
                  rows.push(str)
              })
              $('#cmt-list').empty().append(rows.join(''))
          }
      })
  }

  getCommentList()

  $(function () {
      $('#formAddCmt').submit(function (e) {
          e.preventDefault()
          var data = $(this).serialize()
          $.post('http://xxx.yyy.zzz/api/addcmt', data, function (res) {
              if (res.status !== 201) {
                  return alert('发表评论失败！')
              }
              getCommentList()
              $('#formAddCmt')[0].reset()
          })
      })
  })
  ```

- **index.html**
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
      <script src="./js/cmt.js"></script>
  </head>

  <body style="padding: 15px;">

      <!-- 评论面板 -->
      <div class="panel panel-primary">
          <div class="panel-heading">
              <h3 class="panel-title">发表评论</h3>
          </div>
          <form class="panel-body" id="formAddCmt">
              <div>评论人：</div>
              <input type="text" class="form-control" name="username" autocomplete="off" />
              <div>评论内容：</div>
              <textarea class="form-control" name="content"></textarea>

              <button type="submit" class="btn btn-primary">发表评论</button>
          </form>
      </div>


      <!-- 评论列表 -->
      <ul class="list-group" id="cmt-list">
          <li class="list-group-item">
              <span class="badge" style="background-color: #F0AD4E;">评论时间：</span>
              <span class="badge" style="background-color: #5BC0DE;">评论人：</span> Item 1
          </li>
      </ul>

  </body>

  </html>
  ```

# 4. 模板引擎的基本概念
## 4.1 渲染UI结构时遇到的问题
- 如下代码是通过**字符串拼接**的形式，来渲染UI结构。
- 如果UI结构比较复杂，则拼接字符串的时候需要格外注意引号之前的嵌套。且一旦需求发生变化，修改起来也非常麻烦。
  ```javascript
  var rows = []
  $.each(res.data, function (i, item) { // 循环拼接字符串
      rows.push('<li class="list-group-item">' + item.content + '<span class="badge cmt-date">评论时间：' + item.time + '</span><span class="badge cmt-person">评论人：' + item.username + '</span></li>')
  })
  $('#cmt-list').empty().append(rows.join('')) // 渲染列表的UI结构
  ```

## 4.2 什么是模板引擎
- 模板引擎：根据指定的**模板结构和数据**，自动**生成**一个完整的**HTML页面**。
  ![20241106203840](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241106203840.png)

## 4.3 模板引擎的好处
- 减少了字符串的拼接操作
- 使代码结构更清晰
- 使代码更易于阅读与维护

# 5. art-template模板引擎
## 5.1 art-template简介
- art-template 是一个简约、超快的模板引擎。
- 中文官网地址：[aui.github.io/art-template/zh-cn/index.html](https://aui.github.io/art-template/zh-cn/index.html)
  ![20241106204013](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241106204013.png)

## 5.2 art-template的安装
- 下载地址：[aui.github.io/art-template/zh-cn/docs/installation.html](https://aui.github.io/art-template/zh-cn/docs/installation.html)
- 找到下载链接后，鼠标右键，选择“链接另存为”，将 art-template 下载到本地，然后，通过 `<script>` 标签加载到网页上进行使用。
  ![20241106204105](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241106204105.png)

## 5.3 art-template模板引擎的使用步骤
- 导入 art-template
- 定义数据
- 定义模板
- 调用 template 函数
- 渲染HTML结构

- **使用传统方式渲染UI结构：**
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
      <div id="title"></div>
      <div>姓名：<span id="name"></span></div>
      <div>年龄：<span id="age"></span></div>
      <div>会员：<span id="isVIP"></span></div>
      <div>注册时间：<span id="regTime"></span></div>
      <div>爱好：
          <ul id="hobby">
              <li>爱好1</li>
              <li>爱好2</li>
          </ul>
      </div>

      <script>
          var data = {
              title: '<h3>用户信息</h3>',
              name: 'zs',
              age: 20,
              isVIP: true,
              regTime: new Date(),
              hobby: ['吃饭', '睡觉', '打豆豆']
          }

          $(function () {
              $('#name').html(data.name)
              $('#title').html(data.title)
              $('#age').html(data.age)
              $('#isVIP').html(data.isVIP)
              $('#regTime').html(data.regTime)

              var rows = []
              $.each(data.hobby, function (i, item) {
                  rows.push('<li>' + item + '</li>')
              })
              $('#hobby').html(rows.join(''))
          })
      </script>
  </body>

  </html>
  ```

- **演示模板引擎的使用：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>Document</title>
      <!-- 1. 导入模板引擎 -->
      <!-- 在 window 全局，多一个函数，叫做 template('模板的Id', 需要渲染的数据对象) -->
      <script src="./lib/template-web.js"></script>
      <script src="./lib/jquery.js"></script>
  </head>

  <body>

      <div id="container"></div>

      <!-- 3. 定义模板 -->
      <!-- 3.1 模板的 HTML 结构，必须定义到 script 中 -->
      <script type="text/html" id="tpl-user">
          <h1>{{name}} ------ {{age}}</h1>
          {{@ test}}

          <div>
              {{if flag === 0}} flag的值是0 {{else if flag === 1}} flag的值是1 {{/if}}
          </div>

          <ul>
              {{each hobby}}
              <li>索引是:{{$index}}，循环项是:{{$value}}</li>
              {{/each}}
          </ul>

          <h3>{{regTime | dateFormat}}</h3>
      </script>

      <script>
          // 定义处理时间的过滤器
          template.defaults.imports.dateFormat = function (date) {
              var y = date.getFullYear()
              var m = date.getMonth() + 1
              var d = date.getDate()
              return y + '-' + m + '-' + d
          }

          // 2. 定义需要渲染的数据
          var data = {
              name: 'zs',
              age: 20,
              test: '<h3>测试原文输出</h3>',
              flag: 1,
              hobby: ['吃饭', '睡觉', '写代码'],
              regTime: new Date()
          }

          // 4. 调用 template 函数
          var htmlStr = template('tpl-user', data)
          console.log(htmlStr)

          // 5. 渲染HTML结构
          $('#container').html(htmlStr)
      </script>
  </body>

  </html>
  ```

## 5.4 art-template标准语法
- 什么是标准语法：art-template 提供了 `{{ }}` 这种语法格式，在 `{{ }}` 内可以进行变量输出，或循环数组等操作，这种 `{{ }}` 语法在 **art-template** 中被称为标准语法。
- 标准语法输出：在 `{{ }}` 语法中，可以进行**变量的输出**、**对象属性的输出**、**三元表达式输出**、**逻辑或输出**、**加减乘除等表达式输出**。
  ```html
  {{value}}
  {{obj.key}}
  {{obj['key']}}
  {{a ? b : c}}
  {{a || b}}
  {{a + b}}
  ```

- 标准语法**原文输出**：如果要输出的 value 值中，包含了 **HTML 标签结构**，则需要使用原文输出语法，才能保证 HTML 标签被正常渲染。
  ```html
  {{@ value }}
  ```

- 标准语法**条件输出**：如果要实现条件输出，则可以在 `{{ }}` 中使用 `if … else if … /if` 的方式，进行按需输出。
  ```html
  {{if value}} 按需输出的内容 {{/if}}

  {{if v1}} 按需输出的内容 {{else if v2}} 按需输出的内容 {{/if}}
  ```

- 标准语法**循环输出**：如果要实现循环输出，则可以在 `{{ }}` 内，通过 **each** 语法循环数组，当前循环的索引使用 `$index` 进行访问，当前的循环项使用 `$value` 进行访问。
  ```html
  {{each arr}}
      {{$index}} {{$value}}
  {{/each}}
  ```

- 标准语法**过滤器**：过滤器的本质，就是一个处理函数，将输入数据处理完再输出
  - 过滤器语法类似管道操作符，它的上一个输出作为下一个输入：`{{value | filterName}}`
  - 定义过滤器：`template.defaults.imports.filterName = function(value){/*return处理的结果*/}`

  ```javascript
  //定义一个格式化时间的过滤器 dateFormat 如下：
  template.defaults.imports.dateFormat = function (date) {
      var y = date.getFullYear()
      var m = date.getMonth() + 1
      var d = date.getDate()

      return y + '-' + m + '-' + d // 注意，过滤器最后一定要 return 一个值
  }
  ```

  ```html
  <div>注册时间：{{regTime | dateFormat}}</div>
  ```

# 6. 模板引擎的实现原理
## 6.1 正则匹配
- `exec()` 函数用于**检索字符串**中的正则表达式的匹配。如果字符串中有匹配的值，则**返回该匹配值**，否则返回 `null`。
- 语法：`RegExpObject.exec(string)`

  ```javascript
  var str = 'hello'
  var pattern = /o/
  // 输出的结果["o", index: 4, input: "hello", groups: undefined]
  console.log(pattern.exec(str)) 
  ```

- 分组：正则表达式中 `( )` 包起来的内容表示一个分组，可以通过分组来提取自己想要的内容。
  ```javascript
  var str = '<div>我是{{name}}</div>'
  var pattern = /{{([a-zA-Z]+)}}/

  // ["{{name}}", "name", index: 7, input: "<div>我是{{name}}</div>", groups: undefined]
  console.log(pattern.exec(str))
  ```

## 6.2 字符串替换
- `replace()` 函数用于在字符串中用一些字符**替换**另一些字符。返回替换后的新字符串。
- 语法：`var result = '123456'.replace('123', 'abc') //得到的 result 的值为字符串 'abc456'`

## 6.3 正则匹配再替换
- 单次替换
  ```javascript
  var str = '<div>我是{{name}}</div>'
  var pattern = /{{([a-zA-Z]+)}}/

  var patternResult = pattern.exec(str)
  str = str.replace(patternResult[0], patternResult[1]) // replace 函数返回值为替换后的新字符串
  // 输出的内容是：<div>我是name</div>
  console.log(str)
  ```

- 多次替换
  ```javascript
  var str = '<div>{{name}}今年{{ age }}岁了</div>'
  var pattern = /{{\s*([a-zA-Z]+)\s*}}/

  var patternResult = pattern.exec(str)
  str = str.replace(patternResult[0], patternResult[1])
  console.log(str) // 输出 <div>name今年{{ age }}岁了</div>

  patternResult = pattern.exec(str)
  str = str.replace(patternResult[0], patternResult[1])
  console.log(str) // 输出 <div>name今年age岁了</div>

  patternResult = pattern.exec(str)
  console.log(patternResult) // 输出 null
  ```

- 循环替换
  ```javascript
  var str = '<div>{{name}}今年{{ age }}岁了</div>'
  var pattern = /{{\s*([a-zA-Z]+)\s*}}/

  var patternResult = null
  while (patternResult = pattern.exec(str)) {
      str = str.replace(patternResult[0], patternResult[1])
  }
  console.log(str) // 输出 <div>name今年age岁了</div>
  ```

- 真值替换
  ```javascript
  var data = { name: '张三', age: 20 }
  var str = '<div>{{name}}今年{{ age }}岁了</div>'
  var pattern = /{{\s*([a-zA-Z]+)\s*}}/

  var patternResult = null
  while ((patternResult = pattern.exec(str))) {
      str = str.replace(patternResult[0], data[patternResult[1]])
  }
  console.log(str)
  ```

## 6.4 实现简易的模板引擎
- 定义模板结构
  ```html
  <!-- 定义模板结构 -->
  <script type="text/html" id="tpl-user">
    <div>姓名：{{name}}</div>
    <div>年龄：{{ age }}</div>
    <div>性别：{{  gender}}</div>
    <div>住址：{{address  }}</div>
  </script>
  ```

- 预调用模板引擎
  ```html
  <script>
      // 定义数据
      var data = { name: 'zs', age: 28, gender: '男', address: '北京顺义马坡' }
      // 调用模板函数
      var htmlStr = template('tpl-user', data)
      // 渲染HTML结构
      document.getElementById('user-box').innerHTML = htmlStr
  </script>
  ```

- 封装 template 函数
  ```javascript
  function template(id, data) {
      var str = document.getElementById(id).innerHTML
      var pattern = /{{\s*([a-zA-Z]+)\s*}}/
      var pattResult = null
      while ((pattResult = pattern.exec(str))) {
          str = str.replace(pattResult[0], data[pattResult[1]])
      }
      return str
  }
  ```

- 导入并使用自定义的模板引擎
  ```html
  <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <meta http-equiv="X-UA-Compatible" content="ie=edge" />
      <title>自定义模板引擎</title>
      <!-- 导入自定义的模板引擎 -->
      <script src=". /js/template.js"></script>
  </head>
  ```

{% endraw %}