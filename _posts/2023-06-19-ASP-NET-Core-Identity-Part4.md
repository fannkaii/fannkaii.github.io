---
layout: post
title: "转：ASP.NET Core Identity 系列之四：身份认证"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486183&idx=1&sn=baeb28f24399a9b0203f33185e1399a6

这节我们主要介绍ASP.NET Core Identity认证，认证是决定用户是否能够成功登录应用程序的一个过程，用户通过提供自己的用户名和密码来证明他们自己是真实用户，当登录成功时, Identity 认证用户并且赋予访问ASP.NET Core 应用程序安全资源

# 1. ASP.NET Core Identity如何认证
我们创建ASP.NET Core Identity Authentication特性，允许对Identity User 进行认证，首先我们创建一个HomeController并修改Index Action 返回一个字符串到View
![20240205135920](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135920.png)

当我们运行应用程序时，我们可以没有任何限制的访问Index action，浏览器发送匿名请求(anonymous request) 到Home Controller 的Index 方法，这个匿名请求能访问应用程序Home Controller资源，因此我们能在浏览器上看到Hello消息

现在我们给Index Action增加认证，注意 Identity Authorization 和 Authentication 不同，Authorization 在 Authentication 之后，用户授权是指 Identity 授权一个用户访问应用程序资源

提交一个Identity authentication 非常容易，我们仅仅需要在Index 方法上提交一个 `[Authorize]` 特性，该特性位于在Microsoft.AspNetCore.Authorization命名空间，这将限制用户访问

现在我们运行app 将显示如下图
![20240205135933](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135933.png)

如下两点我们注意一下：
- 在Index 方法上使用`[Authorize]`特性，用户访问Controller之前需要做认证
- ASP.NET Core Identity 导航到 `AccountController` 的 `Login` 方法，这个是Identity默认登录页面，https://localhost:7296/Account/Login?ReturnUrl=%2Fhome%2Findex 这页面可以登录应用程序，我们稍后将构建这个页面

注意URL包含查询字符串变量叫ReturnUrl，包含%2F ，%2F是一个编码的字符，代表 / ，因此认证之后，用户将跳转到应用程序的主页面，在认证之前ReturnUrl包含一个用户访问的安全页面，一旦Identity认证成功将会跳转到ReturnUrl指定的安全页面

# 2. 修改Identity 中默认登录的URL
ASP.NET Core Identity 默认的登录地址是/Account/Login，可以通过如下代码改变默认的登录URL：
```csharp
builder.Services.ConfigureApplicationCookie(opts => opts.LoginPath = "/Authenticate/Login");
```

我们在这里指定了新的登录地址/Authenticate/Login 例如: https://localhost:7296/Authenticate/Login

# 3. ASP.NET Core Identity 登录页面
为了实现Identity认证，我们将创建两个页面登录页面和退出，在登录页面中用户将输入用户名和密码进行认证，当用户退出时，用户点击logout按钮将退出登录

## 3.1 ASP.NET Core Identity 登录页面
在Models文件夹下创建Login文件，实体类中包含了三个属性，分别为Email、Password、ReturnUrl
```csharp
public class Login
{
       [Required]
       [DisplayName("用户名")]
       public string Email { get; set; } = null!;
       [Required]
       [DisplayName("密码")]
       [DataType(DataType.Password)]
       public string Password { get; set; } = null!;
       public string? ReturnUrl { get; set; }
}
```

Email和Password是必填字段，用户在登录过程中必须输入这两个字段，Identity 默认登录地址是https://localhost:7296/Account/Login ，因此我们需要添加一个Account 控制器，并在该控制器中添加Login 方法
```csharp
[Authorize]
public class AccountController : Controller
{
	private UserManager<AppUser> _userManager;
	private SignInManager<AppUser> _signInManager;
	public AccountController(UserManager<AppUser> userManager, SignInManager<AppUser> signInManager)
	{
		_userManager = userManager;
		_signInManager = signInManager;
	}

	[AllowAnonymous]
	public IActionResult Login(string returnUrl)
	{
		var login = new Login();
		login.ReturnUrl = returnUrl;
		return View(login);
	}
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
				var signInResult = await _signInManager.PasswordSignInAsync(appUser, login.Password, false, false);
				if (signInResult.Succeeded)
				{
					return Redirect(login.ReturnUrl ?? "/");
				}
			}
		}
		return View(login);
	}
}
```

我们在Controller上应用了 `[Authorize]` 特性，从而阻止匿名请求调用，在Login方法上添加了 `[AllowAnonymous]` 特性，允许匿名请求访问这个方法，login 方法必须允许匿名访问，否则我们永远无法登录，在 `AccountController` 构造函数中添加了 `UserManager<AppUser>` 和 `SignInManager<AppUser>` 引用，`UserManager<T>` 主要负责管理Identity的用户，`SignInManager<T>` 主要负责Identity的认证过程

```csharp
private UserManager<AppUser> _userManager;
private SignInManager<AppUser> _signInManager;
public AccountController(UserManager<AppUser> userManager, SignInManager<AppUser> signInManager)
{
     _userManager = userManager;
     _signInManager = signInManager;
}
```

下面我们添加一个Http Get版本的Login方法，在该方法上使用 `[AllowAnonymous]` 特性，可以匿名访问该方法，否则用户不能登录，这个action方法接受一个returnUrl的参数，该参数提供查询字符串，背后的原理：模型绑定将自动绑定该URL变量
```csharp
[AllowAnonymous]
public IActionResult Login(string returnUrl)
{
    var login = new Login();
    login.ReturnUrl = returnUrl;
    return View(login);
}
```

下面我们添加一个Post版本的Login 方法，实际上认证用户的逻辑将在该方法中，代码如下:
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
            var signInResult = await _signInManager.PasswordSignInAsync(appUser, login.Password, false, false);
            if (signInResult.Succeeded)
            {
                return Redirect(login.ReturnUrl ?? "/");
            }
        }
    }
    return View(login);
}
```

这个方法接受Login类型的参数，可以从用户登录表单中获取email和password的值，这个方法提供了两个特性：
- `[AllowAnonymous]` 表示允许匿名用户访问
- `[ValidateAntiForgeryToken]` 阻止CSRF攻击

首先我们根据 `UserManager` 的 `FindByEmailAsync()` 方法获取到用户信息，如果用户信息不为空，我们先退出登录，然后再调用 `PasswordSignInAsync` 进行登录，这个方法第三个参数和第四个参数分别设置成false，第三个参数表示我们不想持久化Cookie，第四个参数表示用户登录失败时是否锁定用户，该方法返回 `SignInResult` 登录结果对象，如果成功Succeeded返回true，否则返回false，登录成功之后，跳转到客户指定的URL

我们需要创建一个Login View的登录表单，这个视图是Identity登录页面，处理用户账户登录过程，我们在 Views/Account 下创建View页面，代码如下：
```cshtml
@model Login
@{
    ViewData["Title"] = "Login";
}
<div class="text-danger" asp-validation-summary="All"></div>
<form class="form-horizontal" role="form" method="post">
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
            <button type="submit" class="btn btn-primary">登录</button>
        </div>
    </div>
</form>
```

我们测试一下刚才创建的认证功能，注册了如下用户：
- 姓名 - tom
- 邮件 - tom@yahoo.com
- 密码 - Coder77@

现在我们使用用户的邮箱和密码进行登陆，如果你没有任何用户信息，你可以通过 https://localhost:7296/Admin/Create 创建一个用户，运行应用程序输入用户名和密码：
![20240205140008](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140008.png)

我们可以使用 `UserManager<T>` 的 `GetUserAsyns()` 方法类获取当前登录的用户信息并显示在View上：
![20240205140028](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140028.png)

编辑HomeController控制器，并获取User信息，将用户名传递到View
```csharp
[Authorize]
public async Task<IActionResult> Index()
{
     var appUser = await _userManager.GetUserAsync(HttpContext.User);
     var message = "Hello " + appUser?.UserName;
     return View((object)message);
}
```

# 4. ASP.NET Core Identity 退出页面
Logout非常简单，我们调用SignInManager的SignOutAsync方法退出登录
```csharp
public async Task<IActionResult> Logout()
{
   await _signInManager.SignOutAsync();
   return RedirectToAction("Index", "Home");
}
```

Logout方法将退出登录，并跳转到Home控制器的Index方法。`User.Identity.IsAuthenticated` 属性返回true表示用户已经登录，否则将返回 null，当用户成功登录应用程序时显示 Logout 链接。

运行应用程序并进行登录，登录之后将跳转到Home页我们能看到 Logout按钮，点击它可以退出 ASP.NET Core Identity，看下面图片:
![20240205140045](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140045.png)

# 5. ASP.NET Core Identity Cookie
ASP.NET Core Identity使用Cookie确定一个用户是否被认证，一旦这个用户被认证Cookie就会被创建并且存储在浏览器，每次HTTP请求时都会发送Cookie到服务器，当我们在浏览器中打开应用程序的任何URL，Identity就能确定请求来自哪个用户，Cookie名字为.AspNetCore.Identity.Application，我们可以在浏览器中查看到这个Cookie
![20240205140053](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140053.png)

## 5.1 Identity Cookie超时
我们可以使用IServiceCollection 的ConfigureApplicationCookie方法设置ASP.NET Core Identity Cookie超时时间
```csharp
builder.Services.ConfigureApplicationCookie(
	opts =>
	{
		opts.LoginPath = "/Account/Login";
		//设置Cookie名称
		opts.Cookie.Name = ".AspNetCore.Identity.Application";
		//设置Cookie超时时间
		opts.ExpireTimeSpan = TimeSpan.FromMinutes(20);
		//设置滑动时间
		opts.SlidingExpiration = true;
	}
);
```

## 5.2 ASP.NET Core Identity Remember Me
我们创建一个Remember Me功能，帮助客户记住密码，我们只需要登录一次，Identity将创建一个持久Cookie在浏览器，我们在login.cshtml的页面添加一个type=checkbox的输入框
```cshtml
@model Login
@{
    ViewData["Title"] = "Login";
}
<div class="text-danger" asp-validation-summary="All"></div>
<form class="form-horizontal" role="form" method="post">
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
        <label asp-for="RememberMe" class="col-sm-1 control-label"></label>
        <div class="col-sm-11">
            <input type="checkbox" asp-for="RememberMe" />
        </div>
    </div>
    <div class="mb-3 row">
        <div class="col-sm-11 offset-sm-1">
            <button type="submit" class="btn btn-primary">登录</button>
        </div>
    </div>
</form>
```

并在Login实体类中增加属性：
```csharp
public class Login
{
	[Required]
	[DisplayName("用户名")]
	public string Email { get; set; } = null!;
	[Required]
	[DisplayName("密码")]
	[DataType(DataType.Password)]
	public string Password { get; set; } = null!;
	public string? ReturnUrl { get; set; }
	[Required]
	[DisplayName("记住密码")]
	public bool RememberMe { get; set; }
}
```

修改Login登录方法
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
        }
        ModelState.AddModelError(nameof(login.Email),"Login Failed: Invalid Email or password");
    }
    return View(login);
}
```
![20240205140108](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205140108.png)

# 6. 总结
这节我们主要介绍了在Identity如何认证用户，我们可以为我们WebSite区域做一些安全认证

源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/AspNetCore.Identity/Identity
补充：ASP.NET Core Identity 系列之三 源代码地址以废弃，上面那是最新地址

