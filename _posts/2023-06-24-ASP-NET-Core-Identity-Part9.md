---
layout: post
title: "转：ASP.NET Core Identity 系列之九：2FA 认证"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486232&idx=1&sn=05688acdeb382499c36a7ce054da2565

这一节中我们主要使用ASP.NET Core Identity 2FA(双因子验证)，双重因子认证是一个用户认证过程，当用户在登录页面输入账户和密码验证成功之后，用户会通过邮件和SMS收到一个一次性密码OTP(one-time-password)。用户在第二步登录验证中需要输入这个一次性密码OTP，这个过程极增加了应用程序的安全性

# 1. 在Identity启用双因子验证
![20240205141836](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205141836.png)

相同的方式，我们也能更新一个Identity 用户并且设置它的TwoFactorEnabled为true，代码如下：
![20240205141851](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205141851.png)

启用双因子验证时，我们需要将EmailComfirmed设置为Ture
![20240205141905](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205141905.png)

# 2. 实现Identity双因子验证
为了实现ASP.NET Core Identity双因子验证我们需要修改一下AccountController内Login方法，代码如下所示：
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
            if (appUser.TwoFactorEnabled)
            {
                return RedirectToAction("LoginTwoStep", new { Email = appUser.Email, ReturnUrl = login.ReturnUrl });
            }
        }
        ModelState.AddModelError(nameof(login.Email), "Login Failed: Invalid Email or password");
    }
    return View(login);
}
```

我们可以看到在登录过程中我们查看用户是否启用双重验证，如果是则会跳转到LoginTwoStep，我们看一下LoginTwoStep是如何工作的，在AccountController中添加LoginTwoStep方法，代码如下：
```csharp
[AllowAnonymous]
public async Task<IActionResult> LoginTwoStep(string email, string returnUrl)
{
    var appUser = await _userManager.FindByEmailAsync(email);
    //创建Token
    var token = await _userManager.GenerateTwoFactorTokenAsync(appUser ?? new AppUser(), "Email");
    //发送邮件
    _emailService.Send(appUser?.Email ?? "450190369@qq.com", "授权码", $"<h2>{token}</h2>");
    //发送SMS
    //_smsService.Send(appUser?.PhoneNumber ?? "13333333333", token);
    return View("LoginTwoStep", new TwoFactor { ReturnUrl = returnUrl });
}
[HttpPost]
[AllowAnonymous]
public async Task<IActionResult> LoginTwoStep(TwoFactor twoFactor, string returnUrl)
{
    if (!ModelState.IsValid)
    {
        return View("LoginTwoStep", new TwoFactor { TwoFactorCode = twoFactor.TwoFactorCode, ReturnUrl = returnUrl });
    }
    var result = await _signInManager.TwoFactorSignInAsync("Email", twoFactor.TwoFactorCode, false, false);
    if (result.Succeeded)
    {
        return Redirect(returnUrl ?? "/");
    }
    else
    {
        ModelState.AddModelError("", "登录失败");
        return View();
    }
}
```

注意：使用 `UserManager<T>` 类 `GenerateTwoFactorTokenAsync()` 方法来创建token，接着我们将token发送给到用户邮箱，
我们使用`EmailService`类在位于根目录下`CommonService`文件夹。这个类的主要工作是发送token到用户注册的邮箱地址，该类的代码如下：
```csharp
public class EmailSetting
{
    public string EmailFrom { get; set; } = null!;
    public string EmailTo { get; set; } = null!;
    public string SmtpHost { get; set; } = null!;
    public int SmtpPort { get; set; }
    public string SmtpUser { get; set; } = null!;
    public string SmtpPass { get; set; } = null!;
}
public interface IEmailService
{
    void Send(string to, string subject, string html, string from = null);
}
public class EmailService : IEmailService
{
    private readonly EmailSetting _appSettings;

    public EmailService(IOptions<EmailSetting> options)
    {
        _appSettings = options.Value;
    }
    public void Send(string to, string subject, string html, string from = null)
    {
        //Create Message
        var email = new MimeMessage();
        email.From.Add(MailboxAddress.Parse(_appSettings.EmailFrom));
        email.To.Add(MailboxAddress.Parse(to));
        email.Subject = subject;
        email.Body = new TextPart(TextFormat.Html) { Text = html };

        //Send Mail

        using var smtp = new SmtpClient();
        smtp.Connect(_appSettings.SmtpHost, _appSettings.SmtpPort, MailKit.Security.SecureSocketOptions.StartTls);
        smtp.Authenticate(_appSettings.SmtpUser, _appSettings.SmtpPass);
        smtp.Send(email);
        smtp.Disconnect(true);
    }
}
```

最后，`TwoFactorSignInAsync` 会验证用户的token，`TwoFactor`类如下：
```csharp
public class TwoFactor
{
    [Required]
    [DisplayName("授权码")]
    public string TwoFactorCode { get; set; } = null!;
    public string? ReturnUrl { get; set; }
}
```

我们在 Views -> Account 新建一个 LoginTwoStep.cshtml 视图
代码如下：
```cshtml
@model TwoFactor
@{
    ViewData["Title"] = "输入授权码";
}
<div class="container">
    <form asp-action="LoginTwoStep" method="post">
        <div class="mb-3 row align-items-center">
            <div class="col-sm-1">
                <label asp-for="TwoFactorCode" class="control-label"></label>
                <input type="hidden" name="returnUrl" value="@Model.ReturnUrl" />
            </div>
            <div class="col-sm-11">
                <input asp-for="TwoFactorCode" class="form-control" value="@Model.TwoFactorCode" />
            </div>
        </div>
        <div class="mb-3 row align-items-center">
            <div class="col-sm-11 offset-sm-1">
                <button class="btn btn-primary" type="submit">登录</button>
            </div>
        </div>
    </form>
</div>
```

# 3. 测试整个功能
在登录页面我们输入有效的登录凭证，我们看到一个新的页面要求我们输入认证编码。如下图所示
![20240205142327](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142327.png)

进入邮箱找到认证码，我们将认证码输入进去，将会跳转到主页或者别的页面，这依赖于你访问的资源是否有权限
![20240205142336](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142336.png)

到此，这节我们学习了如何通过用户提供的邮件使用Identity来完成双重认证。但是，除了邮件我们还可以使用SMS,SMS也可以用作这个处理过程，相同的逻辑处理使用SMS提供程序，我们只需要修改一部分email的发送部分，如下代码：
![20240205142349](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205142349.png)

# 4. 总结
这节我们主要介绍了用户使用邮件进行双因子验证，同样方式我们可以不用修改任何逻辑进行SMS验证

源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/AspNetCore.Identity/Identity
