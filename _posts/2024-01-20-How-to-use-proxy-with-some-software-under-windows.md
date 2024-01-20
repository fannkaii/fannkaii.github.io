---
layout: post
title: "Windows下的一些软件使用代理的方法(备忘)"
author: "Kai"
header-style: text
tags:
  - Windows
---

# 前提：在windows下，科学上网用的v2rayN

## 1. 让windows下cmd使用代理
```shell
set https_proxy=http://127.0.0.1:10808
set https_proxy=https://127.0.0.1:10809
```
缺点：每次打开cmd都要执行一次

## 2. 让Git使用代理
### 2.1 执行以下命令即可：
```shell
git config --global https.proxy http://127.0.0.1:10808
git config --global https.proxy https://127.0.0.1:10809
git config --global http.proxy 'socks5://127.0.0.1:10808'
git config --global https.proxy 'socks5://127.0.0.1:10809'
```

### 2.2 如果想取消，执行以下命令即可：
```shell
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 3. 让VSCode使用代理
在VSCode的设置里搜索“proxy”，点击“在setting.json 中编辑”，在尾部输入：
```json
"http_proxy": "http://127.0.0.1:10808",
"https_proxy": "http://127.0.0.1:10809"
```

参考：
- [https://lynxux.github.io/posts/2021-07-01-windows-terminal-proxy](https://lynxux.github.io/posts/2021-07-01-windows-terminal-proxy)
- [https://meaninglive.com/2022/06/21/1447/](https://meaninglive.com/2022/06/21/1447/)
- [https://blog.csdn.net/m0_57236802/article/details/132169445](https://blog.csdn.net/m0_57236802/article/details/132169445)