---
layout: post
title: "SqlServer数据行转列"
author: "Kai"
header-style: text
tags:
  - SQLSERVER
---

## 准备表和插入数据
```sql
CREATE TABLE [dbo].[Test](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[Type] [nvarchar](30) NOT NULL,
	[Status] [nvarchar](30) NOT NULL,
	[AppNo] [nvarchar](50) NOT NULL,
 CONSTRAINT [PK_DocumentSet] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY],
 CONSTRAINT [unique_Test_Type_AppNo] UNIQUE NONCLUSTERED 
(
	[Type] ASC,
	[AppNo] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

insert into Test
select 'type1', 'status_a', '100021'
union all
select 'type2', 'status_b', '100021'
union all
select 'type1', 'status_b', '100022'
union all
select 'type2', 'status_c', '100022'
union all
select 'type1', 'status_a', '100023'
union all
select 'type2', 'status_c', '100023'
union all
select 'type1', 'status_e', '100024'
union all
select 'type2', 'status_n', '100024'
union all
select 'type1', 'status_b', '100025'
union all
select 'type2', 'status_y', '100025'
union all
select 'type1', 'status_e', '100026'
union all
select 'type2', 'status_c', '100027'
GO
```

------------


## 查询当前数据
```sql
select * from Test
```
查询结果：
![20240121220457](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121220457.png)

------------


## 当前数据行转列
```sql
select AppNo, max(case [Type] when N'type1' then status end) as 'Type1 status', 
 max(case [Type] when N'type2' then status end) as 'Type2 status'
FROM [dbo].[Test]
group by AppNo
```
查询结果：
![20240121220508](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121220508.png)
