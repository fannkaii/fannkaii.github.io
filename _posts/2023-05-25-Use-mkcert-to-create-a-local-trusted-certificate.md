---
layout: post
title: "使用mkcert(开源软件)制作本地可信证书"
author: "Kai"
header-style: text
tags:
  - Windows
---

使用mkcert(开源软件)制作本地可信证书
https://github.com/FiloSottile/mkcert

在 Windows 上，使用Chocolatey的命令安装：
```
choco install mkcert
```
安装后，执行命令，即可在浏览器中创建根证书，在当前目录创建两个pem文件，一个是证书，一个是私钥（不会在certlm.msc中创建证书）
```
mkcert 127.0.0.1
```
返回：
```
Created a new certificate valid for the following names
 - "127.0.0.1"
The certificate is at "./127.0.0.1.pem" and the key at "./127.0.0.1-key.pem"
It will expire on 9 July 2025
```

可以在管理员权限的CMD下运行下面的命令，将两个pem文件合并为一个带私钥的pfx格式的证书文件。
```shell
openssl pkcs12 -export -out certificate.pfx -inkey 127.0.0.1-key.pem -in 127.0.0.1.pem
```
在写好一个self-host的WCF Service后，还需要执行下面的PowerShell脚本来打开端口，以及创建SSL/TLS绑定：
```powershell
# 使用powershell脚本，打开端口 8088
New-NetFirewallRule -DisplayName "Allow Port 8088" -Direction Inbound -LocalPort 8088 -Protocol TCP -Action Allow

# 创建 SSL/TLS 绑定
netsh http add sslcert ipport=127.0.0.1:8087 certhash=1918c98b805fbcab57d16b27c6b25009a35ab8de appid='{F29BD455-202D-4DEB-8002-A3A2D9B52CED}'
# Add urlacl
netsh http add urlacl https://+:8087/MyWcfService/ user=运行此self-host server的用户名
```
**对上述命令的说明**
- ipport 域名或IP: 端口
- certhash 证书指纹
- appid 是一个应用程序标识符，用于标识使用 SSL/TLS 绑定的应用程序。它的值是一个 GUID，可以是任意的 GUID，只要它是唯一的即可。

如果你想同时使用域名和 IP 地址访问网站，建议同时配置基于域名和 IP 地址的 SSL/TLS 绑定。在 netsh 命令中，你可以使用 IP 地址和端口号来设置一个基于 IP 地址的 SSL/TLS 绑定，使用域名和端口号来设置另一个基于域名的 SSL/TLS 绑定。这样，无论用户使用域名还是 IP 地址来访问你的网站，都可以建立 SSL/TLS 连接并访问网站。

https://127.0.0.1:8080/MyWcfService
https://localhost:8080/MyWcfService

可以使用以下命令来列出计算机上所有的 SSL/TLS 绑定:
```powershell
netsh http show sslcert | Select-String 'IP:|Certificate Hash|Application ID|Certificate Store|SSL Protocol' | Out-String -stream | ConvertFrom-Csv -Delimiter ":" | Format-Table -AutoSize
```

使用以下命令来列出计算机上所有的 URLACL:
```
netsh http show urlacl
```
URLACL 全称是 URL Access Control List，它是 Windows 中一种控制网络权限的机制。URLACL 可以让你为一个特定的 URL（或 URL 模式）配置安全权限，以控制哪些用户或组可以访问该 URL，以及以何种方式进行访问。

URLACL 可以在 Windows 中的 HTTP.SYS 内核级别配置，因此可以在不同的应用程序或服务之间共享。在 Windows 中，IIS 和 WCF 服务等应用程序都使用 URLACL 来配置其 HTTP 终结点。同时，通过使用 netsh 命令，用户也可以对 URLACL 进行查询、添加、删除等操作，以满足自己的需求。