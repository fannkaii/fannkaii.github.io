---
layout: post
title: "在命令行下使用Nuget.exe下载单个包，及其所有依赖的包"
author: "Kai"
header-style: text
tags:
  - Windows
---

```powershell
#下载单个包，及其所有依赖的包
.\nuget.exe install <package_name> -SolutionDirectory <save_path> -PackageSaveMode nupkg


#根据packages.config文件直接下载Nuget包
.\nuget.exe restore <packages.config文件所在目录> -DirectDownload -PackagesDirectory <Nuget包存储路径>
#Example:
.\nuget.exe restore C:\Users\FK\Desktop\4.10 -DirectDownload -PackagesDirectory C:\Users\FK\Desktop\4.10\save
```