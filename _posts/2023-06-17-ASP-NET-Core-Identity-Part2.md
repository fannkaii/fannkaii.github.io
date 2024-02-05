---
layout: post
title: "转：ASP.NET Core Identity 系列之二：用户管理"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486148&idx=1&sn=dae55b414e123c6718e470c21c8c8c21

这节我们主要演示在ASP.NET Core Identity中创建、修改、删除、查询用户
# 1. ASP.NET Core Identity UserManager 类
UserManager类位于Microsoft.AspNetCore.Identity命名空间中，我们可以使用该类管理数据库中的用户。我们使用泛型版本的 `UserManager<T>` 类来实现基本的CRUD操作

下面描述泛型版本类常用成员

|方法名称|描述|
| ------------ | ------------ |
| FindByIdAsync(id) | 根据指定的ID查询用户对象 |
| CreateAsync(user,password) | 注册一个新的用户 |
| UpdateAsync(user) | 修改一个已经存在的用户 |
| DeleteAsync(user) | 移除一个指定的用户 |
| AddToRoleAsync(user, name) | 给用户添加角色 |
| RemoveFromRoleAsync(user, name) | 从角色中移除用户 |
| GetRolesAsync(user) | 获取角色名字列表 |
| IsInRoleAsync(user, name) | 返回一个标志，表示是否是给与角色名字的成员 |

在前面的例子中，我们创建了EF Core DBContext类和AppUser类，AppUser表示数据库中的用户

表格中显示AppUser类常用的属性

|名称|描述|
| ------------ | ------------ |
| Id | 包含用户唯一ID |
| UserName | 包含用户名称 |
| Email | 包含用户Email |

# 2. 创建Identity 用户
我们在Models文件创建一个User.cs的类，该类中有三个公共属性`Name，Email & Password`，类型都是字符串，我们给每个属性都加上 `[Required]` 特性。

为了验证正确的Email输入，在Email属性上加上正则表达式验证(RegularExpression)
```csharp
 public class User
 {
    [Required]
    [DisplayName("用户名")]
    public string Name { get; set; } = null!;
    [Required]
    [DisplayName("邮箱")]
    [RegularExpression("^[a-zA-Z0-9_\\.-]+@([a-zA-Z0-9-]+\\.)+[a-zA-Z]{2,6}$", ErrorMessage = "电子邮件格式不正确")]
    public string Email { get; set; } = null!;
    [Required]
    [DataType(DataType.Password)]
    [DisplayName("密码")]
    public string Password { get; set; } = null!;
}
```

在Controllers文件夹下新增一个新的AdminController类，在该控制器中创建CURD方法，AdminController如下：
```csharp
public class AdminController : Controller
{
    private UserManager<AppUser> _userManager;
    public AdminController(UserManager<AppUser> userManager)
    {
       _userManager = userManager;
    }
    public IActionResult Index()
    {
       return View();
    }
}
```

在AdminController的构造函数中引用UserManager类，接下来我们添加Create方法来创建新用户，如下代码：
```csharp
public ViewResult Create() => View();

[HttpPost]
public async Task<IActionResult> Create(User user)
{
    if (ModelState.IsValid)
    {
        AppUser appUser = new AppUser
        {
            UserName = user.Name,
            Email = user.Email,
        };
        var identityResult = await _userManager.CreateAsync(appUser, user.Password);
        if (identityResult.Succeeded)
        {
            return RedirectToAction("Index", "Admin");
        }
        else
        {
            foreach (IdentityError error in identityResult.Errors)
            {
                ModelState.AddModelError("", error.Description);
            }
        }
    }
    return View(user);
}
```

Post版本的Create方法创建一个新的Identity用户，该方法的参数获取一个User类并检查模型绑定是否成功。如果模型绑定成功创建一个AppUser类的实例并且将UserName和Email赋值给该对象
```csharp
AppUser appUser = new AppUser
{
   UserName = user.UserName,
   Email = user.Email,
};
```

在这个之后，我们通过调用UserManager类的CreateAsync创建一个用户，CreateAsync方法有两个参数，一个是AppUser对象类，第二个是Password。该方法返回IdentityResult类型
```csharp
var identityResult = await _userManager.CreateAsync(appUser, user.Password);
```

IdentityResult 类包含下面属性：

Succeeded – 如果这个属性返回true，则表示该用户创建成功
Errors – 这个属性是`IEnumerable<IdentityError>`类型，表示在创建用户过程中发生的错误信息，IdentityError有个Description属性表示错误的详细信息。

我们可以使用Succeeded属性来判断用户是否创建成功。如果成功则跳转到Index视图，否则我们将所有的错误添加到ModelState中，代码如下：
```csharp
if (identityResult.Succeeded)
{
    return RedirectToAction("Index", "Admin");
}
else
{
   foreach (IdentityError error in identityResult.Errors)
   {
      ModelState.AddModelError("", error.Description);
   }
}
```

最后一行我们将user返回到Create视图 - `return View(user)`，将错误信息显示在浏览器提示用户改正
接下来我们在视图文件Views -> Admin文件夹下创建一个Create.cshtml，代码如下：
```cshtml
@model User
@{
    ViewData["Title"] = "新增用户";
}
<div asp-validation-summary="All" class="text-danger"></div>
<form class="form-horizontal" role="form" method="post">
    <div class="mb-3 row">
        <label asp-for="Name" class="col-sm-1 control-label"></label>
        <div class="col-sm-11">
            <input asp-for="Name" class="form-control" />
        </div>
    </div>
    <div class="mb-3 row">
        <label asp-for="Email" class="col-sm-1 control-label"></label>
        <div class="col-sm-11">
            <input asp-for="Email" class="form-control" />
        </div>
    </div>
    <div class="mb-3 row">
        <label asp-for="Password" class="col-sm-1 control-label"></label>
        <div class="col-sm-11">
            <input asp-for="Password" class="form-control" />
        </div>
    </div>
    <div class="mb-3 row">
        <div class="col-sm-11 offset-sm-1">
            <button type="submit" class="btn btn-primary">保存</button>
            <button asp-action="Index" class="btn btn-secondary">
                返回
            </button>
        </div>
    </div>
</form>
```

用户可以通过该表单将 `用户名&邮件&密码` 提交 AdminController 的 Create 方法，从而保存到数据库中

接下来我们创建一个新的用户，运行应用程序在浏览器中输入下面地址：https://localhost:7296/Admin/Create
![20240205135505](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135505.png)

输入下面信息并点击确认：
- 姓名: Yogi
- 邮箱: yogi@gmail.com
- 密码: Admin@123

请注意，Identity要求密码长度至少为6个字符，并且必须至少包含一个非字母数字、一个小写和一个大写字符

每次创建完用户之后都会跳转到Index 视图，该视图展示了数据库中所有的用户信息。接下来我们创建显示用户列表的页面

不能创建一个相同的用户，如果你创建相同的用户你会发现获取错误提示 `User name ‘xxx’ is already taken`

我们创建所有Identity用户存储到AspNetUsers表，打开这个表发现用户记录已经被创建。出于安全目的，ASP.NET Core Identity将我们密码进行加密存储，并不是存储的平面文本：
![20240205135514](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135514.png)

# 3. 查询Identity所有用户
我们使用UserManager类能从数据库中获取所有的Identity用户。Users属性将返回一个IEnumerable对象，修改一下AdminController的Index方法：
```csharp
public class AdminController : Controller
{
    private UserManager<AppUser> _userManager;
    public AdminController(UserManager<AppUser> userManager)
    {
        _userManager = userManager;
    }
    public IActionResult Index()
    {
        return View(_userManager.Users);
    }
}
```

我们将所有的用户以IEnumerable形式返回到View。

现在我们在Views -> Admin目录下创建Index.cshtml视图，显示用户的详细信息。
```cshtml
@model IEnumerable<AppUser>
@{
    ViewData["Title"] = "用户列表";
}
<div class="container">
    <div class="row mb-3">
        <div class="col-sm-3">
            <a asp-action="Create" class="btn btn-primary">新增</a>
        </div>
        <div class="col-sm-3"></div>
        <div class="col-sm-3"></div>
        <div class="col-sm-3"></div>
    </div>
    <div class="row mb-3">
        <div class="col-sm">
            <table class="table table-bordered align-middle">
                <thead>
                    <tr>
                        <th>编号</th>
                        <th>名称</th>
                        <th>邮件</th>
                    </tr>
                </thead>
                <tbody>
                    @foreach (AppUser user in Model)
                    {
                        <tr>
                            <td>@user.Id</td>
                            <td>@user.UserName</td>
                            <td>@user.Email</td>
                        </tr>
                    }
                </tbody>
            </table>
        </div>
    </div>
</div>
```

在这个View中接受一个 `IEnumerable<AppUser>` 的模型类，使用foreach循环该类将所有用户的信息显示在表格内

我们测试一下该功能，在浏览其中输入https://localhost:7296/Admin ，将调用Index方法。下面图中显示了在数据库中的所有的用户记录：
![20240205135526](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135526.png)

# 4. 更新用户记录
现在对用户记录做更新，在Index.cshtml页面表格中添加Update列，在这一列中添加锚定链接到Update方法
```cshtml
@model IEnumerable<AppUser>
@{
    ViewData["Title"] = "用户列表";
}
<div class="container">
    <div class="row mb-3">
        <div class="col-sm-3">
            <a asp-action="Create" class="btn btn-primary">新增</a>
        </div>
        <div class="col-sm-3"></div>
        <div class="col-sm-3"></div>
        <div class="col-sm-3"></div>
    </div>
    <div class="row mb-3">
        <div class="col-sm">
            <table class="table table-bordered align-middle">
                <thead>
                    <tr>
                        <th>编号</th>
                        <th>名称</th>
                        <th>邮件</th>
                        <th>编辑</th>
                    </tr>
                </thead>
                <tbody>
                    @foreach (AppUser user in Model)
                    {
                        <tr>
                            <td>@user.Id</td>
                            <td>@user.UserName</td>
                            <td>@user.Email</td>
                            <td>
                                <a class="btn btn-primary btn-sm" asp-action="Update" asp-route-id="@user.Id">编辑</a>
                            </td>
                        </tr>
                    }
                </tbody>
            </table>
        </div>
    </div>
</div>
```

我们需要在AdminController的构造函数添加IPasswordHasher依赖。使用它来获取用户密码的哈希值。出于安全目ASP.NET Core Identity存储用户密码使用的是用户密码的哈希值，而不是平面文本。因此黑客获取到用户信息，是无法登录

我们修改一下 AdminController:
```csharp
public class AdminController : Controller
    {
        private UserManager<AppUser> _userManager;
        private IPasswordHasher<AppUser> _passwordHasher;
        public AdminController(UserManager<AppUser> userManager,
                               IPasswordHasher<AppUser> passwordHash)
        {
            _userManager = userManager;
            _passwordHasher = passwordHash;
        }
}
```

我们在AdminController中添加Update 方法，代码如下：
```csharp
public async Task<IActionResult> Update(string Id)
{
    var appUser = await _userManager.FindByIdAsync(Id);
    if (appUser != null)
    {
        var updateUserDTO = new UpdateUserDTO();
        updateUserDTO.Id = appUser.Id;
        updateUserDTO.Name = appUser.UserName ?? "";
        updateUserDTO.Email = appUser.Email == null ? "" : appUser.Email;
        return View(updateUserDTO);
    }
    else
    {
        return RedirectToAction("Index");
    }
}

[HttpPost]
public async Task<IActionResult> Update(UpdateUserDTO updateUserDTO)
{
    var appUser = await _userManager.FindByIdAsync(updateUserDTO.Id);
    if (appUser != null)
    {
        if (updateUserDTO.Email != null)
            appUser.Email = updateUserDTO.Email;
        else
            ModelState.AddModelError("", "邮箱不能为空");
        if (updateUserDTO.Name != null)
            appUser.UserName = updateUserDTO.Name;
        else
            ModelState.AddModelError("", "用户名不能为空");
        if (updateUserDTO.Password != null)
            appUser.PasswordHash = _passwordHasher.HashPassword(appUser, updateUserDTO.Password);
        else
            ModelState.AddModelError("", "密码不能为空");
        if (!string.IsNullOrEmpty(updateUserDTO.Email) &&
            !string.IsNullOrEmpty(updateUserDTO.Password))
        {
            var result = await _userManager.UpdateAsync(appUser);
            if (result.Succeeded)
                return RedirectToAction("Index");
            else
            {
                foreach (IdentityError error in result.Errors)
                {
                    ModelState.AddModelError("", error.Description);
                }
            }
        }
    }
    else
        ModelState.AddModelError("", "没有发现该用户");
    return View(updateUserDTO);
}
```

HTTP GET版本的 Update 方法参数接受一个字符串类型的Id。这个Id表示用户注册时生成的Id，用 `UserManager` 类 `FindByIdAsync()` 方法从 Identity数据库 中查找该用户记录，该方法提供了通过用户Id查找用户信息
```csharp
var appUser = await _userManager.FindByIdAsync(Id);
```

每次查找完用户信息后会将用户信息作为实体发送到Update.cshtml 视图。当用户点击提交按钮时会将表单的数据提交到HTTP POST 版本的Update方法，该方法会更新用户数据。首先使用FindByIdAsync()方法获取用户信息：
```csharp
var appUser = await _userManager.FindByIdAsync(Id);
```

如果AppUser对象不是null，我们检查email和password是否为null，如果不为null我们更新AppUser类Email和PasswordHash属性值，我们将修改后的AppUser作为参数传递给UserManager类的UpdateAsync方法
```csharp
if (updateUserDTO.Email != null)
    appUser.Email = updateUserDTO.Email;
else
    ModelState.AddModelError("", "邮箱不能为空");
if (updateUserDTO.Password != null)
    appUser.PasswordHash = _passwordHasher.HashPassword(appUser, updateUserDTO.Password);
else
    ModelState.AddModelError("", "密码不能为空");
```

注意我们使用HashPassword()方法生成用户密码的哈希值
```csharp
appUser.PasswordHash = _passwordHasher.HashPassword(appUser, updateUserDTO.Password);
```

使用UpdateAsync方法更新，这个方法参数是一个AppUser的对象。代码如下：
```csharp
var result = await _userManager.UpdateAsync(appUser);
if (result.Succeeded)
    return RedirectToAction("Index");
else
     foreach (IdentityError error in result.Errors)
         Errors(error);
```

如果更新方法没有执行成功，我们需要调用Errors将对应的错误信息添加到ModelState中，并将这些错误信息显示在浏览器上。代码如需：
```csharp
void Errors(IdentityResult result)
{
   foreach (IdentityError error in result.Errors)
       ModelState.AddModelError("", error.Description);
}
```

在Views -> Admin文件夹下创建 Update.cshtml 文件：
```cshtml
@model UpdateUserDTO
@{
    ViewData["Title"] = "修改用户";
}
<div asp-validation-summary="All" class="text-danger"></div>
<form class="form" method="post" role="form">
    <div class="row mb-3">
        <label asp-for="Id" class="col-sm-1 control-label"></label>
        <div class="col-sm-11">
            <input asp-for="Id" class="form-control" disabled />
        </div>
    </div>
     <div class="mb-3 row">
        <label asp-for="Name" class="col-sm-1 control-label"></label>
        <div class="col-sm-11">
            <input asp-for="Name" class="form-control" />
        </div>
    </div>
    <div class="row mb-3">
        <label asp-for="Email" class="col-sm-1 form-label"></label>
        <div class="col-sm-11">
            <input asp-for="Email" class="form-control" />
        </div>
    </div>
    <div class="row mb-3">
        <label asp-for="Password" class="col-sm-1 form-label"></label>
        <div class="col-sm-11">
            <input asp-for="Password" class="form-control" />
        </div>
    </div>
    <div class="row mb-3">
        <div class="col-sm-11 offset-sm-1">
            <button class="btn btn-primary">保存</button>
            <button asp-action="index" class="btn btn-secondary">返回</button>
        </div>
    </div>
</form>
```

```csharp
public class UpdateUserDTO
{
    [Required]
    [DisplayName("编号")]
    public string Id { get; set; } = null!;
    [Required]
    [DisplayName("用户名")]
    public string Name { get; set; } = null!;
    [Required]
    [DisplayName("邮箱")]
    [RegularExpression("^[a-zA-Z0-9_\\.-]+@([a-zA-Z0-9-]+\\.)+[a-zA-Z]{2,6}$", ErrorMessage = "E-mail is not valid")]
    public string Email { get; set; } = null!;
    [Required]
    [DataType(DataType.Password)]
    [DisplayName("密码")]
    public string Password { get; set; } = null!;
}
```

我们测试一下更新功能，启动应用程序在浏览器中输入如下地址：https://localhost:7296/Admin ，点击更新按钮会跳转到对应的更新页面，输入邮箱和密码并且点击保存按钮将会更新数据库中的用户记录：
![20240205135550](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135550.png)
![20240205135558](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135558.png)
![20240205135606](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135606.png)

# 4. 删除用户
最后我们完成删除操作。使用AdminControoler中的Delete方法完成。代码如下：
```csharp
[HttpPost]
public async Task<IActionResult> Delete(string Id)
{
    var appUser = await _userManager.FindByIdAsync(Id);
    if (appUser != null)
    {
        var result = await _userManager.DeleteAsync(appUser);
        if (result.Succeeded)
            return RedirectToAction("Index");
        else
            Errors(result);
    }
   else
     ModelState.AddModelError("", "没有发现该用户");
   return View("Index", _userManager.Users);
}
```

删除方法接受一个用户Id并且调用FindByIdAsync()方法查询对应用户记录，获取到相匹配的用户之后使用DeleteAsync方法删除。代码如下：
```csharp
var result = await _userManager.DeleteAsync(appUser);
```

如果删除成功，用户会跳转到Index方法，否则会将相应的错误显示到浏览器

我们在Index.cshtml页面添加删除按钮。代码如下：
```cshtml
@model IEnumerable<AppUser>
@{
    ViewData["Title"] = "用户列表";
}
<div class="container">
    <div class="row mb-3">
        <div class="col-sm-3">
            <a asp-action="Create" class="btn btn-primary">新增</a>
        </div>
        <div class="col-sm-3"></div>
        <div class="col-sm-3"></div>
        <div class="col-sm-3"></div>
    </div>
    <div class="row mb-3">
        <div class="col-sm">
            <table class="table table-bordered align-middle">
                <thead>
                    <tr>
                        <th>编号</th>
                        <th>名称</th>
                        <th>邮件</th>
                        <th>编辑</th>
                        <th>删除</th>
                    </tr>
                </thead>
                <tbody>
                    @foreach (AppUser user in Model)
                    {
                        <tr>
                            <td>@user.Id</td>
                            <td>@user.UserName</td>
                            <td>@user.Email</td>
                            <td>
                                <a class="btn btn-primary btn-sm" asp-action="Update" asp-route-id="@user.Id">编辑</a>
                            </td>
                            <td>
                                <form method="post" asp-action="Delete" asp-route-id="@user.Id" role="form">
                                    <button type="submit" class="btn btn-danger btn-sm">删除</button>
                                </form>
                            </td>
                        </tr>
                    }
                </tbody>
            </table>
        </div>
    </div>
</div>
```

运行应用程序，在浏览器中输入https://localhost:7296/admin 地址，我们看到所有的Identity User记录旁边有都一个删除按钮，当我们点击删除按钮时，会将用户从数据库中删除
![20240205135620](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135620.png)
![20240205135627](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135627.png)

# 5. 总结
这节我们主要介绍了在ASP.NET Core Identity中新增、查询、修改、删除用户，在下一节我们将针对用户名、邮箱、密码创建策略，以及客户自定义规则
源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/AspNetCore.Identity/Identity

