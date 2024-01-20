---
layout: post
title: "创建及使用一个SqlServer的用户自定义表类型（User-Defined Table Type）"
author: "Kai"
header-style: text
tags:
  - SQLSERVER
  - C#
---
创建一个用户自定义表类型（User-Defined Table Type）
```sql
CREATE TYPE [dbo].[MyTypeName] AS TABLE(
	[Field1] [nvarchar](50) NOT NULL,
	[Field2] [nvarchar](100) NULL,
	[Field3] [nvarchar](50) NULL,
	[Field4] [nvarchar](20) NULL,
	[Field5] [nvarchar](20) NULL
)
GO
```

------------

直接使用sql：
```sql
declare @infos MyTypeName
insert into @infos
select 'a1','b1','c1','d1','e1'
union
select 'a2','b2','c2','d2','e2'

--select * from @infoList
exec my_proc InfoList = @infos
```

在C#中使用：
```csharp
var dataTable = new DataTable("MyTypeName");
dataTable.Columns.Add("Field1", typeof(string));
dataTable.Columns.Add("Field2", typeof(string));
dataTable.Columns.Add("Field3", typeof(string));
dataTable.Columns.Add("Field4", typeof(string));
dataTable.Columns.Add("Field5", typeof(string));

foreach (var info in Infos)
{
	var newrow = dataTable.NewRow();
	newrow["Field1"] = info.ContractNo;
	newrow["Field2"] = info.MortgageType;
	newrow["Field3"] = info.Channel;
	newrow["Field4"] = info.CustomerType;
	newrow["Field5"] = info.LisencePlateCity;
	dataTable.Rows.Add(newrow);
}

using (SqlConnection conn = new SqlConnection(DBConn))
{
	using (SqlCommand cmd = new SqlCommand("my_proc", conn))
	{
		cmd.CommandType = CommandType.StoredProcedure;
		cmd.Parameters.Add(new SqlParameter("@InfoList", dataTable));
		conn.Open();

		using (SqlDataReader reader = cmd.ExecuteReader())
		{
			if (reader.HasRows)
			{
				while (reader.Read())
				{
				   //...
				}
			}
			reader.Close();
		}
	}
}
```