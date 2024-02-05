---
layout: post
title: "转：ASP.NET Core Identity 系列之七：如何使用Claim"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486208&idx=1&sn=846fd71c89106c6c8779d48987156e08

在这节中我们主要介绍在ASP.NET Core Identity如何使用Claims，ASP.NET Core Identity Claims是颁发给用户键值对(name=value)表示用户允许做什么。例如，驾驶执照颁发机构给个人颁发驾驶执照，如果驾照上的DOB是1990年12月21日，那么在这种情况下，claim name 是DOB，claim value是1990年12月21日，签发人是驾驶执照当局。这意味着用户是由驾驶执照当局授权驾驶汽车

在前面的Identity例子可以看到认证用户是基于用户邮箱和密码，我们也可以授权一个用户访问特定资源。注意Identity的Role是Claims，但不是所有Claims都是Role，ASP.NET Core Identity 通过使用claims 做授权和认证，这节我们将覆盖更详细细节。Identity Claims 授权，最简单的方法是检查claim value，并允许基于该值访问资源

创建一个ClaimsController控制器，修改Index方法返回 User.Claims，代码如下：
```csharp
public class ClaimsController : Controller
{
    public ClaimsController()
    {
    }
    public IActionResult Index()
    {
        return View(User.Claims);
    }
}
```

在ASP.NET Identity中如何获取用户的Claims呢? 我们可以通过HttpContext对象的User属性，它返回当前用户的ClaimsPrincipal对象，该对象包含用户的所有claims，我们将该对象通过View展示到浏览器，创建一个Index.cshtml在 Views->Claims 文件夹下，代码如下:
```cshtml
@model IEnumerable<System.Security.Claims.Claim>
@{
    ViewData["Title"] = "Claims";
}
<div class="container">
    <div class="row mb-3">
        <div class="col-sm">
            <table class="table table-bordered align-middle">
                <thead>
                    <tr>
                        <th>名称</th>
                        <th>颁发机构</th>
                        <th>类型</th>
                        <th>值</th>
                    </tr>
                </thead>
                <tbody>
                    @foreach (var claim in Model.OrderBy(x => x.Type))
                    {
                        <tr>
                            <td>@claim?.Subject?.Name</td>
                            <td>@claim?.Issuer</td>
                            <td>@claim?.Type</td>
                            <td>@claim?.Value</td>
                        </tr>
                    }
                </tbody>
            </table>
        </div>
    </div>
</div>
```

我们运行应用程序使用并使用如下邮箱和密码进行登陆(如果没有可以自行添加)
- 邮箱：tom@yahoo.com
- 密码：Coder77@
- 导航到 https://localhost:7296/Claims

![20240205140623](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140623.png)

# 1. 创建和删除Claims
对一个用户创建和删除Identity Claims，首先创建一个新的Create.cshtml，在 Views -> Claims 目录下，代码如下：
```cshtml
@{
    ViewData["Title"] = "新增Claim";
}
<div asp-validation-summary="All" class="text-danger"></div>
<form method="post">
    <div class="mb-3 row align-items-center">
        <div class="col-sm-1">
            <label for="ClaimType" class="control-label">Claim 类型:</label>
        </div>
        <div class="col-sm-11">
            <input name="ClaimType" class="form-control" />
        </div>
    </div>
    <div class="mb-3 row align-items-center">
        <div class="col-sm-1">
            <label for="ClaimValue">Claim 值:</label>
        </div>
        <div class="col-sm-11">
            <input name="ClaimValue" class="form-control" />
        </div>
    </div>
    <div class="mb-3 row align-items-center">
        <div class="col-sm-11 offset-sm-1">
            <button type="submit" class="btn btn-primary">保存</button>
            <button asp-action="Index" class="btn btn-secondary">
                返回
            </button>
        </div>
    </div>
</form>
```

接下来，更新Index.cshtml并添加新增按钮和删除按钮
```cshtml
@model IEnumerable<System.Security.Claims.Claim>
@{
    ViewData["Title"] = "Claims";
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
                        <th>名称</th>
                        <th>颁发机构</th>
                        <th>类型</th>
                        <th>值</th>
                        <td>删除</td>
                    </tr>
                </thead>
                <tbody>
                    @foreach (var claim in Model.OrderBy(x => x.Type))
                    {
                        <tr>
                            <td>@claim?.Subject?.Name</td>
                            <td>@claim?.Issuer</td>
                            <td>@claim?.Type</td>
                            <td>@claim?.Value</td>
                            <td>
                                <form asp-action="Delete" method="post">
                                    <input type="hidden" name="claimValues" value="@claim?.Type;@claim?.Value;@claim?.Issuer" />
                                    <button type="submit" class="btn btn-sm btn-danger">
                                        删除
                                    </button>
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

最后我们在ClaimsController添加新增和删除方法，与此同时我们将`UserManager<T>`类通过构造函数依赖注入进去
```csharp
public class ClaimsController : Controller
{
    private UserManager<AppUser> _userManager;
    public ClaimsController(UserManager<AppUser> userManager)
    {
        _userManager = userManager;
    }
    public IActionResult Index()
    {
        return View(User.Claims);
    }
    public IActionResult Create() => View();
    [HttpPost]
    public async Task<IActionResult> Create(string claimType, string claimValue)
    {
        var user = await _userManager.GetUserAsync(HttpContext.User);
        Claim claim = new Claim(claimType, claimValue, ClaimValueTypes.String);
        IdentityResult result = await _userManager.AddClaimAsync(user ?? new AppUser(), claim);
        if (result.Succeeded)
            return RedirectToAction("Index");
        else
            Errors(result);
        return View();
    }
    [HttpPost]
    public async Task<IActionResult> Delete(string claimValues)
    {
        var user = await _userManager.GetUserAsync(HttpContext.User);
        string[] claimValuesArray = claimValues.Split(";");
        string claimType = claimValuesArray[0], claimValue = claimValuesArray[1], claimIssuer = claimValuesArray[2];
        Claim? claim = User.Claims.Where(x => x.Type == claimType && x.Value == claimValue && x.Issuer == claimIssuer).FirstOrDefault();
        IdentityResult result = await _userManager.RemoveClaimAsync(user ?? new AppUser(), claim);
        if (result.Succeeded)
            return RedirectToAction("Index");
        else 
            Errors(result);
        return View("Index");
    }
    void Errors(IdentityResult result)
    {
        foreach (IdentityError error in result.Errors)
            ModelState.AddModelError("", error.Description);
    }
}
```

## 1.1 新增方法
首先我们从`_userManager.GetUserAsync()`获取当前用户，接着我们添加Claim对象，最后我们将Claim对象添加到用户中。`_userManager.AddClaimAsync()`可以为用户创建claim

## 1.2 删除方法
删除方法获取Claim值，切割字符串之后我们获取到了claim type, claim value and claim issuer 对应的值，使用Linq获取选择的Claim
```csharp
Claim claim = User.Claims.Where(x => x.Type == claimType && x.Value == claimValue && x.Issuer == claimIssuer).FirstOrDefault();
```
通过调用`_userManager.RemoveClaimAsync()`将用户Claim删除

# 2. 测试
测试这个特性，运行应用程序并且使用tom用户进行登录，进入https://localhost:7296/Claims 页面，点击新增按钮：
![20240205140643](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140643.png)

我们在新增页面输入下面值：
![20240205140649](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140649.png)

每次创建完一个Claim，我们都需要重新登录，为了看到新的Claim我们必须重新登录一次：
![20240205140656](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140656.png)

现在我们通过点击删除按钮删除刚才创建的claim。每次删除完成之后我们需要重新登录一下，才能看到最新的数据状态
![20240205140703](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140703.png)

# 3. 总结
这一节我们主要介绍什么是Claims，针对当前用户如何创建和删除Claims，以及在表格内显示用户当前登录用户所有Claims。下一节我们将介绍如何使用Claims创建相应的策略进行用户认证.

源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/AspNetCore.Identity/Identity

