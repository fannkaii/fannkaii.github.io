---
layout: post
title: "JavaScript高级 二、构造函数&常用函数"
author: "Kai"
header-style: text
tags:
  - Javascript
---

转自：https://juejin.cn/post/7238859514750369849

# 1. 深入对象
## 1.1 创建对象三种方式
- 利用对象字面量创建对象
- 利用 new Object 创建对象
- 利用构造函数创建对象

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>创建对象</title>
    </head>

    <body>
        <script>
        //1.对象字面量创建对象
        const o1 = {
            name: "zhangsan",
        };
        console.log(o1);

        //2.new Object()创建对象，再属性赋值
        const o2 = new Object();
        o2.uname = "lisi";
        console.log(o2);

        //new Object()创建对象直接传入属性值
        const o3 = new Object({
            uname: "wangwu",
        });
        console.log(o3);
        </script>
    </body>
    </html>
    ```

## 1.2 构造函数
- **构造函数**：是一种特殊的函数，主要用来**快速初始化**类似的**对象**
- **构造函数语法**
  - 使用 **new** 关键字调用函数的行为被称为**实例化**
  - 实例化构造函数时没有参数时可以省略
  - 构造函数**内部无需写return**，返回值即为新创建的对象，构造函数内部的 **return** 返回的值无效
  - 命名以**大写**字母开头
- **实例化执行过程**
  - 创建新对象
  - 构造函数this指向新对象
  - 执行构造函数代码，修改this，添加新的属性
  - 返回新对象

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>自定义构造函数</title>
    </head>

    <body>
        <script>
            // 创建一个构造函数
            function Goods(name, price, count) {
                this.name = name
                this.price = price
                this.count = count
                this.sayhi = function() {}
            }
            //使用构造函数创建对象
            const mi = new Goods('小米', 1999, 20)
            console.log(mi)
            const hw = new Goods('华为', 3999, 59)
            console.log(hw)
        </script>
    </body>

    </html>
    ```

## 1.3 实例成员&静态成员
- **实例成员**：通过构造函数创建的对象称为实例对象，**实例对象中的属性和方法称为实例成员**。构造函数创建的实例对象彼此独立互不影响。
- **静态成员**：在 JavaScript 中函数底层本质上也是对象类型，因此允许直接为函数动态添加属性或方法，**构造函数的属性和方法被称为静态成员**，一般**公共特征**的属性或方法设置为静态成员，静态成员方法中的 **this 指向构造函数本身**。

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>实例成员静态成员</title>
    </head>

    <body>
        <script>
        // 创建一个构造函数
        function Goods(name, price, count) {
            this.name = name;
            this.price = price;
            this.count = count;
            this.sayhi = function () {};
        }
        //使用构造函数创建对象
        const mi = new Goods("小米", 1999, 20);
        console.log(mi);
        const hw = new Goods("华为", 3999, 59);
        console.log(hw);

        //实例成员，只改当前实例成员中的数据
        mi.name = "vivo";
        console.log(mi);
        console.log(hw);

        // 静态成员，修改的数据在所有实例间生效
        Goods.num = 10;
        console.log(Goods.num); //10
        console.log(mi); //构造函数中包含num
        console.log(hw); //构造函数中包含num
        </script>
    </body>
    </html>
    ```

    ![20241101180441](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241101180441.png)

# 2. 内置构造函数
## 2.1 包装类型
- 在 JavaScript 中最主要的数据类型有 6 种：
  - **基本数据类型**：字符串、数值、布尔、undefined、null
  - **引用类型**: 对象

- **包装类型**：字符串、数值、布尔、等基本类型也都有专门的构造函数，就叫包装类型，我们平时直接赋值，具有对像的使用特征，实际上是js**底层**帮我们调用了**构造函数**，实际上就是个对象。

- 内置构造函数
  - **引用类型**：Object，Array，RegExp，Date 等
  - **包装类型**：String，Number，Boolean 等

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>基本包装类型</title>
    </head>

    <body>
        <script>
        // 平时直接赋值，具有对像的使用特征
        // const str = 'pink'
        // console.log(str.length)

        // 实际js底层完成了构造函数的调用
        const str = new String("pink");
        </script>
    </body>
    </html>
    ```

## 2.2 Object
- **Object** 是内置的构造函数，用于创建普通对象。**推荐使用字面量方式声明对象**，而不是 **Object** 构造函数。
- `Object.keys` 获取对象中所有属性（键），返回的是一个数组
- `Object.values` 获取对象中所有属性值，返回的是一个数组
- `Object.assign` 常用于对象拷贝，经常使用的场景给对象添加属性

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Object静态方法</title>
    </head>

    <body>
        <script>
        // 1.构造函数创建对象
        // const o = new Object({
        //     uname: 'pink',
        //     age: 18
        // })

        // 1.字面量方式声明对象，推荐
        const o = {
            uname: "pink",
            age: 18,
        };
        // 2.获得所有的属性名
        console.log(Object.keys(o)); //返回数组['uname', 'age']
        // 3.获得所有的属性值
        console.log(Object.values(o)); //返回数组['pink', 18]
        // 4.对象的拷贝
        const oo = {};
        // 把o拷贝给oo
        Object.assign(oo, o);
        console.log(oo);
        // 4.对象拷贝，给对象添加属性
        // 把属性gender: '女'添加给o
        Object.assign(o, {
            gender: "女",
        });
        console.log(o);
        </script>
    </body>
    </html>
    ```

    ![20241101180732](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241101180732.png)


## 2.3 Array
- **Array** 是内置的构造函数，用于创建数组，**建议使用字面量创建**，不用 Array构造函数创建。
- **常见实例方法**
  - **reduce**：返回函数累计处理的结果，经常用于求和等
    - 语法：`arr.reduce(function(累计值,当前元素[,索引号][,源数组]), 起始值)`
    - 说明：起始值可以省略，写就作为第一次累计的起始值，不写就是把数组的第一个值作为起始值，后面每次遍历就会用后面的数组元素 累计到 累计值 里面
  - **forEach**：用于遍历数组，替代 **for** 循环 (重点)
  - **filter**：过滤数组单元值，生成新数组(重点)
  - **map**：迭代原数组，生成新数组(重点)
  - **join**：数组元素拼接为字符串，返回字符串(重点)
  - **find**：查找元素， 返回符合条件的第一个数组元素值，如果没有返回 undefined(重点)
  - **every**：检测数组所有元素是否都符合指定条件，如果**所有元素**都通过检测返回 true，否则返回 false(重点)
  - **some**：检测数组中的元素是否满足指定条件，**如果数组中有**元素满足条件返回 true，否则返回 false
  - **concat**：合并两个数组，返回生成新数组
  - **sort**：对原数组单元值排序
  - **splice**：删除或替换原数组单元
  - **reverse**：反转数组
  - **findIndex**：查找元素的索引值
- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Array常用方法</title>
    </head>

    <body>
        <script>
        //reduce 返回函数累计处理的结果，经常用于求和等
        //arr.reduce(function(累计值, 当前元素){}, 起始值)
        const arr = [1, 2, 3];
        //没有给起始值的时候，数组第一个值就是起始值
        arr.reduce(function (prev, item) {
            console.log(prev); //1 3
            return prev + item;
        });
        //给起始值之后，用起始值
        arr.reduce(function (prev, item) {
            console.log(prev); //0 1 3
            return prev + item;
        }, 0);

        //find 查找元素， 返回符合条件的第一个数组元素值，如果没有返回 undefined(重点)
        const arr2 = ["red", "blue", "green"];
        const re1 = arr2.find(function (item) {
            return item === "blue";
        });
        console.log(re1); //blue

        //every 每一个是否都符合条件，如果都符合返回 true ，否则返回false
        const arr3 = [10, 20, 30];
        const re2 = arr3.every((item) => item >= 20);
        console.log(re2); //false
        </script>
    </body>
    </html>
    ```

- 常用静态方法
  - `Array.from(arr)`：伪数组转换为真数组

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>伪数组转换为真数组</title>
    </head>

    <body>
        <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
        </ul>
        <script>
        // Array.from(lis) 把伪数组转换为真数组
        const lis = document.querySelectorAll("ul li");
        console.log(lis);
        // lis.pop() 报错，伪数组不能直接操作
        const liss = Array.from(lis);
        liss.pop();
        console.log(liss);
        </script>
    </body>
    </html>
    ```

## 2.4 String
- **String** 是内置的构造函数，用于创建字符串。String 也可以当做普通函数使用，这时它的作用是强制转换成字符串数据类型。
- 常用实例属性
  - **length**：用来获取字符串的度长(重点)
- 常用实例方法
  - `split('分隔符')`：用来将字符串拆分成数组(重点)
  - `substring(开始的索引号[,结束的索引号])`：用于字符串截取(重点)
  - `startsWith(检测字符串[, 检测位置索引号])`：检测是否以某字符开头(重点)
  - `includes(搜索的字符串[, 检测位置索引号])`：判断一个字符串是否包含在另一个字符串中，根据情况返回 true 或 false(重点)
  - **toUpperCase**：用于将字母转换成大写
  - **toLowerCase**：用于将就转换成小写
  - **indexOf** ：检测是否包含某字符
  - **endsWith**：检测是否以某字符结尾
  - **replace**：用于替换字符串，支持正则匹配
  - **match**：用于查找字符串，支持正则匹配

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>String常见方法</title>
    </head>

    <body>
        <script>
        // split 把字符串转换为数组，和join()相反
        const str = "pink,red";
        const arr = str.split(",");
        console.log(arr); //['pink','red']
        const str1 = "2022-4-8";
        const arr1 = str1.split("-");
        console.log(arr1); //['2022','4','8']

        // substring(开始的索引号[， 结束的索引号]) 字符串的截取
        //  如果省略结束的索引号，默认取到最后
        //  结束的索引号不包含想要截取的部分
        const str2 = "今天又要做核酸了";
        console.log(str2.substring(5, 7)); //核酸

        // startsWith 判断是不是以某个字符开头
        const str3 = "pink老师上课中";
        console.log(str3.startsWith("pink")); //true

        // includes 判断某个字符是不是包含在一个字符串里面
        const str4 = "我是pink老师";
        console.log(str4.includes("pink")); // true

        // 转换成字符串
        const num = 10;
        console.log(String(num));
        console.log(num.toString());
        </script>
    </body>
    </html>
    ```

## 2.5 Number
- **Number** 是内置的构造函数，用于创建数值，推荐使用字面量方式声明数值，而不是 **Number** 构造函数。
- 常用实例方法
  - `toFixed()`：设置保留小数位的长度

- **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Number常用方法</title>
    </head>

    <body>
        <script>
        // toFixed 方法可以让数字指定保留的小数位数
        const num = 10.923;
        console.log(num.toFixed()); //11
        console.log(num.toFixed(1)); //10.9
        const num1 = 10;
        console.log(num1.toFixed(2)); //10.00
        </script>
    </body>
    </html>
    ```

# 3. 综合案例
- 需求：根据后台提供的数据，渲染购物车页面
    ![20241104131626](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241104131626.png)

- 分析业务模块：
  - 渲染图片、标题、颜色、价格、赠品等数据
  - 单价和小计模块
  - 总价模块
- 解决方案：
  - 把整体的结构直接生成然后渲染到大盒子.list 里面
  - 哪个方法可以遍历的同时还有返回值？map 方法
  - 最后计算总价模块，那个方法可以求和？reduce 方法
- 实现细节：
  - 先利用map来遍历，有多少条数据，渲染多少相同商品，注意map返回值是数组，我们需要用 join 转换为字符串，把返回的字符串 赋值 给 list 大盒子的 `innerHTML`。
  - 更换数据，先更换不需要处理的数据，图片，商品名称，单价，数量，采取对象解构的方式，注意 单价要保留2位小数， `489.00 toFixed(2)`
  - 更换数据，处理 规格文字 模块，获取 每个对象里面的 spec , 上面对象解构添加 spec，获得所有属性值是：`Object.values()` 返回的是数组，拼接数组是 `join('')` 这样就可以转换为字符串了
  - 更换数据，处理 赠品 模块，获取 每个对象里面的 gift，上面对象解构添加 gift，注意要判断是否有gif属性，没有的话不需要渲染，利用变成的字符串然后写到 p.name里面
  - 更换数据，处理 小计 模块，`小计 = 单价 * 数量`，小计名可以为: `subTotal = price * count`，注意保留2位小数。
  - 计算 合计 模块，求和用到数组 reduce 方法 累计器，根据数据里面的数量和单价累加和即可，注意 reduce方法有2个参数，第一个是回调函数，第二个是 初始值，这里写 0。

- **代码如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>综合案例</title>
        <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        .list {
            width: 990px;
            margin: 100px auto 0;
        }

        .item {
            padding: 15px;
            transition: all 0.5s;
            display: flex;
            border-top: 1px solid #e4e4e4;
        }

        .item:nth-child(4n) {
            margin-left: 0;
        }

        .item:hover {
            cursor: pointer;
            background-color: #f5f5f5;
        }

        .item img {
            width: 80px;
            height: 80px;
            margin-right: 10px;
        }

        .item .name {
            font-size: 18px;
            margin-right: 10px;
            color: #333;
            flex: 2;
        }

        .item .name .tag {
            display: block;
            padding: 2px;
            font-size: 12px;
            color: #999;
        }

        .item .price,
        .item .sub-total {
            font-size: 18px;
            color: firebrick;
            flex: 1;
        }

        .item .price::before,
        .item .sub-total::before,
        .amount::before {
            content: "¥";
            font-size: 12px;
        }

        .item .spec {
            flex: 2;
            color: #888;
            font-size: 14px;
        }

        .item .count {
            flex: 1;
            color: #aaa;
        }

        .total {
            width: 990px;
            margin: 0 auto;
            display: flex;
            justify-content: flex-end;
            border-top: 1px solid #e4e4e4;
            padding: 20px;
        }

        .total .amount {
            font-size: 18px;
            color: firebrick;
            font-weight: bold;
            margin-right: 50px;
        }
        </style>
    </head>

    <body>
        <div class="list">
        <!-- <div class="item">
        <img src="https://yanxuan-item.nosdn.127.net/84a59ff9c58a77032564e61f716846d6.jpg" alt="">
        <p class="name">称心如意手摇咖啡磨豆机咖啡豆研磨机 <span class="tag">【赠品】10优惠券</span></p>
        <p class="spec">白色/10寸</p>
        <p class="price">289.90</p>
        <p class="count">x2</p>
        <p class="sub-total">579.80</p>
        </div> -->
        </div>
        <div class="total">
        <div>合计：<span class="amount">1000.00</span></div>
        </div>
        <script>
        const goodsList = [
            {
            id: "4001172",
            name: "称心如意手摇咖啡磨豆机咖啡豆研磨机",
            price: 289.9,
            picture:
                "https://yanxuan-item.nosdn.127.net/84a59ff9c58a77032564e61f716846d6.jpg",
            count: 2,
            spec: {
                color: "白色",
            },
            },
            {
            id: "4001009",
            name: "竹制干泡茶盘正方形沥水茶台品茶盘",
            price: 109.8,
            picture:
                "https://yanxuan-item.nosdn.127.net/2d942d6bc94f1e230763e1a5a3b379e1.png",
            count: 3,
            spec: {
                size: "40cm*40cm",
                color: "黑色",
            },
            },
            {
            id: "4001874",
            name: "古法温酒汝瓷酒具套装白酒杯莲花温酒器",
            price: 488,
            picture:
                "https://yanxuan-item.nosdn.127.net/44e51622800e4fceb6bee8e616da85fd.png",
            count: 1,
            spec: {
                color: "青色",
                sum: "一大四小",
            },
            },
            {
            id: "4001649",
            name: "大师监制龙泉青瓷茶叶罐",
            price: 139,
            picture:
                "https://yanxuan-item.nosdn.127.net/4356c9fc150753775fe56b465314f1eb.png",
            count: 1,
            spec: {
                size: "小号",
                color: "紫色",
            },
            gift: "50g茶叶,清洗球,宝马, 奔驰",
            },
        ];

        // 1. 根据数据渲染页面
        document.querySelector(".list").innerHTML = goodsList
            .map((item) => {
            // console.log(item)  // 每一条对象
            // 对象解构  item.price item.count
            const { picture, name, count, price, spec, gift } = item;
            // 规格文字模块处理
            const text = Object.values(spec).join("/");
            // 计算小计模块 单价 * 数量  保留两位小数
            // 注意精度问题，因为保留两位小数，所以乘以 100  最后除以100
            const subTotal = ((price * 100 * count) / 100).toFixed(2);
            // 处理赠品模块 '50g茶叶,清洗球'
            const str = gift
                ? gift
                    .split(",")
                    .map((item) => `<span class="tag">【赠品】${item}</span> `)
                    .join("")
                : "";
            return `
            <div class="item">
            <img src=${picture} alt="">
            <p class="name">${name} ${str} </p>
            <p class="spec">${text} </p>
            <p class="price">${price.toFixed(2)}</p>
            <p class="count">x${count}</p>
            <p class="sub-total">${subTotal}</p>
            </div>`;
            })
            .join("");

        // 3. 合计模块
        const total = goodsList.reduce(
            (prev, item) => prev + (item.price * 100 * item.count) / 100,
            0
        );
        // console.log(total)
        document.querySelector(".amount").innerHTML = total.toFixed(2);
        </script>
    </body>
    </html>
    ```

- **实现效果：**
    ![20241104131904](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241104131904.png)
