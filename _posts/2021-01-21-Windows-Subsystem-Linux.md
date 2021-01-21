---
legout: post
title: Windows下创建子系统
categories: 文章
tags: 教程

---

## 什么是WSL

来自[百度百科](https://baike.baidu.com/item/wsl/20359185)的回答

> Windows Subsystem for Linux（简称 WSL）是一个为在 Windows 10 上能够原生运行 Linux 二进制可执行文件（ELF 格式）的兼容层。它是由微软与 Canonical 公司合作开发，目标是使纯正的 Ubuntu 14.04 "Trusty Tahr"映像能下载和解压到用户的本地计算机，并且映像内的工具和实用工具能在此子系统上原生运行。



### 相对于虚拟机的优势

相比于 VMware 等虚拟机，WSL 占用内存和 CPU 资源更少，在 WSL 上运行软件的消耗和直接在 Windows 上差不多。而且，Windows 下可以直接访问 WSL 的环境。



### 相对于多系统的优势

假如需要重启 Linux 系统，WSL 只需要把软件关掉重开即可。同时，相较于多系统，文件交互也更为简单。



## 安装



### 手动安装步骤



#### 启用适用于 Linux 的 Windows 子系统

在控制面板中选择程序，在程序和功能中可以看到[启动或关闭 Windows 功能」中勾选「适用于 Linux 的 Windows 子系统] 。或者可以使用命令的方式打开。

**以管理员身份打开 PowerShell 并运行：**

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

<img src="https://i.loli.net/2021/01/21/IeRxVoPWAH2cZ1k.png" alt="image-20210121151303582" style="zoom:80%;" />

#### 检查运行 WSL 2 的要求

若要更新到 WSL 2，需要运行 Windows 10。

- 对于 x64 系统：**版本 1903** 或更高版本，采用 **内部版本 18362** 或更高版本。
- 对于 ARM64 系统：**版本 2004** 或更高版本，采用 **内部版本 19041** 或更高版本。
- 低于 18362 的版本不支持 WSL 2。 使用 [Windows Update 助手](https://www.microsoft.com/software-download/windows10)更新 Windows 版本。

若要检查 Windows 版本及内部版本号，选择 Windows 徽标键 + R，然后键入“winver”，选择“确定”。 

如果运行的是 Windows 10 版本1903 或 1909， 内部版本号必须是 18362.1049+ 或 18363.1049+，次要内部版本号需要高于 .1049。

![image-20210121152015640](https://i.loli.net/2021/01/21/iScRJ6HPZX4gTnr.png)

#### 启用虚拟机功能

安装 WSL 2 之前，必须启用“虚拟机平台”可选功能。

以管理员身份打开 PowerShell 并运行：

**重新启动** 计算机，以完成 WSL 安装并更新到 WSL 2。

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

#### 下载 Linux 内核更新包

1. 下载最新包：

- [wsl_update_x64](https://spvian.lanzous.com/i4bhHknavpe)

- [wsl_update_arm64](https://spvian.lanzous.com/i5FZJknb3xa)

如果不确定自己计算机的类型，可以打开控制面板查看

![image-20210121153713299](https://i.loli.net/2021/01/21/eoDMLh4iWAU9Rlw.png)

2. 运行上一步中下载的更新包

   双击以运行 - 系统将提示你提供提升的权限，选择“是”以批准此安装。

#### 将 WSL 2 设置为默认版本

打开 PowerShell，然后在安装新的 Linux 发行版时运行以下命令，将 WSL 2 设置为默认版本：

```powershell
wsl --set-default-version 2
```

####  安装所选的 Linux 分发

单击以下链接会打开每个分发版的 Microsoft Store 页面：

- [Ubuntu 16.04 LTS](https://www.microsoft.com/store/apps/9pjn388hp8c9)
- [Ubuntu 18.04 LTS](https://www.microsoft.com/store/apps/9N9TNGVNDL3Q)
- [Ubuntu 20.04 LTS](https://www.microsoft.com/store/apps/9n6svws3rx71)
- [openSUSE Leap 15.1](https://www.microsoft.com/store/apps/9NJFZK00FGKV)
- [SUSE Linux Enterprise Server 12 SP5](https://www.microsoft.com/store/apps/9MZ3D1TRP8T1)
- [SUSE Linux Enterprise Server 15 SP1](https://www.microsoft.com/store/apps/9PN498VPMF3Z)
- [Kali Linux](https://www.microsoft.com/store/apps/9PKR34TNCV07)
- [Debian GNU/Linux](https://www.microsoft.com/store/apps/9MSVKQC78PK6)
- [Fedora Remix for WSL](https://www.microsoft.com/store/apps/9n6gdm4k2hnc)
- [Pengwin](https://www.microsoft.com/store/apps/9NV1GV1PXZ6P)
- [Pengwin Enterprise](https://www.microsoft.com/store/apps/9N8LP0X93VCP)
- [Alpine WSL](https://www.microsoft.com/store/apps/9p804crf0395)

在分发版的页面中，选择“获取”。

![image-20210121154239160](https://i.loli.net/2021/01/21/Cgl6T4feQJWhoOm.png)

首次启动新安装的 Linux 分发版时，将打开一个控制台窗口，系统会要求你等待一分钟或两分钟，以便文件解压缩并存储到电脑上。 未来的所有启动时间应不到一秒。

![image-20210121154608425](https://i.loli.net/2021/01/21/s9xi3MRGWvODUgo.png)

输入用户名和密码后，会提示安装成功。此帐户将被视为 Linux 管理员，能够运行 `sudo` (Super User Do) 管理命令。

![image-20210121154735485](https://i.loli.net/2021/01/21/Svu2eaj8VYBzQL1.png)

如果出现Enter new UNIX username: user 参考的对象类型不支持尝试的操作。运行 NoLsp.exe。

使用管理员身份启动PowerShell

- [NoLsp.exe](https://spvian.lanzous.com/iMvqNknciud)

- 输入D:\NoLsp.exe C:\windows\system32\wsl.exe（这里注意你自己下载的NoLsp.exe位置）

##### 重置 Linux 密码

若要更改密码，请打开 Linux 分发版（例如 Ubuntu）并输入以下命令：`passwd`

系统会要求你输入当前密码，然后要求输入新密码，之后再确认新密码。

##### 忘记密码

如果忘记了 Linux 分发版的密码：

1. 请打开 PowerShell，并使用以下命令进入默认 WSL 分发版的根目录：`wsl -u root`

   > 如果需要在非默认分发版中更新忘记的密码，请使用命令：`wsl -d Debian -u root`，并将 `Debian` 替换为目标分发版的名称。

2. 在 PowerShell 内的根级别打开 WSL 发行版后，可使用此命令更新密码：`passwd <WSLUsername>`，其中 `<WSLUsername>` 是 DISTRO 中帐户的用户名，而你忘记了它的密码。

3. 系统将提示你输入新的 UNIX 密码，然后确认该密码。 在被告知密码已成功更新后，请使用以下命令在 PowerShell 内关闭 WSL：`exit`

## 切换源并更新

```bash
sudo sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
sudo apt update -y
sudo apt upgrade -y
```

## 配置图形界面

如果要运行一些带图形界面的程序，WSL 原生是没有支持 GUI 的。这里通过 XServer 实现可视化操作，用到的软件是`VcXsrv`。

### 实现原理

1. VcXsrv 启动 Xserver 服务用于监听；
2. WSL 启动程序后把界面数据发送给 Xserver；
3. Xserver 接收到数据进行绘制，于是在 Win 下看到图形界面。

### 下载并安装 VcXsrv

[vcxsrv-64.1.20.8.1.installer](https://spvian.lanzous.com/i5hSjkndp6h)  

一路 next 即可。第一步是选择组件，默认是全部安装；第二步是安装路径。

### 启动 VcXsrv

开始菜单里现在出现了一个XLaunch，运行它

下面是我的配置

![image-20210121161144647](https://i.loli.net/2021/01/21/hSHLq2QZtXegEVa.png)

![image-20210121161303808](https://i.loli.net/2021/01/21/Pm2j3JTdWs6oIOY.png)

![image-20210121161316674](https://i.loli.net/2021/01/21/4iFhnHsqWKS1ulG.png)

![image-20210121161540108](https://i.loli.net/2021/01/21/5XxowyRqZBtTfhQ.png)

#### 配置 DISPLAY

运行如下代码：

```bash
echo "export DISPLAY=ip:0.0" >> ~/.bashrc  //ip是指window下的ip，可以使用ipconfig查看

//更新配置
source ~/.bashrc
```

### 检验

运行下述代码，安装 firefox 检验效果：

```bash
sudo apt install firefox
```

随后直接运行下述代码即可查看效果。**前提使在root账户下**

```bash
firefox
```

![image-20210121162343948](https://i.loli.net/2021/01/21/miFpMrqsYDH18tz.png)

### 解决中文乱码问题

运行下述代码，成功解决。现在可以正常显示中文了。

```
sudo apt install fonts-noto-cjk
```

![image-20210121162628693](https://i.loli.net/2021/01/21/9wJvZN81pSxVEKG.png)

### 修改默认语言环境为中文（可选）

安装中文语言包

```bash
sudo apt install language-pack-zh-hans language-pack-zh-hans-base
```

设置本地化环境变量

```bash
echo "LANG=zh_CN.UTF-8" >> ~/.profile
source ~/.profile
```

### 安装桌面

这里选择`xfce4`桌面，它的优点是轻量、美观、占用系统资源少。

```bash
sudo apt install xfce4 dbus-x11
```

完成后，执行下面这段代码就可以看到桌面的图形界面了。

```bash
xfce4-session  //在root账户下运行
```

![image-20210121163949840](https://i.loli.net/2021/01/21/vLZKFINrtMPJASu.png)

### 中文输入法

```bash
sudo apt install fcitx fcitx-pinyin
echo -e "export XMODIFIERS=@im=fcitx\nexport GTK_IM_MODULE=fcitx\nexport QT_IM_MODULE=fcitx\n" >> .profile
```

软件包已装，在应用程序 - 设置 - 会话与启动 - 应用程序自启动，添加 /usr/bin/fcitx。

## 配置工作环境

这里以安装、配置 vscode 为例。

```bash
sudo add-apt-repository ppa:ubuntu-desktop/ubuntu-make
sudo apt update
sudo apt install ubuntu-make
```

### 安装 vscode

```bash
sudo umake ide visual-studio-code
```

执行后分别会让你选择安装地址，然后输入`a`确认。稍等片刻就安装好啦。重启 bash，重新进入 xfce 桌面，在应用程序-开发里就可以找到安装好的 vscode 啦。