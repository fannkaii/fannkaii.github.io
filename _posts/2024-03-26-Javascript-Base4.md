---
layout: post
title: "JavaScript基础四、集合类型"
author: "Kai"
header-style: text
tags:
  - Javascript
---

转自：https://juejin.cn/post/7207298477978042425

# 1. Map
## 1.1 Map是什么
- Map 是一组**键值对**的结构，具有极快的查找速度。

## 1.2 Map基本使用
- Map声明：`var m = new Map()`
- Map操作：
  - 增：`m.set(key,value)`，存在就被修改，不存在就添加
  - 删：`m.delete(key)`
  - 改：`m.set(key,value)`，存在就被修改，不存在就添加
  - 查：`m.get(key)`
  - 存在：`m.has(key)`
- 注意事项：由于一个key只能对应一个value，所以，多次对一个key放入value，后面的值会把前面的值覆盖

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Map基本使用</title>
    </head>

    <body>
        <script>
            //声明且赋值
            let m1 = new Map([
                ['Michael', 95],
                ['Bob', 75],
                ['Tracy', 85]
            ])

            //初始化一个空Map，再进行设置
            let m2 = new Map(); // 空Map
            console.log(m2.set('Adam', 67)) //返回Map
            console.log(m22.set('Bob', 59)) //返回Map
            console.log(m2.has('Adam')) // 是否存在key 'Adam': true
            console.log(m2.get('Adam')) // 返回value 67
            console.log(m2.delete('Adam')) //删除key'Adam':true
            console.log(m2.get('Adam')) // undefined

            //由于一个key只能对应一个value，所以，多次对一个key放入value，后面的值会把前面的值覆盖
            let m3 = new Map()
            m3.set('Adam', 67)
            m3.set('Adam', 88)
            console.log(m3.get('Adam')) // 88
        </script>
    </body>

    </html>
    ```

# 2. Set
- Set 是一组key的集合，在 Set 中，没有重复的key。
- 重复元素在 Set 中自动被过滤
- 添加key：`add(key)`
- 删除key：`delete(key)`

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Map基本使用</title>
    </head>

    <body>
        <script>
            //声明
            let s1 = new Set(); // 空Set
            let s2 = new Set([1, 2, 3]); // 含1, 2, 3

            //重复元素在 Set 中自动被过滤 3 和 '3' 是不同的
            let s3 = new Set([1, 2, 3, 3, '3'])
            console.log(s3) // Set {1, 2, 3, "3"}

            //添加删除key
            let s4 = new Set([1, 2, 3])
            console.log(s4.add(4)) // Set {1, 2, 3, 4}
            s4.delete(3)
            console.log(s4) // Set {1, 2, 4 }
        </script>
    </body>

    </html>
    ```

# 3. Array
## 3.1 数组是什么
- 数组：(Array)是一种可以按顺序保存数据的**引用数据类型**。
- 场景：如果有多个数据可以用数组保存起来，然后放到一个变量中，管理非常方便
    ![20241005205943](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005205943.png)

- ## 3.2 数组基本使用
- 声明语法
  - 数组是按顺序保存，所以每个数据都有自己的编号
  - 计算机中的编号从0开始，所以小明的编号为0，小刚编号为1，以此类推
  - 在数组中，数据的编号也叫**索引或下标**
  - 数组可以存储任意类型的数据

    ![20241005210023](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005210023.png)

    ![20241005210030](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005210030.png)

- 数组长度：
  - 获取数组长度`arr.length`
  - 直接修改数组长度：`arr.length = newlength`，这个会引起数组的变化，一般不建议这样操作，防止索引越界数据丢失等情况。
- 一些术语
  - **元素**：数组中保存的每个数据都叫数组元素
  - **下标**：数组中数据的编号
  - **长度**：数组中数据的个数，通过数组的length属性获得
- 操作数组：数组本质是数据集合， 操作数据无非就是 `增 删 改 查`
    ![20241005210152](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005210152.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>体验数组</title>
    </head>

    <body>
        <script>
            // let arr = [10, 20, 30]
            // 1. 声明数组 有序 
            let arr = ['刘德华', '张学友', '黎明', '郭富城', 'pink老师', 10]
                // 2. 使用数组  数组名[索引号] 从0
                // console.log(arr)
            console.log(arr[0]) // 刘德华
            console.log(arr[4])
                // 3. 数组长度 =  索引号 + 1 
            console.log(arr.length) // 6

            //修改数组长度
            arr.length = 3
            console.log(arr) //['刘德华', '张学友', '黎明']
            arr.length = 6
            console.log(arr) //['刘德华', '张学友', '黎明',undefined, undefined, undefined]
        </script>
    </body>

    </html>
    ```

## 3.3 修改：`数组[下标] = 新值`
**案例如下：**
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>数组操作-改</title>
</head>

<body>
    <script>        
        let arr = ['pink', 'red', 'green']
        // 修改
        arr[0] = 'hotpink'
        console.log(arr)
    </script>
</body>

</html>
```

## 3.4 新增：push、unshift
- `arr.push()` 方法将一个或多个元素添加到数组的末尾，并返回该数组的新长度 (重点)
- `arr.unshift(新增的内容)` 方法将一个或多个元素添加到数组的**开头**，并返回该数组的新长度

    ![20241005210330](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005210330.png)

    ![20241005210336](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005210336.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>数组操作-增</title>
    </head>

    <body>
    <script>
        let arr = ['pink', 'hotpink']
        // 新增 push 推末尾
        console.log(arr.push('deeppink'))  // 3
        console.log(arr.push('deeppinnk', 'linghtpink'))//5
        // 新增 unshift 开头追加
        console.log(arr.unshift('red'))//6
    </script>
    </body>

    </html>
    ```

## 3.5 删除：pop、shift、splice
- `arr.pop()` 方法从数组中删除最后一个元素，并返回该元素的值
- `arr.shift()` 方法从数组中删除第一个元素，并返回该元素的值
- `arr.splice(start, deleteCount)` 方法 删除指定元素
  - `start`：指定修改的开始位置（从0计数）
  - `deleteCount`：表示要移除的数组元素的个数，可选， 如果省略则默认从指定的起始位置删除到最后

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>数组操作-删</title>
    </head>

    <body>
    <script>
        let arr = ['red', 'green', 'blue']
        // 1. pop() 删除最后一个元素
        console.log(arr.pop()) // blue

        // 2. shift() 删除第一个元素
        arr.shift()
        console.log(arr)

        // 3. splice 删除指定元素  
        // splice(起始位置-索引号, 删除几个)
        arr.splice(1, 1) // 从索引号1的位置开始删， 只删除1个
        arr.splice(1) // 从索引号1的位置开始删,删除到最后
        console.log(arr)
    </script>
    </body>

    </html>
    ```

## 3.6 排序：sort
- `arr.sort()`：默认排序规则
- 可以传入排序规则实现自定义排序

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数组操作-排序</title>
    </head>

    <body>
        <script>
            let arr = [2, 4, 3, 5, 1]
            // arr.sort() 排序
            // sort 升序排列
            arr.sort(function(a, b) {
                return a - b
            })
            console.log(arr)

            // sort() 降序
            arr.sort(function(a, b) {
                return b - a
            })
            console.log(arr)
        </script>
    </body>

    </html>
    ```

## 3.7 查索引：indexOf
- `indexOf(内容)`：返回元素在数组中的索引

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数组操作-查索引</title>
    </head>

    <body>
        <script>
            let arr = [10, 20, '30', 'xyz']
            console.log(arr.indexOf(10)) // 元素10的索引为0
            console.log(arr.indexOf(20)) // 元素20的索引为1
            console.log(arr.indexOf(30)) // 元素30没有找到，返回-1
            console.log(arr.indexOf('30')) // 元素'30'的索引为2
        </script>
    </body>

    </html>
    ```

## 3.8 截取：slice
- `arr.slice(start, end)`：返回索引start到end的新数组，不包含end
- `arr.slice(start)`：返回索引start到数组结束的新数组

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数组操作-截取</title>
    </head>

    <body>
        <script>
            let arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G']
            console.log(arr.slice(0, 3)) //从索引0开始，到索引3结束，但不包括索引3: ['A', 'B', 'C']
            console.log(arr.slice(3)) // 从索引3开始到结束: ['D', 'E', 'F', 'G']
        </script>
    </body>

    </html>
    ```

## 3.9 拼接：concat
- `arr.concat(newArrary)`：arr拼接数组newArrary，返回一个拼接后的新数组，不会影响源数组

**案例如下：**
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>数组操作-拼接</title>
</head>

<body>
    <script>
        let arr = ['A', 'B', 'C']
        let added = arr.concat([1, 2, 3])
        console.log(added) // ['A', 'B', 'C', 1, 2, 3]
        //不会影响源数组
        console.log(arr) // ['A', 'B', 'C']
    </script>
</body>

</html>
```

## 3.10 翻转：reverse
- `arr.reverse()`，返回新数组

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数组操作-翻转</title>
    </head>

    <body>
        <script>
            let arr = ['one', 'two', 'three']
            arr.reverse()
            console.log(arr) // ['three', 'two', 'one']
        </script>
    </body>

    </html>
    ```

## 3.11 数组拼接字符串：join
- `arr.join('指定字符')` 方法是一个非常实用的方法，它把当前 Array 的每个元素都用指定的字符串连接起来，然后返回连接后的字符串

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数组操作-数组拼接字符串</title>
    </head>

    <body>
        <script>
            let arr = ['A', 'B', 'C', 1, 2, 3];
            console.log(arr.join('-')) // 'A-B-C-1-2-3'
        </script>
    </body>

    </html>
    ```

## 3.12 迭代数组：map
- **作用**：map 迭代数组
- **语法**：
    ![20241005211010](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005211010.png)
- **使用场景**：map可以处理数据，并且返回新的数组，不影响原数组
- **map 也称为映射**：映射是个术语，指两个元素的集之间元素相互“对应”的关系
    ![20241005211046](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241005211046.png)

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>数组map方法</title>
    </head>

    <body>
        <script>
            const arr = ['red', 'blue', 'green']
                // map方法也是遍历处理数据，返回一个新数组
            const newArr = arr.map(function(item, i) {
                console.log(item) // 数组元素  'red' 'blue' 'green'
                console.log(i) // 下标 0 1 2
                return item + '老师'
            })
            console.log(newArr) //['red老师', 'blue老师', 'green老师']

            const arr1 = [10, 20, 30]
            const newArr1 = arr1.map(function(item) {
                return item + 5
            })
            console.log(newArr1) //[15, 25, 35]
        </script>
    </body>

    </html>
    ```

# 4. Iterable
- 遍历 Array 可以采用下标循环，遍历Map 和 Set 就无法使用下标。
- 为了统一集合类型，ES6标准引入了新的 iterable 类型，Array，Map，Set 属于继承 iterable 类型的集合可以通过新的 `for ... of` 循环来遍历。
- 更好的方式是直接使用 iterable 内置的 forEach 方法，它接收一个函数，每次迭代就自动回调该函数。
  - `forEach()` 方法是ES5.1标准引入的，你需要测试浏览器是否支持。
  - Set 没有索引，因此回调函数的前两个参数都是元素本身
  - Map 的回调函数参数依次为 value 、 key 和 map 本身

    **案例如下：**
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>遍历集合</title>
    </head>

    <body>
        <script>
            //for of  遍历 集合
            let a = ['A', 'B', 'C']
            let s = new Set(['A', 'B', 'C'])
            let m = new Map([
                [1, 'x'],
                [2, 'y'],
                [3, 'z']
            ])
            for (let x of a) { // 遍历Array
                console.log(x)
            }
            for (let x of s) { // 遍历Set
                console.log(x)
            }
            for (let x of m) { // 遍历Map
                console.log(x[0] + ':' + x[1])
            }

            //forEach 遍历数组
            a.forEach(function(element, index, array) {
                // element: 指向当前元素的值
                // index: 指向当前索引
                // array: 指向Array对象本身
                console.log(element + ', index = ' + index)
            });

            //forEach 遍历Set
            //Set 没有索引，因此回调函数的前两个参数都是元素本身
            s.forEach(function(element, sameElement, set) {
                console.log(element)
            })

            //forEach 遍历Map
            //Map 的回调函数参数依次为 value 、 key 和 map 本身
            m.forEach(function(value, key, map) {
                console.log(value)
            });
        </script>
    </body>

    </html>
    ```
