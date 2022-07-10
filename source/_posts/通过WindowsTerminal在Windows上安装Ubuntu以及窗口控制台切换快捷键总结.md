---
title: 通过WindowsTerminal在Windows上安装Ubuntu以及窗口控制台切换快捷键总结
top: false
cover: false
toc: true
mathjax: true
date: 2021-10-27 21:47:46
password:
summary:
tags: 利器库
categories: 工具方法 
---
通过WindowsTerminal在Windows上安装Ubuntu以及窗口控制台切换快捷键总结
### 一、安装教程
Linux在程序员中属于高逼格的存在，当然安装了图形界面的程序员要减分，毕竟你需要用鼠标了！程序员的桌面不能比谁的更酷更炫，要比谁的屏幕多，桌面颜色少！
Windows向来是没有这种光圈的，因为它的目标是白痴用户。当然它的```cmd```命令从始至终充满了科技的味道，一直都是黑白两色。
为什么终端这么重要呢？对于一个程序员来说，没有了命令行相当于少了半条手臂。这条手臂到底美不美，壮不壮，要看命令行终端好不好用。
在远程连接其他```Linux```的时候，我通常使用```Xshell```，就因为它长得比较漂亮耐看。在```Windows```上，就可以安装```Windows Terminal```。有点类似于```MacOS```上的```iTerm```，可以说是```Windows```下最舒适的终端。
#### ①  打开"应用商店"
安装```Windows Terminal```需要从"应用商店"去获取，就是下面这个按钮。
![应用商店](https://i.loli.net/2021/11/06/IvPT3LckYN8Z4eg.png)
#### ② 搜索Windows Terminal
在搜索框里搜索```Windows Terminal```，即可找到这个软件。比较人性化的一点是，它不像Mac的应用商店一样，需要你先准备一个账号。WT不需要登录即可获取。如果你的页面一直打转也不要紧，关闭重新打开几次就好了。由于众所周知的原因，国外网站就没有几个不转圈的。
![Windows Terminal安装包](https://i.loli.net/2021/11/06/Z3HP7tmKwQBDloI.png)
#### ③ 安装Ubuntu子系统
此时，我们仅仅安装了一个命令行终端而已，离我们扔掉Linux的目标还差上一小节。别担心，下面就介绍怎么在Windows上安装Ubuntu。
- 首先，如下图，在控制面板，找到程序选项，点击  “启用或关闭Windows功能”。
![启用或关闭Windows功能](https://pic.imgdb.cn/item/618622452ab3f51d91ae90b0.png)
- 其次，从应用商店安装Ubuntu系统，这个系统将会以软件的形式存在。我这里选择的是LTS版本，可以看到给它打分的人并不多，可能大多数都是像我一样没有微软账号的游客。
![下载Ubuntu系统](https://pic.imgdb.cn/item/618622972ab3f51d91af1c34.png)
- 然后，查看安装镜像
此时，神奇的事情发生了。在我们的```Windows Terminal```右上角，有一个向下的箭头，点击它，就可以看到刚刚安装的```Ubuntu```。
![选择Ubuntu镜像](https://pic.imgdb.cn/item/618623392ab3f51d91b03585.png)
进入Linux系统之后，我们就可以像配置一个普通Linux一样配置这台机器。把ubuntu的软件源给换掉。编辑/etc/apt/sources.list文件，把它的内容换成下面的源。
vim /etc/apt/sources.list
```vim
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
 ```
 安装最好用的oh-my-zsh。先用sudo apt install zsh安装shell终端，然后运行下面的命令
 ```shell
 sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
 ```
### 二、基本快捷键

| 快捷键           | 用处                 |
| ---------------- | -------------------- |
| ```Ctrl +Alt+数字 ```  | 切换打开的控制台     |
| ``` Ctrl+Shift+数字 ``` | 打开新的控制台       |
| ``` Shift+Alt++/= ```   | 横向打开新控制台     |
| ``` Shift+Alt+_/- ```   | 纵向打开新控制台     |
| ``` Shift+Alt+方向键 ```| 控制控制台尺寸       |
| ``` Alt+方向键 ```      | 在本标签内切换控制台 |
|         ``` Ctrl+Shift+w  ```       |        关闭当前控制台              |
