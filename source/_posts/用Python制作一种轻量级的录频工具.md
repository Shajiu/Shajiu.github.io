---
title: 用Python制作一种轻量级的录屏工具
top: false
cover: false
toc: true
mathjax: true
date: 2022-01-08 11:23:44
password:
summary:
tags: 利器库
categories: 工具方法
---
### 背景介绍
&emsp; 屏幕录制的意思是监控显示器中的特定区域，并将其以视频的形式存储。要实现屏幕录制就要使用屏幕录像技术，这就要借助一些屏幕录像软件。但严格意义上来说，屏幕录制除了包含录制电脑桌面操作，还包括了另一个重要来源，即录制计算机视窗环境中的视频内容，譬如录制播放器视频、录制QQ视频、录制游戏视频等等。因为录屏软件动不动就开始收费，所以我们经常更换录屏软件。今天我利用闲暇之余，感觉可以用万能的Python来解决此烦恼。

### 主题思想
&emsp; 通过上网搜寻了相关知识，录制视频基本上都用的图像处理库 PIL 的 ImageGrab 模块。此模块可以用于将当前屏幕的内容或者剪贴板上的内容拷贝到 PIL 图像内存。既然这个模块可以获取当前屏幕上的内容，那么我一直不间断地获取，然后把这些获取的内容拼起来，那不就是视频了吗？
### 实现方案
&emsp;首先，使用 PIL 模块中的 ImageGrab 不断获得当前屏幕，其次，利用 opencv 写入视频流。以下直接上代码即可。
``` python
# -*- coding: utf-8 -*-
# @Author  : Shajiu
# @FileName: video_record.py
# @Time    : 2021/12/27 16:06
import time, threading
from datetime import datetime
from PIL import ImageGrab
import numpy as np
from cv2.cv2 import VideoCapture, VideoWriter_fourcc, VideoWriter, cvtColor, CAP_PROP_FPS, CAP_PROP_FRAME_COUNT, \
    CAP_PROP_FRAME_WIDTH, CAP_PROP_FRAME_HEIGHT, COLOR_RGB2BGR
from pynput import keyboard
# 录入视频
def video_record(sttime):
    global name
    # 当前的时间（当文件名）
    name = datetime.now().strftime('%Y-%m-%d %H-%M-%S')
    # 获取当前屏幕
    screen = ImageGrab.grab()
    # 获取当前屏幕的大小
    width, high = screen.size
    # MPEG-4编码,文件后缀可为.avi .asf .mov等
    fourcc = VideoWriter_fourcc('X', 'V', 'I', 'D')
    # （文件名，编码器，帧率，视频宽高）
    video = VideoWriter('%s.avi' % name, fourcc, 15, (width, high))
    print(str(sttime) + '秒后开始录制----')
    time.sleep(int(sttime))
    print('开始录制!')
    global start_time
    start_time = time.time()
    while True:
        if flag:
            print("录制结束！")
            global final_time
            final_time = time.time()
            # 释放
            video.release()
            break
        # 图片为RGB模式
        im = ImageGrab.grab()
        # 转为opencv的BGR模式
        imm = cvtColor(np.array(im), COLOR_RGB2BGR)
        # 写入
        video.write(imm)
# 监听按键
def on_press(key):
    global flag
    if key == keyboard.Key.esc:
        flag = True
        # 返回False，键盘监听结束！
        return False
# 视频信息
def video_info():
    # 记得文件名加格式不要错！
    video = VideoCapture('%s.avi' % name)
    fps = video.get(CAP_PROP_FPS)
    count = video.get(CAP_PROP_FRAME_COUNT)
    size = (int(video.get(CAP_PROP_FRAME_WIDTH)), int(video.get(CAP_PROP_FRAME_HEIGHT)))
    print('帧率=%.1f' % fps)
    print('帧数=%.1f' % count)
    print('分辨率', size)
    print('视频时间=%.3f秒' % (int(count) / fps))
    print('录制时间=%.3f秒' % (final_time - start_time))
    print('推荐帧率=%.2f' % (fps * ((int(count) / fps) / (final_time - start_time))))
if __name__ == '__main__':
    flag = False
    print("工具使用：输入1-9秒必须为整数的延迟时间，点击esc按钮结束录屏")
    sstime = input("请输入多少秒后开始录制(1-9秒)必须为整数：", )
    th = threading.Thread(target=video_record, args=sstime)
    th.start()
    with keyboard.Listener(on_press=on_press) as listener:
        listener.join()
    # 等待视频释放过后
    time.sleep(1)
    video_info()
 ```
&emsp;以上代码包含：监听键盘事件、主体控制、视频信息、效果等。
<video src="https://www.youtube.com/watch?v=0cdCEDvR5lU" controls="controls" width="500" height="300">您的浏览器不支持播放该视频！</video>
&emsp;若你想获取源码，请点击[这里](https://github.com/Shajiu/PythonObject/blob/main/tool/video_record.py)即可获取相应的源码。
