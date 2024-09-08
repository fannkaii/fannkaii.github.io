---
layout: post
title: "Webpack详解"
author: "Kai"
header-style: text
tags:
  - webpack
---

转自：https://juejin.cn/post/7252251628089966629

# 1. webpack基本概念
## 1.1 webpack是什么
- 静态模块打包工具
- 官网：[webpack.docschina.org](https://webpack.docschina.org/)
- 官网文档：[webpack.docschina.org/concepts](https://webpack.docschina.org/concepts/)

## 1.2 webpack为何学
- 开发完的网站，文件很多，体积很大。**为了让网页加载的更快**，需要缩小网站代码的体积。
  ![20240908185346](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908185346.png)

## 1.3 webpack的作用是什么
- webpack是一个**静态模块打包工具**，作用是**分析、压缩、打包**代码。
  - 支持所有类型文件的打包
  - 支持less/sass => css
  - 支持ES6/7/8 => ES5
  - 压缩代码, 提高加载速度

  ![20240908185427](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908185427.png)

# 2. webpack使用步骤
## 2.1 webpack安装
- webpack本质是node环境下的包，所以用npm直接安装即可。
- node安装之后自动安装了npm，node相关知识请参考[node初识](https://blog.csdn.net/liyou123456789/article/details/131240626)，npm相关知识请参考[npm与包](https://blog.csdn.net/liyou123456789/article/details/131241258)。
- webpack不同的版本是有差异的，不同的项目不建议使用相同版本，不建议安装全局版本，建议安装项目版本。
- webpack准备工作如下：
  1. 先创建项目文件夹`webpack-study`
  2. 打开`cmd`，切换当前路径到`webpack-study`
  3. 初始化环境命令`npm init -y`直接生成配置文件`package.json`
  4. 安装webpack包到当前项目`npm install webpack webpack-cli -D`
  5. 在配置文件`package.json`节点`scripts`添加`build`配置
  6. 运行打包命令`npm run build`就会执行`webpack`打包

  ![20240908185653](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908185653.png)

## 2.2 webpack使用
- **需求**：打包2个js文件 -> 打包成1个js文件
- **步骤**:
  1. 新建`src/add/add.js`定义求和函数导出
    ```javascript
    export const addFn = (a, b) => a + b
    ```
  2. 新建`src/index.js`导入使用
    ```javascript
    // webpack打包的入口
    import { addFn } from './add/add'
    console.log(addFn(5, 2));
    ```
  3. 运行打包命令`npm run build`，效果：
     1. 在src并列处, 生成`dist`目录和`main.js`文件
     2. 查看`main.js`文件, 是打包压缩后的代码
      ```javascript
      (()=>{"use strict";console.log(7)})();
      ```
     3. 打包关系图
      ![20240908190049](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908190049.png)
     4. 最终目录结构
      ![20240908190107](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908190107.png)

## 2.3 webpack更新打包
  - **需求**：代码变化后再更新打包
  - **步骤**:
    1. 新建`src/tool/tool.js`导出数组求和方法
        ```javascript
        export const getArrSum = arr => arr.reduce((sum, val) => sum += val, 0)
        ```
    2. `src/index.js`导入使用
        ```javascript
        import { addFn } from './add/add'
        import { getArrSum } from './tool/tool'

        console.log(addFn(5, 2));
        console.log(getArrSum([5, 6, 9, 10]));
        ```
    3. 运行打包命令`npm run build`，效果：
       1. 自动覆盖原dist中的main.js，生成的内容压缩极致
          ```javascript
          (()=>{"use strict";console.log(7),console.log([5,6,9,10].reduce(((o,e)=>o+e),0))})();
          ```
       2. 打包关系图
        ![20240908190544](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908190544.png)

# 3. webpack的配置
## 3.1 入口和出口
- **概要**：
  - webpack默认的入口: `src/index.js`，默认的出口: `dist/main.js`。
  - 修改入口和出口，需要添加`webpack.config.js`配置文件。
  - 配置文档：[webpack.docschina.org/concepts/#entry](https://webpack.docschina.org/concepts/#entry)

- **步骤**：
  1. 创建项目文件夹`webpack-config`
  2. `webpack-study`案例的文件复制一份进来
  3. 在项目根目录新建`webpack.config.js`文件 (默认配置文件名)
      ```javascript
      const path = require("path")

      module.exports = {
          entry: "./src/main.js", // 入口：可以是相对路径
          output: {
              path: path.join(__dirname, "dist"), // 出口：必须是绝对路径
              filename: "bundle.js" // 出口"文件"名
          }
      }
      ```
  4. 重命名文件`src/index.js` 为 `src/main.js`
  5. 运行打包命令`npm run build`

- **效果**：在dist目录生成bundle.js文件

## 3.2 打包流程
- 运行`yarn build` / `npm run build`发生了什么
  ![20240908191020](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908191020.png)
- 源码一定要和**入口产生直接/间接引入关系**, 才会被一起打包
  ![20240908191045](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908191045.png)

## 3.3 案例-隔行变色
- **概要**：引入jquery，实现隔行变色
- **步骤**：
  1. 创建项目文件夹`webpack-jquery`
  2. `webpack-config`案例的文件复制一份进来
  3. 安装`jquery`包`npm install jquery`
  4. 新建前端首页`public/index.html`
      ```html
      <!DOCTYPE html>
      <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
      </head>
      <body>

      <div id="app">
        <!-- ul>li{我是第$个li}*10 -->
        <ul>
          <li>我是第1个li</li>
          <li>我是第2个li</li>
          <li>我是第3个li</li>
          <li>我是第4个li</li>
          <li>我是第5个li</li>
          <li>我是第6个li</li>
          <li>我是第7个li</li>
          <li>我是第8个li</li>
          <li>我是第9个li</li>
        </ul>
      </div>

      </body>
      </html>
      ```

  5. 修改`src/main.js`文件
      ```javascript
      import $ from 'jquery'

      $(function() {
        $('#app li:nth-child(odd)').css('color', 'red')
        $('#app li:nth-child(even)').css('color', 'green')
      })
      ```

  6. 运行打包命令 **npm run build**
  7. 把`public/index.html`文件**手动**复制到`dist/index.html`，在`dist/index.html`中**手动**引入打包后的`bundle.js`
      ```html
      <script src="../dist/bundle.js"></script>
      ```

- **效果**：
  - 最终运行效果
    ![20240908191417](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908191417.png)

  - 打包关系图
    ![20240908191436](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908191436.png)


## 3.4 打包html文件
- **概要**：
  - webpack默认只打包js文件，其他文件是不会被打包的，需要安装插件。
  - `html-webpack-plugin`可以帮助我们将html文件打包。
  - 配置文档：**webpack.docschina.org/plugins/html-webpack-plugin**

- **步骤**：
  1. 创建项目文件夹`webpack-htmlplugin`
  2. `webpack-jquery`案例的文件复制一份进来
  3. 安装`html-webpack-plugin`
      ```bash
      npm install html-webpack-plugin -D
      ```
  4. 修改`webpack.config.js`配置文件
      ```javascript
      const path = require("path")

      // 引入自动生成 html 的插件
      const HtmlWebpackPlugin = require('html-webpack-plugin')

      module.exports = {
          entry: "./src/main.js", // 入口：可以是相对路径
          output: {
              path: path.join(__dirname, "dist"), // 出口：必须是绝对路径
              filename: "bundle.js" // 出口"文件"名
          },
          plugins: [
              // html 插件
              new HtmlWebpackPlugin({
                  // 指定要复制的HTML文件位置
                  // 可以使用相对路径
                  // 作用: 每次打包时自动从该目录下复制HTML到出口, 同时自动引入js文件, 并添加defer属性
                  // defer: 等页面资源加载完成后加载js文件
                  template: './public/index.html'
              })
          ]
      }
      ```

  5. 执行打包命令`npm run build`

- **效果**：和上面隔行变色手动复制一样

## 3.5 处理CSS文件
- **概要**：
  - `css-loader`：让webpack能够识别css文件的内容
  - `style-loader`：把css插入到dom中
  - `css-loader`配置文档：[webpack.docschina.org/loaders/css-loader](https://webpack.docschina.org/loaders/css-loader/)
  - `style-loader`配置文档：[webpack.docschina.org/loaders/style-loader](https://webpack.docschina.org/loaders/style-loader/)

- **步骤**：
  1. 创建项目文件夹`webpack-css`
  2. `webpack-htmlplugin`案例的文件复制一份进来
  3. 安装`css-loader`，`style-loader`
      ```bash
      npm install css-loader style-loader -D
      ```
  4. 修改`webpack.config.js`配置文件
      ```javascript
      const path = require("path")

      // 引入自动生成 html 的插件
      const HtmlWebpackPlugin = require('html-webpack-plugin')

      module.exports = {
          entry: "./src/main.js", // 入口：可以是相对路径
          output: {
              path: path.join(__dirname, "dist"), // 出口：必须是绝对路径
              filename: "bundle.js" // 出口"文件"名
          },
          plugins: [
              // html 插件
              new HtmlWebpackPlugin({
                  // 指定要复制的HTML文件位置
                  // 可以使用相对路径
                  // 作用: 每次打包时自动从该目录下复制HTML到出口, 同时自动引入js文件, 并添加defer属性
                  // defer: 等页面资源加载完成后加载js文件
                  template: './public/index.html'
              })
          ],
          module: { // 如何处理项目中不同模块文件
              rules: [ // 规则
                  {
                      // . 表示除换行符以外的任意字符
                      // \ 转义符
                      test: /\.css$/, // 匹配所有的css文件
                      // loader的加载顺序是从右往左
                      // 先用css-loader让webpack能够识别css文件并打包
                      // 再用style-loader将样式插入到dom中
                      use: ["style-loader", "css-loader"]
                  }
              ]
          }
      }
      ```

  5. 新建`src/css/index.css`，填上样式去除li圆点样式
      ```css
      li{
          list-style: none;
      }
      ```

  6. 在`main.js`引入`index.css`，一定要引入到入口才会被webpack打包
      ```javascript
      import "./css/index.css"
      ```

- **效果**：实现去除li圆点样式

## 3.6 处理Less文件
- **概要**：
  - `less-loader`：让webpack识别处理less文件
  - `less`：翻译less代码成css代码
  - `less-loader`配置文档：[webpack.docschina.org/loaders/less-loader](https://webpack.docschina.org/loaders/less-loader/)

- **步骤**：
  1. 创建项目文件夹`webpack-less`
  2. `webpack-css`案例的文件复制一份进来
  3. 安装`less-loader`，`less`
      ```bash
      npm install less less-loader -D
      ```
  4. 修改`webpack.config.js`配置文件
      ```javascript
      const path = require("path")

      // 引入自动生成 html 的插件
      const HtmlWebpackPlugin = require('html-webpack-plugin')

      module.exports = {
          entry: "./src/main.js", // 入口：可以是相对路径
          output: {
              path: path.join(__dirname, "dist"), // 出口：必须是绝对路径
              filename: "bundle.js" // 出口"文件"名
          },
          plugins: [
              // html 插件
              new HtmlWebpackPlugin({
                  // 指定要复制的HTML文件位置
                  // 可以使用相对路径
                  // 作用: 每次打包时自动从该目录下复制HTML到出口, 同时自动引入js文件, 并添加defer属性
                  // defer: 等页面资源加载完成后加载js文件
                  template: './public/index.html'
              })
          ],
          module: { // 如何处理项目中不同模块文件
              rules: [ // 规则
                  {
                      // . 表示除换行符以外的任意字符
                      // \ 转义符
                      test: /\.css$/, // 匹配所有的css文件
                      // loader的加载顺序是从右往左
                      // 先用css-loader让webpack能够识别css文件并打包
                      // 再用style-loader将样式插入到dom中
                      use: ["style-loader", "css-loader"]
                  },
                  {
                      test: /\.less$/, // 匹配.less结尾文件
                      // 使用less-loader让webpack能够识别less文件 
                      // 内置还会用less模块, 翻译less代码成css代码
                      // 然后再打包CSS文件，并将样式插入到dom中
                      use: ["style-loader", "css-loader", 'less-loader']
                  }
              ]
          }
      }
      ```

  5. 新建`src/less/index.less`，设置li字体大小24px
      ```css
      @size:24px;

      ul, li{
          font-size: @size
      }
      ```

  6. 引入到main.js中
      ```javascript
      import "./less/index.less"
      ```

  7. 执行打包命令`npm run build`

- **效果**：字体设置成需求大小

## 3.7 处理图片文件
- **概要**：
  - 配置webpack, 打包图片文件
  - 资源模块文档：[webpack.docschina.org/guides/asset-modules](https://webpack.docschina.org/guides/asset-modules/)

- **步骤**：
  1. 创建项目文件夹`webpack-pic`
  2. `webpack-less`案例的文件复制一份进来
  3. `src`文件夹下创建`assets`文件夹，放入两张图片
  4. 在`css/less/index.less`设置body背景图片
      ```less
      body{
          background: url(../assets/logo_small.png) no-repeat center;
      }
      ```

  5. 在`src/main.js`把图片添加body上显示
      ```javascript
      // 引入图片-使用
      import imgUrl from './assets/1.gif'
      const theImg = document.createElement("img")
      theImg.src = imgUrl
      document.body.appendChild(theImg)
      ```

  6. 修改`webpack.config.js`配置文件
      ```javascript
      const path = require("path")

      // 引入自动生成 html 的插件
      const HtmlWebpackPlugin = require('html-webpack-plugin')

      module.exports = {
          entry: "./src/main.js", // 入口：可以是相对路径
          output: {
              path: path.join(__dirname, "dist"), // 出口：必须是绝对路径
              filename: "bundle.js" // 出口"文件"名
          },
          plugins: [
              // html 插件
              new HtmlWebpackPlugin({
                  // 指定要复制的HTML文件位置
                  // 可以使用相对路径
                  // 作用: 每次打包时自动从该目录下复制HTML到出口, 同时自动引入js文件, 并添加defer属性
                  // defer: 等页面资源加载完成后加载js文件
                  template: './public/index.html'
              })
          ],
          module: { // 如何处理项目中不同模块文件
              rules: [ // 规则
                  {
                      // . 表示除换行符以外的任意字符
                      // \ 转义符
                      test: /\.css$/, // 匹配所有的css文件
                      // loader的加载顺序是从右往左
                      // 先用css-loader让webpack能够识别css文件并打包
                      // 再用style-loader将样式插入到dom中
                      use: ["style-loader", "css-loader"]
                  },
                  {
                      test: /\.less$/, // 匹配.less结尾文件
                      // 使用less-loader让webpack能够识别less文件 
                      // 内置还会用less模块, 翻译less代码成css代码
                      // 然后再打包CSS文件，并将样式插入到dom中
                      use: ["style-loader", "css-loader", 'less-loader']
                  },
                  {
                      test: /\.(png|jpg|gif|jpeg)$/i, // 匹配图片文件
                      // asset
                      // 大于 8KB 不转 base64 直接复制
                      // 小于 8KB 转成 base64 插入到 js 中
                      type: 'asset',
                      // generator 就是定义打包输出的规则
                      generator: {
                          // [] 的内容当做内置的变量
                          // [name] 表示原先的文件名
                          // [hash:6] 表示使用哈希字符串, 长度6
                          // [ext] 表示后缀名 带 .
                          filename: 'imgs/[name].[hash:4][ext]'
                      }
                  }
              ]
          }
      }
      ```

​ 7. 执行打包命令`npm run build`

- **效果**：
  - 图片正确显示
  - 类型设置type
    - **大于 8KB** 不转 base64 **直接复制**
    - **小于 8KB** 转成 base64 插入到 js 中
  - 转base64
    - 优点：**减少网络请求**
    - 缺点：**增加30%的体积**

## 3.8 处理字体文件
- **概要**：配置webpack，打包字体文件
- **步骤**：
  1. 创建项目文件夹`webpack-font`
  2. `webpack-pic`案例的文件复制一份进来
  3. `assets`文件夹下创建`fonts`文件夹，放入字体文件
  4. 在`main.js`引入`iconfont.css`
      ```javascript
      // 引入字体图标文件
      import './assets/fonts/iconfont.css'
      ```
  5. 在`public/index.html`使用字体图标样式
      ```html
      <i class="iconfont icon-weixin"></i>
      ```
  6. 修改`webpack.config.js`配置文件
      ```javascript
      const path = require("path")

      // 引入自动生成 html 的插件
      const HtmlWebpackPlugin = require('html-webpack-plugin')

      module.exports = {
          entry: "./src/main.js", // 入口：可以是相对路径
          output: {
              path: path.join(__dirname, "dist"), // 出口：必须是绝对路径
              filename: "bundle.js" // 出口"文件"名
          },
          plugins: [
              // html 插件
              new HtmlWebpackPlugin({
                  // 指定要复制的HTML文件位置
                  // 可以使用相对路径
                  // 作用: 每次打包时自动从该目录下复制HTML到出口, 同时自动引入js文件, 并添加defer属性
                  // defer: 等页面资源加载完成后加载js文件
                  template: './public/index.html'
              })
          ],
          module: { // 如何处理项目中不同模块文件
              rules: [ // 规则
                  {
                      // . 表示除换行符以外的任意字符
                      // \ 转义符
                      test: /\.css$/, // 匹配所有的css文件
                      // loader的加载顺序是从右往左
                      // 先用css-loader让webpack能够识别css文件并打包
                      // 再用style-loader将样式插入到dom中
                      use: ["style-loader", "css-loader"]
                  },
                  {
                      test: /\.less$/, // 匹配.less结尾文件
                      // 使用less-loader让webpack能够识别less文件 
                      // 内置还会用less模块, 翻译less代码成css代码
                      // 然后再打包CSS文件，并将样式插入到dom中
                      use: ["style-loader", "css-loader", 'less-loader']
                  },
                  {
                      test: /\.(png|jpg|gif|jpeg)$/i, // 匹配图片文件
                      // asset
                      // 大于 8KB 不转 base64 直接复制
                      // 小于 8KB 转成 base64 插入到 js 中
                      type: 'asset',
                      // generator 就是定义打包输出的规则
                      generator: {
                          // [] 的内容当做内置的变量
                          // [name] 表示原先的文件名
                          // [hash:6] 表示使用哈希字符串, 长度6
                          // [ext] 表示后缀名 带 .
                          filename: 'imgs/[name].[hash:4][ext]'
                      }
                  },
                  {
                      test: /\.(eot|svg|ttf|woff|woff2)$/i, // 匹配字体文件
                      // asset/resource:不做base64转换, 无论多大多小都直接复制到出口
                      type: 'asset/resource',
                      generator: {
                          // [] 的内容当做内置的变量
                          // [name] 表示原先的文件名
                          // [hash:6] 表示使用哈希字符串, 长度6
                          // [ext] 表示后缀名 带 .
                          filename: 'fonts/[name].[hash:4][ext]'
                      }
                  }
              ]
          }
      }
      ```

  7. 执行打包命令`npm run build`

- **效果**：
  - 正确显示字体
  - asset/resource: 不做base64转换, 无论多大多小都直接复制到出口

## 3.9 处理高版本js语法
- **概要**：
  - 让webpack用babel编译器处理高版本js语法降级成低版本js
  - babel官网：[www.babeljs.cn](https://www.babeljs.cn/)
  - babel-loader文档：[webpack.docschina.org/loaders/babel-loader](https://webpack.docschina.org/loaders/babel-loader/)

- **步骤**：
  1. 创建项目文件夹`webpack-es6`
  2. `webpack-font`案例的文件复制一份进来
  3. 在`src/main.js`编写箭头函数
      ```javascript
      const fn = () => { // 高级语法
        console.log("你好babel");
      }
      console.log(fn) // 一定打印函数, 才会被webpack把"函数体"打包起来
      ```
  4. 安装包`babel-loader`，`@babel/core`，`@babel/preset-env`
      ```bash
      npm install babel-loader @babel/core @babel/preset-env -D
      ```
  5. 修改`webpack.config.js`配置文件
      ```javascript
      const path = require("path")

      // 引入自动生成 html 的插件
      const HtmlWebpackPlugin = require('html-webpack-plugin')

      module.exports = {
          entry: "./src/main.js", // 入口：可以是相对路径
          output: {
              path: path.join(__dirname, "dist"), // 出口：必须是绝对路径
              filename: "bundle.js" // 出口"文件"名
          },
          plugins: [
              // html 插件
              new HtmlWebpackPlugin({
                  // 指定要复制的HTML文件位置
                  // 可以使用相对路径
                  // 作用: 每次打包时自动从该目录下复制HTML到出口, 同时自动引入js文件, 并添加defer属性
                  // defer: 等页面资源加载完成后加载js文件
                  template: './public/index.html'
              })
          ],
          module: { // 如何处理项目中不同模块文件
              rules: [ // 规则
                  {
                      // . 表示除换行符以外的任意字符
                      // \ 转义符
                      test: /\.css$/, // 匹配所有的css文件
                      // loader的加载顺序是从右往左
                      // 先用css-loader让webpack能够识别css文件并打包
                      // 再用style-loader将样式插入到dom中
                      use: ["style-loader", "css-loader"]
                  },
                  {
                      test: /\.less$/, // 匹配.less结尾文件
                      // 使用less-loader让webpack能够识别less文件 
                      // 内置还会用less模块, 翻译less代码成css代码
                      // 然后再打包CSS文件，并将样式插入到dom中
                      use: ["style-loader", "css-loader", 'less-loader']
                  },
                  {
                      test: /\.(png|jpg|gif|jpeg)$/i, // 匹配图片文件
                      // asset
                      // 大于 8KB 不转 base64 直接复制
                      // 小于 8KB 转成 base64 插入到 js 中
                      type: 'asset',
                      // generator 就是定义打包输出的规则
                      generator: {
                          // [] 的内容当做内置的变量
                          // [name] 表示原先的文件名
                          // [hash:6] 表示使用哈希字符串, 长度6
                          // [ext] 表示后缀名 带 .
                          filename: 'imgs/[name].[hash:4][ext]'
                      }
                  },
                  {
                      test: /\.(eot|svg|ttf|woff|woff2)$/i, // 匹配字体文件
                      // asset/resource:不做base64转换, 无论多大多小都直接复制到出口
                      type: 'asset/resource',
                      generator: {
                          // [] 的内容当做内置的变量
                          // [name] 表示原先的文件名
                          // [hash:6] 表示使用哈希字符串, 长度6
                          // [ext] 表示后缀名 带 .
                          filename: 'fonts/[name].[hash:4][ext]'
                      }
                  },
                  {
                      // 强烈不建议大家手写, 容易出错
                      test: /\.m?js$/, // 匹配js结尾文件
                      exclude: /(node_modules|bower_components)/, // 不转换这2个文件夹里的js
                      use: {
                          loader: 'babel-loader', // 使用加载器处理
                          options: {
                              presets: ['@babel/preset-env'] // 预设转码规则(用bable开发环境)
                          }
                      }
                  }
              ]
          }
      }
      ```

  6. 执行打包命令 `npm run build`

- **效果**：打包后观察`dist/bundle.js`文件, 自动变成普通函数

# 4. webpack开发服务器
## 4.1 问题
- **问题: 每次修改代码, 重新打包, 非常费时 (30s - 60s)**
- 原因:
  1. 再次找到入口并开始构建依赖关系图
  2. 磁盘读取对应文件到内存
  3. webpack用配置好的loader和plugin翻译和处理文件
  4. 再将处理后内容, 写入到磁盘出口位置
  5. 代码再变化，重复1~4步

## 4.2 解决方案
- **概要**：
- 下载webpack-dev-server，启动一个开发服务器, 用于快速开发应用程序
- webpack-dev-server文档：[webpack.docschina.org/configuration/dev-server](https://webpack.docschina.org/configuration/dev-server/)
  - 构建入口和所有模块依赖关系图
  - 磁盘读取对应的文件到内存, 才能加载
  - 用对应的 loader 进行处理和翻译
  - 将处理完的内容, 输出到**内存里而非磁盘上**
  - 以后代码变化, **自动更新打包变化的代码**, 显示到浏览器上

- **步骤**：
  1. 创建项目文件夹`webpack-server`
  2. `webpack-es6`案例的文件复制一份进来
  3. 安装包`webpack-dev-server`
      ```bash
      npm install webpack-dev-server -D
      ```
  4. 修改`package.json`配置文件`scripts`节点
      ```json
      scripts: {
        "build": "webpack",
        "serve": "webpack serve"
      }
      ```
  5. 修改`webpack.config.js`配置文件
      ```javascript
      const path = require("path")

      // 引入自动生成 html 的插件
      const HtmlWebpackPlugin = require('html-webpack-plugin')

      module.exports = {
          entry: "./src/main.js", // 入口：可以是相对路径
          output: {
              path: path.join(__dirname, "dist"), // 出口：必须是绝对路径
              filename: "bundle.js" // 出口"文件"名
          },
          plugins: [
              // html 插件
              new HtmlWebpackPlugin({
                  // 指定要复制的HTML文件位置
                  // 可以使用相对路径
                  // 作用: 每次打包时自动从该目录下复制HTML到出口, 同时自动引入js文件, 并添加defer属性
                  // defer: 等页面资源加载完成后加载js文件
                  template: './public/index.html'
              })
          ],
          module: { // 如何处理项目中不同模块文件
              rules: [ // 规则
                  {
                      // . 表示除换行符以外的任意字符
                      // \ 转义符
                      test: /\.css$/, // 匹配所有的css文件
                      // loader的加载顺序是从右往左
                      // 先用css-loader让webpack能够识别css文件并打包
                      // 再用style-loader将样式插入到dom中
                      use: ["style-loader", "css-loader"]
                  },
                  {
                      test: /\.less$/, // 匹配.less结尾文件
                      // 使用less-loader让webpack能够识别less文件 
                      // 内置还会用less模块, 翻译less代码成css代码
                      // 然后再打包CSS文件，并将样式插入到dom中
                      use: ["style-loader", "css-loader", 'less-loader']
                  },
                  {
                      test: /\.(png|jpg|gif|jpeg)$/i, // 匹配图片文件
                      // asset
                      // 大于 8KB 不转 base64 直接复制
                      // 小于 8KB 转成 base64 插入到 js 中
                      type: 'asset',
                      // generator 就是定义打包输出的规则
                      generator: {
                          // [] 的内容当做内置的变量
                          // [name] 表示原先的文件名
                          // [hash:6] 表示使用哈希字符串, 长度6
                          // [ext] 表示后缀名 带 .
                          filename: 'imgs/[name].[hash:4][ext]'
                      }
                  },
                  {
                      test: /\.(eot|svg|ttf|woff|woff2)$/i, // 匹配字体文件
                      // asset/resource:不做base64转换, 无论多大多小都直接复制到出口
                      type: 'asset/resource',
                      generator: {
                          // [] 的内容当做内置的变量
                          // [name] 表示原先的文件名
                          // [hash:6] 表示使用哈希字符串, 长度6
                          // [ext] 表示后缀名 带 .
                          filename: 'fonts/[name].[hash:4][ext]'
                      }
                  },
                  {
                      // 强烈不建议大家手写, 容易出错
                      test: /\.m?js$/, // 匹配js结尾文件
                      exclude: /(node_modules|bower_components)/, // 不转换这2个文件夹里的js
                      use: {
                          loader: 'babel-loader', // 使用加载器处理
                          options: {
                              presets: ['@babel/preset-env'] // 预设转码规则(用bable开发环境)
                          }
                      }
                  }
              ]
          },
          //webpackserver配置
          devServer: {
              port: 3000, // 端口号
              open: true // 自动打开浏览器
          }
      }
      ```

  6. 运行命令`npm run serve`启动webpack开发服务器

- **效果**：修改`src/public`文件夹下的代码，自动打包更新到浏览器效果

## 4.3 方案对比
- webpack:
  1. 再次找到入口并开始构建依赖关系图
  2. 磁盘读取对应文件到内存
  3. webpack用配置好的loader和plugin翻译和处理文件
  4. 再将处理后内容, 写入到**磁盘**出口位置
  5. 代码再变化，**重复1~4步**

- webpack-dev-server:
  1. 构建, 入口和依赖关系图
  2. 磁盘读取对应文件到内存
  3. webpack用配置好的loader和plugin翻译和处理文件
  4. 再将处理后内容, 放到**内存**里
  5. 代码再变化, **只重新打包变化的代码**, 自动更新到页面

# 5. 项目打包发布
## 5.1 环境
- 开发环境（development）：开发的时候随时修改调试
- 生产环境（production）：开发完成之后，打包到dist，发布到线上

## 5.2 版本发布
- 开发完成，运行命令`npm run build`，生成打包到dist文件夹
- dist文件夹的静态资源发布到生产环境目录

## 5.3 部署
- 使用node+express发布静态网站
    ```javascript
    const express = require('express')
    const app = express()

    app.use(express.static('./dist'))

    app.listen(4005)
    ```

# 6. 扩展功能
## 6.1 webpack4-图片打包
- **概要**：
  - 如用的是webpack4版本, 尝试这里配置
  - 当然webpack5是向下兼容的, 也可以使用
  - url-loader文档：[webpack.docschina.org/loaders/url-loader](https://webpack.docschina.org/loaders/url-loader/)
  - file-loader文档：[webpack.docschina.org/loaders/file-loader](https://webpack.docschina.org/loaders/file-loader/)

- **步骤**：
  1. 安装包url-loader，file-loader
      ```bash
      npm install url-loader file-loader -D
      ```
  2. webpack.config.js 配置
      ```javascript
      {
        test: /\.(png|jpg|gif|jpeg)$/i,
        use: [
          {
            loader: 'url-loader', // 匹配文件, 尝试转base64字符串打包到js中
            options: {  // 配置limit, 超过8k, 不转base64字符串, 自动用file-loader复制文件到dist下
              limit: 8 * 1024,
            }
          }
        ]
      }
      ```

## 6.2 webpack4-字体图标
- webpack.config.js配置
  ```javascript
  { 
      // 处理字体图标的解析
      test: /\.(eot|svg|ttf|woff|woff2)$/,
          use: [
              {
                  loader: 'url-loader',
                  options: {
                      limit: 2 * 1024,
                      // 配置输出的文件名
                      name: '[name].[ext]',
                      // 配置输出的文件目录
                      outputPath: "fonts/"
                  }
              }
          ]
  }
  ```

## 6.3 devtool：source map
- **概要**：
  - source map：用于在浏览器调试错误使用，记录代码打包前原始位置
  - 问题: 看浏览器控制台报错信息，**发现看不出哪行代码报错了**
    - 原因: 在生产环境下，webpack对代码压缩, 混淆, 减小文件的体积
      - 变量被替换成没有任何语义的名称
      - 空行和注释被剔除, 压缩到一行
  - 解决方案: 启用source map

- **步骤**：
  1. webpack.config.js - 配置
      ```javascript
      module.exports = {
        // ...其他配置
        // development开发模式,webpack内部不会使用内置优化, 不压缩代码
        // production生产模式,会压缩代码
        mode: 'development', 
        devtool: 'cheap-module-source-map', // cheap-module-source-map 开发模式下使用, 保证运行时的行数和源代码行数一致(默认不写是eval模式)
      }
      ```
  2. 重新启动开发服务器/打包, 观察是否有错误代码打包前的位置信息了

- **devtool值说明**：
  - 规则字符串列表：[webpack.docschina.org/configuration/devtool](https://webpack.docschina.org/configuration/devtool/)
  - 格式: `[inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map`  (了解即可)

  | 规则值 | 速度 | 位置 | 说明 |
  | --- | --- | --- | --- |
  | false | 建立：最快；重建：最快 | 无 | 不开启sourcemap(规则值写错也是这个) |
  | inline | 建立：最慢；重建：最慢 | 内嵌 | 报错信息, 以及源码和源码位置信息 |
  | hidden | 建立：最慢；重建：最慢 | 独立map文件 | 报错信息, 没有源码和源码位置信息 |
  | eval | 建立：快；重建：最快 | 内嵌 | 报错信息, 以及源码 (mode为development时使用这个值) |
  | cheap | 建立：固定；重建：慢 | 独立map文件 | 报错信息, 以及源码和源码的行数(没有列) |
  | module | 建立：慢；重建：快速 | 与别的一起用 | 是否为loaders加载器生成source map |
  | [xxx-...]source-map | 建立：最慢；重建：最慢 | 独立map文件 | 报错信息, 以及源码和源码位置信息 |
  | nosource | 建立：最慢；重建：最慢 | 独立map文件 | 报错信息, 不显示源码 |

- **devtool常用组合**
  ![20240908200006](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240908200006.png)

# 7. 常见面试题
## 7.1 什么是webpack？（必会）
  1. webpack是一个javascript的静态模块打包工具
  2. webpack里一切文件皆模块，通过loader转换文件，通过plugin注入钩子
  3. 最后输出由多个模块组合成的文件，webpack专注构建模块化项目

## 7.2 webpack的优点是什么？（必会）
  1. 专注于处理模块化的项目，能做到开箱即用，一步到位
  2. 通过plugin扩展，完整好用又不失灵活
  3. 通过loaders扩展, 可以让webpack把所有类型的文件都解析打包
  4. 区庞大活跃，经常引入紧跟时代发展的新特性，能为大多数场景找到已有的开源扩展

## 7.3 webpack的构建流程是什么?（必会）
> webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

1. 初始化参数：从配置文件读取与合并参数，得出最终的参数
2. 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，开始执行编译
3. 确定入口：根据配置中的 entry 找出所有的入口文件
4. 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
5. 完成模块编译：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系
6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
7. 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

在以上过程中，webpack 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 webpack 提供的 API 改变 webpack 的运行结果

## 7.4 webpack 的热更新原理？（必会）
- webpack 的热更新又称热替换（Hot Module Replacement），缩写为 HMR。这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。
- HMR的核心就是客户端从服务端拉去更新后的文件，准确的说是 chunk diff (chunk 需要更新的部分)，实际上 WDS(webpack-dev-server) 与浏览器之间维护了一个 Websocket，当本地资源发生变化时，WDS 会向浏览器推送更新，并带上构建时的 hash，让客户端与上一次资源进行对比。客户端对比出差异后会向 WDS 发请求来获取更改内容(文件列表、hash)，这样客户端就可以再借助这些信息继续向 WDS 发起 jsonp 请求获取该chunk的增量更新。
- 后续的部分(拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？)由 HotModulePlugin 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像react-hot-loader 和 vue-loader 都是借助这些 API 实现 HMR。

## 7.5 webpack与grunt、gulp的不同？（必会）
- **三者之间的区别**
  - 三者都是前端构建工具，grunt和gulp在早期比较流行，现在webpack相对来说比较主流，不过一些轻量化的任务还是会用gulp来处理，比如单独打包CSS文件等。
    - grunt和gulp是基于任务和流（Task、Stream）的。类似jQuery，找到一个（或一类）文件，对其做一系列链式操作，更新流上的数据， 整条链式操作构成了一个任务，多个任务就构成了整个web的构建流程。
    - webpack是基于入口的。webpack会自动地递归解析入口所需要加载的所有资源文件，然后用不同的Loader来处理不同的文件，用Plugin来扩展webpack功能。
- **从构建思路来说**：gulp和grunt需要开发者将整个前端构建过程拆分成多个**Task**，并合理控制所有**Task**的调用关系 webpack需要开发者找到入口，并需要清楚对于不同的资源应该使用什么Loader做何种解析和加工
- **对于知识背景来说**：gulp更像后端开发者的思路，需要对于整个流程了如指掌 webpack更倾向于前端开发者的思路

## 7.6 有哪些常见的Loader？他们是解决什么问题的？（必会）
1. file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件
2. url-loader：和 file-loader 类似，但是能在文件很小的情况下以 base64 的方式把文件内容注入到代码中去
3. source-map-loader：加载额外的 Source Map 文件，以方便断点调试
4. image-loader：加载并且压缩图片文件
5. babel-loader：把 ES6 转换成 ES5
6. css-loader：加载 CSS，支持模块化、压缩、文件导入等特性
7. style-loader：把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS。
8. eslint-loader：通过 ESLint 检查 JavaScript 代码

## 7.7 Loader和Plugin的不同？（必会）
- **不同的作用**
  - loader直译为"加载器"。webpack将一切文件视为模块，但是webpack原生是只能解析js文件，如果想将其他文件也打包的话，就会用到loader。 所以loader的作用是让webpack拥有了加载和解析非JavaScript文件的能力。
    - Plugin直译为"插件"。Plugin可以扩展webpack的功能，让webpack具有更多的灵活性。 在 webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 webpack 提供的 API 改变输出结果。

- **不同的用法**
  - Loader在module.rules中配置，也就是说他作为模块的解析规则而存在。 类型为数组，每一项都是一个Object，里面描述了对于什么类型的文件（test），使用什么加载(loader)和使用的参数（options）
    - Plugin在plugins中单独配置。 类型为数组，每一项是一个plugin的实例，参数都通过构造函数传入。
