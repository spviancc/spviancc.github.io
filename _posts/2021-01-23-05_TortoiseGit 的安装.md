---
title: ToroiseGit的安装 
tags: 教程
---

## TortoiseGit



### 简介

git 图形化软件，与 TortiseSVN 是同门。TortoiseGit 是一个Git 版本控制客户端，作为 Microsoft Windows
的外壳扩展实现。一个新的[版本管理](https://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=54528924&ss_c=ssc.citiao.link)工具 Git，它本来是
Linux 下的基于Linux 内核的版本控制工具。



### 下载

这里提供了压缩包，压缩包中有 TortoiseGit 安装包和它的中文语言安装包 TortoiseGit-LanguagePack

点击[下载](https://spvian.lanzous.com/iucnnkphloh)



### 安装

因为 TortoiseGit 只是一个程序壳,必须依赖一个 Git Core,所以安装前请确定已完成 git 安装和配置



#### 先安装 TortoiseGit

双击运行，然后一直 next 直到出现下面那张图，说明安装成功了

![image-20210122222542523](https://i.loli.net/2021/01/22/SQFdqbo6957RDmN.png)



#### 再安装语言包

双击运行，然后一直 next 直到出现，记得勾选下面的选项，就不用再去设置里更改了

![image-20210122222738966](https://i.loli.net/2021/01/22/CMA1nlQwOgtszj5.png)



如果忘记勾选可以去Settings中修改

![image-20210122223349598](https://i.loli.net/2021/01/22/DiLSZBC67pIMw9G.png)

![image-20210122223547883](https://i.loli.net/2021/01/22/TkWj4ozHp8Jm5I6.png)



### 配置



#### 基本配置

我是因为之前有用密钥使用过 git， 所以这里没有配置密码

```json
[user]
　　name =   //注册用的账号
　　email = 	//注册用的联系邮箱
　　signingkey = //注册用的密码
```

![image-20210122224117641](https://i.loli.net/2021/01/22/iznrST4JqPCwmav.png)



#### 密钥配置

在 TortoiseGit 中选择 PuTTYgen 后，点击 Generate 来生成密钥

- 注意：生成时鼠标要不停划过进度条，不然进度条会一直不动

![image-20210122223349598](https://i.loli.net/2021/01/22/DiLSZBC67pIMw9G.png)

![image-20210122225031485](https://i.loli.net/2021/01/22/8jliwRK7hxsQDUE.png)

记住保存私钥的位置，很重要

这里以 GitHub 为例，复制公钥到 GitHub 上配置 SSH Keys

![image-20210122230143452](https://i.loli.net/2021/01/22/kivU65bVpnDBAft.png)

![image-20210122230031855](https://i.loli.net/2021/01/22/lacwxyChzvX6nro.png)

![image-20210122230416788](https://i.loli.net/2021/01/22/Gs2DqgjYHpFZv7R.png)

在 TortoiseGit 中选择 Pageant ，添加保存的私钥。默认不显示画面，会有个小图标在右下角。

![image-20210122230900273](https://i.loli.net/2021/01/22/zH5Rx1jtge3odm7.png)

到此为止算是配置完成了