---
layout: post
title: "前后端交互四 跨域与JSONP"
author: "Kai"
header-style: text
tags:
  - Javascript
  - Ajax
---

转自：https://juejin.cn/post/7242258285159972920
{% raw %}

# 1. 同源策略和跨域
## 1.1 同源
- 如果两个页面URL的**协议，域名和端口**都相同，则两个页面具有相同的源。
- 例如，下表给出了相对于 [www.test.com/index.html](http://www.test.com/index.html) 页面的同源检测：

  | URL | 是否同源 | 原因 |
  | --- | --- | --- |
  | www.test.com/other.html | 是 | 同源（协议、域名、端口相同） |
  | www.test.com/about.html | 否 | 协议不同（http 与 https） |
  | blog.test.com/movie.html | 否 | 域名不同（www.test.com 与 blog.test.com） |
  | www.test.com:7001/home.html | 否 | 端口不同（默认的 80 端口与 7001 端口） |
  | www.test.com:80/main.html | 是 | 同源（协议、域名、端口相同） |

## 1.2 同源策略
- **同源策略**（英文全称 Same origin policy）是**浏览器**提供的一个安全功能。
- 同源策略限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的重要安全机制。
- 通俗的理解：浏览器规定，A 网站的 JavaScript，不允许和**非同源**的网站 C 之间，进行资源的交互，例如：
  - 无法读取非同源网页的 Cookie、LocalStorage 和 IndexedDB
  - 无法接触非同源网页的 DOM
  - 无法向非同源地址发送 Ajax 请求

## 1.3 跨域
- **同源**指的是两个 URL 的协议、域名、端口一致，**反之**，则是**跨域**。
- 出现跨域的根本原因：**浏览器的同源策略**不允许非同源的 URL 之间进行资源的交互。
- 浏览器对跨域请求的拦截：浏览器**允许发起跨域请求**，但是，跨域请求回来的数据，**会被浏览器拦截**，无法被页面获取到！
  ![20241119185432](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119185432.png)

- 实现跨域数据请求：两种解决方案，分别是 **JSONP** 和 **CORS**。
  - JSONP：出现的早，**兼容低版本IE**。是前端程序员为了解决跨域问题，被迫想出来的一种临时解决方案。缺点是**只支持 GET 请求**，不支持 POST 请求。
  - CORS：出现的较晚，它是 W3C 标准，属于跨域 Ajax 请求的根本解决方案。**支持 GET 和 POST 请求**。缺点是**不兼容某些低版本的浏览器**。

# 2. JSONP
## 2.1 JSONP是什么
- JSONP (JSON with Padding) 是 JSON 的一种“使用模式”，可用于解决主流浏览器的跨域数据访问的问题。

## 2.2 JSONP的实现原理
- 由于**浏览器同源策略**的限制，网页中**无法通过 Ajax 请求非同源的接口数据**。但是 `<script>` 标签不受浏览器同源策略的影响，可以通过 src 属性，请求非同源的 js 脚本。
- 因此，JSONP 的实现原理，就是通过 `<script>` 标签的 src 属性，请求跨域的数据接口，并通过**函数调用**的形式，接收跨域接口响应回来的数据。

## 2.3 自己实现一个简单的JSONP
- 定义一个 success 回调函数：
  ```html
  <script>
      function success(data) {
          console.log('获取到了data数据：')
          console.log(data)
      }
  </script>
  ```

- 通过 `<script>` 标签，请求接口数据：
  ```html
  <script src="http://xxx.yyy.zzz/api/jsonp?callback=success&name=zs&age=20"></script>
  ```

## 2.4 JSONP的缺点
- 由于 JSONP 是通过 `<script>` 标签的 src 属性，来实现跨域数据获取的，所以，JSONP 只支持 GET 数据请求，不支持 POST 请求。
- 注意：**JSONP 和 Ajax 之间没有任何关系**，不能把 JSONP 请求数据的方式叫做 Ajax，因为 JSONP 没有用到 XMLHttpRequest 这个对象。

## 2.5 jQuery中的JSONP
- jQuery 提供的 `$.ajax()` 函数，除了可以发起真正的 Ajax 数据请求之外，还能够发起 JSONP 数据请求
- 默认情况下，使用 jQuery 发起 JSONP 请求，会自动携带一个 `callback=jQueryxxx` 的参数，jQueryxxx 是随机生成的一个回调函数名称。
  ```javascript
  $.ajax({
      url: 'http://xxx.yyy.zzz/api/jsonp?name=zs&age=20',
      // 如果要使用 $.ajax() 发起 JSONP 请求，必须指定 datatype 为 jsonp
      dataType: 'jsonp',
      success: function (res) {
          console.log(res)
      }
  })
  ```

## 2.6 自定义参数及回调函数名称
- 在使用 jQuery 发起 JSONP 请求时，如果想要自定义 JSONP 的参数以及回调函数名称，可以通过如下两个参数来指定：
  ```javascript
  $.ajax({
      url: 'http://xxx.yyy.zzz/api/jsonp?name=zs&age=20',
      dataType: 'jsonp',
      // 发送到服务端的参数名称，默认值为 callback
      jsonp: 'callback',
      // 自定义的回调函数名称，默认值为 jQueryxxx 格式
      jsonpCallback: 'abc',
      success: function (res) {
          console.log(res)
      }
  })
  ```

## 2.7 jQuery中JSONP的实现过程
- jQuery 中的 JSONP，也是通过 `<script>` 标签的 src 属性实现跨域数据访问的，只不过，jQuery 采用的是动态创建和移除 `<script>` 标签的方式，来发起 JSONP 数据请求。
- 在发起 JSONP 请求的时候，动态向 `<header>` 中 append 一个 `<script>` 标签；
- 在 JSONP 请求成功以后，动态从 `<header>` 中移除刚才 append 进去的 `<script>` 标签；

# 3. 案例-淘宝搜索
## 3.1 要实现的UI效果
![20241119190045](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119190045.png)

## 3.2 获取用户输入的搜索关键词
- 为了获取到用户每次按下键盘输入的内容，需要监听输入框的 keyup 事件，示例代码如下：
```javascript
// 监听文本框的 keyup 事件
$('#ipt').on('keyup', function () {
    // 获取用户输入的内容
    var keywords = $(this).val().trim()
    // 判断用户输入的内容是否为空
    if (keywords.length <= 0) {
        return
    }
    // TODO：获取搜索建议列表
})
```

## 3.3 封装getSuggestList函数
- 将获取搜索建议列表的代码，封装到 getSuggestList 函数中，示例代码如下：
  ```javascript
  function getSuggestList(kw) {
      $.ajax({
          // 指定请求的 URL 地址，其中，q 是用户输入的关键字
          url: 'https://suggest.taobao.com/sug?q=' + kw,
          // 指定要发起的是 JSONP 请求
          dataType: 'jsonp',
          // 成功的回调函数
          success: function (res) { console.log(res) }
      })
  }
  ```

## 3.4 渲染建议列表的UI结构
- 定义搜索建议列表
  ```html
  <div class="box">
      <!-- tab 栏区域 -->
      <div class="tabs"></div>
      <!-- 搜索区域 -->
      <div class="search-box"></div>
      <!-- 搜索建议列表 -->
      <div id="suggest-list"></div>
  </div>
  ```

- 定义模板结构
  ```html
  <!-- 模板结构 -->
  <script type="text/html" id="tpl-suggestList">
      {{each result}}
        <div class="suggest-item">{{$value[0]}}</div>
      {{/each}}
  </script>
  ```

- 定义渲染模板结构的函数
  ```javascript
  // 渲染建议列表
  function renderSuggestList(res) {
      // 如果没有需要渲染的数据，则直接 return
      if (res.result.length <= 0) {
          return $('#suggest-list').empty().hide()
      }
      // 渲染模板结构
      var htmlStr = template('tpl-suggestList', res)
      $('#suggest-list').html(htmlStr).show()
  }
  ```

- 搜索关键词为空时隐藏搜索建议列表
  ```javascript
  $('#ipt').on('keyup', function () {
      // 获取用户输入的内容
      var keywords = $(this).val().trim()
      // 判断用户输入的内容是否为空
      if (keywords.length <= 0) {
          // 如果关键词为空，则清空后隐藏搜索建议列表
          return $('#suggest-list').empty().hide()
      }
      getSuggestList(keywords)
  })
  ```

## 3.5 输入框的防抖
- **防抖策略**（debounce）：是当事件被触发后，**延迟 n 秒**后再执行**回调**，如果在这 **n 秒内事件又被触发**，则**重新计时**。
- 防抖的应用场景：用户在输入框中连续输入一串字符时，可以通过防抖策略，只在输入完后，才执行查询的请求，这样可以有效减少请求次数，节约请求资源；
- 实现输入框的防抖
  ```javascript
  var timer = null                    // 1. 防抖动的 timer

  function debounceSearch(keywords) { // 2. 定义防抖的函数
      timer = setTimeout(function () {
          // 发起 JSONP 请求
          getSuggestList(keywords)
      }, 500)
  }

  $('#ipt').on('keyup', function () {  // 3. 在触发 keyup 事件时，立即清空 timer
      clearTimeout(timer)
      // ...省略其他代码
      debounceSearch(keywords)
  })
  ```

## 3.6 缓存搜索的建议列表
- 定义全局缓存对象
  ```javascript
  // 缓存对象
  var cacheObj = {}
  ```

- 将搜索结果保存到缓存对象中
  ```javascript
  // 渲染建议列表
  function renderSuggestList(res) {
      // ...省略其他代码
      // 将搜索的结果，添加到缓存对象中
      var k = $('#ipt').val().trim()
      cacheObj[k] = res
  }
  ```

- 优先从缓存中获取搜索建议
  ```javascript
  // 监听文本框的 keyup 事件
  $('#ipt').on('keyup', function () {
      // ...省略其他代码

      // 优先从缓存中获取搜索建议
      if (cacheObj[keywords]) {
          return renderSuggestList(cacheObj[keywords])
      }
      // 获取搜索建议列表
      debounceSearch(keywords)
  })
  ```

{% endraw %}