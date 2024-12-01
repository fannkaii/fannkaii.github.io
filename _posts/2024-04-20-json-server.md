---
layout: post
title: "json-server详解"
author: "Kai"
header-style: text
tags:
  - json-server
---

转自：https://juejin.cn/post/7261533443900014649
{% raw %}

# 1. 简介
- Json-server 是一个**零代码快速搭建本地 RESTful API 的工具**。它使用 **JSON 文件作为数据源**，并提供了一组简单的路由和端点，可以模拟后端服务器的行为。
- github地址：[github.com/typicode/json-server](https://github.com/typicode/json-server)
- npm地址：[www.npmjs.com/package/json-server](https://www.npmjs.com/package/json-server)

# 2. 安装
- json-server是基于npm安装的，安装了node就自动安装了npm，所以安装json-server需要先安装node，node相关知识请参考[node入门](https://blog.csdn.net/liyou123456789/article/details/131240626)，npm相关知识请参考[npm入门](https://blog.csdn.net/liyou123456789/article/details/131241258)。
- **安装json-server**：使用npm或yarn全局安装json-server。
    ```bash
    npm install -g json-server
    ```

- 验证安装是否成功：显示版本号就是安装成功了
    ```bash
    json-server -v
    ```

# 3. 快速使用
1. **创建JSON文件**：创建一个JSON文件作为数据源，例如 **db.json**，并在其中定义你想要模拟的数据，例如：
    ```json
    {
      "users": [
        {
          "id": 1,
          "name": "John"
        },
        {
          "id": 2,
          "name": "Jane"
        }
      ]
    }
    ```

2. **启动json-server**：使用以下命令启动json-server，并将JSON文件作为参数传递给服务器。这将在本地计算机的**3000端口**上启动服务器，并将data.json文件中的数据暴露为RESTful API。
    ```bash
    json-server --watch db.json
    ```

3. **使用API**：可以使用Web浏览器或任何HTTP客户端程序（如Postman）来访问json-server提供的的数据。例如，以下URL将检索JSON文件中的所有用户：
    ```
    http://localhost:3000/users
    ```

4. 参数说明
- 语法：`json-server [options] <source>`
    
    | 参数               | 简写  | 说明                   | 默认值           |
    | ------------------ | ----- | ---------------------- | ---------------- |
    | --config           | -c    | 指定配置文件路径       | json-server.json |
    | --port             | -p    | 指定端口               | 3000             |
    | --host             | -H    | 指定主机名             | localhost        |
    | --watch            | -w    | 监控文件变化           |                  |
    | --routes           | -r    | 指定路由文件路径       |                  |
    | --middlewares      | -m    | 指定中间件文件路径     |                  |
    | --static           | -s    | 指定静态文件文件夹路径 |                  |
    | --read-only        | --ro  | 指定只允许get请求      |                  |
    | --no-cors          | --nc  | 禁止跨域共享           |                  |
    | --no-gzip          | --ng  | 禁止gzip压缩           |                  |
    | --snapshots        | -S    | 指定快照目录           | .                |
    | --delay            | -d    | 指定延迟返回时长(ms)   |                  |
    | --id               | -i    | 指定数据库的ID属性     | id               |
    | --foreignKeySuffix | --fks | 指定外键前缀           | Id               |
    | --quiet            | -q    | 抑制来自输出的日志消息 |                  |
    | --help             | -h    | 显示帮助               |                  |
    | --version          | -v    | 显示版本号             |                  |

## 4.1 创建json数据
- 在空文件夹test下，执行命令
    ```bash
    json-server --watch db.json
    ```
    ![20241201103758](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201103758.png)

- 文件夹下就会生成一个默认内容的**db.json**文件
    ```json
    {
      "posts": [
        {
          "id": 1,
          "title": "json-server",
          "author": "typicode"
        }
      ],
      "comments": [
        {
          "id": 1,
          "body": "some comment",
          "postId": 1
        }
      ],
      "profile": {
        "name": "typicode"
      }
    }
    ```

- 访问地址`http://localhost:3000/`会展示如下页面，可以分别点击`/posts`、`/comment`、`/profile`链接，能看到各自的json数据。`http://localhost:3000/db`链接能访问到完整的数据。
    ![20241201104121](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201104121.png)

## 4.2 修改端口
- json-server 默认是 3000 端口，我们也可以自己指定端口，指令如下：
    ```bash
    json-server --watch db.json --port 8887
    ```

## 4.3 使用配置文件
- 通过命令行配置会让命令变的很长，而且容易敲错；
- json-server允许我们把所有的配置放到一个配置文件中，这个配置文件一般命名为 **json_sever_config.json**
    ```json
    {
      "port": 8887,
      "watch": true,
      "read-only": false,
      "no-cors": false,
      "no-gzip": false
    }
    ```

- 然后启动服务命令如下
    ```bash
    json-server -c json_sever_config.json db.json
    ```

## 4.4 静态服务器
- 可以配置静态服务器，**./static**是静态文件路径，里面放一张图片**test.jpg**
- 服务启动后，直接在浏览器输入**http://localhost:3000/test.jpg**就能访问到这张图片
    ```bash
    json-server -s ./static db.json
    ```

# 5. 增删改查
## 5.1 准备数据
- 我们准本一个测试数据`db.json`，这是15篇文章内容数据，注意数据格式要符合JSON格式，如果数据格式有误，命令窗口会报错，可以根据错误提示进行修整。
- 启动服务：`json-server --watch db.json`
    ```json
    {
      "articles": [
        {
          "userId": 1,
          "id": 1,
          "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
          "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
        },
        {
          "userId": 1,
          "id": 2,
          "title": "qui est esse",
          "body": "est rerum tempore vitae\nsequi sint nihil reprehenderit dolor beatae ea dolores neque\nfugiat blanditiis voluptate porro vel nihil molestiae ut reiciendis\nqui aperiam non debitis possimus qui neque nisi nulla"
        },
        {
          "userId": 1,
          "id": 3,
          "title": "ea molestias quasi exercitationem repellat qui ipsa sit aut",
          "body": "et iusto sed quo iure\nvoluptatem occaecati omnis eligendi aut ad\nvoluptatem doloribus vel accusantium quis pariatur\nmolestiae porro eius odio et labore et velit aut"
        },
        {
          "userId": 2,
          "id": 11,
          "title": "et ea vero quia laudantium autem",
          "body": "delectus reiciendis molestiae occaecati non minima eveniet qui voluptatibus\naccusamus in eum beatae sit\nvel qui neque voluptates ut commodi qui incidunt\nut animi commodi"
        },
        {
          "userId": 2,
          "id": 12,
          "title": "in quibusdam tempore odit est dolorem",
          "body": "itaque id aut magnam\npraesentium quia et ea odit et ea voluptas et\nsapiente quia nihil amet occaecati quia id voluptatem\nincidunt ea est distinctio odio"
        },
        {
          "userId": 2,
          "id": 13,
          "title": "dolorum ut in voluptas mollitia et saepe quo animi",
          "body": "aut dicta possimus sint mollitia voluptas commodi quo doloremque\niste corrupti reiciendis voluptatem eius rerum\nsit cumque quod eligendi laborum minima\nperferendis recusandae assumenda consectetur porro architecto     ipsum ipsam"
        },
        {
          "userId": 3,
          "id": 21,
          "title": "asperiores ea ipsam voluptatibus modi minima quia sint",
          "body": "repellat aliquid praesentium dolorem quo\nsed totam minus non itaque\nnihil labore molestiae sunt dolor eveniet hic recusandae veniam\ntempora et tenetur expedita sunt"
        },
        {
          "userId": 3,
          "id": 22,
          "title": "dolor sint quo a velit explicabo quia nam",
          "body": "eos qui et ipsum ipsam suscipit aut\nsed omnis non odio\nexpedita earum mollitia molestiae aut atque rem suscipit\nnam impedit esse"
        },
        {
          "userId": 3,
          "id": 23,
          "title": "maxime id vitae nihil numquam",
          "body": "veritatis unde neque eligendi\nquae quod architecto quo neque vitae\nest illo sit tempora doloremque fugit quod\net et vel beatae sequi ullam sed tenetur perspiciatis"
        },
        {
          "userId": 4,
          "id": 31,
          "title": "ullam ut quidem id aut vel consequuntur",
          "body": "debitis eius sed quibusdam non quis consectetur vitae\nimpedit ut qui consequatur sed aut in\nquidem sit nostrum et maiores adipisci atque\nquaerat voluptatem adipisci repudiandae"
        },
        {
          "userId": 4,
          "id": 32,
          "title": "doloremque illum aliquid sunt",
          "body": "deserunt eos nobis asperiores et hic\nest debitis repellat molestiae optio\nnihil ratione ut eos beatae quibusdam distinctio maiores\nearum voluptates et aut adipisci ea maiores voluptas maxime"
        },
        {
          "userId": 4,
          "id": 33,
          "title": "qui explicabo molestiae dolorem",
          "body": "rerum ut et numquam laborum odit est sit\nid qui sint in\nquasi tenetur tempore aperiam et quaerat qui in\nrerum officiis sequi cumque quod"
        },
        {
          "userId": 5,
          "id": 41,
          "title": "non est facere",
          "body": "molestias id nostrum\nexcepturi molestiae dolore omnis repellendus quaerat saepe\nconsectetur iste quaerat tenetur asperiores accusamus ex ut\nnam quidem est ducimus sunt debitis saepe"
        },
        {
          "userId": 5,
          "id": 42,
          "title": "commodi ullam sint et excepturi error explicabo praesentium voluptas",
          "body": "odio fugit voluptatum ducimus earum autem est incidunt voluptatem\nodit reiciendis aliquam sunt sequi nulla dolorem\nnon facere repellendus voluptates quia\nratione harum vitae ut"
        },
        {
          "userId": 5,
          "id": 43,
          "title": "eligendi iste nostrum consequuntur adipisci praesentium sit beatae perferendis",
          "body": "similique fugit est\nillum et dolorum harum et voluptate eaque quidem\nexercitationem quos nam commodi possimus cum odio nihil nulla\ndolorum exercitationem magnam ex et a et distinctio debitis"
        }
      ]
    }
    ```

## 5.2 获取全部数据
- 获取所有文章内容，返回**对象数组**
    ```
    http://localhost:3000/articles
    ```

## 5.3 过滤条件
- 根据id获取数据，返回**单个对象**
    ```
    http://localhost:3000/articles/1
    ```
    ```json
    {
      "userId": 1,
      "id": 1,
      "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
      "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
    }
    ```

- 还可以用url参数写法，返回**对象数组**
    ```
    http://localhost:3000/articles?id=1
    ```
    ```json
    [
      {
        "userId": 1,
        "id": 1,
        "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
        "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
      }
    ]
    ```

- 也可以指定多个条件，用 `&` 符号连接，返回**对象数组**
    ```
    http://localhost:3000/articles?userId=1&id=1
    ```
    ```json
    [
      {
        "userId": 1,
        "id": 1,
        "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
        "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
      }
    ]
    ```

- 用`_gte`、`_lte` 来设置一个字段的取值范围。包含开头和结尾。
    ```
    http://localhost:3000/articles?id_gte=1&id_lte=12
    ```
    ```json
    [
      {
        "userId": 1,
        "id": 1,
        "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
        "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
      },
      {
        "userId": 1,
        "id": 2,
        "title": "qui est esse",
        "body": "est rerum tempore vitae\nsequi sint nihil reprehenderit dolor beatae ea dolores neque\nfugiat blanditiis voluptate porro vel nihil molestiae ut reiciendis\nqui aperiam non debitis possimus qui neque nisi nulla"
      },
      {
        "userId": 1,
        "id": 3,
        "title": "ea molestias quasi exercitationem repellat qui ipsa sit aut",
        "body": "et iusto sed quo iure\nvoluptatem occaecati omnis eligendi aut ad\nvoluptatem doloribus vel accusantium quis pariatur\nmolestiae porro eius odio et labore et velit aut"
      },
      {
        "userId": 2,
        "id": 11,
        "title": "et ea vero quia laudantium autem",
        "body": "delectus reiciendis molestiae occaecati non minima eveniet qui voluptatibus\naccusamus in eum beatae sit\nvel qui neque voluptates ut commodi qui incidunt\nut animi commodi"
      },
      {
        "userId": 2,
        "id": 12,
        "title": "in quibusdam tempore odit est dolorem",
        "body": "itaque id aut magnam\npraesentium quia et ea odit et ea voluptas et\nsapiente quia nihil amet occaecati quia id voluptatem\nincidunt ea est distinctio odio"
      }
    ]
    ```

- 用`_ne`来设置不包含某个值。
    ```
    http://localhost:3000/articles?id_ne=1&id_ne=2
    ```
    ```json
    [
      {
        "userId": 1,
        "id": 3,
        "title": "ea molestias quasi exercitationem repellat qui ipsa sit aut",
        "body": "et iusto sed quo iure\nvoluptatem occaecati omnis eligendi aut ad\nvoluptatem doloribus vel accusantium quis pariatur\nmolestiae porro eius odio et labore et velit aut"
      },
      {
        "userId": 2,
        "id": 11,
        "title": "et ea vero quia laudantium autem",
        "body": "delectus reiciendis molestiae occaecati non minima eveniet qui voluptatibus\naccusamus in eum beatae sit\nvel qui neque voluptates ut commodi qui incidunt\nut animi commodi"
      },
      {
        "userId": 2,
        "id": 12,
        "title": "in quibusdam tempore odit est dolorem",
        "body": "itaque id aut magnam\npraesentium quia et ea odit et ea voluptas et\nsapiente quia nihil amet occaecati quia id voluptatem\nincidunt ea est distinctio odio"
      }
      ......
    ]
    ```

- `_start` 来指定开始位置， `_end` 来指定结束位置，或者是用`_limit`来指定从开始位置起往后取几个数据。不包含`_start`，包含`_end`。
    ```
    http://localhost:3000/articles?_start=2&_end=5
    ```
    ```json
    [
      {
        "userId": 1,
        "id": 3,
        "title": "ea molestias quasi exercitationem repellat qui ipsa sit aut",
        "body": "et iusto sed quo iure\nvoluptatem occaecati omnis eligendi aut ad\nvoluptatem doloribus vel accusantium quis pariatur\nmolestiae porro eius odio et labore et velit aut"
      },
      {
        "userId": 2,
        "id": 11,
        "title": "et ea vero quia laudantium autem",
        "body": "delectus reiciendis molestiae occaecati non minima eveniet qui voluptatibus\naccusamus in eum beatae sit\nvel qui neque voluptates ut commodi qui incidunt\nut animi commodi"
      },
      {
        "userId": 2,
        "id": 12,
        "title": "in quibusdam tempore odit est dolorem",
        "body": "itaque id aut magnam\npraesentium quia et ea odit et ea voluptas et\nsapiente quia nihil amet occaecati quia id voluptatem\nincidunt ea est distinctio odio"
      }
    ]
    ```

- 用`_like`来设置模糊匹配某个字段
    ```
    http://localhost:3000/articles?id_like=3
    ```
    ```json
    [
      {
        "userId": 1,
        "id": 3,
        "title": "ea molestias quasi exercitationem repellat qui ipsa sit aut",
        "body": "et iusto sed quo iure\nvoluptatem occaecati omnis eligendi aut ad\nvoluptatem doloribus vel accusantium quis pariatur\nmolestiae porro eius odio et labore et velit aut"
      },
      {
        "userId": 2,
        "id": 13,
        "title": "dolorum ut in voluptas mollitia et saepe quo animi",
        "body": "aut dicta possimus sint mollitia voluptas commodi quo doloremque\niste corrupti reiciendis voluptatem eius rerum\nsit cumque quod eligendi laborum minima\nperferendis recusandae assumenda consectetur porro architecto     ipsum ipsam"
      },
      {
        "userId": 3,
        "id": 23,
        "title": "maxime id vitae nihil numquam",
        "body": "veritatis unde neque eligendi\nquae quod architecto quo neque vitae\nest illo sit tempora doloremque fugit quod\net et vel beatae sequi ullam sed tenetur perspiciatis"
      },
      {
        "userId": 4,
        "id": 31,
        "title": "ullam ut quidem id aut vel consequuntur",
        "body": "debitis eius sed quibusdam non quis consectetur vitae\nimpedit ut qui consequatur sed aut in\nquidem sit nostrum et maiores adipisci atque\nquaerat voluptatem adipisci repudiandae"
      },
      {
        "userId": 4,
        "id": 32,
        "title": "doloremque illum aliquid sunt",
        "body": "deserunt eos nobis asperiores et hic\nest debitis repellat molestiae optio\nnihil ratione ut eos beatae quibusdam distinctio maiores\nearum voluptates et aut adipisci ea maiores voluptas maxime"
      },
      {
        "userId": 4,
        "id": 33,
        "title": "qui explicabo molestiae dolorem",
        "body": "rerum ut et numquam laborum odit est sit\nid qui sint in\nquasi tenetur tempore aperiam et quaerat qui in\nrerum officiis sequi cumque quod"
      },
      {
        "userId": 5,
        "id": 43,
        "title": "eligendi iste nostrum consequuntur adipisci praesentium sit beatae perferendis",
        "body": "similique fugit est\nillum et dolorum harum et voluptate eaque quidem\nexercitationem quos nam commodi possimus cum odio nihil nulla\ndolorum exercitationem magnam ex et a et distinctio debitis"
      }
    ]
    ```

- 用 `q` 来设置全文搜索
    ```
    http://localhost:3000/articles?q=maxime
    ```
    ```json
    [
      {
        "userId": 3,
        "id": 23,
        "title": "maxime id vitae nihil numquam",
        "body": "veritatis unde neque eligendi\nquae quod architecto quo neque vitae\nest illo sit tempora doloremque fugit quod\net et vel beatae sequi ullam sed tenetur perspiciatis"
      },
      {
        "userId": 4,
        "id": 32,
        "title": "doloremque illum aliquid sunt",
        "body": "deserunt eos nobis asperiores et hic\nest debitis repellat molestiae optio\nnihil ratione ut eos beatae quibusdam distinctio maiores\nearum voluptates et aut adipisci ea maiores voluptas maxime"
      }
    ]
    ```

## 5.4 数据分页
- 分页采用 `_page` 来设置页码， `_limit` 来控制每页显示条数。如果没有指定 `_limit` ，默认每页10条。
    ```
    http://localhost:3000/articles?_page=2&_limit=3
    ```
    ```json
    [
      {
        "userId": 2,
        "id": 11,
        "title": "et ea vero quia laudantium autem",
        "body": "delectus reiciendis molestiae occaecati non minima eveniet qui voluptatibus\naccusamus in eum beatae sit\nvel qui neque voluptates ut commodi qui incidunt\nut animi commodi"
      },
      {
        "userId": 2,
        "id": 12,
        "title": "in quibusdam tempore odit est dolorem",
        "body": "itaque id aut magnam\npraesentium quia et ea odit et ea voluptas et\nsapiente quia nihil amet occaecati quia id voluptatem\nincidunt ea est distinctio odio"
      },
      {
        "userId": 2,
        "id": 13,
        "title": "dolorum ut in voluptas mollitia et saepe quo animi",
        "body": "aut dicta possimus sint mollitia voluptas commodi quo doloremque\niste corrupti reiciendis voluptatem eius rerum\nsit cumque quod eligendi laborum minima\nperferendis recusandae assumenda consectetur porro architecto     ipsum ipsam"
      }
    ]
    ```

## 5.5 数据排序
- `_sort` 指定要排序的字段， `_order` 指定正序还是逆序（asc | desc ，默认是asc）
    ```
    http://localhost:3000/articles?_sort=id&_order=desc
    ```
    ```json
    [
      {
        "userId": 5,
        "id": 43,
        "title": "eligendi iste nostrum consequuntur adipisci praesentium sit beatae perferendis",
        "body": "similique fugit est\nillum et dolorum harum et voluptate eaque quidem\nexercitationem quos nam commodi possimus cum odio nihil nulla\ndolorum exercitationem magnam ex et a et distinctio debitis"
      },
      {
        "userId": 5,
        "id": 42,
        "title": "commodi ullam sint et excepturi error explicabo praesentium voluptas",
        "body": "odio fugit voluptatum ducimus earum autem est incidunt voluptatem\nodit reiciendis aliquam sunt sequi nulla dolorem\nnon facere repellendus voluptates quia\nratione harum vitae ut"
      },
      {
        "userId": 5,
        "id": 41,
        "title": "non est facere",
        "body": "molestias id nostrum\nexcepturi molestiae dolore omnis repellendus quaerat saepe\nconsectetur iste quaerat tenetur asperiores accusamus ex ut\nnam quidem est ducimus sunt debitis saepe"
      },
      ......
    ]
    ```

- 还可以指定多个字段排序，先排前面字段，前面字段相同的再排第二个字段
    ```
    http://localhost:3000/articles?_sort=userId,id&_order=desc,asc
    ```
    ```json
    [
      {
        "userId": 5,
        "id": 41,
        "title": "non est facere",
        "body": "molestias id nostrum\nexcepturi molestiae dolore omnis repellendus quaerat saepe\nconsectetur iste quaerat tenetur asperiores accusamus ex ut\nnam quidem est ducimus sunt debitis saepe"
      },
      {
        "userId": 5,
        "id": 42,
        "title": "commodi ullam sint et excepturi error explicabo praesentium voluptas",
        "body": "odio fugit voluptatum ducimus earum autem est incidunt voluptatem\nodit reiciendis aliquam sunt sequi nulla dolorem\nnon facere repellendus voluptates quia\nratione harum vitae ut"
      },
      {
        "userId": 5,
        "id": 43,
        "title": "eligendi iste nostrum consequuntur adipisci praesentium sit beatae perferendis",
        "body": "similique fugit est\nillum et dolorum harum et voluptate eaque quidem\nexercitationem quos nam commodi possimus cum odio nihil nulla\ndolorum exercitationem magnam ex et a et distinctio debitis"
      },
      {
        "userId": 4,
        "id": 31,
        "title": "ullam ut quidem id aut vel consequuntur",
        "body": "debitis eius sed quibusdam non quis consectetur vitae\nimpedit ut qui consequatur sed aut in\nquidem sit nostrum et maiores adipisci atque\nquaerat voluptatem adipisci repudiandae"
      },
      {
        "userId": 4,
        "id": 32,
        "title": "doloremque illum aliquid sunt",
        "body": "deserunt eos nobis asperiores et hic\nest debitis repellat molestiae optio\nnihil ratione ut eos beatae quibusdam distinctio maiores\nearum voluptates et aut adipisci ea maiores voluptas maxime"
      },
      {
        "userId": 4,
        "id": 33,
        "title": "qui explicabo molestiae dolorem",
        "body": "rerum ut et numquam laborum odit est sit\nid qui sint in\nquasi tenetur tempore aperiam et quaerat qui in\nrerum officiis sequi cumque quod"
      }
      ......
    ]
    ```

## 5.6 添加数据
- 可以使用api调用工具apifox直接调用，返回值是完整的数据对象
    ![20241201105401](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201105401.png)

- 也可以使用ajax发起请求
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>添加数据</title>
        <script src="https://cdn.jsdelivr.net/npm/jquery/dist/jquery.min.js"></script>
    </head>

    <body>
        <button id="add">添加数据</button>
        <script>
            //添加数据
            $("#add").click(function () {
                $.ajax({
                    type: 'post',
                    url: 'http://localhost:3000/articles',
                    data: {
                        userId: 6,
                        id: 51,
                        title: '添加数据',
                        body: '添加数据'
                    },
                    success: function (data) {
                        console.log("post success")
                    },
                    error: function () {
                        alert("post error")
                    }
                })
            })
        </script>
    </body>

    </html>
    ```

## 5.7 更新数据
- 接口调试工具
    ![20241201105506](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201105506.png)

- ajax请求
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>更新数据</title>
        <script src="https://cdn.jsdelivr.net/npm/jquery/dist/jquery.min.js"></script>
    </head>

    <body>
        <button id="update">更新数据</button>
        <script>
            //更新数据
            $("#update").click(function() {
                $.ajax({
                    type: 'put',
                    url: 'http://localhost:3000/articles/51',
                    data: {
                        userId: 7,
                        title: '更新数据',
                        body: '更新数据'
                    },
                    success: function(data) {
                        console.log("put success")
                    },
                    error: function() {
                        alert("put error")
                    }
                })
            })
        </script>
    </body>

    </html>
    ```

## 5.8 删除数据
- 接口调试工具
    ![20241201105548](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201105548.png)

- ajax请求
    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>删除数据</title>
        <script src="https://cdn.jsdelivr.net/npm/jquery/dist/jquery.min.js"></script>
    </head>

    <body>
        <button id="delete">删除数据</button>
        <script>
            //删除数据
            $("#delete").click(function () {
                $.ajax({
                    type: 'delete',
                    url: 'http://localhost:3000/articles/51',
                    dataType: "json",
                    success: function (data) {
                        console.log("delete success")
                    },
                    error: function () {
                        alert("delete error")
                    }
                })
            })
        </script>
    </body>

    </html>
    ```

{% endraw %}