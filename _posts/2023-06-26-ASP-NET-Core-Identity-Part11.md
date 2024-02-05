---
layout: post
title: "转：ASP.NET Core Identity 系列之十一：重置密码功能"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486238&idx=1&sn=32fae2f0fa6c50982e160c892c1314bc

这节我们主要介绍重置密码功能，当一个用户忘记自己密码时需要选择重置密码，使用ASP.NET Core Identity进行重置密码是非常方便的

# 1. ASP.NET Core Identity 重置密码
接下来我们看一下Identity中是如何重置密码，当一个用户忘记密码时，我们将他带入进入忘记密码页面，如下图所示：
![20240205143103](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143103.png)

这个页面会向用户指定的邮箱发送一个重置密码的URL，当用户点击这个URL时，会携带上相应的token和Email链接到重置密码页面，下图为重置密码页面:
![20240205143114](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143114.png)

# 2. 在Identity中启用Token生成功能
在应用程序中添加 `AddDefaultTokenProviders` 扩展方法启用token生成功能。下面代码：
```csharp
builder.Services.AddIdentity<AppUser,IdentityRole>().
AddEntityFrameworkStores<AppIdentityDbContext>().
AddDefaultTokenProviders();
```

重置密码时每次发送邮件Identity会生成一个token，当用户点击邮件中的链接地址时，Identity会校验该Token并且确保用户重置密码之前Token是可用的并且没有过期，这是一个很好的特性，我们通过添加下面代码来设置Token的有效时间：
```csharp
//dot net 6
builder.Services.Configure<DataProtectionTokenProviderOptions>(opts => opts.TokenLifespan = TimeSpan.FromHours(10));
```

在这我们设置Token的有效时间是10，这意味着用户在10小时内可以修改密码

# 3. 创建一个忘记密码功能
在 `AccountController` 中添加一个新的方法叫 `ForgotPassword` 。这个方法主要用来给用户指定的邮箱发送邮件，用户可以使用这个链接重置密码。添加另外一个 `ForgotPasswordConfirmation` 方法提示用户邮件已经发送成功
```csharp
[AllowAnonymous]
 public IActionResult ForgotPassword()
 {
     return View();
 }
 [HttpPost]
 [AllowAnonymous]
 public async Task<IActionResult> ForgotPassword([Required] string email)
 {
     if (!ModelState.IsValid)
     {
         return View(email);
     }
     var appUser = await _userManager.FindByEmailAsync(email);
     if (appUser == null)
     {
         return RedirectToAction(nameof(ForgotPasswordConfirmation));
     }
     var token = await _userManager.GeneratePasswordResetTokenAsync(appUser);
     var url = Url.Action("ResetPassword", "Account", new { Email = email, Token = token }, Request.Scheme);
     _emailService.Send(appUser!.Email ?? "450190369@qq.com", "重置密码", $"{url}");
     return RedirectToAction("ForgotPasswordConfirmation");
 }
 [AllowAnonymous]
 public IActionResult ForgotPasswordConfirmation()
 {
     return View();
 }
```

下面4行代码比较重要：
```csharp
var appUser = await _userManager.FindByEmailAsync(email);
if (appUser == null)
{
     return RedirectToAction(nameof(ForgotPasswordConfirmation));
}
var token = await _userManager.GeneratePasswordResetTokenAsync(appUser);
var url = Url.Action("ResetPassword", "Account", new { Email = email, Token = token }, Request.Scheme);
_emailService.Send(appUser!.Email ?? "450190369@qq.com", "重置密码", $"{url}");
```

我们使用 `UserManager<T>` 类的 `GeneratePasswordResetTokenAsync` 方法创建了一个重置密码的token，我们将这个token添加到链接地址中并且将这个链接地址发送给用户，用户使用这个链接来重置密码
在 Views -> Account 中添加如下2个View文件，ForgotPassword.cshtml & ForgotPasswordConfirmation.cshtml , 代码如下:           

ForgotPassword.cshtml
```cshtml
@{
    ViewData["Title"] = "忘记密码";
}
<div class="container">
    <form asp-action="ForgotPassword" method="post">
        <div class="mb-3 row align-items-center">
            <div class="col-sm-1">
                <label class="control-label">电子邮箱</label>
            </div>
            <div class="col-sm-11">
                <input name="email" class="form-control" />
            </div>
        </div>
        <div class="mb-3 row align-items-center">
            <div class="col-sm-11 offset-sm-1">
                <button class="btn btn-primary" type="submit">发送邮件</button>
            </div>
        </div>
    </form>
</div>
```

ForgotPasswordConfirmation.cshtml
```cshtml
@{
    ViewData["Title"] = "忘记密码确认";
}

<h1>忘记密码确认</h1>
<p>
    邮件已经发送，请检查邮件并重置密码
</p>
```

# 4. 测试忘记密码功能
运行应用程序进入忘记密码URL并且输入邮件地址，点击发送邮件。下图：
![20240205143457](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143457.png)

发送完邮件之后会跳转到如下页面：
![20240205143509](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143509.png)

下面我们打开邮箱查看一下刚才发的邮件：
![20240205143520](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143520.png)

# 5. 修改密码
当打开这个链接时，这个链接将用户重定向 `AccountController` 的 `ResetPassword` 方法，因此我们需要添加 `ResetPassword` 方法和 `ResetPasswordConfirmation` 方法(修改完密码之后跳转的页面)，代码如下：
```csharp
[AllowAnonymous]
public IActionResult ResetPassword(string token, string email)
{
    var resetPassword = new ResetPassword() { Token = token, Email = email };
    return View(resetPassword);
}

[HttpPost]
[AllowAnonymous]
public async Task<IActionResult> ResetPassword(ResetPassword resetPassword)
{
    if (!ModelState.IsValid)
        return View(resetPassword);
    var appUser = await _userManager.FindByEmailAsync(resetPassword.Email);
    if (appUser == null)
        RedirectToAction("ResetPasswordConfirmation");

    var resetPassResult = await _userManager.ResetPasswordAsync(appUser, resetPassword.Token, resetPassword.Password);

    if (!resetPassResult.Succeeded)
    {
        foreach (var error in resetPassResult.Errors)
            ModelState.AddModelError(error.Code, error.Description);
        return View();
    }
    return RedirectToAction("ResetPasswordConfirmation");
}
```

当用户打开邮件中的地址时，将调用Get版本的 `ResetPassword` 方法，这个方法主要作用是拿到token和Email并且将这两个参数对应的数据绑定到View。接下来用户输入新密码和确认密码在视图中 (i.e. ResetPassword.cshtml) 并点击按钮保存。这将调用Post版本的 `ResetPassword` 方法，这个方法通过模型绑定来获取新密码和用户邮件以及token。代码如下：
![20240205143639](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143639.png)

下面，我们需要创建一个ResetPassword.cs类在Models 文件
```csharp
public class ResetPassword
{
    [Required]
    [DisplayName("密码")]
    public string Password { get; set; } = null!;

    [Compare("Password", ErrorMessage = "密码和确认密码不匹配")]
    [DisplayName("确认密码")]
    public string ConfirmPassword { get; set; } = null!;

    public string Email { get; set; } = null!;
    public string Token { get; set; } = null!;
}
```

![20240205143729](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143729.png)

我们在内 Views -> Account 文件夹中创建2个视图 ResetPassword & ResetPasswordConfirmation 使用如下代码:
ResetPassword.cshtml
```cshtml
@model ResetPassword
@{
    ViewData["Title"] = "重置密码";
}
<div class="text-danger" asp-validation-summary="All"></div>

<form asp-action="ResetPassword" method="post">
    <div class="mb-3 row align-items-center">
        <div class="col-sm-1">
            <label asp-for="Password"></label>
        </div>
        <div class="col-sm-11">
            <input asp-for="Password" class="form-control" />
        </div>
    </div>
    <div class="mb-3 row align-items-center">
        <div class="col-sm-1">
            <label asp-for="ConfirmPassword"></label>
        </div>
        <div class="col-sm-11">
            <input asp-for="ConfirmPassword" class="form-control" />
        </div>
    </div>
    <input type="hidden" asp-for="Email" class="form-control" />
    <input type="hidden" asp-for="Token" class="form-control" />
    <div class="mb-3 row align-items-center">
        <div class="col-sm-11 offset-sm-1">
            <button class="btn btn-primary" type="submit">提交</button>
        </div>
    </div>
</form>
```

ResetPasswordConfirmation.cshtml
```cshtml
@{
    ViewData["Title"] = "重置密码确认";
}

<h1>重置密码确认</h1>

<p>
    密码已经被重置. 请 <a asp-action="Login">登录</a>
</p>
```

ResetPassword视图是用户修改密码
ResetPasswordConfirmation 视图提示用户密码已经修改成功

# 6. 测试修改密码特性
首先我们进入忘记密码页面，(url – https://localhost:7296/Account/ForgotPassword) 输入邮件。点击发送邮件，会向我们指定的邮箱发送电子邮件，将邮箱中收到的url输入到浏览器中。如下图所示：
![20240205143854](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143854.png)

注意Token和Email添加到了url查询字符串中，这两个值通过ResetPassword Get 版本的方法传递到了View，现在可以修改我们的密码了，修改完之后会跳转到如下页面：
![20240205143905](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205143905.png)

# 7. 总结
这节我们主要实现了实现忘记密码功能，注册token并且设置token的声明周期，实现重置密码功能

源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/AspNetCore.Identity/Identity
