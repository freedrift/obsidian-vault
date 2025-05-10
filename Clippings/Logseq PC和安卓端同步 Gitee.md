---
title: Logseq电脑端+安卓端同步gitee或github - overfit.cn
source: https://avoid.overfit.cn/post/621f5ed9f0014e45be93c8856ff85dee
author: 
published: 
created: 2025-05-10
description:
---
## 1.电脑端

mac和windows是同样的同步方式，此处以gitee为例，因为国内gitee更稳定。

### 1.1 新建仓库

首先在gitee创建自己的仓库，注意改为私有，别被别人知道了，如下图所示。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f630b40b574243e9a8f98b0bc9306134.png)

### 1.2 克隆项目，生成秘钥

使用ssh的方式，并且在电脑端生成相应的秘钥和公钥，不管是mac端还是windows端，都是在当前用户目录下的.ssh文档中生成的，然后根据提示，配置到网页端即可。  
![点击克隆](https://img-blog.csdnimg.cn/direct/a2f43144ec364ac69f4f3c41cd592669.png) ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/0e30504c3f5846fe8a153fee88fc6ec8.png)  
克隆完毕后会出现一个空的，只带隐藏文件

```
.git
```

的目录。

### 1.3 添加图谱，选择文件目录，我是原本就有笔记，所以会如下所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/fecc1c0eb9b74546a31580d504cf9536.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/6ce74e48360f414db5c450de8585f197.png)

### 1.4 下载脚本文件

打开自动提交的脚本网址 ，下载文件，在hook目录下，将

```
post-commit
```

和

```
pre-commit
```

复制粘贴到本地的

```
.git/hooks
```

目录下。

### 1.5赋权限 （windows可跳过）

如果是mac需要执行命令，将其变成可执行文件。

```
chmod +x ./pre-commit &&chmod +x ./post-commit
```

### 1.6 修改脚本命令

如果是 gitee 记得把

```
post-commit
```

的命令改为

```
git push origin master
```

因为gitee默认的分支就是master，如果是github则不需要动

### 1.7 logseq设置同步

打开 Logseq > 设置 > 版本控制 > 切换“启用 Git 自动提交”选项，可以更改自动提交的秒数，比如10s。

### 1.8 重启应用

10s后如果没有报错说明成功了，失败了请根据报错再看看是否与教程不同。

## 2.安卓端

### 2.1 大致流程

大致的流程是这样的，下载一个模拟linux环境的软件，安装git，ssh，秘钥，克隆仓库地址，logseq添加文件夹，最后通过脚本手动拉取或者推送仓库，实现手机端同步。

### 2.2 下载软件安装

首先我们需要做一些准备工作，下载这两个软件。  
termux下载链接

termux-widget下载链接

termux主要是模拟linux环境的软件，可以下载一系列的小工具。  
termux-widget主要是用来运行termux上的脚本，而不用我们一遍遍的敲代码拉取，推送。

### 2.3 更新、下载包

在termux界面中，输入

```
apt update && apt upgrade
```

，更新版本。  
安装ssh，

```
pkg install openssh
```

安装git

```
pkg install git
```

### 2.4 设置git权限，ssh

设置你的git用户名和邮箱，模板如下。

```
git config --global user.name "xxx"git config --global user.email xxxx@example.com
```

接着生成ssh秘钥

```
ssh-keygen -t rsa
```

查看公钥

```
cat ~/.ssh/id_rsa.pub
```

在手机上将这一串公钥复制到gitee的设置界面中。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/3db9ee41a58645ba9ebe6334faf064a5.png)

### 2.5 赋予文件访问权限

一般情况下，我们进入的目录会在~下，如果没有，也可以通过

```
cd ~/
```

命令进入。  
接着输入

```
termux-setup-storage
```

，点确定，让termux可以访问我们手机上的文件。

### 2.6 拉仓库

接着进入 进入shared目录，输入

```
cd ~/storage/shared/
```

。  
我们可以查看之前的地址，拉取仓库。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a5761c77df4348669454965832460ece.png)  
然后进入

```
my-logseq
```

目录，检查git是否能正常工作，比如输入

```
git status
```

。  
一般是有报错的，需要输入

```
git config --global --add safe.directory ~/storage/shared/my-logseq
```

以在该文件夹上启用 Git 操作。(你换成自己的地址)。  
它可能会报红框里的错误，复制执行下即可。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/66e3dc0514d747b38db227350da00b21.png)  
再次尝试下git命令应该就能成功了。

### 2.7 添加图谱

这个是logseq里的叫法，其实就是按照电脑端的步骤，将文件添加到logseq中。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/0431419c28d949bbacdb068e4b64a5f4.png)  
在这个文件夹下就能找到了，界面显示的内容就是shared文件夹下的。

### 2.8 添加脚本

我们进入

```
~/
```

目录， 创建

```
.shortcuts
```

文件夹。

```
cd .shortcuts
```

进入目录。

安装vim包

```
pkg install vim
```

使用vim命令，命名为

```
pull-note.sh
```

，

```
vim pull-note.sh
```

在里面编写下面内容。

```
#!/usr/bin/bashsource ~/../usr/bin/source-ssh-agentcd ~/storage/shared/my-logseq   #这里换成你的目录git pull
```

接着再编写提交脚本

```
vim push-note.sh
```
```
#!/usr/bin/bashsource ~/../usr/bin/source-ssh-agentcd ~/storage/shared/my-logseq   #这里换成你的目录gitadd-Agit commit -m"sync from android"git push
```

### 2.9 添加快捷方式

保存退出后，就要用到我们之前安装的

```
Termux Widget
```

软件了，它提供了一种快捷方式在手机桌面上调用脚本。

我用的是华为手机，两指往中间拉，弹出下面界面，点击服务卡片。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/8725e131f0a44b6c811b80679a7522e5.png)  
拉到最底下，窗口小工具。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/55e49c9626ce4a7ba5994c6086bc352c.png)  
找到Termux Widget，添加即可。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/04e50c92f39249c0a6a830ee1aadd58e.png)  
你会发现，在手机的最后，有下面的内容显示，就成功了。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/33a9a6969be044bc9beb33fd8917a218.png)  
这样，每次写完笔记就push一下，有笔记没更新就pull下就好。

注意，实际使用我发现，单单点击命令是无效的，还得在进termux才能让命令完全执行，所以我们可以在权限管理中，打开termux-widget的允许弹窗权限，它会打开termux执行命令，有无报错也会一目了然。

如果发现同步失败的，可以进入Termux 中查看，会有相应的报错信息的，说白了，这就是一个简易的调用命令的方式，不用想的那么复杂。

## 3.使用提升

### 3.1 冲突解决

在使用过程中，我经常会发现，手机端版本落后，导致最新笔记pull不下来，考虑到，大多情况下只会在电脑端记笔记，手机端记的较少，基本上读的较多，所以我们完全可以舍弃原本的提交，拉取远程最新笔记。

所以这就需要再新建一个脚本了。  
手机端进入

```
~/.shortcut
```

目录，新建

```
use-remote.sh
```

文件，加入下面内容

```
#!/usr/bin/bashcd ~/storage/shared/my-logseqgit fetch --allgit reset --hard origin/master
```

该命令先拉取最新的分支，然后将笔记强制设置为远程库的最新提交，如果说手机端笔记记了不少，那建议备份下，之后再做补充。

可能有同学问可以使用冲突合并啊，但有时候在手机端上确实不好操作，并不知道到底差异在哪里，这种做法在实际使用上更为靠谱，简单。

最后在Termux Widget快捷方式处，点击刷新，即可解决冲突。

ps：如果电脑端有冲突，比手机端解决来的方便很多，如果是手机端提交的代码比较重要，电脑端落后了，也可以使用上面的命令来解决。

### 3.2 插件

在电脑端使用的过程中，建议装一个git插件，有时候自动保存或者拉笔记没那么及时，可以在插件市场上安装git，使用也很方便，如下所示。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d385e0ec8a1b440f81552e609866a470.png)  
pull拉取最新提交，commit则是提交。

如果遇到什么问题可以给我留言，有时间我会回复的~

> 你选择记录什么，你才可能成为什么。记录、思考、总结多了之后，会慢慢形成一套自己的东西，也就逐渐沉淀为自己的价值观。

标签：

---

本文转载自: [https://blog.csdn.net/weixin\_44353507/article/details/136473961](https://blog.csdn.net/weixin_44353507/article/details/136473961)  
版权归原作者 **机智的爆爆哥** 所有， 如有侵权，请联系我们删除。