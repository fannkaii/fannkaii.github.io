---
layout: post
title: "转：ASP.NET Core Identity 系列之三：用户名、密码、邮件策略"
author: "Kai"
header-style: text
tags:
  - ASP.NET Core
---

转自：https://mp.weixin.qq.com/s?__biz=MzA3NDM1MzIyMQ==&mid=2247486177&idx=1&sn=44c5277b6882deb798db1944d4163e04

在这节中我们将研究ASP.NET Core Identity中用户名、邮件、密码策略，解释一下Identity默认提供的策略以及如何实现自定义策略，最后我们针对用户名、邮件、密码实现客户自定义策略

# 1. ASP.NET Core Identity密码策略
ASP.NET Core Identity默认的密码策略要求密码满足如下条件：
- 密码必须至少6个字符
- 密码至少有一个除字母和数字以外的字符
- 密码必须至少有一个数字('0'-'9')
- 密码必须至少有一个大写字母('A'-'Z')

打开用户新增页面，输入如下信息并保存，我们将看到如下错误：
- 姓名: Yogi
- 邮箱: yogi@gmail.com
- 密码: xxx

![20240205135720](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135720.png)

我们可以在ASP.NET Core Identity中设置密码策略来增强用户密码的安全性，例如我们要求用户输入的密码满足如下条件：
- 必须至少有8个字符
- 不能包含常用的短语像“123”,“admin”
- 必须包含至少有一个小写字母

我们在Program.cs 文件中配置一下密码策略，代码如下：
```csharp
builder.Services.Configure<IdentityOptions>(options =>
{
    options.Password.RequiredLength = 8;
    options.Password.RequireLowercase = true;
});
```

下面表格提供了IdentityOptions 类常用的一些属性：

|名称|描述|
| ------------ | ------------ |
| RequiredLength | 设置密码最少长度 |
| RequireNonAlphanumeric | 设置用户名和密码是否有一个非数字和字符.默认值为True. 如果我们不希望有特殊字符，我们可以设置成false.例如： – @, !, #, $. |
| RequireLowercase | 当我们设置这个属性为True时，Identity 将要求至少有一个小写字母 |
| RequireUppercase | 当我们设置属性为false，Identity 不要求至少有一个大写字母在密码中 |
| RequireDigit | 当我们设置属性false，Identity 不要求至少有一个数字在密码中 |

我们测试一下刚才设置的密码策略，打开浏览器输入如下地址：
https://localhost:7296/Admin/Create ，创建一个新的用户

- 姓名: Yogi
- 邮箱: yogi@gmail.com
- 密码: ABC

我们注册一个新的用户，看到如下错误：
![20240205135736](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135736.png)

将密码改为Secret@123，它满足密码的标准，所以能创建成功，但是没有满足"不能包含常用的短语像"123","admin" "，这时我们需要创建自定义策略

# 2. 自定义密码策略
创建自定义密码策略，以至于客户输入的密码能满足我们自定义的规则，我们需要继承 `PasswordValidator<T>` 类

例子：针对用户密码创建自定义密码策略并满足如下2个规则：
- 密码不能包含用户名
- 密码不能包含123策略，用户不能输入Admin123，Happy123

在项目根目录下创建一个文件夹IdentityPolicy，并在该文件夹下创建CustomPasswordPolicy类，添加如下代码：
```csharp
public class CustomPasswordPolicy : PasswordValidator<AppUser>
{
    public override async Task<IdentityResult> ValidateAsync(UserManager<AppUser> manager, AppUser user, string password)
    {
        IdentityResult result = await base.ValidateAsync(manager, user, password);
        List<IdentityError> errors = result.Succeeded ? new List<IdentityError>() : result.Errors.ToList();

        if (password.ToLower().Contains(user.UserName.ToLower()))
        {
            errors.Add(new IdentityError
            {
                Description = "密码不能包含用户名"
            });
        }
        if (password.Contains("123"))
        {
            errors.Add(new IdentityError
            {
                Description = "密码不能包含123数字序列"
            });
        }
        return errors.Count == 0 ? IdentityResult.Success : IdentityResult.Failed(errors.ToArray());
    }
}
```
`PasswordValidator<T>`类定义`ValidateAsync()`方法。我们重写该方法并实现自定义密码策略的逻辑

我们判断密码是否包含用户名和123数组序列，如果包含将相应错误添加到List对象中：
```csharp
if (password.ToLower().Contains(user.UserName.ToLower()))
{
    errors.Add(new IdentityError
    {
         Description = "密码不能包含用户名"
    });
}
if (password.Contains("123"))
{
    errors.Add(new IdentityError
    {
         Description = "密码不能包含123数字序列"
    });
}
```

最后一行
```csharp
return errors.Count == 0 ? IdentityResult.Success : IdentityResult.Failed(errors.ToArray());
```

我们检查错误个数，如果错误个数为0我们将返回`IdentityResult.Success`否则返回`IdentityResult.Failed`返回一个错误列表。`IPasswordValidator`接口中定义了密码验证功能，我们在启动类中添加该服务
```csharp
builder.Services.AddTransient<IPasswordValidator<AppUser>, CustomPasswordPolicy>();
```

我们测试一下刚才创建的自定义密码策略功能。运行应用程序，在浏览器中输入如下地址：https://localhost:7296/Admin/Create ，我们注册一个新的用户：
- 姓名– Ronny
- 邮箱– rons@gmail.com
- 密码 – ronny123

点击保存按钮，我们会看见我们创建的自定义的密码策略
- 密码不能包含用户名
- 密码不能包含123数字序列

这个错误来自于我们自定义的密码策略类CustomPasswordPolicy的验证方法。如下图所示：
![20240205135754](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135754.png)


# 3. 用户名和邮件策略
我们可以在ASP.NET Core Identity中配置用户名和邮件策略，也可以定义一些规则来指定用户名和邮件输入特定的值。我们配置用户名和邮件策略：
```csharp
builder.Services.Configure<IdentityOptions>(options =>
{
    options.User.RequireUniqueEmail = true;
    options.User.AllowedUserNameCharacters = "abcdefghijklmnopqrstuvwxyz";
});
```

RequireUniqueEmail属性设置为true，限制用户邮箱必须是唯一

AllowedUserNameCharacters属性为特定字符集，说明创建用户名时只能使用AllowedUserNameCharacters中指定的字符集

我们已经有一个用户注册使用admin@gmail.com, 现在我们使用相同的邮箱注册一个新的用户我们将获取到一个错误，运行应用程序，并输入如下URL：https://localhost:7296/Admin/Create

- 姓名 – admin1
- 邮箱 – admin@gmail.com
- 密码 – Admin77@#

我们将获取到两个错误:

- User name ‘admin1’ is invalid, can only contain letters or digits
- Email ‘admin@gmail.com’ is already taken

这些错误显示如下：
![20240205135808](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135808.png)

# 4. 客户自定义用户名和密码策略
使用UserValidator类创建ASP.NET Core Identity 自定义用户名和邮箱策略。这个类提供了验证用户名和邮箱的服务

让我们增加下面2个限制：
- 不允许添加叫goole的用户名
- 只允许使用yahoo.com邮箱

在IdentityPolicy文件夹下创建CustomUsernameEmailPolicy类，让该类继承自UserValidator类，重写ValidateAsync()并在该方法内创建自定义用户名和邮箱策略
```csharp
public class CustomUsernameEmailPolicy : UserValidator<AppUser>
{
        public override async Task<IdentityResult> ValidateAsync(UserManager<AppUser> manager, AppUser user)
        {
            IdentityResult result = await base.ValidateAsync(manager, user);
            List<IdentityError> errors =result.Succeeded? new List<IdentityError>() : result.Errors.ToList();
            if (user.UserName == "google")
            {
                errors.Add(new IdentityError
                {
                    Description = "Google cannot be used as a user name"
                });
            }
            if (!user!.Email!.ToLower().EndsWith("@yahoo.com"))
            {
                errors.Add(new IdentityError
                {
                    Description = "Only yahoo.com email addresses are allowed"
                });
            }
            return errors.Count == 0 ? IdentityResult.Success : IdentityResult.Failed(errors.ToArray());
        }
}
```

在启动类添加用户验证服务，如下代码：
```csharp
builder.Services.AddTransient<IUserValidator<AppUser>, CustomUsernameEmailPolicy>();
```

现在我们测试一下该功能，打开浏览器，输入如下地址:https://localhost:7296/Admin/Create 并添加下面用户信息
- 姓名 – tom
- 邮件 – tom@gmail.com
- 密码 – Coder77@

你将收到下列错误：
![20240205135826](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135826.png)

相同的方式添加一个叫google用户名，输入下面信息：
- 姓名 – google
- 邮件 – tom@yahoo.com
- 密码 – Coder77@

你将获取到下面错误：
![20240205135833](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135833.png)

# 5. 更新用户方法
我们把刚才添加的自定义策略添加到修改用户的方法中，代码如下：
```csharp
[HttpPost]
public async Task<IActionResult> Update(UpdateUserDTO updateUserDTO)
{
    var appUser = await _userManager.FindByIdAsync(updateUserDTO.Id);
    if (appUser != null)
    {
        IdentityResult validEmail = null;
        if (!string.IsNullOrEmpty(updateUserDTO.Name) && !string.IsNullOrEmpty(updateUserDTO.Email))
        {
            appUser.UserName = updateUserDTO.Name;
            appUser.Email = updateUserDTO.Email;
            validEmail = await _userValidator.ValidateAsync(_userManager, appUser);
            if (!validEmail.Succeeded)
                Errors(validEmail);
        }
        else
            ModelState.AddModelError("", "用户名和邮件不能为空");
        IdentityResult validPass = null;
        if (!string.IsNullOrEmpty(updateUserDTO.Password))
        {
            validPass = await _passwordValidator.ValidateAsync(_userManager, appUser, updateUserDTO.Password);
            if (validPass.Succeeded)
                appUser.PasswordHash = _passwordHasher.HashPassword(appUser, updateUserDTO.Password);
            else
                Errors(validPass);
        }
        else
            ModelState.AddModelError("", "密码不能为空");
        if (!string.IsNullOrEmpty(updateUserDTO.Name) &&
            !string.IsNullOrEmpty(updateUserDTO.Email) &&
            !string.IsNullOrEmpty(updateUserDTO.Password) &&
            validEmail.Succeeded &&
            validPass.Succeeded)
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

在AdminController的构造函数中添加IPasswordValidator 和 IUserValidator引用
```csharp
public class AdminController : Controller
{
    private UserManager<AppUser> _userManager;
    private IPasswordHasher<AppUser> _passwordHasher;
    private IPasswordValidator<AppUser> _passwordValidator;
    private IUserValidator<AppUser> _userValidator;
    public AdminController(UserManager<AppUser> userManager,
                            IPasswordHasher<AppUser> passwordHash,
                            IPasswordValidator<AppUser> passwordValidator,
                            IUserValidator<AppUser> userValidator
                            )
    {
        _userManager = userManager;
        _passwordHasher = passwordHash;
        _passwordValidator = passwordValidator;
        _userValidator = userValidator;
    }
}
```

我们再次更新一下用户，这是将显示如下错误：
![20240205135844](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240205135844.png)

# 6. 总结
这节我们主要学习了在ASP.NET Core Identity中如何使用密码、用户名和邮箱策略

源代码地址：
https://github.com/bingbing-gui/Asp.Net-Core-Skill/tree/master/SecurityIdentity/AspNetCore.Authentication/Identity
