---
layout: post
title: "GitHub教程"
author: "Kai"
header-style: text
tags:
  - Git
---

# 1. 概述
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121221941.png width=250 />

## 1.1 Git和代码托管中心
代码托管中心的任务：维护远程库
局域网环境下：
- GitLab服务器

外网环境下：
- GitHub
- 码云

## 1.2 本地库和远程库
- 团队内部协作
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222035.png width=630 />

- 跨团队协作
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222106.png width=630 />

# 2. Git命令行操作
## 2.1 本地库操作
- 命令：`git init`
- 效果：
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222142.png width=830 />

- 注意：.git目录中存放的是本地库相关的子目录和文件，不要删除，也不要胡乱修改。

## 2.2 设置签名
- 形式
	用户名：tom
	Email地址：goodMorning@a.com
- 作用：区分不同的开发人员
- 辨析：这里设置的签名和登录远程库（代码托管中心）的账户、密码没有任何关系。
- 命令
	- 项目级别/仓库级别：仅在当前本地库范围内有效
		- `git config user.name tom_pro`
		- `git config user.email goodMorning_pro@a.com`
		- 信息保存的位置：./.git/config文件
		<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222209.png width=780 />
	- 系统用户级别：登录当前系统的用户范围
		- `git config --global user.name tom_glb`
		- `git config --global user.email goodMorning_glb@a.com`
		- 信息保存位置：~/.gitconfig文件 （家目录下的.gitconfig文件）
		<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222235.png width=480 />
	- 级别优先级：
		- 就近原则：项目级别优先于系统用户级别，二者都有时采用项目级别的签名
		- 如果只有系统用户级别的签名，就以系统用户级别的签名为准
		- 二者都没有，不允许

## 2.3 基本操作
- 状态查看操作
	- `git status`
		查看工作区、暂存区状态
			- **git status输出的绿字表明**：暂存区的内容与工作区一致，与本地库不一致（暂存区的内容即将要提交到本地库。此时提交到本地库后，三者的内容将一致）
			- **git status输出的红字表明：**暂存区的内容与工作区有不一致，但与本地库一致（此时要做的事情是，将工作区修改好的文件添加到暂存区，此时git status将会输出绿字）
	- 添加操作
		- `git add [filename]`
			将工作区的“新建/修改”添加到暂存区
		- 提交操作
			- `git commit -m "commit message" [filename]`
				将暂存区的内容提交到本地库
		- 查看历史记录操作
			- `git log`
			- 多屏显示控制方式：
				- 空格向下翻页
				- b 向上翻页
				- q 退出
		- `git log --pretty=oneline`
		<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222311.png width=780 />
		- `git log --oneline`
		<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222337.png width=500 />
		- `git reflog`  （在--oneline的基础上还显示了：HEAD指针到某个版本，需要移动几步）
		<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222402.png width=750 />

## 2.4 Git版本的前进、后退
- 本质：HEAD是一个指针，指针可以指向过去的版本，即后退；后退之后，也可以再指向前面的版本，即前进。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222428.png width=820 />
- 基于索引值操作[推荐] （建议配合git reflog命令使用）
	`git reset --hard [索引值]`
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222450.png width=780 />
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222515.png width=780 />
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222536.png width=780 />

- 使用^符号：只能后退，一个异或符号退1步
	`git reset --hard HEAD^^^` （该命令后退3步）
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222559.png width=780 />

- 使用~符号：只能后退，波浪线符合后的数字表示后退的步数
	`git reset --hard HEAD~3` （该命令后退3步）
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222631.png width=700 />

## 2.5 git reset命令的三个参数对比
- --soft
	- 不会碰暂存区和工作区。仅仅会在本地库移动HEAD指针
	- 执行命令 `git reset --soft 9a9ebe0` 后，工作区的good.txt还是以eeeeee为最新的一行，和未执行该命令时相同。然而暂存区变成了“modified:    good.txt”，为什么呢？因为本来在命令执行前，本地库、暂存区、工作区，三者是一致的（working tree clean）。但是后来通过执行命令，把本地库的HEAD指针往前（前进）移动了几个commit，此时三者的一致就被打破了，所以现在暂存区的内容并没有提交到本地库指针现在指向的位置（或者说本地库后退或前进了，就显得暂存区变化了），实际上暂存区的内容没有变化。所以暂存区变成绿字，不是它变了，而是本地库的指针发生了变化。
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222655.png width=700 />
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222716.png width=700 />
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222739.png width=200 />
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222800.png width=500 />
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222827.png width=360 />

- --mixed
	- 在本地库移动HEAD指针
	- 重置暂存区（用指针指向的commit）
	
	基于上图的`git reflog`命令的结果，执行命令：`git reset --mixed 42e7e84`，命令执行后，工作区没有改变，而本地库、暂存区往后移动了几个commit。
	暂存区目前是红字，但实际上没有变化，如何理解？该命令使得本地库、暂存区都往后移动了几个commit，工作区没有移动，所以就显得工作区往前走了（被改动了，所以就变成红字了）。
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222854.png width=750 />

- --hard
	- 在本地库移动HEAD指针
	- 重置暂存区（用指针指向的commit）
	- 重置工作区（用指针指向的commit）
	
	`git reset --hard xxxxxx`    该命令会让三者（本地库、暂存区、工作区）同时“移动”。即移动了本地库的指针，然后将暂存区、工作区刷新为当前指针指向的commit对应的内容。
	一般来说，前--soft和--mixed用的少。

## 2.6 删除文件并找回
- 前提：删除前，文件存在时的状态提交到了本地库。
- 操作：`git reset --hard` [指针位置]
	- 删除操作已经提交到本地库：指针位置指向历史记录（那个文件存在的历史记录）
	- 删除操作尚未提交到本地库：指针位置使用HEAD

## 2.7 比较文件差异
- `git diff [文件名]`
	将工作区中的文件和暂存区进行比较
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222914.png width=455 />

- `git diff [本地库中的历史版本] [文件名]`
	将工作区中的文件和本地库历史记录比较
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222931.png width=405 />

- `git diff`
	不带文件名，比较多个文件
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121222950.png width=410 />

# 3. 分支管理
## 3.1 什么是分支？
在版本控制过程中，使用多条线同时推进多个任务。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223008.png width=800 />

## 3.2 分支的好处？
- 同时并行推进多个功能开发，提高开发效率。
- 各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败的分支删除重新开始即可。

## 3.3 分支操作
### 3.3.1 创建分支
```
git branch [分支名]
```
### 3.3.2 查看分支
```
git branch -v
```
### 3.3.3 切换分支
```
git checkout [分支名]
```
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223031.png width=820 />

### 3.3.4 合并分支
- 第一步：切换到接受修改的分支（被合并，增加新内容）上
	```
	git checkout [被合并分支名]
	```
- 第二步：执行merge命令
	```
	git merge [有新内容的分支名]
	```
    <img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223052.png width=540 />

### 3.3.5 解决冲突
**冲突的解决：**
1. 编辑文件，删除特殊符号
2. 把文件修改到满意的的程度，保存退出
3. `git add [文件名]`
4. `git commit -m "日志信息"`
	注意：此时commit一定不能带具体文件名

<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223135.png width=530 />
<br />
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223153.png width=750 />
<br />
<span style="color:red">当前发生了冲突的文件：</span><br />
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223217.png width=550 />

下图是在发生冲突的文件中，将无用的特殊符合删掉，并编辑到自己认为满意的状态：
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223234.png width=340 />

下图是发生了冲突之后，用ll命令看到工作区并没有因为发生冲突而多出来任何文件。
然后通过`vim good.txt`手动编辑这个带有冲突的文件
编辑后，用命令`git status`看到有未合并的路径，通过`git add <file>`去标记已解决
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223257.png width=820 />

然后再用`git status`就看到“所有冲突已修复，但是你仍处于合并状态。使用`git commit`来结束合并”。如果用`git commit -m "resolve conflict" good.txt`这种带文件名来提交，就会报一个致命错误。所以要根据提交，用`git commit -m "resolve conflict"`不带文件名来提交即可。
提交后，分支从“hot_fix|MERGING”变回了“hot_fix”
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223318.png width=820 />

# 4. Git基本原理
## 4.1 哈希
哈希是一系列的加密算法，各个不同的哈希算法虽然加密强度不同，但是有以下几个共同点：
1. 不管输入数据的数据量有多大，输入同一个哈希算法，得到的加密结果长度固定。
2. 哈希算法确定，输入数据确定，输出数据能够保证不变。
3. 哈希算法确定，输入数据有变化，输出数据一定有变化，而且通常变化很大
4. 哈希算法不可逆

Git底层采用的是SHA-1算法。
哈希算法可以被用来验证文件。原理如下图所示：
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223338.png width=820 />

## 4.2 Git保存版本的机制
### 4.2.1 集中式版本控制工具的文件管理机制
以文件变更列表的方式存储信息。这类系统将它们保存的信息看作是一组基本文件和每个文件随时间逐步累积的差异。（SVN）
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223402.png width=820 />

### 4.2.2 Git的文件管理机制
Git把数据看作是小型文件系统的一组快照。每次提交更新时Git都会对当前的全部文件制作一个快照并保存这个快照的索引。为了高效，如果文件没有修改，Git不再重新存储该文件，而是只保留一个链接指向之前存储的文件。所以Git的工作方式可以称之为快照流。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223424.png width=820 />

### 4.2.3 Git文件管理机制细节
#### Git的“提交对象”
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223452.png width=820 />

#### 提交对象及其对象形成的链条
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223535.png width=820 />

### 4.2.4 Git分支管理机制
#### 分支的创建
创建分支，就是创建新的指针。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223602.png width=500 />

#### 分支的切换
切换分支，就是切换指针的位置。HEAD指针从指向master，切换为指向testing分支。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223625.png width=500 />

testing分支向前走了一步，所以HEAD指针指向了最新的commit“87ab2”
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223645.png width=650 />

当需要再切换回master分支的时候，直接让HEAD指针指向master分支就好了。因为只涉及到指针的切换，所以速度很快。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223702.png width=650 />

现在又切换回了master，并且该分支向前走了一个commit：
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223722.png width=650 />

# 5. Git远程库
## 5.1 一般操作
**在本地库创建远程库地址别名：**
```
git remote add origin https://github.com/atguigu2018ybuq/huashan.git
```

创建别名是用于和远程库建立关联（不直接使用https的地址，也是由于其太长，不方便），便于本地库对远程库执行fetch和push操作。
命令格式：`git remote -v`		查看当前所有远程地址别名
命令格式：`git remote add [别名] [远程地址]`

<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223744.png width=800 />

使用命令 `git push origin master` 将本地库的master分支推送到远程仓库
命令格式：`git push [别名] [分支名]`
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223810.png width=800 />

上述命令在回车后，在其被执行之前，会先弹出如下一个对话框，填写你在GitHub的用户名和密码：
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223922.png width=420 />

另一个人，可以在本地克隆该远程库：
`git clone https://github.com/atguigu2018ybuq/huashan.git`
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121223941.png width=800 />

如下图，远程库的文件在本地库工作区已经存在；并且clone命令为我们自动生成了.git目录的内容；并且还为我们创建了远程库的别名。
命令格式：`git clone [远程地址]`
克隆操作能达到3个效果：
- 完整的把远程库下载到本地
- 创建origin远程地址别名
- 初始化本地库

<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224013.png width=800 />

假设克隆完成后，在本地库提交了一个commit，想要推送到远程库。默认情况下是没有权限向github远程库push的。必须在该库的owner邀请了该人之后，才会有push权限。
做为该库的owner，邀请他人参与该库的方法如下图。邀请之后，被邀请人会收到邀请邮件。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224036.png width=800 />

## 5.2 拉取操作
- pull = fetch + merge		（举例说明：假设当前在分支master上，在本地有一个远端仓库的别名分支，名为origin/master，与master分支有关联。pull操作中的fetch操作会将远程库的内容下载到origin/master分支上，再在master分支上执行merge操作，将origin/master分支上的更新的内容合并到本地的master分支）
- `git fetch [远程库地址别名] [远程分支名]`
- `git merge [远程库地址别名/远程分支名]`

## 5.3 解决冲突
要点
- 如果不是基于GitHub远程库的最新版所做的修改，不能推送，必须先拉取。
- 拉取下来后，如果进入冲突状态，则按照“分支冲突解决”操作解决即可。

## 5.4 SSH免密登录
GitHub有Https和ssh两种方式在客户机终端登录。在win10使用https方式可以利用windows的凭据功能来避免每次pull操作都重新输入用户名和密码。也可以用ssh的方式，ssh的方式的缺点是只能保存一个用户的账户信息。
ssh方式的配置方法：
- 打开git的bash，输入`cd ~` 进入到家目录
- 输入命令：`ssh-keygen -t rsa -C atguigu2018@aa.com` （然后一路回车即可）
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224108.png width=800 />
- 然后输入`cd .ssh` 进入该目录，输入ll看到上图的命令生成了两个文件：
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224123.png width=600 />

	输入`cat id_rsa.pub` 可以看到该文件的内容
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224151.png width=800 />

	将上图的文本，复制到下图的文本框：
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224223.png width=800 />
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224247.png width=800 />

	此时回到git工作区的目录，修改文件并生成一个commit，然后在push之前，需要配置好ssh免密登录。用命令`git remote add origin_ssh git@github.com:atiguigu2018@aa.com` 将别名origin_ssh添加到本地库的配置。然后用命令`git push origin_ssh master` 即可使用ssh免密登录进行推送。
	<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224309.png width=800 />

## 5.5 添加.ignore文件
在家目录中的.gitconfig（git全局配置文件）中可以如下图这样配置。
java.gitignore文件可以参照GitHub官方给的ignore文件基础上，加一些自己项目需要忽略的文件类型或目录。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224339.png width=450 />

# 6. Git工作流介绍
## 6.1 集中式工作流
像SVN一样，集中式工作流以中央仓库作为项目所有修改的单点实体。所有修改都提交到master这个分支上。
这种方式与SVN的主要区别就是开发人员有本地库。Git很多特性并没有用到。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224355.png width=800 />

## 6.2 GitFlow工作流
GitFlow工作流通过为功能开发、发布准备和维护设立了独立的分支，让发布迭代过程更流畅。严格的分支模型也为大型项目提供了一些非常必要的结果。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224415.png width=800 />

## 6.3 Forking工作流
Forking工作流是在GitFlow基础上，充分利用了Git的Fork和pull request的功能已达到代码审核的目的。更适合安全可靠地管理大团队的开发者，而且能接受不信任贡献者的提交。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224444.png width=800 />

## 6.4 GitFlow工作流详解
分支种类
- 主干分支master
主要负责管理正在运行的生产环境代码。永远保持与正在运行的生产环境完全一致。
- 开发分支 develop
主要负责管理正在开发过程中的代码。一般情况下应该是最新的代码。
- bug修复分支 hotfix
主要负责管理生产环境下出现的紧急修复的代码。从主干分支分出，修复完毕并测试上线后，合并回主干分支。合并回后，视情况可以删除该分支。
- 准生产分支（预发布分支）release
较大的版本上线前，会从开发分支中发布准生产分支，进行最后阶段的集成测试。该版本上线后，会合并到主干分支。生产环境运行一个阶段稳定后可以视情况删除。
- 功能分支 feature
为了不影响较短周期的开发工作，一般把中长期开发模块，会从开发分支中独立出来。开发完成后会合并到开发分支。
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224518.png width=800 />

# 7. GitLab服务器搭建过程
官网地址
首页：https://about.gitlab.com
安装说明：https://about.gitlab.com/installation

该服务器在CentOS7中安装，首先安装CentOS7。可以用虚拟机安装，安装后，打开终端，进入目录：
`cd /etc/sysconfig/network-scripts/`  该目录里有一个名为ifcfg-ens33的文件，用于配置IP地址的配置。
输入`sudo vim ifcfg-ens33`打开编辑此文件。该文件初始状态：
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224538.png width=500 />

对其配置后：（IP地址根据自己真实机的虚拟网卡的实际情况配置）
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224553.png width=500 />

然后用命令重启网络服务 `service network restart`
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224613.png width=800 />

ping百度可以ping通，说明网络通畅：
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224631.png width=800 />

**以下是在CentOS 7 中安装GitLab社区版的步骤：**
1. 下载此文件，放到CentOS的opt目录下
https://packages.gitlab.com/gitlab/gitlab-ce/packages/el/7/gitlab-ce-13.6.1-ce.0.el7.x86_64.rpm
2. 在opt命令下执行vim install.sh，将以下文本复制进去，保存退出
	```bash
	sudo rpm -ivh /opt/gitlab-ce-13.6.1-ce.0.el7.x86_64.rpm
	sudo yum install -y curl policycoreutils-python openssh-server cronie
	sudo systemctl enable sshd
	sudo systemctl start sshd
	sudo firewall-cmd --permanent --add-service=http
	sudo firewall-cmd --permanent --add-service=https
	sudo systemctl reload firewalld
	sudo yum install postfix
	sudo service postfix start
	sudo chkconfig postfix on
	curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
	sudo EXTERNAL_URL="http://gitlab.example.com" yum -y install gitlab-ce
	```
3. 执行`chmod 755 install.sh`设置该文件的权限为可执行。
4. 输入`./install.sh` 命令执行此文件进行GitLab的安装。
5. 安装完成后，输入命令`reboot`重启CentOS
6. 重启后，打开终端，执行GitLab的初始化配置：`gitlab-ctl reconfigure`
7. 然后，启动GitLab服务：`gitlab-ctl start`
8. 打开浏览器，输入服务器IP地址访问GitLab服务即可。初次登录需要为GitLab的root用户设置密码。
9. 附注
	1. 停止GitLab服务：`gitlab-ctl stop`
	2. 如果在CentOS之外不能访问GitLab服务，可以停止CentOS的防火墙：`service firewalld stop`

**搭建配置GitLab服务器的域名**
<img src=https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121224655.png width=410 />
