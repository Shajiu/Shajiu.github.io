---
title: Python将PDF转为WORD
top: false
cover: false
toc: true
mathjax: true
date: 2021-12-22 17:06:45
password:
summary:
tags: 利器库
categories: 工具方法
---
6行代码！用Python将PDF转为word

pdf转word应该算是一个很常见的需求了。网上有些免费的转换工具，一方面不安全，有文件泄露风险，另一方面有免费转换的次数限制。今天向大家分享一个很好用的工具：pdf2docx

安装
```pip
pip install pdf2docx
```
用法也很简单，核心方法是Converter，我写了一个小脚本，如有需要，大家可以直接copy走。

``` Python
# -*- coding: utf-8 -*-
# @Author  : Shajiu
# @FileName: pdf_word.py
# @Time    : 2021/12/22 17:12
import argparse
from pdf2docx import Converter
def main(pdf_file, docx_file):
    cv = Converter(pdf_file)
    cv.convert(docx_file, start=0, end=None)
    cv.close()
if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--pdf_file", type=str)
    parser.add_argument('--docx_file', type=str)
    args = parser.parse_args()
    main(args.pdf_file, args.docx_file)
  ```
用法
``` Python
python pdf2word.py --pdf_file  pdf文件路径\example.pdf --docx_file 输出word文件的路径\example.docx
 ```
不喜欢命令行跑脚本的同学可以copy下面简化版
```Python
from pdf2docx import Converter
pdf_file = 'pdf文件路径'
docx_file = '输出word文件的路径'
cv = Converter(pdf_file)
cv.convert(docx_file, start=0, end=None)
cv.close()
 ```
> 关于我，欢迎关注
  博客：[甘南第一勇士](https://shajiu.github.io/)  微信公众号:"源码上门取算法码上到" 。

![请关注且回复“Python安装包”关键字，便可获取不同版本的Python,Pycharm等相关工具](https://s2.loli.net/2021/12/23/6lBOuQad1n2GCwA.jpg)
