---
title: Markdown相关基本语法
top: false
cover: false
toc: true
mathjax: true
date: 2021-10-17 11:05:04
password:
summary:
tags: 专业
categories: 语言(Markdown)
---
Markdown数学公式语法
### 行内与独行
- 行内公式：将公式插入到本行内，符号：$公式内容$，如：```$xyz$```
- 独行公式：将公式插入到新的一行内，并且居中，符号：```$$公式内容$$```，如：```$$xyz$$```
### 上标、下标与组合
- 上标符号，符号：^，如：```$x^4$```
- 下标符号，符号：_，如：```$x_1$```
- 组合符号，符号：{}，如：```${16}_{8}O{2+}_{2}$```
### 汉字、字体与格式
-  汉字形式，符号：```\mbox{}```，如：```$V_{\mbox{初始}}$```
-  字体控制，符号：```\displaystyle```，如：```$\displaystyle \frac{x+y}{y+z}$```
- 下划线符号，符号：```\underline```，如：```$\underline{x+y}$```
- 标签，符号\tag{数字}，如：```$\tag{11}$```
- 上大括号，符号：```\overbrace{算式}```，如：```$\overbrace{a+b+c+d}^{2.0}$```
- 下大括号，符号：```\underbrace{算式}```，如：```$a+\underbrace{b+c}_{1.0}+d$```
- 上位符号，符号：\stacrel{上位符号}{基位符号}，如：```$\vec{x}\stackrel{\mathrm{def}}{=}{x_1,\dots,x_n}$```


https://www.jianshu.com/p/e74eb43960a1
