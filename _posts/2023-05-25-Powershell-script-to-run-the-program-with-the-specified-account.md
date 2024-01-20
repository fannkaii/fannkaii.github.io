---
layout: post
title: "powershell脚本实现以指定的账户运行程序"
author: "Kai"
header-style: text
tags:
  - Windows
---

```powershell
$uname="cnvwfs02\dkx4pxk" #指定用户名
$pwd=ConvertTo-SecureString  "Beijing2023!" -AsPlainText -Force #password”指定用户的密码   
$cred=New-Object System.Management.Automation.PSCredential($uname,$pwd) #将用户和密码保存在这个变量中   
Start-Process -FilePath  "C:\WINDOWS\system32\notepad.exe" -Credential $cred #以指定的账户运行
```