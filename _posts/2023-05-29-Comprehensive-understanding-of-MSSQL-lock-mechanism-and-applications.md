---
layout: post
title: "转：全面了解MSSQL锁机制以及应用"
author: "Kai"
header-style: text
tags:
  - SQLSERVER
---

转自：https://juejin.cn/post/6844903844724342798

# 1. 锁概念及锁应用
## 1.1 锁的概念
当用户并发对数据库进行操作时会带来数据不一致的问题，例如：

- 更新丢失（两个用户读同一个数据并进行修改，一个用户破坏了另一个用户的修改结果）
- 脏读（读出尚未提交事务的数据，产生了脏读）
- 不可重复读（用户多次读取的数据结果不一致）

所以在出现用户并发操作的时候，应该提供锁，就是在一段时间内禁止用户做某些操作以避免产生数据不一致。

## 1.2 锁的分类
1. 数据库的读写的角度来分：分为独占锁（即排它锁），共享锁和更新锁
	- 共享锁 (S)：
		共享 (S) 锁允许并发事务读取 (SELECT) 一个资源。资源上存在共享 (S) 锁时，任何其它事务都不能修改数据。一旦已经读取数据，便立即释放资源上的共享 (S) 锁，除非将事务隔离级别设置为可重复读或更高级别，或者在事务生存周期内用锁定提示保留共享 (S) 锁

	- 更新锁
		更新 (U) 锁可以防止通常形式的死锁。一般更新模式由一个事务组成，此事务读取记录，获取资源（页或行）的共享 (S) 锁，然后修改行，此操作要求锁转换为排它 (X) 锁。如果两个事务获得了资源上的共享模式锁，然后试图同时更新数据，则一个事务尝试将锁转换为排它 (X) 锁。共享模式到排它锁的转换必须等待一段时间，因为一个事务的排它锁与其它事务的共享模式锁不兼容；发生锁等待。第二个事务试图获取排它 (X) 锁以进行更新。由于两个事务都要转换为排它 (X) 锁，并且每个事务都等待另一个事务释放共享模式锁，因此发生死锁。

		若要避免这种潜在的死锁问题，请使用更新 (U) 锁。一次只有一个事务可以获得资源的更新 (U) 锁。如果事务修改资源，则更新 (U) 锁转换为排它 (X) 锁。否则，锁转换为共享锁。

	- 排它锁
		排它 (X) 锁可以防止并发事务对资源进行访问。其它事务不能读取或修改排它 (X) 锁锁定的数据

	- 意向锁
		意向锁表示 SQL Server 需要在层次结构中的某些底层资源上获取共享 (S) 锁或排它 (X) 锁。例如，放置在表级的共享意向锁表示事务打算在表中的页或行上放置共享 (S) 锁。在表级设置意向锁可防止另一个事务随后在包含那一页的表上获取排它 (X) 锁。意向锁可以提高性能，因为 SQL Server 仅在表级检查意向锁来确定事务是否可以安全地获取该表上的锁。而无须检查表中的每行或每页上的锁以确定事务是否可以锁定整个表。

		意向锁包括意向共享 (IS)、意向排它 (IX) 以及与意向排它共享 (SIX)

		意向共享 (IS) 通过在各资源上放置 S 锁，表明事务的意向是读取层次结构中的部分（而不是全部）底层资源。

		意向排它 (IX) 通过在各资源上放置 X 锁，表明事务的意向是修改层次结构中的部分（而不是全部）底层资源。IX 是 IS 的超集。

		与意向排它共享 (SIX) 通过在各资源上放置 IX 锁，表明事务的意向是读取层次结构中的全部底层资源并修改部分（而不是全部）底层资源。允许顶层资源上的并发 IS 锁。例如，表的 SIX 锁在表上放置一个 SIX 锁（允许并发 IS 锁），在当前所修改页上放置 IX 锁（在已修改行上放置 X 锁）。虽然每个资源在一段时间内只能有一个 SIX 锁，以防止其它事务对资源进行更新，但是其它事务可以通过获取表级的 IS 锁来读取层次结构中的底层资源

2. 从程序的角度进行分：（也就是经常会提到的乐观锁和悲观锁）
	- 乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性，`事后处理`--（数据版本/时间戳）
	- 悲观锁：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作，`事前处理`--（使用数据库的锁机制）

## 1.3 锁的粒度
> 锁粒度是被封锁目标的大小,封锁粒度小则并发性高,但开销大,封锁粒度大则并发性低但开销小,SQL Server支持的锁粒度可以分为为行、页、键、键范围、索引、表或数据库获取锁

- DATABASE
- FILE
- EXTENT
- ALLOCATION_UNIT
- TABLE
- Heap or B-Tree (HOBT)
- PAGE
- Row ID (RID)
- KEY
- METADATA
- APPLICATION

## 1.4 锁在SQL中的应用
1. HOLDLOCK: 在该表上保持共享锁，直到整个事务结束，而不是在语句执行完立即释放所添加的锁。
2. NOLOCK：不添加共享锁和排它锁，当这个选项生效后，可能读到未提交读的数据或“脏数据”，这个选项仅仅应用于SELECT语句。
3. PAGLOCK：指定添加页锁（否则通常可能添加表锁）。
4. READCOMMITTED用与运行在提交读隔离级别的事务相同的锁语义执行扫描。默认情况下，SQL Server 2000 在此隔离级别上操作。
5. READPAST: 跳过已经加锁的数据行，这个选项将使事务读取数据时跳过那些已经被其他事务锁定的数据行，而不是阻塞直到其他事务释放锁，READPAST仅仅应用于READ COMMITTED隔离性级别下事务操作中的SELECT语句操作。
6. READUNCOMMITTED：等同于NOLOCK。
7. REPEATABLEREAD：设置事务为可重复读隔离性级别。
8. ROWLOCK：使用行级锁，而不使用粒度更粗的页级锁和表级锁。
9. SERIALIZABLE：用与运行在可串行读隔离级别的事务相同的锁语义执行扫描。等同于 HOLDLOCK。
10. TABLOCK：指定使用表级锁，而不是使用行级或页面级的锁，SQL Server在该语句执行完后释放这个锁，而如果同时指定了HOLDLOCK，该锁一直保持到这个事务结束。
11. TABLOCKX：指定在表上使用排它锁，这个锁可以阻止其他事务读或更新这个表的数据，直到这个语句或整个事务结束。
12. UPDLOCK ：指定在读表中数据时设置更新 锁（update lock）而不是设置共享锁，该锁一直保持到这个语句或整个事务结束，使用UPDLOCK的作用是允许用户先读取数据（而且不阻塞其他用户读数据），并且保证在后来再更新数据时，这一段时间内这些数据没有被其他用户修改。

**总结:**
- 粒度锁：PAGLOCK, TABLOCK, TABLOCKX, ROWLOCK, NOLOCK
- 模式锁：HOLDLOCK, UPDLOCK, XLOCK

## 1.5 处理死锁
如何避免死锁，最小化锁竞争
- 使用事务时，尽量缩短事务的逻辑处理过程，及早提交或回滚事务，事务持有锁的时间越短，锁竞争发生的机会就越少；将不是事务所管理的工作单元锁必需的命令移出事务；
- 设置死锁超时参数为合理范围，如：3分钟-10分种；超过时间，自动放弃本次操作，避免进程悬挂；
- 优化程序，检查并避免死锁现象出现；
- 一般不要修改SQL SERVER事务的默认级别。不推荐强行加锁
- 将组成事务的语句作为一个的单独的批命令处理，以消除 BEGIN TRAN 和 COMMIT  TRAN 语句之间的网络延迟造成的不必要的延迟。
- 考虑完全地使用存储过程编写事务代码。典型地，存储过程比批命令运行更快。
- 在游标中尽可早地Commit更新。因为游标处理比面向集合的处理慢得多，因此导致锁被持有的时间更久。
- 使用每个进程所需的最低级别的锁隔离。比如说，如果脏读是可接受的并且不要求结果必须精确，那么可以考虑使用事务隔离级别0（Read Uncommitted）,仅在绝对必要时才使用Repeatable Read or Serializable隔离级别。
- 在 BEGIN TRAN 和 COMMIT TRAN 语句之间，绝不允许用户交互，因为这样做可能锁被持有无限期的时间。

# 2. 示例讲解
## 2.1 初始化数据
新建一个数据库dblock,并执行一下SQL
```sql
DROP TABLE dbo.LockTest
CREATE TABLE LockTest(ID INT IDENTITY,NAME CHAR(4000) DEFAULT 'name')
--插入6条数据，刚好3个数据页
--4000字节 两条数据就是一个数据页
INSERT INTO dbo.LockTest DEFAULT VALUES
INSERT INTO dbo.LockTest DEFAULT VALUES
INSERT INTO dbo.LockTest DEFAULT VALUES
INSERT INTO dbo.LockTest DEFAULT VALUES
INSERT INTO dbo.LockTest DEFAULT VALUES
INSERT INTO dbo.LockTest DEFAULT VALUES
```
![20240121225926](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121225926.png)
分以下多种情况进行，看看锁是如何工作

## 2.2 SQL SERVER Profiler使用
这个工具它对SQL Server的监视能力可以说是无所不能，如下图我们新建事件勾选两项，分别是锁的获取和释放：
![20240121225936](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121225936.png)

1. 首先我们查看下，如刚开始初始化数据所写一直，看一下在sqlserver中数据是如何分布的，分别可以看到表`LockTest`中有三个数据页，后面还会继续在监视中看到
![image](https://img2023.cnblogs.com/blog/1013203/202305/1013203-20230529154202341-554160112.png)


2. 了解基本的加锁情况
	- 通过查询和更新/删除，查看Profiler中的显示情况
		```sql
		SELECT * FROM LockTest
		```
        ![20240121230013](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230013.png)
		产生了意向锁（IS） , 锁的粒度分别是 TABLE,PAGE 这里就已经可以看出来是3个数据页了。先是Object 锁 然后逐页添加 Page 锁 扫描一数据页 释放一个 IS锁

	- 索引解决刚刚出现的查询阻塞
        ![20240121230029](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230029.png)
		先是Object IX 锁 page的 IU锁 rid u锁 扫描到指定需要更改的 page IX rid X 最后扫描完 再释放 rid x page IX object IX。这条是需要注意锁释放的时间

	- 通过事务演示阻塞问题
		```sql
		-- 会话1
		BEGIN TRAN
		UPDATE LockTest SET NAME='4' WHERE ID = '1'
		ROLLBACK TRAN

		-- 会话2
		SELECT * FROM LockTest WHERE ID=2
		```
        ![20240121230048](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230048.png)
		如图所示，显示会在78页获取IS锁时候发生等待释放X锁而发生阻塞
		
		```sql
		SELECT  l.request_session_id,
		 DB_NAME(l.resource_database_id),OBJECT_NAME(p.object_id),
		 l.resource_description,l.request_type,
		 l.request_status,request_mode 
		 FROM sys.dm_tran_locks AS l
		 LEFT JOIN sys.partitions AS p
		 ON l.resource_associated_entity_id=p.hobt_id
		```
		以上SQL 可以显示出 发生阻塞再等待

	- 利用索引跳过阻塞
		```sql
		-- 创建索引
		CREATE INDEX idx_LockTest ON dbo.LockTest(ID)

		-- 显示索引
		SELECT * FROM LockTest WITH(INDEX(idx_LockTest)) WHERE ID=2
		```
        ![20240121230104](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230104.png)
		可以看出在执行`ROLLBACK TRAN`前，未释放X锁的前提下`SELECT`通过索引直接查出了结果
		使用主键一样可以实现以上
		>   数据库在创建主键同时，会自动建立一个唯一索引。如果这个表之前没有聚集索引，同时建立主键时候没有强制指定使用非聚集索引，则建立主键时候，同时建立一个唯一的聚集索引

	- 通过SQL Server Profiler可以对以上粒度锁进行一一查看，更加深入的了解每种粒度

## 2.3 锁机制以及各种锁出现的情况
在使用SQL 语句的时候，经常会遇到在一个事务的中，解决不可重复读或者是丢失更新的问题

一般解决办法就是使用锁和事物的联合机制：
1. 把select放在事务中, 否则select完成, 锁就释放了
2. 要阻止另一个select , 则要手工加锁, select 默认是共享锁, select之间的共享锁是不冲突的, 所以, 如果只是共享锁, 即使锁没有释放, 另一个select一样可以下共享锁, 从而select出数据
```sql
BEGIN TRAN
SELECT * FROM Table WITH(UPDLOCK)
--或者 SELECT * FROM Table WITH(TABLOCKX, READPAST) 具体情况而定。
UPDATE ....
COMMIT TRAN
```
所有Select加 With (NoLock)解决阻塞死锁,在查询语句中使用 NOLOCK 和 READPAST
处理一个数据库死锁的异常时候，其中一个建议就是使用 NOLOCK 或者 READPAST 。有关 NOLOCK 和 READPAST的一些技术知识点：
对于非银行等严格要求事务的行业，搜索记录中出现或者不出现某条记录，都是在可容忍范围内，所以碰到死锁，应该首先考虑，我们业务逻辑是否能容忍出现或者不出现某些记录，而不是寻求对双方都加锁条件下如何解锁的问题。
NOLOCK 和 READPAST 都是处理查询、插入、删除等操作时候，如何应对锁住的数据记录。但是这时候一定要注意NOLOCK 和 READPAST的局限性，确认你的业务逻辑可以容忍这些记录的出现或者不出现：
简单来说：
1. NOLOCK 可能把没有提交事务的数据也显示出来
2. READPAST 会把被锁住的行不显示出来
不使用 NOLOCK 和 READPAST ，在 Select 操作时候则有可能报错误：事务(进程 ID **)与另一个进程被死锁在 锁 资源上，并且已被选作死锁牺牲品。

SELECT * FROM Table WITH(NOLOCK)
SELECT * FROM Table WITH(READPAST)

### 2.3.1 死锁
如下图可以看到发生死锁，我们可以通过上面所说的 设置优先级别和超时来控制发生锁竞争的结果，分别会显示“并且已被选作死锁牺牲品”，“已超过了锁请求超时时段”
![20240121230117](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230117.png)

### 2.3.2 插入锁
"表锁"锁定对该表的Select、Update、Delete操作，但不影响对该表的Insert操作也不影响以主键Id为条件的Select，所以Select如果不想等待就要在Select后加With(Nolock)，但这样会产生脏数据就是其他事务已更新但并没有提交的数据，如果该事务进行了RollBack则取出的数据就是错误的，所以好自己权衡利弊，一般情况下90%以上的Select都允许脏读，只有账户金额相关的不允许。
![20240121230127](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230127.png)

### 2.3.3 更新锁
"表锁"锁定对该表的Select、Update、Delete操作，但不影响对该表的Insert操作也不影响以主键Id为条件的Select
![20240121230138](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230138.png)

### 2.3.4 索引锁
"行锁+表锁"锁定对该表的Select、Update、Delete操作，但不影响对该表的Insert操作也不影响以主键Id为条件的Select、Update、Delete，也不影响以索引列Name为条件的Update、Delete但不可以Select
![20240121230148](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230148.png)

### 2.3.5 主键锁
"行锁+表锁"锁定对该表的Select、Update、Delete操作，但不影响对该表的Insert操作也不影响以主键Id为条件的Select、Update、Delete
![20240121230214](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230214.png)

### 2.3.6 悲观锁
![20240121230225](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121230225.png)

### 2.3.7 乐观锁
通过版本号或者时间戳，或者使用乐观锁的两种快照事务隔离级别（READ COMMITTED SNAPSHOT/SNAPSHOT）
