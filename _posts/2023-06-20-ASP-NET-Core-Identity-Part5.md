---
layout: post
title: "转：ASP.NET Core Identity 系列之五：角色管理"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486194&idx=1&sn=a213c72dd0564c31a7624c6d99f0d277

这节我们将介绍在Identity中如何使用Role，在我们应用程序中可以通过ASP.NET Core Identity创建Roles并且该角色可以包含一系列权限来执行应用程序的一系列活动

例如：一个组织可以有4个角色：
1. Admin – 管理员角色给员工分配工作
2. Manager –查看客户需求并按时完成项目
3. Network –用于保持组织的互联网以安全的方式运行。
4. Security - 系统安全相关权限

在ASP.NET Core Identity 我们能创建任何数量的Roles并且可以将这些Roles赋值给Identity Users

# 1. ASP.NET Core Identity RoleManager类
我们使用ASP.NET Core Identity RoleManager来管理Role，`RoleManager<T>` 泛型版本T表示Identity在数据库中的Roles

RoleManager定义了一些重要的功能和属性：

|名称|描述|
| ------------ | ------------ |
| CreateAsync(role) | 创建一个新的角色 |
| DeleteAsync(role) | 删除一个指定的角色 |
| FindByIdAsync(id) | 根据角色Id查找一个角色 |
| FindByNameAsync(name) | 根据角色名称查找一个角色 |
| RoleExistsAsync(name) | 根据角色名称检查角色是否存在 |
| UpdateAsync(name) | 更新角色 |
| Roles | 返回Identity中的所有角色 |

# 2. Identity中角色管理
我们使用ASP.NET Core Identity实现一个创建和删除角色的功能，创建一个名字为 RoleController.cs 并且添加如下代码：
```csharp
public class RoleController : Controller
{
    private RoleManager<IdentityRole> _roleManager;
    public RoleController(RoleManager<IdentityRole> roleManager)
    {
        _roleManager = roleManager;
    }
    public IActionResult Index() => View(_roleManager.Roles);
    private void Errors(IdentityResult result)
    {
        foreach (IdentityError error in result.Errors)
            ModelState.AddModelError("", error.Description);
    }
}
```

在RoleController中，通过构造函数注入了RoleManager类，我们可以通过依赖注入获取到该类，并使用它来管理Identity角色
```csharp
private RoleManager<IdentityRole> _roleManager;
public RoleController(RoleManager<IdentityRole> roleManager)
{
    _roleManager = roleManager;
}
```

## 2.1 获取所有Identity的角色
RoleManager类Roles属性提供了Identity所有的角色，我们将所有的Roles作为模型类传递给Index视图，代码如下：
```csharp
public IActionResult Index()
{
    return View(_roleManager.Roles);
}
```

接下来我们在 View -> Role 文件夹下创建一个Index.cshtml文件
```cshtml
@using Microsoft.AspNetCore.Identity;
@model IEnumerable<IdentityRole>
@{
    ViewData["Title"] = "Roles";
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
            <table class="table-content-center table table-bordered">
                <thead>
                    <tr>
                        <th>编号</th>
                        <th>角色名称</th>
                        <td>用户</td>
                        <th>编辑</th>
                        <td>删除</td>
                    </tr>
                </thead>
                <tbody>
                    @foreach (var role in Model)
                    {
                        <tr>
                            <td>@role.Id</td>
                            <td>@role.Name</td>
                            <td i-role="@role.Id"></td>
                            <td>
                                <a class="btn btn-primary btn-sm" asp-action="Update" asp-route-id="@role.Id">编辑</a>
                            </td>
                            <td>
                                <form method="post" asp-action="Delete" asp-route-id="@role.Id" role="form">
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

这个视图中获取了一个 `IEnumerable<IdentityRole>` 类型集合，它将包含Identity所有Role，我们通过foreach循环将所有Role展示在table内，注意i-role我们使用了第三方Attribute，这个Attribute将调用客户自定义的TagHelper，这个特性会修改td并显示当前角色的用户列表

接下来在项目根目录一下创建一个文件夹CustomTagHelpers，在该文件夹下添加一个RoleUsersTH.cs类，并且该类继承TagHelper，这个类提供自定义的CustomerTagHelper
```csharp
/// <summary>
/// 自定义TagHelper
/// </summary>
[HtmlTargetElement("td", Attributes = "i-role")]
public class RoleUsersTH : TagHelper
{
    private UserManager<AppUser> _userManager;

    private RoleManager<IdentityRole> _roleManager;
    public RoleUsersTH(UserManager<AppUser> userManager, RoleManager<IdentityRole> roleManager)
    {
        _userManager = userManager;
        _roleManager = roleManager;
    }

    [HtmlAttributeName("i-role")]
    public string Role { get; set; } = null!;

    public override async Task ProcessAsync(TagHelperContext context, TagHelperOutput output)
    {
        List<string> names = new List<string>();
        var role = await _roleManager.FindByIdAsync(Role);
        if (role != null)
        {
            foreach (var user in _userManager.Users)
            {
                if (user != null && await _userManager.IsInRoleAsync(user, role.Name ?? ""))
                    names.Add(user.UserName ?? "");
            }
        }

        output.Content.SetContent(names.Count == 0 ? "No Users" : string.Join(", ", names));
    }
}
```

客户自定义的TagHelper操作td中i-role特性，这个特性获取对应的角色ID，并在后台被处理

我们需要更新_ViewImports.cshtml文件
![20240205140210](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140210.png)

## 2.2 ASP.NET Core Identity创建角色
我们使用RoleManager的CreateAsync方法创建一个Identity Role，在RoleController类中创建一个CreateAsync方法，代码如下：
```csharp
public IActionResult Create() => View();
[HttpPost]
public async Task<IActionResult> CreateAsync([Required] string name)
{
    if (ModelState.IsValid)
    {
        var result = await _roleManager.CreateAsync(new IdentityRole(name));
        if (result.Succeeded)
            return RedirectToAction("Index");
        else
            Errors(result);
    }
    return View(name);
}
```

CreateAsync方法入参是name(角色名称)的字符串参数并且使用RoleManager的CreateAsync()方法来创建一个Identity Role
```csharp
var result = await _roleManager.CreateAsync(new IdentityRole(name));
```

我们接下来在Views->Role目录下添加一个Create 视图，代码如下：
```cshtml
@model IdentityRole
@{
    ViewData["Title"] = "新增角色";
}
<div asp-validation-summary="All" class="text-danger"></div>
<form class="form-horizontal" method="post">
    <div class="mb-3 row">
        <div class="col-sm-1">
            <label for="Name" class="control-label">角色名称：</label>
        </div>
        <div class="col-sm-11">
            <input asp-for="Name" class="form-control" placeholder="请输入角色名称" />
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

## 2.3 ASP.NET Core Identity删除角色
我们使用RoleManager的`DeleteAsync()`来删除一个Identity Role， 在RoleController.cs的控制器中创建一个DeleteAsync 方法并接受一个角色id(需要删除的角色)，代码如下：
```csharp
public async Task<IActionResult> DeleteAsync(string id)
{
    var role = await _roleManager.FindByIdAsync(id);
    if (role != null)
    {
        var identityResult = await _roleManager.DeleteAsync(role);
        if (identityResult.Succeeded)
        {
            return RedirectToAction("Index");
        }
        else
        {
            Errors(identityResult);
        }
    }
    else
    {
        ModelState.AddModelError("", "No role found");
    }
    return View("Index", _roleManager.Roles);
}
```

## 2.4 测试Identity创建和删除角色功能
运行应用程序并将URL导航 https://localhost:7296/Role/Create 每次创建完一个角色就会跳转到Index View显示Identity数据库中所有的角色

创建role页面如下：
![20240205140222](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140222.png)

我们可以通过这个删除按钮删除Identity 数据库中的Role:
![20240205140229](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140229.png)

# 3.添加用户到Roles或从Roles中移除用户
现在我们创建一个新的功能，将完成两件事情：
- 添加用户到指定角色
- 将用户从角色中移除

为了实现这个功能，我们在Models文件加下添加两个类，分别为RoleEdit和RoleModification 
```csharp
public class RoleEdit
{
    public IdentityRole? Role { get; set; }
    public IEnumerable<AppUser>? Members { get; set; }
    public IEnumerable<AppUser>? NoMembers { get; set; }
}
```

RoleEdit表示一个角色和他关联的用户以及和该角色未关联的用户，RoleModification这个类将帮助我们修改一个角色，具体定义如下：
```csharp
public class RoleModification
{
    [Required]
    public string RoleName { get; set; } = null!;

    public string RoleId { get; set; } = null!;

    public string[]? AddIds { get; set; }

    public string[]? DeleteIds { get; set; }
}
```
这两个类帮助我们将一个用户添加到角色中和从角色中移除用户，我们修改一些RoleController类，添加UpdateAsync方法，下面Get版本的UpdateAsync方法查询两部分数据，属于该角色的用户和不属于该角色的用户
```csharp
public async Task<IActionResult> UpdateAsync(string id)
{
    var role = await _roleManager.FindByIdAsync(id);
    List<AppUser> members = new List<AppUser>();
    List<AppUser> nonMembers = new List<AppUser>();
    foreach (var appUser in _userManager.Users)
    {
        var list = await _userManager.IsInRoleAsync(appUser, role?.Name ?? "") ? members : nonMembers;
        list.Add(appUser);
    }
    return View(new RoleEdit() { Role = role, Members = members, NoMembers = nonMembers });
}
```

下面Post版本的UpdateAsync方法表示给用户添加和移除角色
```csharp
[HttpPost]
public async Task<IActionResult> UpdateAsync(RoleModification roleModification)
{
    if (ModelState.IsValid)
    {
        foreach (var userId in roleModification.AddIds ?? new string[] { })
        {
            var appUser = await _userManager.FindByIdAsync(userId);
            if (appUser != null)
            {
                var identityResult = await _userManager.AddToRoleAsync(appUser, roleModification.RoleName);
                if (!identityResult.Succeeded)
                    Errors(identityResult);
            }
        }
        foreach (var userId in roleModification.DeleteIds ?? new string[] { })
        {
            var appUser = await _userManager.FindByIdAsync(userId);
            if (appUser != null)
            {
                var identityResult = await _userManager.RemoveFromRoleAsync(appUser, roleModification.RoleName);
                if (!identityResult.Succeeded)
                    Errors(identityResult);
            }
        }
    }
    if (ModelState.IsValid)
        return RedirectToAction(nameof(Index));
    else
        return await UpdateAsync(roleModification.RoleId);
}
```

注意我们在构造函数中添加了UserManager依赖：
```csharp
private RoleManager<IdentityRole> _roleManager;
private UserManager<AppUser> _userManager;
public RoleController(RoleManager<IdentityRole> roleManager, UserManager<AppUser> userManager)
{
    _roleManager = roleManager;
    _userManager = userManager;
}
```

我们使用UserManager类的下面方法类管理ASP.NET Core Identity Roles

|名称|描述|
| ------------ | ------------ |
| AddToRoleAsync(AppUser user, string name) | 将用户添加到指定角色中 |
| RemoveFromRoleAsync(AppUser user, string name) | 从指定角色中删除用户 |
| GetRolesAsync(AppUser user) | 获取当前用户所有角色 |
| IsInRoleAsync(AppUser user, string name) | 判断一个用户是否是指定的角色成员如果是返回ture,否则false |

接下来，在Views -> Role 文件夹下添加Update.csthml，代码如下：
```cshtml
@model RoleEdit
@{
    ViewData["Title"] = "编辑角色";
}
<div asp-validation-summary="All" class="text-danger"></div>
<style>
    .table-column-width td {
        width: 200px
    }
</style>
<form class="form" method="post" role="form">
    <input type="hidden" name="roleName" value="@Model.Role?.Name" />
    <input type="hidden" name="roleId" value="@Model.Role?.Id" />
    <h2><small> @Model.Role.Name 角色包含的用户</small></h2>
    <table class="table-column-width table table-bordered">
        @if (!Model.Members!.Any())
        {
            <tr>
                <td>该角色没有关联任何用户</td>
            </tr>
        }
        else
        {
            foreach (var appUser in Model.Members ?? new List<AppUser>())
            {
                <tr>
                    <td>@appUser.UserName</td>
                    <td>
                        <input type="checkbox" name="DeleteIds" value="@appUser.Id" />
                    </td>
                </tr>
            }
        }
    </table>
    <h2><small> @Model.Role.Name 角色未包含的用户</small></h2>
    <table class="table-column-width table table-bordered">
        @if (!Model.NoMembers!.Any())
        {
            <tr>
                <td>该角色保护所有用户</td>
            </tr>
        }
        else
        {
            foreach (var appUser in Model.NoMembers ?? new List<AppUser>())
            {
                <tr>
                    <td>@appUser.UserName</td>
                    <td>
                        <input type="checkbox" name="AddIds" value="@appUser.Id" />
                    </td>
                </tr>
            }
        }
    </table>
    <button class="btn btn-primary">保存</button>
    <button asp-action="index" class="btn btn-secondary">返回</button>
</form>
```

这个页面包含两个Table:
- 当前角色包含的用户
- 当前角色未包含的用户

我们可以选择对应的 checkbox 给角色添加和删除用户

## 3.1 测试更新功能
我们先注册三个用户(密码为：Coder77@1):
![20240205140302](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140302.png)

同时我们也创建了三个角色：
![20240205140308](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140308.png)

我们查看当前每个角色都没有用户：
![20240205140317](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140317.png)
![20240205140324](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140324.png)
![20240205140332](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140332.png)
![20240205140338](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140338.png)

一个用户可以指定多个角色，使用这个功能仅仅完成了ASP.NET Core Identity 更新角色的功能，下面我们将实现基于角色的认证

# 4. 基于ASP.NET Core Identity Role 认证
ASP.NET Core Identity 角色可以作为认证，特定角色所包含的用户能访问特定的资源。例如：我们给指定方法设定一个`[Authorize(Roles = “SomeRole”)]`特性，这个角色下的所有用户都能访问这个方法

在HomeController的Index方法添加Authorize特性`[Authorize(Roles = "Manager")]` , 指明只有Manager角色的用户才能访问HomeController的Index 方法，代码如下：
```csharp
public class HomeController : Controller
{
    private readonly ILogger<HomeController> _logger;
    private UserManager<AppUser> _userManager;
    public HomeController(UserManager<AppUser> userManager, ILogger<HomeController> logger)
    {
        _userManager = userManager;
        _logger = logger;
    }
    [Authorize(Roles = "Manager")]
    public async Task<IActionResult> Index()
    {
        var appUser = await _userManager.GetUserAsync(HttpContext.User);
        var message = "Hello " + appUser?.UserName;
        return View((object)message);
    }
}
```

运行应用程序，使用tom登录，访问HomeController方法我们可以正常访问HomeController的Index方法

因为tom所拥有的角色是Manager。现在我们使用alice 用户进行登录，alice不属于Manager角色，所以当我们尝试访问Home/Index时，应用程序将会跳转到 https://localhost:7296/Account/AccessDenied?ReturnUrl=%2F ，因为AccountController控制器中没有创建AccessDenied方法，因此会获取一个HTTP 404 错误，因此我们在ActionController下创建一个AccessDenied方法：
```csharp
public IActionResult AccessDenied()
{
    return View();
}
```

在 Views -> Account 目录下添加 AccessDenied.cshtml 视图，代码如下：
```cshtml
<h2>Access Denied</h2>
<a asp-controller="Account" asp-action="Logout" class="btn btn-primary">退出登录</a>
```

现在，运行应用程序，并进入登录页面 https://localhost:7296/Account/Login

使用 alice账户登录:
- 邮箱 – alice@yahoo.com
- 密码 – Coder77@1

登录之后将被调转到Denied URL, 如下图所示:
![20240205140410](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140410.png)

Identity为我们提供设置了一个默认的/Account/AccessDenied URL地址，我们可以通过配置改变这个地址：
```csharp
builder.Services.ConfigureApplicationCookie(
	opts =>
	{
		//默认登录页面
		opts.LoginPath = "/Account/Login";
		opts.AccessDeniedPath= "/Account/AccessDenied";
		//设置Cookie名称
		opts.Cookie.Name = ".AspNetCore.Identity.Application";
		//设置Cookie超时时间
		opts.ExpireTimeSpan = TimeSpan.FromMinutes(20);
		//设置滑动时间
		opts.SlidingExpiration = true;
	}
);
```

# 5. 总结
这节我们主要讲解了Identity角色管理和身份认证

源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/AspNetCore.Identity/Identity

