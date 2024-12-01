---
layout: post
title: "VMware Workstation Pro详解"
author: "Kai"
header-style: text
tags:
  - VMware
  - 虚拟机
---

转自：https://juejin.cn/post/7286266688193478656
{% raw %}

# 1. 虚拟机介绍
## 1.1 介绍
- VMware Workstation Pro 是行业标准桌面 Hypervisor，使用它可在 Windows 或 Linux 桌面上运行 Windows、Linux 和 BSD 虚拟机。
- VMware官网地址：[www.vmware.com](https://www.vmware.com/)
- VMware Workstation Pro地址：[www.vmware.com/products/desktop-hypervisor/workstation-and-fusion](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)

## 1.2 构建虚拟机
![20241201214655](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201214655.png)

## 1.3 功能特性
![20241201214712](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201214712.png)

## 1.4 应用场景
- 面向 IT 专业人员的解决方案：可为几乎任何设备、平台或云环境设计、测试和演示软件解决方案。
- 面向开发人员的解决方案：可轻松构建和测试应用和操作系统。
- 面向企业的解决方案：通过随时随地向任何用户交付企业桌面和应用，可支持自带设备。

## 1.5 系统要求
- 主机（虚拟机软件运行的系统）系统要求
  ![20241201214745](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201214745.png)

- 客户机（虚拟机内安装的系统）系统要求，支持的列表请参考：[knowledge.broadcom.com/external/article?legacyId=2129859](https://knowledge.broadcom.com/external/article?legacyId=2129859)
  ![20241201215029](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201215029.png)

- VMware Workstation 17 支持安装的客户机列表
![20241201215043](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201215043.png)

# 2. 虚拟机安装
## 2.1 下载地址
- 官网下载地址：[www.vmware.com/products/desktop-hypervisor/workstation-and-fusion](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)
- 百度网盘下载地址：[pan.baidu.com/share/init?surl=PoKMuYpgjQ4o-yTcEcc8Ew&pwd=1234](https://pan.baidu.com/share/init?surl=PoKMuYpgjQ4o-yTcEcc8Ew&pwd=1234)
- 可以安装试用版和收费版，我们这边选择试用版进行演示
  ![20241201215221](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201215221.png)
- VMware Workstation Pro支持Windows和Linux安装，操作和功能差不多，我们这边下载比较常用的Windows平台安装。
  ![20241201215237](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201215237.png)

## 2.2 Windows安装
- 双击exe下一步下一步直到结束，输入注册码。

## 2.3 无法安装的原因
- 你的电脑不支持虚拟化或者是没有开启虚拟化。
- Windows10系统默认有一个虚拟机Hyper-V，把windows自带虚拟机给干掉。

# 3. 虚拟机安装CentOS
## 3.1 CentOS介绍
- 介绍
  - linux相关知识请参考[Linux详解](https://blog.csdn.net/liyou123456789/article/details/121548156)
  - CentOS，是基于Red Hat Linux提供的可自由使用源代码的企业级Linux发行版本；是一个稳定，可预测，可管理和可复制的免费企业级计算平台。

- 相关地址
  - 官方主页: [www.centos.org](https://www.centos.org/)
  - 邮件列表: [wiki.centos.org/GettingHelp(2f)ListInfo.html](https://wiki.centos.org/GettingHelp(2f)ListInfo.html)
  - 论坛: [discussion.fedoraproject.org/c/neighbors/centos/71](https://discussion.fedoraproject.org/c/neighbors/centos/71)
  - 文档: [docs.centos.org/en-US/docs](https://docs.centos.org/en-US/docs/)
  - Wiki: [wiki.centos.org](https://wiki.centos.org/)

- Fedora，CentOS Stream，RedHat，CentOS，RockyLinux之间的关系
  - 在CentOS8之前，Fedora是RedHat的“试验场”，很多新功能和特性先加入Fedora稳定后再加入RedHat（收费），然后从RedHat再拉出CentOS(免费，培养用户和生态）
    ![20241201215800](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201215800.png)

  - 在CentOS8之后，IBM（收购了redhat）取消CentOS，改为在Fedora 和RedHat 之间滚动发布CentOS stream
    ![20241201215815](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201215815.png)

  - CentOS（Kurtzer）的最初创造者不满这种划分，宣布创建RockyLinux，它将是一个“社区企业操作系统，设计成100%的bug-for-bug兼容Enterprise Linux。”很多人将其视作centos的延续。

- CentOS版本说明，大家看情况选择
  - CentOS 8已经在2021年底停止支持。
  - CentOS 7生命周期将于2024年6月30日终止。
  - CentOS以后就没有新版本了，没有CentOS 9，只有CentOS Stream 9。

## 3.2 CentOS下载
- 阿里镜像下载地址：[mirrors.aliyun.com/centos](https://mirrors.aliyun.com/centos/)
- 找到CentOS7.9版本地址：[mirrors.aliyun.com/centos/7.9.2009/isos/x86_64](https://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/)
- 版本说明如下，自行选择安装版本：
  - CentOS-7-x86_64-DVD-2009.iso （ 标准安装，推荐下载）
  - CentOS-7-x86_64-Everything-2009.iso （ 完整版，集成所有软件）
  - CentOS-7-x86_64-Minimal-2009.iso （ 精简版，自带的软件最少）
  - CentOS-7-x86_64-NetInstall-2009.iso （ 网络安装版，从网络安装或者救援系统）

- 百度网盘地址：[pan.baidu.com/share/init?surl=hQybzrKnfanqGAi53jKsmg&pwd=1234](https://pan.baidu.com/share/init?surl=hQybzrKnfanqGAi53jKsmg&pwd=1234)

## 3.3 CentOS安装
- 我们选择最新标准版本 **CentOS-7-x86_64-DVD-2207-02** 进行安装
- 创建虚拟机
  ![20241201220120](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220120.png)

  ![20241201220133](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220133.png)

- 选择对应的安装镜像文件
  ![20241201220148](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220148.png)

- 给虚拟机命名
  ![20241201220203](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220203.png)

- 指定磁盘
  ![20241201220215](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220215.png)

- 自定义硬件
  ![20241201220228](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220228.png)

  ![20241201220239](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220239.png)

- 完成虚拟机配置
  ![20241201220251](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220251.png)

- 运行虚拟机安装系统，选择语言
  ![20241201220305](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220305.png)

- 选择自动分区
  ![20241201220318](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220318.png)

- 设置root密码123456，之后等待自动安装
  ![20241201220331](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220331.png)

- 安装完成重启系统
  ![20241201220344](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220344.png)

- 输入账号密码，即可登录成功，系统安装完成
  ![20241201220357](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201220357.png)

# 4. 虚拟机安装Ubuntu
## 4.1 Ubuntu介绍
- 介绍
  - Ubuntu，是一款基于Debian Linux的以桌面应用为主的操作系统，内容涵盖文字处理、电子邮件、软件开发工具和Web服务等，可供用户免费下载、使用和分享。

- 相关地址
  - 官方主页: [ubuntu.com](https://ubuntu.com/)
  - 邮件列表: [discourse.ubuntu.com/t/mailing-lists/710](https://discourse.ubuntu.com/t/mailing-lists/710)
  - 论坛: [ubuntuforums.org](https://ubuntuforums.org/)
  - 中文论坛: [forum.ubuntu.org.cn](https://forum.ubuntu.org.cn/)
  - Wiki: [wiki.ubuntu.com](https://wiki.ubuntu.com/)
  - 帮助: [help.ubuntu.com](https://help.ubuntu.com/)

## 4.2 Ubuntu下载
- 阿里镜像下载地址：[mirrors.aliyun.com/oldubuntu-releases](https://mirrors.aliyun.com/oldubuntu-releases/)
- 找到Ubuntu22.04版本地址：[mirrors.aliyun.com/oldubuntu-releases/releases/22.04](https://mirrors.aliyun.com/oldubuntu-releases/releases/22.04/)
- 版本说明如下，自行选择安装版本：
  - ubuntu-22.04-desktop-amd64.iso （ 适用于64位的AMD处理器，桌面版）
  - ubuntu-22.04-live-server-amd64.iso （  适用于64位的AMD处理器，服务器版）
- 百度网盘地址：[pan.baidu.com/share/init?surl=uLAy9fGMVqRhexqonyqydQ&pwd=1234](https://pan.baidu.com/share/init?surl=uLAy9fGMVqRhexqonyqydQ&pwd=1234)

## 4.3 Ubuntu安装
- 我们选择版本 **ubuntu-22.04-live-server-amd64** 进行安装
- 创建虚拟机
  ![20241201221100](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221100.png)

  ![20241201221110](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221110.png)

- 选择对应的安装镜像文件
  ![20241201221125](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221125.png)

- 给虚拟机命名
  ![20241201221140](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221140.png)

- 指定磁盘
  ![20241201221151](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221151.png)

- 自定义硬件
  ![20241201221204](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221204.png)

  ![20241201221212](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221212.png)

- 完成虚拟机配置
  ![20241201221225](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221225.png)

- 运行虚拟机安装系统，选择语言
  ![20241201221239](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221239.png)

- 键盘配置
  ![20241201221250](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221250.png)

- 选择安装类型
  ![20241201221303](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221303.png)

- 网络先跳过
  ![20241201221313](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221313.png)

- 代理配置
  ![20241201221325](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221325.png)

- 配置存档镜像
  ![20241201221336](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221336.png)

- 引导存储配置
  ![20241201221349](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221349.png)

- 存储配置
  ![20241201221401](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221401.png)

  ![20241201221408](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221408.png)

- 用户配置，用户名liyou，密码123456
  ![20241201221420](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221420.png)

- SSH配置
  ![20241201221432](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221432.png)

- 查看全部配置，重启
  ![20241201221443](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221443.png)

- 输入用户名密码，登录系统
  ![20241201221454](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221454.png)

# 5. 虚拟机安装Windows Server
## 5.1 Windows Server 2022下载
- 百度网盘地址：[pan.baidu.com/share/init?surl=SRijXp23fjdpCQUeZSg2Vw&pwd=1234](https://pan.baidu.com/share/init?surl=SRijXp23fjdpCQUeZSg2Vw&pwd=1234)

## 5.2 Windows Server 2022安装
- Windows Server 2022分成Datacenter版本和Standard版本，Datacenter版本是微软服务器操作系统的最高级别版本，Standard版本是一个适用于物理或最低限度虚拟化环境的版本，我们这边选择安装Datacenter版本。
- 创建虚拟机
  ![20241201221604](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221604.png)

  ![20241201221615](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221615.png)

- 选择对应的安装镜像文件
  ![20241201221630](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221630.png)

- 输入注册码，用户名liyou，密码123456
  ![20241201221658](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221658.png)

- 给虚拟机命名
  ![20241201221711](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221711.png)

- 指定磁盘
  ![20241201221727](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221727.png)

- 自定义硬件
  ![20241201221739](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221739.png)

  ![20241201221746](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221746.png)

  ![20241201221753](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221753.png)

- 启动虚拟机安装系统
  ![20241201221805](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221805.png)

- 安装完成登录系统
  ![20241201221817](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221817.png)

# 6. 虚拟机网络
## 6.1 三种网络模式
- 虚拟机的网络配置可以有多种模式，其中 **桥接模式、NAT模式、仅主机模式** 是常见的几种。不同的虚拟机网络配置模式适用于不同的应用场景和需求，需要根据具体情况进行选择和配置。
  ![20241201221847](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221847.png)

  ![20241201221856](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221856.png)

## 6.2 三个虚拟交换机
- 安装完虚拟机之后，可以出现三个虚拟交换机
  - VMnet0：这是桥接模式下的虚拟交换机，虚拟机可以连接到VMnet0，并具有独立的IP地址，与主机和其他虚拟机进行通信。
  - VMnet1：这是仅主机模式下的虚拟交换机，虚拟机只能连接到VMnet1，不能与其他主机或外部网络进行通信。
  - VMnet8：这是NAT模式下的虚拟交换机，虚拟机通过VMnet8连接到一个共享的NAT网络，并能够访问外部网络资源。
- 虚拟网络编辑器，默认进入是两种模式
  ![20241201221934](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221934.png)

  ![20241201221944](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201221944.png)

- 点击更改设置改成管理员权限，可以手动添加桥接模式
  ![20241201222003](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222003.png)

  ![20241201222010](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222010.png)

  ![20241201222022](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222022.png)

- 进入宿主机操作系统可以看到三张虚拟网卡，和三种模式对应
  ![20241201222044](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222044.png)

- 如果将宿主机上的这两块虚拟网卡卸载了，可通过顶部选项栏-[编辑]-[虚拟网络编辑器]-[还原默认设置]，重新将虚拟网卡还原。

## 6.3 桥接模式配置
- 原理
  - 虚拟机都会连接到虚拟交换机VMnet0上，VMnet0通过虚拟网桥连接到宿主机网卡。
- 虚拟机IP地址与宿主机在同一个网段，宿主机能访问公网，虚拟机就能访问公网。
  - 虚拟机与宿主机以及与宿主机在同一个局域网下的主机之间都是可以相互访问的。
- 物理连接图
  ![20241201222132](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222132.png)

- 逻辑网络图
  ![20241201222147](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222147.png)

- 实际配置步骤
  1. 选中虚拟机右键，设置虚拟机的网络连接模式为桥接模式
    ![20241201222210](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222210.png)
    -=-=-=-=-=-=-=-=-=-=-=-=
    ![20241201222220](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222220.png)

  2. 查看当前宿主机网络ipconfig / all
    ![20241201222246](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222246.png)

  3. 启动虚拟机，登录系统，修改网络配置文件
    ```bash
    cd /etc/sysconfig/network-scripts
    ls
    ```
    -=-=-=-=-=-=-=-=-=-=-=-=
    ![20241201222316](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222316.png)

    ```bash
    vi ifcfg-ens33
    ```
    -=-=-=-=-=-=-=-=-=-=-=-=
    初始文件内容如下：
    ![20241201222345](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222345.png)
    -=-=-=-=-=-=-=-=-=-=-=-=
    修改后的内容如下：`BOOTPROTO=static`表示使用静态地址协议，`ONBOOT=yes`表示系统启动是激活网络
    ![20241201222414](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222414.png)
    -=-=-=-=-=-=-=-=-=-=-=-=
    修改完成需要重启虚拟机网卡
    ```bash
    systemctl restart network.service
    ```

  4. 验证网络
     - 虚拟机`ping 宿主机`，验证和宿主机通信是否ok
     - 虚拟机`ping www.baidu.com`，验证和公网通信是否ok
      ![20241201222530](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222530.png)

     - 宿主机ping虚拟机，验证和虚拟机通信是否ok
      ![20241201222545](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222545.png)

## 6.4 NAT模式配置
- 原理
  - 虚拟NAT设备及虚拟DHCP服务器连接到VMnet8虚拟交换机，虚拟机网卡和宿主机网卡VMnet8也连接到VMnet8虚拟交换机，虚拟NAT设备连接宿主机网卡。
  - 宿主机通过网卡VMnet8访问虚拟机，虚拟机通过虚拟NAT设备访问宿主机，宿主机所在局域网内的其他主机不能访问虚拟机。
  - 宿主机能访问公网，虚拟机就能访问公网，虚拟机不占用宿主机所在网络IP资源而是独立组成一个局域网络。
  - 宿主机的VMnet8网卡紧紧是用来和虚拟机进行通信的，禁用这张网卡，不影响虚拟机访问宿主机和公网，只影响宿主机访问虚拟机。
- 网络结构图
  ![20241201222630](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222630.png)

- 实际配置步骤
    1. 选中虚拟机右键，设置虚拟机的网络连接模式为NAT模式
      ![20241201222655](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222655.png)

      ![20241201222704](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222704.png)

    2. 查看NAT模式下的NAT设置和DHCP设置的参数
      ![20241201222721](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222721.png)

      ![20241201222727](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222727.png)

    3. 配置宿主机网卡VMnet8
      ![20241201222753](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222753.png)

    4. 启动虚拟机，登录系统，修改网络配置文件
      ```bash
      cd /etc/sysconfig/network-scripts
      ls
      ```
      ![20241201222823](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222823.png)

      ```bash
      vi ifcfg-ens33
      ```
      初始文件内容如下：
      ![20241201222848](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222848.png)

      修改后的内容如下：`BOOTPROTO=static`表示使用静态地址协议，`ONBOOT=yes`表示系统启动是激活网络
      ![20241201222914](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201222914.png)

      修改完成需要重启虚拟机网卡
      ```bash
      systemctl restart network.service
      ```

    5. 验证网络
       - 虚拟机`ping 宿主机`，验证和宿主机通信是否ok
       - 虚拟机`ping www.baidu.com`，验证和公网通信是否ok
        ![20241201223001](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223001.png)

       - 宿主机ping虚拟机，验证和虚拟机通信是否ok
        ![20241201223029](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223029.png)

## 6.5 仅主机模式配置
- 原理
  - 仅主机模式就是NAT模式的基础上去掉了虚拟NAT设备，使虚拟机只能与宿主机通信，不能访问公网。
- 网络结构图
  ![20241201223056](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223056.png)

- 实际配置步骤
    1. 选中虚拟机右键，设置虚拟机的网络连接模式为仅主机模式
      ![20241201223116](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223116.png)

      ![20241201223123](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223123.png)

    2. 查看仅主机模式下的DHCP设置参数
      ![20241201223138](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223138.png)

    3. 启动虚拟机，登录系统，修改网络配置文件
      ```bash
      cd /etc/sysconfig/network-scripts
      ls
      ```
      ![20241201223205](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223205.png)

      ```bash
      vi ifcfg-ens33
      ```
      
      初始文件内容如下：
      ![20241201223233](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223233.png)

      修改后的内容如下：`BOOTPROTO=static`表示使用静态地址协议，`ONBOOT=yes`表示系统启动是激活网络
      ![20241201223252](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223252.png)

      修改完成需要重启虚拟机网卡
      ```bash
      systemctl restart network.service
      ```

    4. 验证网络
       - 虚拟机`ping 宿主机`，验证和宿主机通信是否ok
       - 虚拟机`ping www.baidu.com`，验证和公网通信是否ok
        ![20241201223419](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223419.png)

       - 宿主机ping虚拟机，验证和虚拟机通信是否ok
        ![20241201223434](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223434.png)

# 7. 克隆，快照
## 7.1 克隆是什么
- 在VMware Workstation Pro中，克隆功能可以将一个虚拟机复制成多个虚拟机，而不需要重新安装虚拟机。这个过程会生成一个和原始虚拟机完全独立的一个拷贝，它不和原始虚拟机共享任何资源，可以脱离原始虚拟机独立存在。

## 7.2 创建克隆的步骤
- VMware Workstation Pro克隆功能操作步骤如下：
  - 关闭虚拟机中的所有软件并关闭虚拟机。
  - 在已有的虚拟机下右键点击“管理” -> “克隆”。
  - 选择完整克隆或者创建链接克隆（建议采用第二种克隆方式）。
  - 点击下一步直到去修改虚拟机信息。
  - 等待克隆完成后关闭操作窗口。

    ![20241201223517](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223517.png)

    ![20241201223527](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223527.png)

    ![20241201223536](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223536.png)

    ![20241201223548](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223548.png)

    ![20241201223557](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223557.png)

    ![20241201223605](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223605.png)

    ![20241201223613](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223613.png)

## 7.3 快照是什么
- VMware Workstation Pro快照是虚拟机运行过程中的一个状态，它保存了虚拟机在某一状态时的所有参数、配置等信息。利用快照功能可以实现虚拟机某一特定状态的保存与恢复，这对于虚拟机的调试与配置有极大的帮助。

## 7.4 创建快照的步骤
- 创建VMware Workstation Pro快照的步骤如下：
  - 打开VMware Workstation Pro软件，并开启虚拟机。
  - 选中已经开机的虚拟机，右键单击，在弹出的下拉菜单中选择“快照”。
  - 在弹出的下拉菜单中选择“拍摄快照”。
  - 在弹出的拍摄快照窗口中，输入快照名称及快照描述，完成后点击“拍摄快照”。

- 使用VMware Workstation Pro快照的方法如下：
  - 打开VMware Workstation Pro软件，并开启需要使用快照的虚拟机。
  - 在虚拟机菜单中，选择“快照”选项，然后点击“快照管理”。
  - 在快照管理界面中，可以看到已经创建的所有快照，以及它们之间的层次关系。
  - 选择需要恢复的快照，并点击“转到”按钮。
  - 确认恢复到该快照的操作，并点击“是”按钮。
  - 等待虚拟机恢复到指定快照的状态。

    ![20241201223705](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223705.png)

    ![20241201223714](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223714.png)

    ![20241201223727](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223727.png)

    ![20241201223735](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20241201223735.png)

## 7.5 快照和克隆的区别
- 目的不同：快照旨在创建虚拟机的备份，保存虚拟机在某个特定时间点的状态；克隆则是创建一个全新的虚拟机副本，独立于原始虚拟机。
- 对原始虚拟机的依赖：快照是基于原始虚拟机创建的，它只记录原始虚拟机在创建快照时的状态，并通过后续的增量变化来跟踪更新。因此，在恢复快照时，依赖于原始虚拟机的存在；克隆则是基于原始虚拟机创建一个独立的副本，不再依赖于原始虚拟机。
- 对存储空间的使用：快照通常采用增量方式存储，只记录与原始虚拟机状态变化相关的差异数据，因此可以节省存储空间；克隆则是创建一个完全独立的虚拟机副本，占用与原始虚拟机相同的存储空间。
- 是否独立：快照不能脱离原始虚拟机独立运行，克隆是部分脱离原始虚拟机，完整克隆是完全脱离原始虚拟机。
- 能否同时使用：快照不能原始虚拟机同时使用。克隆的虚拟机可以和原始虚拟机同时使用。

{% endraw %}