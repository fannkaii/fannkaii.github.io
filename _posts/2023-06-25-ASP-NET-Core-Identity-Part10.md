---
layout: post
title: "转：ASP.NET Core Identity 系列之十：如何执行用户的电子邮件确认"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486233&idx=1&sn=70654867aaecbcf62c8b918a0fe7253b

这节中我们主要讲解在ASP.NET Core Identity中如何启用电子邮件确认，ASP.NET Core Identity邮件确认是为了验证注册的用户是真人，而不是机器人，意味着注册的用户实际上是他们提供的电子邮件的所有者，Identity提供了在用户注册过程中启用电子邮件确认的选项

# 1. 启用电子邮件确认
通过设置IdentityOptions的属性 `RequireConfirmedEmail` 为 `true` 来启用电子邮件确认
![20240205142457](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142457.png)

在Identity 数据库中有一张AspNetUsers的表，这张表里有EmailConfirmed 列，这一列来跟踪一个用户的Email是否确认
如果已经确认，该值为true，否则为false。下面显示我们所有注册用户的EmailConfirmed 为false
![20240205142510](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142510.png)

# 2. 实现电子邮件确认
在用户注册过程中添加邮件确认，现在我们修改一下 `AdminController` 中的 `Create` 方法 ，使其包含邮件确认功能
![20240205142548](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142548.png)


`UserManager` 类的 `GenerateEmailConfirmationTokenAsync()` 方法为当前注册的用户生成一个email确认的token，接下来我们生成确认的url使用如下代码：
```csharp
var confirmationLink = Url.Action("ConfirmEmail", "Email", new { token, email = user.Email }, Request.Scheme).
```

链接地址将被发送到用户的邮箱，我们可以看到链接指向的是当前 `Controller` 类的 `ConfirmEmail` 方法，在查询字符串中添加了token和email参数，`ConfirmEmail` 方法使用ASP.NET Core模型绑定能获取该值

# 3. 指定邮件确认和验证
当用户点击他的邮件确认地址时，ASP.NET Core Identity 会验证用户的邮件，因此我们需要创建一个ConfirmEmail来确认用户的邮件地址， 当用户点击验证链接时，这个方法将被调用
```csharp
public class EmailController : Controller
{
    private UserManager<AppUser> _userManager;
    public EmailController(UserManager<AppUser> userManager)
    {
        _userManager = userManager;
    }

    public IActionResult Index()
    {
        return View();
    }
    public async Task<IActionResult> ConfirmEmail(string token, string email)
    {
        var user = await _userManager.FindByEmailAsync(email);
        if (user == null)
        {
            return View("Error");
        }
        var result = await _userManager.ConfirmEmailAsync(user, token);
        return View(result.Succeeded ? "ConfirmEmail" : "Error");
    }
}
```

我们在 Views -> Email 文件夹下为这个 Controller 添加两个 View：
ConfirmEmail.cshtml
![20240205142825](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142825.png)

Error.cshtml
![20240205142836](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142836.png)

# 4. 测试邮件确认
然我们创建一个用户
![20240205142852](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142852.png)

下面，我们检查一下邮箱：
![20240205142904](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142904.png)

我们打开这个邮件地址，会看到跳转到如下图：
![20240205142915](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142915.png)

我们在数据库中查看一下AspNetUsers表，我们发现EmailConfirmed列的值
![20240205142928](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142928.png)

# 5. 修改登录方法
用户在没有确认Email连接的情况下是不能登录应用程序，如果登录了我们需要给出提示
`UserManager` 有一个方法 `IsEmailConfirmedAsync()` 是判断是否邮件已经确认。在用户登录期间我们通过这个方法提供信息给用户

```csharp
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(Login login)
{
    if (ModelState.IsValid)
    {
        var appUser = await _userManager.FindByEmailAsync(login.Email);
        if (appUser != null)
        {
            await _signInManager.SignOutAsync();
            var signInResult = await _signInManager.PasswordSignInAsync(appUser, login.Password,
                login.RememberMe, false);
            if (signInResult.Succeeded)
            {
                return Redirect(login.ReturnUrl ?? "/");
            }
            var emailStatus=await _userManager.IsEmailConfirmedAsync(appUser);
            if(emailStatus==false)
            {
                ModelState.AddModelError(nameof(login.Email),"Email为确认，请首先确认!");
            }
            #region 启用2FA登录
            //if (appUser.TwoFactorEnabled)
            //{
            //    return RedirectToAction("LoginTwoStep", new { Email = appUser.Email, ReturnUrl = login.ReturnUrl });
            //}
            #endregion
        }
        ModelState.AddModelError(nameof(login.Email), "Login Failed: Invalid Email or password");
    }
    return View(login);
}
```

# 6. 总结
在这节中我们主要讲解在ASP.NET Core Identity中电子邮件确认，下一篇我们将讲解在ASP.NET Core Identity中如何重置密码

源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/AspNetCore.Identity/Identity

