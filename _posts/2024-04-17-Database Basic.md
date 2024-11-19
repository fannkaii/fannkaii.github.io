---
layout: post
title: "数据库基础"
author: "Kai"
header-style: text
tags:
  - 数据库
---

转自：https://juejin.cn/post/7244819106355658809
{% raw %}

# 1. 数据库概念
## 1.1 数据库基本概念
### 1.1.1 数据库(DataBase:DB)
- 数据库（database）是用来**组织、存储和管理**数据的**仓库**（文件）。
- 当今世界是一个充满着数据的互联网世界，充斥着大量的**数据**。数据的来源有很多，比如出行记录、消费记录、浏览的网页、发送的消息等等。除了文本类型的数据，图像、音乐、声音都是数据。

### 1.1.2 数据库管理系统（Database Management System: DBMS）
- 数据库管理系统是专门用于**管理数据库**的计算机**系统软件**。
- 数据库管理系统能够为数据库提供数据的**定义、建立、维护、查询和统计**等操作功能，并完成对数据完整性、安全性进行控制的功能。
- 注意：我们一般说的数据库，就是指的DBMS

### 1.1.3 管理信息系统（Management Information System: MIS）
- 是一个以人为主导，利用计算机硬件，软件、网络通信设备以及其他办公设备，进行信息的收集、传输、加工、储存、更新、拓展和维护的系统。
- 数据库管理系统DBMS是管理信息系统MIS用来存储数据的一部分。

## 1.2 数据库技术发展历程
1. 层次数据库和网状数据库技术阶段
  使用指针来表示数据之间的联系。

2. 关系型数据库技术阶段
  经典的里程碑阶段，代表的DBMS有：Oracle、DB2、MySQL、SQL Server、SyBase等。

3. 后关系型数据库技术阶段
  由于关系型数据库中存在数据模型、性能、拓展伸缩性差的缺点，所以出现了ORDBMS（面向对象数据库技术），NoSQL（结构化数据库技术）。

## 1.3 常见关系型数据库
- **Oracle**：运行稳定，可移植性高，功能齐全，性能超群。适用于**大型**企业领域。
- **DB2**：速度快、可靠性好，适于海量数据，恢复性极强。适用于**大中型**企业领域。
- **SQL Server**：全面，效率高，界面友好，操作容易，但是不跨平台。适用于**中小型**企业领域。
- **MySQL**：开源，体积小，速度快。适用于**中小型**企业领域。

## 1.4 常见NoSQL数据库
- 键值存储数据库：Oracle BDB、Redis、BeansDB
- 列式储数数据库：HBase、Cassandra、Riak
- 文档型数据库：MongoDB、CouchDB
- 图形数据库：Neo4J、InfoGrid、Infinite Graph

## 1.5 关系型数据库数据组织结构
- 数据的组织结构：指的就是数据以什么样的结构进行存储。
- 传统型数据库的数据组织结构，与 Excel 中数据的组织结构比较类似。
- 因此，我们可以对比着 Excel 来了解和学习传统型数据库的数据组织结构。

### 1.5.1 Excel 的数据组织结构
- 每个 Excel 中，数据的组织结构分别为**工作簿、工作表、数据行、列**这 4 大部分组成。
  - 整个 Excel 叫做**工作簿**
  - users 和 books 是**工作表**
  - users 工作表中有 3 行数据
  - 每行数据由 6 列信息组成
  - 每列信息都有对应的**数据类型**

  ![20241119191922](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119191922.png)

### 1.5.2 关系型数据库的数据组织结构
- 在传统型数据库中，数据的组织结构分为**数据库(database)、数据表(table)、数据行(row)、字段(field)**这 4 大部分组成。
  - **数据库**类似于 Excel 的**工作簿**
  - **数据表**类似于 Excel 的**工作表**
  - **数据行**类似于 Excel 的**每一行数据**
  - **字段**类似于 Excel 的**列**
  - **每个字段**都有对应的**数据类型**

### 1.5.3 实际开发中库、表、行、字段的关系
- 在实际项目开发中，一般情况下，每个项目都对应**独立的数据库**。
- 不同的数据，要存储到数据库的**不同表中**，例如：用户数据存储到 users 表中，图书数据存储到 books 表中。
- **每个表**中具体存储哪些信息，由**字段来决定**，例如：我们可以为 users 表设计 id、username、password 这 3 个字段。
- **表中的行**，代表**每一条具体的数据**。

## 1.6 结构化查询语言(SQL)
Structured Query Language，即SQL，SQL是关系型数据库标准语言，其特点：简单，灵活，功能强大。SQL包含6个部分：

### 1.6.1 数据查询语言（DQL）
- 数据查询语言DQL，也称为“数据检索语句”，用以从表中获得数据，确定数据怎样在应用程序给出。
- 保留字SELECT是DQL语言用得最多的动词，其他DQL常用的保留字有WHERE，ORDER BY，GROUP BY和HAVING。这些DQL保留字常与其他类型的SQL语句一起使用。

### 1.6.2 数据操作语言（DML）
- 数据操作语言DML，也称为动作查询语言。用于添加，修改和删除表中的行。
- DML语句包括动词INSERT，UPDATE和DELETE。

### 1.6.3 事务处理语言（TPL）
- 事务处理语言TPL，能确保被DML语句影响的表的所有行及时得以更新。
- TPL语句包括BEGIN TRANSACTION，COMMIT和ROLLBACK。

### 1.6.4 数据控制语言（DCL）
- 数据控制语言DCL，通过GRANT或REVOKE获得许可，确定单个用户和用户组对数据库对象的访问。某些RDBMS可用GRANT或REVOKE控制对表单个列的访问。

### 1.6.5 数据定义语言（DDL）
- 数据定义语言DDL，包括动词CREATE和DROP。在数据库中创建新表或删除表（CREAT TABLE 或 DROP TABLE），为表加入索引等。
- DDL包括许多数据库目录中获得数据有关的保留字。它也是动作查询的一部分。

### 1.6.6 指针控制语言（CCL）
- 指针控制语言CCL，像DECLARE CURSOR，FETCH INTO和UPDATE WHERE CURRENT用于对一个或多个表单独行的操作。

# 2. 数据库设计
## 2.1 设计的重要性
1. 提高开发效率
2. 节省空间，硬件资源
3. 直接关乎到数据库的性能

## 2.2 设计的流程
1. 确定需求
2. 创建E-R图，可视化数据展示
  ER图一般用来表达数据库表的关系

## 2.3 数据库字段的设计
- 数据库字段的设计可以用PowerDesigner

## 2.3 PowerDesigner实操
1. PowerDesigner16.5下载安装
  链接：[pan.baidu.com/s/1y65sOlVhMyDRrTXxFbq1yg](https://pan.baidu.com/s/1y65sOlVhMyDRrTXxFbq1yg) 提取码：1234

2. PD生成SQL脚本
  建议先画E-R图，再生成数据库

   - **创建项目，填写项目名称，选择保存目录**
     - ![20241119192516](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192516.png)
     - ![20241119192527](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192527.png)

   - **创建模型文件**
     - ![20241119192600](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192600.png)
     - ![20241119192612](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192612.png)
     - ![20241119192621](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192621.png)
     - ![20241119192633](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192633.png)

   - **创建表，添加字段**
     - ![20241119192703](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192703.png)
     - 可以显示字段说明
      ![20241119192723](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192723.png)

   - **生成物理数据模型**
     - ![20241119192744](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192744.png)
     - ![20241119192752](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192752.png)
     - ![20241119192800](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192800.png)

   - **生成SQL脚本**
     - ![20241119192829](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192829.png)
     - ![20241119192837](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192837.png)

3. PD从数据库生成模型
   - **创建模型，选择数据库类型**
     - ![20241119192930](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192930.png)
     - ![20241119192939](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192939.png)

   - 配置数据库连接导出模型
     - ![20241119192958](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119192958.png)
     - ![20241119193006](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119193006.png)
     - ![20241119193015](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119193015.png)
     - ![20241119193024](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119193024.png)
     - ![20241119193033](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119193033.png)

4. PD数据类型说明

  | Standard data type | DBMS-specific physical data type | Content | Length |
  | --- | --- | --- | --- |
  | Integer | int / INTEGER | 32-bit integer | — |
  | Short Integer | smallint / SMALLINT | 16-bit integer | — |
  | Long Integer | int / INTEGER | 32-bit integer | — |
  | Byte | tinyint / SMALLINT | 256 values | — |
  | Number | numeric / NUMBER | Numbers with a fixed decimal point | Fixed |
  | Decimal | decimal / NUMBER | Numbers with a fixed decimal point | Fixed |
  | Float | float / FLOAT | 32-bit floating point numbers | Fixed |
  | Short Float | real / FLOAT | Less than 32-bit point decimal number | — |
  | Long Float | double precision / BINARY DOUBLE | 64-bit floating point numbers | — |
  | Money | money / NUMBER | Numbers with a fixed decimal point | Fixed |
  | Serial | numeric / NUMBER | Automatically incremented numbers | Fixed |
  | Boolean | bit / SMALLINT | Two opposing values (true/false; yes/no; 1/0) | — |

  | Standard data type | DBMS-specific physical data type | Content | Length |
  | --- | --- | --- | --- |
  | Characters | char / CHAR | Character strings | Fixed |
  | Variable Characters | varchar / VARCHAR2 | Character strings | Maximum |
  | Long Characters | varchar / CLOB | Character strings | Maximum |
  | Long Var Characters | text / CLOB | Character strings | Maximum |
  | Text | text / CLOB | Character strings | Maximum |
  | Multibyte | nchar / NCHAR | Multibyte character strings | Fixed |
  | Variable Multibyte | nvarchar / NVARCHAR2 | Multibyte character strings | Maximum |

  | Standard data type | DBMS-specific physical data type | Content | Length |
  | --- | --- | --- | --- |
  | Date | date / DATE | Day, month, year | — |
  | Time | time / DATE | Hour, minute, and second | — |
  | Date & Time | datetime / DATE | Date and time | — |
  | Timestamp | timestamp / TIMESTAMP | System date and time | — |

  | Standard data type | DBMS-specific physical data type | Content | Length |
  | --- | --- | --- | --- |
  | Binary | binary / RAW | Binary strings | Maximum |
  | Long Binary | image / BLOB | Binary strings | Maximum |
  | Bitmap | image / BLOB | Images in bitmap format (BMP) | Maximum |
  | Image | image / BLOB | Images | Maximum |
  | OLE | image / BLOB | OLE links | Maximum |
  | Other | — | User-defined data type | — |
  | Undefined | undefined | Undefined. Replaced by the default data type at generation. | — |

## 2.4 数据库范式
设计关系数据库时，遵从不同的规范要求，设计出合理的**关系型数据库**，这些不同的**规范要求**被称为不同的**范式**，各种范式呈**递次规范**，**越高的范式数据库冗余越小**。

1. **第一范式（1NF）属性不可再分**
   - 数据库表的每一列都是不可分割的原子数据项，而不能是集合，数组，记录等非原子数据项。
   - 即实体中的某个属性有多个值时，必须拆分为不同的属性。

2. **第二范式（2NF）非主属性完全依赖于主键**
   - 满足第二范式（2NF）必须先满足第一范式（1NF）。
   - 数据库表中的每个实例或记录必须可以被**唯一地区分**。为实现区分通常需要为表加上一个列，以存储各个实例的**唯一标识**。这个惟一属性列被称为**主键**。
   - 实体的属性**完全依赖于主关键字**，是指**不能存在仅依赖主关键字一部分的属性**，如果**存在**，那么这个属性和主关键字的这一部分应该**分离出来形成一个新的实体**，新实体与原实体之间是**一对多**的关系。

3. **第三范式（3NF）非主属性不依赖于其它非主属性**
   - 满足第三范式（3NF）必须先满足第二范式（2NF）。
   - 任何非主属性**不依赖于其它非主属性**，也就是说任何非主属性**不得传递依赖于主属性**，要求一个关系中不包含已在其它关系已包含的非主关键字信息。
   - 比如Student表（学号，姓名，年龄，性别，所在院校，院校地址，院校电话）应该拆解成两张表（学号，姓名，年龄，性别，所在院校）+（所在院校，院校地址，院校电话）

4. **巴斯-科德范式（BCNF）主属性不依赖于其它非主属性**
   - 满足巴斯-科德范式（BCNF）必须先满足第三范式（3NF）。
   - 任何主属性**不依赖于其它非主属性**，也就是说任何主属性**不得传递依赖于主属性**。
   - 比如仓库管理关系表为(仓库ID, 存储物品ID, 管理员ID, 数量)，只有数量是非主属性，满足第三范式，但是主属性之间有传递依赖，应该拆解成 仓库管理表(仓库ID, 管理员ID)+仓库表(仓库ID, 存储物品ID, 数量)。

5. **四种范氏之间的关系**
  ![20241119193949](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241119193949.png)

6. **平衡范式与冗余**
   - 冗余是以存储换取性能，范式是以性能换取存储。
   - 模型设计时，这两方面的具体的权衡，首先要以企业提供的计算能力和存储资源为基础。其次，建模也是以需求驱动的，因此冗余和范式的权衡需要符合需求。

## 2.5 表和表关系
1. **一对一**
   - 单个表存储
   - 两个表存储：两个表之间，数据记录是一对一的关系，通过同一个主键来约束

2. **一对多**
   - 单个表存储
   - 两个表存储：（推荐）主表的一条记录对应从表中的多条记录，通过主外键关系来关联存储

3. **多对多**
   - 单个表存储
   - 两个表存储
   - 三个表存储：（推荐）两个数据主要保存数据，关系表保存关系数据

## 2.6 设计习惯
以公司为单位，定制的一些内部的规范，统一的习惯对这个团队来说，会有促进作用

1. **命名规范**
   - **驼峰命名法**:  指当变量名和方法名称是由二个或二个以上单词连结在一起，首个单词首字母小写，其他单词首字母大写，而构成的唯一识别字时，用以增加变量和函式的可读性。
   - **帕斯卡命名法**：指当变量名和方法名称是由二个或二个以上单词连结在一起，每个单词首字母大写。而构成的唯一识别字时，用以增加变量和函式的可读性。
   - 坚决抵制中文，提倡使用英文单词，建议不要用汉语拼音 ，坚决抵制使用拼音首字母，建议不要太长，意思明确

2. **常用公共字段**
   - **ID**：表的物理ID
   - **CreateBy**：行记录创建人
   - **CreateTime**：行记录创建时间
   - **UpdateBy**：行记录更新人
   - **UpdateTime**：行记录更新时间
   - **UserIP**：操作人IP
   - **TS**：时间戳，版本号，乐观锁更新用
   - **IsDel**：假删除标识

{% endraw %}