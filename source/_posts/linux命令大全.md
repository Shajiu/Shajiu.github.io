---
title: Linux命令大全
top: false
cover: false
toc: true
mathjax: true
date: 2021-12-08 16:32:03
password:
summary:
tags: 专业
categories: 语言(Linux)
---
虽然每天都会接触Linux系统, 尤其是使用了Mac之后, 每天都是工作在黑色背景的命令行环境中. 自己记忆力不好, 很多有用的Linux命令不能很好的记忆, 现在逐渐总结一下, 以便后续查看.

##### Linux关机,重启
- 关机
``` Linux
shutdown -h now
 ```
- 重启
```
shutdown -r now
 ```

##### 查看系统,CPU信息
- 查看系统内核信息
``` Linux
uname -a
 ```
- 查看系统内核版本
``` Linux
cat /proc/version
 ```
- 查看当前用户环境变量
```Linux
env
cat /proc/cpuinfo
 ```
- 查看有几个逻辑cpu, 包括cpu型号
```Linux
 cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
 ```
- 查看有几颗cpu,每颗分别是几核
```Linux
cat /proc/cpuinfo | grep physical | uniq -c
 ```
- 查看当前CPU运行在32bit还是64bit模式下, 如果是运行在32bit下也不代表CPU不支持64bit
```Linux
 getconf LONG_BIT
 ```
- 结果大于0, 说明支持64bit计算. lm指long mode, 支持lm则是64bit
```Linux
 cat /proc/cpuinfo | grep flags | grep ' lm ' | wc -l
 ```

##### 搭建虚拟环境
- 创建虚拟名称为"tensorflow"，Python版本为3.6
```conda create -n tensorflow python=3.6 or 2.7
 ```
-  激活虚拟环境
```
conda activate tensorflow
 ```
-  关闭虚拟环境
```
conda deactivate
 ```
- 检查可使用的虚拟环境
```
conda info --envs
  ```
- 卸载虚拟环境
```
conda remove --name tensorflow –all
 ```
- 在Linux中使用Anaconda安装指定文件,以Torch为例
```
anaconda search -t conda torch
 ```
- 可以看到如下所示：
```
Esri/pytorch              |    1.8.2 | conda           | linux-64, win-64 | py3.8_cuda101_cudnn7_0, py3.6_cuda11.1_cudnn8_0, py3.7_cuda101_cudnn7_0, py3.6_cuda101_cudnn7_0, py3.8_cuda11.1_cudnn8_0, py3.6_cuda10.0.130_cudnn7.5.1_0, py3.7_cuda11.1_cudnn8_0, py3.6_cuda11.1_cudnn8.0.5_0, py3.7_cuda11.1_cudnn8.0.5_0, py3.7_cuda10.0.130_cudnn7.6.3_0, py3.7_cuda11.1_cudnn8.1_1, py3.8_cuda10.0.130_cudnn7.6.3_0, py3.9_cuda11.1_cudnn8_0, py3.6_cuda11.1_cudnn8.1_1, py3.6_cuda100_cudnn7_1, py3.6_cuda10.1.243_cudnn7.6.3_0, py3.8_cuda10.1.243_cudnn7.6.3_0, py3.9_cuda11.1_cudnn8.1_1, py3.8_cuda11.1_cudnn8.1_1, py3.8_cuda11.1_cudnn8.0.5_0, py3.6_cuda10.0.130_cudnn7.6.3_0, py3.7_cuda11.2_cudnn8.1_1, py3.7_cuda10.1.243_cudnn7.6.3_0, py3.9_cuda11.1_cudnn8.0.5_0····
 ```
- 接着，使用show指令来查看此包的详细情况
 ```
 anaconda show  Esri/pytorch
  ```
- 根据提示，我们使用如下的命令进行安装
```
conda install --channel https://conda.anaconda.org/Esri pytorch
 ```
- 选择y(yes)，然后等待安装，等安装完成后我们可以通过python中导入的方式进行检查。

##### 深度学习框架等相关操作
- 从文本中(vim)中第n到m行的文件复制到另外一个文本中的命令为：
```
n,mw!./vhost/res.help.com.conf
 ```
- 将file1.txt和file2.txt合并到file.txt
```
cat file1.txt file2.txt > file.txt
 ```
-	将file1.txt追加到file2.txt的末尾
```
cat file1.txt >> file2.txt
  ```
- 从文本中(vim)中第n到m行的文件追加到另外一个文本末尾的命令为：
```
n,m w! >>./vhost/res.help.com.conf
 ```
-	计算文本行数：
```
wc –l filename(计算文本行数)
  ```
- 查看看gpu使用情况
```
nvidia-smi
 ```
- 搭建虚拟环境
```
conda create -n tensorflow python=3.6 or 2.7
 ```
- 激活虚拟环境
```
conda activate pytorch
 ```
- 关闭虚拟环境
```
conda deactivate
 ```
-	卸载虚拟环境
```
 conda remove --name tensorflow –all
 ```
-	引入tensorflow
```
 import tensorflow as tf
 ```
-	用Tensorflow测试能否用cuda
```
 sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
 ```
- 查看tensorflow版本：
```
 tf.__version__
 ```
-	卸载tensorflow
```
 pip uninstall tensorflow
 ```
-	查看虚拟环境
```
 conda info --envs
 ```
- Top命令查看运行进程
```
https://blog.csdn.net/dxl342/article/details/53507673
  ```
-	无法获得锁---找出含有apt-get的进程
```
 ps  -aux
 ```
```
 sudo kill PID
```
-	使用screen命令
```
 screen
  ```
- 使用screen查看进行
```
 screen –ls
 ```
- 创建screen
```
 screen -S han
 ```
- 返回进程
```
 screen -r 3985
 ```
- 	退出快捷键为：
```
 CTRL+A+D
 ```
- 文件运行时加权限
```
 chmod a+x 文件
 ```
- 查看Ubuntu版本
```
lsb_release –a
 ```
- 查看Ubuntu位数
```
 getconf LONG_BIT
 ```
-	查看配置：
```
 df –h
  ```
-	查看CPU是几核：
```
cat /proc/cpuinfo |grep "cores"|uniq
 ```
-	查看位数：
```
 sudo uname --m
 ```
-	ubuntu16.04安装torch
```
http://blog.csdn.net/KGzhang/article/details/72884126
 ```
-	升级python
```
conda updata python
 ```
-	更新python版本
```
conda install python==x.x.x
 ```
-	直接下载包：
```
 git clone xxx(具体下载内容的路径)
 ```
-	指定服务器：
```
CUDA_VISIBLE_DEVICES=7,1 python xxx
 ```
-	命令行可查看当前anaconda的版本
```
conda –version
 ```
-	测试pytorch安装是否成功(在Ipython中)
```
import torch \ import torchvision
 ```
- torch能否使用GPU
```
print(torch.cuda.is_available())
 ```
-	将file1.txt追加到file2.txt的末尾
```
cat file1.txt >> file2.txt
 ```
-	如把文件a.txt得命名为b.txt
```
mv a.txt b.txt
 ```
-	在ubuntu中启动jupyter notebook
```
 jupyter notebook --ip=0.0.0.0 --port=8000
 ```
- 在ubuntu中安装nccl
```
 conda install -c anaconda nccl
 ```
-	在ubuntu中安装pytoch
```
conda install pytorch torchvision cuda80 -c pytorch
 ```
-	测试是否安装成功pytorch
```
import torch
print(torch.cuda.is_available())    output: true
 ```
- jupyter命令把.ipynb文件转化为.py文件---打开进入对应目录
```
jupyter nbconvert --to script *.ipynb
 ```
-	替换文本中的字符串
```
%s/well/good/g（等同于 :g/well/s//good/g） 替换每一行中所有 well 为 good
 ```
-	查看cuda安装路径：
```
whereis cuda
 ```
- 去除文本中字符前后的空格并将每一个字之间的编辑而替换为空格
```
sed ‘s/ //g; s/\B /g’ ./test.txt > test-txt.txt
 ```
说明:’s/ //g’表示去除文本中已有的空格。’s/\B /g’将每个字之间的边界替换为空格
-	验证theano是否安装成功
```
python -c "import theano;theano.test()"
 ```
-	查看具体用户所对应的进程
```
ps –u
 ```
- 验证能否使用gpu版本的tensorflow
``` Python
import tensorflow as tf
import os
os.environ['TF_CPP_MIN_LOG_LEVEL']='2'
print(tf.__version__)
a = tf.constant(1.)
b = tf.constant(2.)
print(a+b)
print('GPU:', tf.test.is_gpu_available())
 ```
-	查看某一个用户的进程例如root
```
ps –u root
 ```
-	查看指定的PID(33799)
```
ps –f –p 33799
 ```
-	如何使用pytorch运行时出现错误，可以使用@torchsnooper.snoop() 装饰函数可以输出对应的内容出现的错误，如下所示:
-	nltk下载程序获取资源
```
import nltk
nltk.download('stopwords')
import nltk
nltk.download('punkt')
 ```
