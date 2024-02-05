---
layout: post
title: "转：ASP.NET Core Identity 系列之一：配置"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：**https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486141&idx=1&sn=f77635080994c6295cb801e846427a15**
桂迹，微信公众号
**https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486318&idx=1&sn=1f06f6de690ba6df16ed812de9588709**

ASP.NET Core Identity提供给我们一组工具包和API，它能帮助我们应用程序创建授权和认证功能，也可以用它创建账户并使用用户名和密码进行登录，同时也提供了角色和角色管理功能。ASP.NET Core Identity使用SQL Server/第三方数据库存储用户名和密码，角色和配置数据

这系列中我们主要使用VS中自带的LocalDB作为演示，你也可以直接从官网上进行下载：https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver16

# 1. 创建项目
理解ASP.NET Core Identity最好的方法是通过一个项目学习，我们创建一个ASP.NET Core MVC 项目，名字叫Identity，接下来，我们将配置该项目并且安装必要的包

# 2. 配置项目
安装下列NuGet包
Microsoft.AspNetCore.Identity.EntityFrameworkCore
Microsoft.EntityFrameworkCore.Design
Microsoft.EntityFrameworkCore.SqlServer

![20240205135228](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135228.png)

# 3. 配置项目
在Program.cs类中添加认证和授权中间件，在app.UseRouting后面添加如下代码：
```csharp
app.UseAuthentication();
app.UseAuthorization();
```

# 4. 设置ASP.NET Core Identity
ASP.NET Core Identity整个设置过程包括创建新的Model类、配置更改、Controller和Action支持身份验证和授权的操作

## 4.1 User类
User类表示应用程序中的用户，这些用户数据存储在数据库中，User类继承自IdentityUser类，位于命名空间Microsoft.AspNetCore.Identity中，在Models文件夹下创建AppUser.cs类
```csharp
namespace Identity.Models{
       public class AppUser : IdentityUser{}
}
```

AppUser类没有包含任何方法，这是因为IdentityUser类中提供了一些用户属性像用户名，电子邮件，电话，密码hash值等

如果IdentityUser类不能满足你的要求，你可以在AppUser中添加自己定义的属性，我们会在后面介绍

IdentityUser 类定义如下常用属性：

|名称|描述|
| ------------ | ------------ |
| Id | 用户唯一ID |
| UserName | 用户名称 |
| Email | 用户Email |
| PasswordHash | 用户密码的Hash值 |
| PhoneNumber | 用户电话号码 |
| SecurityStamp | 当每次用户的数据修改时生成随机值 |

## 4.2 创建DataBase Context
`DataBase Context`类继承自`IdentityDbContext<T>`类，T表示`User`类，在应用程序中使用`AppUser`，`IdentityDbContext`通过使用Entity Framework Core和数据库进行交互

在Models文件夹下创建一个`AppIdentityDbContext`类并继承`IdentityDbContext<AppUser>`类，如下代码：
```csharp
namespace Identity.Models{
   public class AppIdentityDbContext: IdentityDbContext<AppUser>
    {
        public AppIdentityDbContext(DbContextOptions<AppIdentityDbContext> options):
            base(options)
        { }
    }
}
```

## 4.3 创建数据库字符串链接
ASP.NET Core Identity 数据库连接字符串包含数据库名，用户名，密码。通常存储在appsettings.json文件中，这个文件位于根目录下

项目已经包含了这个文件，添加下面配置在你的 appsettings.json 文件中
```json
"ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=IdentityDB;Trusted_Connection=True;MultipleActiveResultSets=true"
}
```
连接字符串中Server指定SQL Server的LocalDB，Database指定数据库名称IdentityDB，你也可以起个别的名字

Trusted_Connection 设置为ture，项目通过使用windows认证链接到数据库，因此我们不需要提供用户名和密码

MultipleActiveResultSets 该特性表示允许在单个连接中执行多个批处理，使SQL语句执行更快，因此我们将它设置成true，`MultipleActiveResultSets=true`

使用AddDbContext()方法添加AppIdentityDbContext类并且指定它使用SQL Server数据库，连接字符串通过配置文件中获取
```csharp
builder.Services.AddDbContext<AppIdentityDbContext>(
    options =>options.UseSqlServer(builder.Configuration["ConnectionStrings:DefaultConnection"])
);
```

# 5. 添加ASP.NET Core Identity服务
现在我们配置一下ASP.NET Core Identity 相关服务，代码如下：
```csharp
builder.Services.AddIdentity<AppUser, IdentityRole>().
	AddEntityFrameworkStores<AppIdentityDbContext>().
	AddDefaultTokenProviders();
```
- AddIdentity方法的参数类型指定AppUser类和IdentityRole类
- AddEntityFrameworkStores方法指定Identity使用EF Core作为存储和项目中使用AppIdentityContext作为DB Context
- AddDefaultTokenProviders方法添加默认Token提供程序，针对重置密码，电话号码和邮件变更操作以及生成双因子认证的
token，这部分我们后面会介绍

我们前面添加了app.UseAuthentication()方法，经过这个方法的每个HTTP请求会将用户的凭据将添加到Cookie或URL中。这使得用户和他发送的HTTP请求就会产生关联。

# 6. 使用EF Migration 命令创建Identity 数据库
现在我们使用Entity Framework Core Migration 命令来创建Identity数据库(**我们使用第二种方式**)

我们在程序包管理器控制台中运行Migration 命令， 你需要安装对应.NET Core CLI，通过下面命令可以安装:
```bash
dotnet tool install --global dotnet-ef
```
接下来我们需要进入 .csproj 项目文件所在的目录。我们在程序包管理器控制台中运行dir来查看当前所在目录。我们可以看到当前目录不是项目所在目录，而是Identity.sIn
![20240205135255](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135255.png)

项目文件位于Identity文件夹内，使用 `cd ./Identity` 进入该文件夹。我们再次运行 `dir` 命令，显示我们当前所在的目录是  Identity.csproj 文件所在目录：
![20240205135302](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135302.png)

现在我们运行 EF Core Migration 命令

**第一个命令：**
```bash
dotnet ef migrations add InitDBCommand
```
上面的命令需要几秒钟完成，完成之后，我们可以在Migrations文件夹能看到生成的文件，文件中包含表的定义。

**第二个命令：**
```bash
dotnet ef database update
```
上面命令将创建数据库以及表，执行完成这个命令我们就可以看到我们表结构。

第二种方法我们可以不使用.NET Core CLI命令，微软给我提供了一个包Microsoft.EntityFrameworkCore.Tools。该包提供一些命令帮助我们生成数据库和表，在程序包管理控制台中运行如下两个命令:
```bash
Add-Migration InitDBCommand 	--创建Migration
Update-Database          		--将Migration 更新到数据库
```

# 7. ASP.NET Core Identity 数据库
我们查看一下刚才创建的数据库
![20240205135312](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135312.png)
注意：我们在Visual Studio 菜单找到视图菜单并且打开SQL Server 对象资源管理器

Identity 数据库总共有8张表，这些表包含用户记录，角色，Claims，token和登录次数详细信息等
![20240205135319](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135319.png)

我们看一下每张表的作用：
- _EFMigrationsHistory – 包含了前面所有的Migration
- AspNetRoleClaims – 按照角色存储Claims
- AspNetRoles – 存储所有的角色
- AspNetUserClaims – 存储用户的Claims
- AspNetUserLogins –存储用户的登录次数
- AspNetUserRoles – 存储用户对应的角色
- AspNetUsers – 存储所有用户
- AspNetUserTokens – 存储外部认证的token

# 8. 总结
这节我们主要介绍了如何配置ASP.NET Core Identity，我们现在可以添加用户，管理用户，添加角色，添加用户到角色，用户认证，Identity可以帮助我们做很多工作，我们在下节继续介绍。

源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/AspNetCore.Identity/Identity

