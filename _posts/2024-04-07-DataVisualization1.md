---
layout: post
title: "数据可视化 一、ECharts"
author: "Kai"
header-style: text
tags:
  - Data Visualization
  - ECharts
---

转自：https://juejin.cn/post/7231370775151591485

# 1. 数据可视化
## 1.1 数据可视化
- 数据可视化主要目的：借助于图形化手段，清晰有效地传达与沟通信息。
- 数据可视化可以把数据从冰冷的数字转换成图形，揭示蕴含在数据中的规律和道理。
  ![20241022210832](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022210832.png)

## 1.2 数据可视化的场景
目前互联网公司通常有这么几大类的可视化需求：
![20241022210857](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022210857.png)

## 1.3 常见的数据可视化库
- D3.js: 目前 Web 端评价最高的 Javascript 可视化工具库(入手难)
- ECharts.js: 百度出品的一个开源 Javascript 数据可视化库
- Highcharts.js: 国外的前端数据可视化库，非商用免费，被许多国外大公司所使用
- AntV: 蚂蚁金服全新一代数据可视化解决方案
- 等等

# 2. ECharts简介
- ECharts是一个使用 JavaScript 实现的开源可视化库，可以流畅的运行在 PC 和移动设备上，兼容当前绝大部分浏览器（IE8/9/10/11，Chrome，Firefox，Safari等），底层依赖矢量图形库 ZRender，提供直观，交互丰富，可高度个性化定制的数据可视化图表。
  ![20241022211020](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022211020.png)
- 常用地址
  - 官网地址：[echarts.apache.org/zh/index.html](https://echarts.apache.org/zh/index.html)
  - 官网下载地址：[echarts.apache.org/zh/download.html](https://echarts.apache.org/zh/download.html)
  - 官网示例代码地址：[echarts.apache.org/examples/zh/index.html](https://echarts.apache.org/examples/zh/index.html)
  - 官网配置项地址：[echarts.apache.org/zh/option.html](https://echarts.apache.org/zh/option.html)
- 优点
  - 丰富的可视化类型
  - 多种数据格式支持
  - 流数据的支持
  - 移动端优化
  - 跨平台使用
  - 绚丽的特效
  - 详细的文档说明
- 常用社区（各种定制图表）如下
  - [www.isqqw.com](https://www.isqqw.com/)
  - [www.makeapie.cn/echarts](https://www.makeapie.cn/echarts)
  - [chartlib.yingxidata.cn/echarts](http://chartlib.yingxidata.cn/echarts)

# 3. ECharts基本使用
## 3.1 使用五步曲
- 步骤1：官网下载并引入echarts.js文件：图表依赖这个js库
- 步骤2：准备一个具备大小的DOM容器：生成的图表会放入这个容器内
- 步骤3：初始化echarts实例对象：实例化echarts对象
- 步骤4：指定配置项和数据(option)：根据具体需求修改配置选项
- 步骤5：将配置项设置给echarts实例对象：让echarts对象根据修改好的配置生效

- **案例如下：**
  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>ECharts体验</title>
      <style>
          .box {
              width: 400px;
              height: 400px;
              background-color: pink;
          }
      </style>
      <!-- 1. 下载引入EChart.js 文件 -->
      <script src="js/echarts.min.js"></script>
  </head>
  <body>
      <!-- 2. 准备一个盒子用来装生成的图表 注意： 这个盒子必须具备大小 -->
    <div class="box"></div>
    <script>
      //    3.初始化echarts 实例对象    
      var myChart =  echarts.init(document.querySelector('.box'));
      //  4.  指定配置项和数据
      var option = {
              title: {
                  text: 'ECharts 入门示例'
              },
              tooltip: {},
              legend: {
                  data:['销量']
              },
              xAxis: {
                  data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
              },
              yAxis: {},
              series: [{
                  name: '销量',
                  type: 'bar',
                  data: [5, 20, 36, 10, 10, 20]
              }]
          };
          // 5. 将配置项和数据(option) 设置给 实例对象(myChart)
          myChart.setOption(option);
    </script>
  </body>
  </html>
  ```
  ![20241022211746](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022211746.png)

## 3.2 如何下载
- 官网下载地址：[echarts.apache.org/zh/download.html](https://echarts.apache.org/zh/download.html)
  ![20241022211834](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022211834.png)
- 找到对应版本的代码库，对应的js文件即可，我们这里取压缩的版本
- git相关知识参考：[blog.csdn.net/liyou123456789/article/details/121411053](https://blog.csdn.net/liyou123456789/article/details/121411053)

  ![20241022211922](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022211922.png)

## 3.3 选择不同图表
- 要展示不同类型的图表，只要更换第四步代码即可，其他部分都是一样的
- 官网示例代码地址：[echarts.apache.org/examples/zh/index.html](https://echarts.apache.org/examples/zh/index.html)
- 找到你需要的，点进去，直接拷贝代码
  ![20241022212009](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022212009.png)

  ![20241022212015](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022212015.png)

- **案例如下：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>选择EChart图表</title>
      <style>
          .box {
              width: 450px;
              height: 450px;
              background-color: pink;
          }
      </style>
      <script src="js/echarts.min.js"></script>
  </head>

  <body>
      <div class="box"></div>
      <script>
          var myChart = echarts.init(document.querySelector('.box'));
          var option = {
              title: {
                  text: 'Referer of a Website',
                  subtext: 'Fake Data',
                  left: 'center'
              },
              tooltip: {
                  trigger: 'item'
              },
              legend: {
                  orient: 'vertical',
                  left: 'left'
              },
              series: [{
                  name: 'Access From',
                  type: 'pie',
                  radius: '50%',
                  data: [{
                      value: 1048,
                      name: 'Search Engine'
                  }, {
                      value: 735,
                      name: 'Direct'
                  }, {
                      value: 580,
                      name: 'Email'
                  }, {
                      value: 484,
                      name: 'Union Ads'
                  }, {
                      value: 300,
                      name: 'Video Ads'
                  }],
                  emphasis: {
                      itemStyle: {
                          shadowBlur: 10,
                          shadowOffsetX: 0,
                          shadowColor: 'rgba(0, 0, 0, 0.5)'
                      }
                  }
              }]
          };
          myChart.setOption(option);
      </script>
  </body>

  </html>
  ```
  ![20241022212055](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022212055.png)

## 3.4 相关配置项
- 官网配置项地址：[echarts.apache.org/zh/option.html](https://echarts.apache.org/zh/option.html#title)
- 先了解以下几个主要配置的作用，其余配置还有具体细节我们可以查阅文档：菜单—配置项手册，学echarts关键在于学会查阅文档，根据需求修改配置
  ![20241022212144](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022212144.png)
- title：标题组件
- tooltip：提示框组件
- legend：图例组件
- toolbox: 工具栏
- grid：直角坐标系内绘图网格
- xAxis：直角坐标系grid 中的 x 轴
- yAxis：直角坐标系 grid 中的 y 轴
- color：调色盘颜色列表
- series: 系列列表
  - type: 类型 (什么类型的图表)  比如line是折线; bar是柱形等
  - name: 系列名称，用于tooltip的显示，legend 的图例筛选 变化
  - stack: 如果设置相同值，则会数据堆叠。如果指定不同值或者去掉这个属性则不会数据堆叠，数据堆叠就是在页面上的数值是下面的数值叠加上去的，不是直接取数组中的值

  ![20241022212257](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022212257.png)

- **案例如下：**
  ```html
  <!DOCTYPE html>
  <html lang="en">

  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>定制ECharts图表相关配置</title>
      <style>
          .box {
              width: 450px;
              height: 450px;
              background-color: pink;
          }
      </style>
      <script src="js/echarts.min.js"></script>
  </head>

  <body>

      <div class="box"></div>
      <script>
          var myChart = echarts.init(document.querySelector('.box'));
          var option = {
              color: ['pink', 'blue', 'green', 'skyblue', 'red'],
              title: {
                  text: '我的折线图'
              },
              tooltip: {
                  trigger: 'axis'
              },
              legend: {
                  data: ['直播营销', '联盟广告', '视频广告', '直接访问']
              },
              grid: {
                  left: '3%',
                  right: '3%',
                  bottom: '3%',
                  // 当刻度标签溢出的时候，grid 区域是否包含坐标轴的刻度标签。如果为true，则显示刻度标签
                  // 如果left right等设置为 0% 刻度标签就溢出了，此时决定是否显示刻度标签
                  containLabel: true
              },
              toolbox: {
                  feature: {
                      saveAsImage: {}
                  }
              },
              xAxis: {
                  type: 'category',
                  // 坐标轴两边留白策略 true，这时候刻度只是作为分隔线，标签和数据点都会在两个刻度之间的带(band)中间。
                  boundaryGap: false,
                  data: ['星期一', '星期二', '周三', '周四', '周五', '周六', '周日']
              },
              yAxis: {
                  type: 'value'
              },
              series: [{
                  name: '直播营销',
                  // 图表类型是线形图
                  type: 'line',
                  data: [120, 132, 101, 134, 90, 230, 210]
              }, {
                  name: '联盟广告',
                  type: 'line',
                  data: [220, 182, 191, 234, 290, 330, 310]
              }, {
                  name: '视频广告',
                  type: 'line',
                  data: [150, 232, 201, 154, 190, 330, 410]
              }, {
                  name: '直接访问',
                  type: 'line',
                  data: [320, 332, 301, 334, 390, 330, 320]
              }]
          };

          myChart.setOption(option);
      </script>
  </body>

  </html>
  ```
  ![20241022212332](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241022212332.png)
