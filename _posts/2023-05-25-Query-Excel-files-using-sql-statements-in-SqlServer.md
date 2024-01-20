---
layout: post
title: "在SQL语句中使用OPENROWSET函数：查询Excel文件，将存储过程数据存到临时表"
author: "Kai"
header-style: text
tags:
  - SQLSERVER
---
#### 要在SQL Server中使用连接器或驱动程序查询.xlsx文件的数据，可以按照以下步骤进行操作：
1. 安装适当的连接器或驱动程序：根据你的需要和具体情况，选择合适的连接器或驱动程序来连接和查询.xlsx文件。例如，如果你使用的是Microsoft SQL Server，可以安装Microsoft ACE OLEDB Provider或者ODBC驱动程序。
2. 配置数据源：在SQL Server中配置数据源，以便连接到.xlsx文件。这通常涉及创建数据源名称（DSN）或配置连接字符串等设置。具体的配置步骤会因所使用的连接器或驱动程序而异。
3. 连接到数据源：在SQL Server中，使用连接器或驱动程序提供的连接函数或语句来连接到.xlsx文件的数据源。这通常涉及指定连接字符串、用户名、密码等相关信息。
4. 执行SQL查询：一旦成功连接到.xlsx文件的数据源，你可以使用SQL语句来查询数据。根据具体的连接器或驱动程序，你可能需要了解所支持的SQL语法和查询方式。

需要注意的是，具体的操作步骤和语法会因所使用的连接器或驱动程序而有所不同。你可以参考连接器或驱动程序的文档、官方指南或示例来获取更详细的操作说明和示例代码。

例如，如果你使用的是Microsoft ACE OLEDB Provider连接器，可以参考相关的Microsoft文档，了解如何设置连接字符串、建立连接以及执行查询语句。类似地，如果你使用的是ODBC驱动程序，可以参考ODBC驱动程序的文档和示例来操作和查询.xlsx文件的数据。

需要根据具体情况选择适合的连接器或驱动程序，并按照其提供的文档和指南进行操作。

#### 在SQL Server Management Studio (SSMS) 中，你可以使用SQL来查询.xlsx文件的数据，但这需要通过特定的操作来实现。以下是一种可能的方法，使用OPENROWSET函数和Microsoft ACE OLEDB Provider连接器来查询.xlsx文件：

```sql
-- 设置适当的服务器选项
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;

-- 启用Ad Hoc分布式查询
EXEC sp_configure 'Ad Hoc Distributed Queries', 1;
RECONFIGURE;

EXEC master.dbo.sp_MSset_oledb_prop N'Microsoft.ACE.OLEDB.12.0' , N'AllowInProcess' , 1
GO

-- 查询.xlsx文件的数据
SELECT *
FROM OPENROWSET('Microsoft.ACE.OLEDB.12.0', 
                'Excel 12.0;Database=C:\Users\xxx\Desktop\aa.xlsx;HDR=YES',
                'SELECT * FROM [Sheet1$]'); -- 修改Sheet1为你的实际工作表名称

--查询存储过程，并将查询结果存到临时表
select * into #b from openrowset('SQLNCLI', 'Server=.;Trusted_Connection=yes;', 'exec MyDB.dbo.my_proc @a=0,@b=2147483647')
```

#### 备注1：
`EXEC master.dbo.sp_MSset_oledb_prop N'Microsoft.ACE.OLEDB.12.0' , N'AllowInProcess' , 1` 是一个用于设置 SQL Server 的存储过程调用语句。

这个语句的目的是设置 Microsoft ACE OLEDB Provider 的属性，具体是设置 "AllowInProcess" 属性为 1，表示允许在 SQL Server 进程中运行该提供程序。

Microsoft ACE OLEDB Provider 是一种用于连接和查询 Office 数据文件（如.xlsx文件）的提供程序。在某些情况下，当你尝试使用 OPENROWSET 函数或其他方法查询.xlsx文件时，可能会遇到错误，其中之一可能是 "The OLE DB provider 'Microsoft.ACE.OLEDB.12.0' cannot be used for distributed queries because the provider is configured to run in single-threaded apartment mode."。

为了解决这个问题，可以使用上述语句来设置 "AllowInProcess" 属性，以便允许 Microsoft ACE OLEDB Provider 在 SQL Server 进程中运行。

请注意，在执行这个语句之前，你需要具有足够的权限来执行系统存储过程。此外，这个设置仅对当前 SQL Server 实例生效，重新启动 SQL Server 之后会失效，因此你可能需要在每次启动 SQL Server 时重新设置。

需要注意的是，在进行这样的设置之前，你应该仔细评估潜在的安全风险和性能影响，并确保你理解该设置的后果。

#### 备注2:
在用sql语句查询Excel文件时，可能还需要为将要在Excel文件上给sqlserver服务的登录账号（下图橙色框）配置访问权限：
![20240120224043](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240120224043.png)