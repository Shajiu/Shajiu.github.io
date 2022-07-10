---
title: git推拉文件步骤
top: false
cover: false
toc: true
mathjax: true
date: 2021-11-23 14:51:08
password:
summary:
tags:  个人提升
categories:   个人提升
---
#### 1、快速实现远程上传
- 1、初始化
``` Git
git init
 ```
- 2、添加项目
``` Git
git add .
 ```
- 3、提交注释
``` Git
git commit -m "first commit"
 ```
- 4、链接远端链接
``` Git
git remote add origin https://gitee.com/guoaibin/build-nginx.git
 ```
- 5、向源端推送
``` Git
git push -u origin master
 ```
#### 2、解决问题措施
- 1、如果第四步出现错误(fatal: remote origin already exists.)，则先删除远程 Git 仓库。
``` Git
git remote rm origin
 ```
- 2、再添加远程 Git 仓库。
``` Git
git remote add origin https://gitee.com/guoaibin/build-nginx.git
 ```
- 3、 获取远程代码并与本地一致。
``` Git
git pull --rebase origin master
 ```
- 4、不慎多次提交后出现 (master|REBASE 1/2)。
``` Git
git rebase --abort
 ```
- 5 把远程分支上的内容都拉取到本地。
 ``` Git
 git pull origin develop(远程分支名称)
 ```

- 6 把本地的master 仓库名称修改为远端的 main (Git 在 push 的时候报的错误：error: src refspec xxx does not match any / error: failed to push some refs to ....)
``` Git
git branch -m master main
 ```

- 7 git上查看冲突文件的具体信息
``` Git
git status
  ```
- 8 git看到分支的合并情况
``` Git
git log --graph --pretty=oneline --abbrev-commit
 ```
- 9 切换分支
``` Git
git switch master
 ```
- 10 在本地分支合并
``` Git
git merge master
 ```
