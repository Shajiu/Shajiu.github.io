---
title: Windows下查看某个端口被谁占用并且关闭该端口
top: false
cover: false
toc: true
mathjax: true
date: 2021-11-23 15:38:50
password:
summary:
tags: 利器库
categories: 工具收录
---
Windows下查看某个端口被谁占用并且关闭该端口
##### 1、打开命令窗口(以管理员身份运行)
``` Windows
开始—->运行—->cmd，或者是 window+R 组合键，调出命令窗口。
 ```
![以管理员方式打开窗口](https://i.loli.net/2021/11/23/9eUt3D57EBqnJhd.jpg)
 #### 2、查找所有运行的端口
  输入如下命令:
 ``` Windows
 netstat -ano
  ```
  ![列出所有端口的使用情况](https://i.loli.net/2021/11/23/ho2afFgHCiwmtLx.jpg)
  #### 3、查看被占用端口对应的 PID
  输入如下命令:
  ``` Windows
  netstat -aon|findstr "9091"
  ```
  [![回车执行该命令，最后一位数字就是 PID, 这里是19416](https://s6.jpg.cm/2021/11/23/IZlgk5.jpg)](https://imagelol.com/image/IZlgk5)
  #### 4、查看指定 PID 的进程
  输入如下命令:
``` Windows
tasklist|findstr "19416"
 ```
  [![查看指定 PID 的进程](https://s6.jpg.cm/2021/11/23/IZlhPu.jpg)](https://imagelol.com/image/IZlhPu)
  此时可以看出端口号为“19416”的程序被 java.exe占用。
  #### 5、结束进程
强制（/F参数）杀死 pid 为 19416 的所有进程包括子进程（/T参数）：
输入如下命令:
输入如下命令:
```Windows
taskkill /T /F /PID 19416
 ```
 [![已终止进程](https://s6.jpg.cm/2021/11/23/IZ6PBW.jpg)](https://imagelol.com/image/IZ6PBW)
