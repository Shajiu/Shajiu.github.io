---
title: Matplotlib绘制靓丽的LOGO
top: false
cover: false
toc: true
mathjax: true
date: 2022-01-15 16:31:32
password:
summary:
tags: 利器库
categories: 工具收录
---
#### 背景介绍
&emsp;Matplotlib 作为一个基础必备且功能强大的绘图库，很多其他功能库的开发也有不少是建立在Matplotlib的基础上的。今天为了记录学习Matplotlib，本人通过Matplotlib绘制属于自己的定制版LOGO，严格来说，这并不完全是一个 LOGO，可以称之为 “文字轮廓图”，这个效果我觉得还是蛮不错的。下面咱们来看下这个是怎么做出来的。
#### 绘制过程
这个图的绘制，只需要用到基础的 numpy 和 matplotlib 库，首先引入这些 Python 库：
``` Python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.patheffects import Stroke
 ```
由于需要显示中文，这里还需要设置下字体的显示。
``` python
plt.rcParams["font.sans-serif"] = ["SimHei"]  # 设置字体、解决 matplotlib 显示中文问题
plt.rcParams["axes.unicode_minus"] = False  # 正常显示负号 解决 matplotlib 显示中文问题、解决保存图像是负号 '-' 显示为方块的问题
   ```
#### 完整代码如下所示：
``` Python
# -*- coding: utf-8 -*-
# @Author  : Shajiu
# @FileName: logo.py
# @Time    : 2022/1/15 16:13
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.patheffects import Stroke
def logo():
    # 解决 matplotlib 显示中文问题
    plt.rcParams["font.sans-serif"] = ["SimHei"]  # 设置字体
    # 解决保存图像是负号 '-' 显示为方块的问题
    plt.rcParams["axes.unicode_minus"] = False  # 正常显示负号
    fig = plt.figure(figsize=(15, 4))
    ax = fig.add_axes([0, 0, 1, 1], frameon=False)
    ax.set_xticks([])
    ax.set_yticks([])
    family = "SimHei"
    size = 80
    cmap = plt.cm.Blues_r
    text = "源码上门取算法码上到"
    for x in np.linspace(0, 1, 20):
        lw, color = x * 225, cmap(1 - x)
        t = ax.text(
            0.5,
            0.45,
            text,
            size=size,
            color="none",
            weight="bold",
            va="center",
            ha="center",
            family=family,
            zorder=-lw,
        )
        t.set_path_effects([Stroke(linewidth=lw + 1, foreground="black")])
        t = ax.text(
            0.5,
            0.45,
            text,
            size=size,
            color='black',  # 中心文字颜色
            weight="bold",
            va="center",
            ha="center",
            family=family,
            zorder=-lw + 1,
        )
        t.set_path_effects([Stroke(linewidth=lw, foreground=color)])
    t = ax.text(
        1.0,
        0.01,
        "https://shajiu.github.io",
        va="bottom",
        ha="right",
        size=4,
        color="white",
        family=family,
        alpha=0.50,
    )
    # plt.savefig("./01Practice/Matplotlib/text-outline.pdf")
    # plt.savefig("text-outline.pdf")
    plt.savefig("text-outline.png")
    plt.show()
if __name__ == '__main__':
    logo()
  ```
#### 最后的效果如下所示：
![绘制LOGO的效果图](https://s3.bmp.ovh/imgs/2022/01/c002de4369b1e371.png)
怎么样，效果还不错吧，自己动手来试试吧。
