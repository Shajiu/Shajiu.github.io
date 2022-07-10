---
title: 如何AI制造程序猿与仙女舞蹈？
top: false
cover: false
toc: true
mathjax: true
date: 2022-01-05 21:03:39
password:
summary:
tags:
categories:
---
- 引言
&emsp;&emsp;前不久写了一份个人的年终总结，题为[《中国特色、民族风格、青年气派的年终总结》](https://mp.weixin.qq.com/s/4RzLcdPRYKEVWk3hdw3cCQ)，全文共有10240个字符。获得了不少人的关注，同时也得到了很多朋友们的建设性的意见。有的人说制作词云更需要突出核心词；有的人说写的篇幅过长可以试着拆分为专题报告；也有人说趁着之前研究AI的宝刀未老可以做的更炫酷些…。为了反馈各位同仁，本人专门制作了一套完全基于AI的全自动化的精简多模态总结。本项目为了能够满足更多人的需求，整个项目可拆分为多个模块功能单独实现。通过本项目，你可以学到：通过自定义添加词汇进行分词、制作炫酷的词云、语音合成、视频切分、语义分割等知识，具体内容如下所示：通过NLP和CV技术将Text、Speech、Vision集成为一个富含民族特色的仙女舞者，让文字跳起藏族舞。
![中国特色、民族风格、青年气派的年终总结词云图](https://s3.bmp.ovh/imgs/2022/01/493676aab77c1790.jpg)
废话少说，直接上代码

- 第一步：下载并安装依赖包。
```python
def install():
    libs = {"lxml", "requests", "pandas", "numpy", "you-get", "opencv-python", "pandas", "fake_useragent", "matplotlib",
            "moviepy", "pixellib"}
    try:
        for lib in libs:
            os.system(f"pip install -i https://pypi.doubanio.com/simple/ {lib}")
            print(lib + "下载完毕");
    except:
        print(lib + "下载失败")
  ```

- 第二步：下载视频。可以使用you-get来可以下载视频，先安装 pip install you-get；找到想要下载视频的链接，使用如下指令，便可下载。
```
you-get -i https://www.bilibili.com/video/BV11C4y1h7nX
 ```
 本次为了凸显出民族特色，为此获取了一段藏族舞蹈。

- 第三步：视频分割。使用Opencv，将视频分割为图片，本项目截取了800张图片用于制作词云，通过Opencv中的VideoCaptrue类对视频进行读取操作以及调用摄像头，具体代码如下：
``` Python
def splitFrames():
    videoFileName = "E:\word_cloud_bject\PrimaryVideoDisplay\\wudao.mp4"
    cap = cv2.VideoCapture(videoFileName)  # 打开视频文件
    num = 1
    while True:
        # success 表示是否成功，data是当前帧的图像数据；.read读取一帧图像，移动到下一帧
        success, img = cap.read()
        if not success:
            break
        img_new = img
        # cv2.flip(img,0,img_new)
        dst_im = cv2.flip(img_new, 1)  # 原型：cv2.flip(src, flipCode[, dst]) → dst  flipCode表示对称轴 0：x轴  1：y轴.  -1：both
        img_new = cv2.transpose(dst_im)
        width = int(img_new.shape[1] * 0.5)
        height = int(img_new.shape[0] * 0.5)
        dim = (width, height)
        img_new = cv2.resize(img_new, dim, interpolation=cv2.INTER_AREA)
        cv2.imwrite('E:\word_cloud_bject\ImageSegmentation\\' + str(num) + ".jpg", img_new)
        print(num)
        num = num + 1
    cap.release()
  ```
![从视频中按照帧切分出来的图](https://s3.bmp.ovh/imgs/2022/01/12180a1192e8235e.jpg)

- 第四步：语义分割。使用mask_rcnn_coco进行图像分割，创建一个分割的应用。由于本文篇幅限制，直接使用已有功能，没有详细介绍基于Mask_rcnn_coco的训练和推理的相关理论原理，若需要整个代码，请您关注微信公众号并与本作者联系，具体代码如下所示：
``` python
def body_segmentation():
    segment_image = semantic_segmentation()
    segment_image.load_pascalvoc_model(
        "E:\Python_Projects\ObjectDetection\models\deeplabv3_xception_tf_dim_ordering_tf_kernels.h5")
    for path, dir_list, file_list in os.walk(r"E:\word_cloud_bject\ImageSegmentation"):
        for file_name in file_list:
            segment_image.segmentAsPascalvoc(os.path.join(path, file_name),
                                             output_image_name="E:\word_cloud_bject\SplitImage\\" + file_name)
 ```

![从彩色图像中通过语义分割技术分割的图像](https://s3.bmp.ovh/imgs/2022/01/0d660779604fa24b.jpg)

- 第五步：将分割的图像转换为二值化图。
``` python
def segmentation_to_binaryzation():
    '''
    功能：将分割图像转换为二值化图
    :return:
    '''
    for path, dir_list, file_list in os.walk(r"E:\word_cloud_bject\SplitImage"):
        for file_name in file_list:
            img = cv2.imread(os.path.join(path, file_name))
            Grayimg = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
            ret, thresh = cv2.threshold(Grayimg, 12, 255, cv2.THRESH_BINARY)
            cv2.imwrite("E:\word_cloud_bject\Binaryzation\\" + file_name, thresh)
 ```
![将分割图像转为二值图像](https://s3.bmp.ovh/imgs/2022/01/e5017674312999a8.jpg)

- 第六步：单通道(二值化图像)的图像转换为三通道图像(RGB)
``` python
def one_to_three():
    '''
    功能：单通道图像转换为三通道的图像
    :return:
    '''
    for path, dir_list, file_list in os.walk(r"E:\word_cloud_bject\Binaryzation"):
        for file_name in file_list:
            img = cv2.imread(os.path.join(path, file_name), 1)
            # 彩色图像转换为灰度图像（3通道变为1通道）
            gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
            # 最大图像灰度值减去原图像，即可得到反转的图像
            dst = 255 - gray
            cv2.imwrite("E:\word_cloud_bject\OneToThree\\" + file_name, dst)
 ```
![将二值图转换为三通道图像](https://s3.bmp.ovh/imgs/2022/01/90a283d498a13584.jpg)
- 第七步：黑白转黑红。为了能够生成的词云图像更加美观，使用红色作为字体颜色。
``` python
def black_to_red():
    '''
    功能： 黑色转换为红色
    :return:
    '''
    for path, dir_list, file_list in os.walk(r"E:\word_cloud_bject\OneToThree"):
        for file_name in file_list:
            src = cv2.imread(os.path.join(path, file_name), 0)
            src_RGB = cv2.cvtColor(src, cv2.COLOR_GRAY2RGB)
            src_RGB[np.where((src_RGB == [0, 0, 0]).all(axis=2))] = [0, 0, 255]
            cv2.imwrite("E:\word_cloud_bject\BlackToRed\\" + file_name, src_RGB)
  ```
![将三通道图中的黑色值转为红色的图像](https://s3.bmp.ovh/imgs/2022/01/4c36661881c328e3.jpg)


- 第八步：图像旋转，将图像顺时针旋转90度。原先从视频中分割出的仙女莫名奇妙的“躺下”了，具体原因未看，有感兴趣的可以了解cv2.resize中相关函数的参数设置。
``` python
def rotate():
    '''
    功能：将图像向右旋转了90度
    :return:
    '''
    for path, dir_list, file_list in os.walk(r"E:\word_cloud_bject\BlackToRed"):
        for file_name in file_list:
            img = cv2.imread(os.path.join(path, file_name))
            img90 = np.rot90(img, -1)  # 对图像矩阵顺时针旋转90度
            cv2.imwrite("E:\word_cloud_bject\Rotate\\" + file_name, img90)  # 保存旋转后的图像
 ```
![将图像向右旋转90度](https://s3.bmp.ovh/imgs/2022/01/4da94e176d42ebd5.jpg)

- 第九步：生成词云图。首先，对语料进行分词；其次，向Jieba分词库中添加自定义词组；然后，获取停用词(非核心词)；最后循环并加载分割的每张图为模板,生成对应的词云图。整个实现过程在代码中有详细解释。
``` python
def word_cloud():
    '''
    功能: 生成词云
    :return:
    '''
    jieba.load_userdict("E:\word_cloud_bject\Corpus\\barrages.txt")
    result_list = []
    with open('E:\word_cloud_bject\Corpus\\cn_stopwords.txt', 'r', encoding='utf-8') as f:
        con = f.read().split('\n')
        stop_words = set()
        for i in con:
            stop_words.add(i)
    with open('E:\word_cloud_bject\Corpus\\barrages.txt', 'r', encoding="utf-8") as f:
        data = f.read()
    new_data = re.findall('[\u4e00-\u9fa5]+', data, re.S)
    new_data = "/".join(new_data)
    # 文本分词
    seg_list_exact = jieba.cut(new_data, cut_all=True)
    for word in seg_list_exact:
        # 设置停用词并去除单个词
        if word not in stop_words and len(word) > 1:
            result_list.append(word)
    space_list = ' '.join(result_list)  # 空格链接词语
    for path, dir_list, file_list in os.walk(r"E:\word_cloud_bject\Rotate"):
        for file_name in file_list:
            backgroud = np.array(Image.open(os.path.join(path, file_name)))
            wc = WordCloud(width=1400, height=2200,
                           background_color='white',
                           mode='RGB',
                           mask=backgroud,  # 添加蒙版，生成指定形状的词云，并且词云图的颜色可从蒙版里提取
                           max_words=500,
                           stopwords=STOPWORDS.add('老年人'),  # 内置的屏蔽词,并添加自己设置的词语
                           font_path='C:\Windows\Fonts\STZHONGS.ttf',
                           max_font_size=150,
                           relative_scaling=0.6,  # 设置字体大小与词频的关联程度为0.4
                           random_state=50,
                           scale=2
                           ).generate(space_list)
            image_color = ImageColorGenerator(backgroud)  # 设置生成词云的颜色，如去掉这两行则字体为默认颜色
            wc.recolor(color_func=image_color)
            plt.imshow(wc)  # 显示词云
            plt.axis('off')  # 关闭x,y轴
            # plt.show()                     # 显示
            wc.to_file("E:\word_cloud_bject\WordCloud\\" + file_name)  # 保存词云图
  ```
![通过加载模板制作对应的词云图](https://s3.bmp.ovh/imgs/2022/01/a1d042c9e20f26c2.jpg)

- 第十步：合成图像转视频。如官方文档所介绍的，moviepy是一个用于视频编辑的Python库，可以切割、拼接、标题插入，视频合成（即非线性编辑），进行视频处理和自定义效果的设计。总的来说，可以非常方便且自由地处理视频、图片等文件，具体上代码。
``` python
def photofunia():
    '''
    通过多张图片(jpg)合成视频(mp4)
    :return:
    '''
    # 输出视频的保存路径
    video_dir = "E:\word_cloud_bject\GeneratedVideo\\Result1.mp4"
    # 帧率
    fps = 30
    # 图片尺寸
    img_size = (1000, 1080)
    fourcc = cv2.VideoWriter_fourcc('M', 'P', '4', 'V')  # opencv3.0 mp4会有警告但可以播放
    videoWriter = cv2.VideoWriter(video_dir, fourcc, fps, img_size)
    for path, dir_list, file_list in os.walk(r"E:\word_cloud_bject\WordCloud"):
        for file_name in file_list:
            frame = cv2.imread(os.path.join(path, file_name))
            frame = cv2.resize(frame, img_size)  # 生成视频   图片尺寸和设定尺寸相同
            videoWriter.write(frame)  # 写进视频里
            print(f'======== 按照视频顺序第{file_name}张图片合进视频 ========')
        videoWriter.release()  # 释放资源
  ```
- 第十一步：提取音频。为了能够跟原先的视频保持一致，从原先的视频中提取音频部分，随后加载到此次编辑的视频中。
``` python
def movie():
    '''
    功能: 从视频(mp4)中提取背景音乐
    :return:
    '''
    video = VideoFileClip('E:\Python_Projects\word_cloud_bject\PrimaryVideoDisplay\\wudao.mp4')
    audio = video.audio
    audio.write_audiofile('E:\Python_Projects\word_cloud_bject\MusicalAudioChimer\\test.mp3')
  ```
-  第十二步：向视频(mp4)中加载背景音乐(mp3)，上代码。
``` python
def add_audio_track():
    '''
    功能：向视频(mp4)中加载背景音乐(mp3)
    :return:
    '''
    # 读取词云视频
    my_clip = mpy.VideoFileClip('E:\Python_Projects\word_cloud_bject\GeneratedVideo\\Result1.mp4')
    # 截取背景音乐
    audio_background = mpy.AudioFileClip('E:\Python_Projects\word_cloud_bject\MusicalAudioChimer\\test.mp3').subclip(0,
                                                                                                                     32)
    # audio_background.write_audiofile('E:\Python_Projects\word_cloud_bject\MusicalAudioChimer\\test.mp3')
    # 视频中插入音频
    final_clip = my_clip.set_audio(audio_background)
    # 保存为最终的视频   动听的音乐！漂亮小姐姐词云跳舞视频！
    final_clip.write_videofile('E:\Python_Projects\word_cloud_bject\GeneratedVideo\\final_video.mp4', audio_codec='aac')
 ```
- 最终的结果如下所示：
<video src="https://youtu.be/0uhi3UJFDXY" controls="controls" width="500" height="300">您的浏览器不支持播放该视频！</video>
若您需要此项目完整版源码和对应的数据，您可以点击[这里](https://github.com/Shajiu/PythonObject/tree/main/tool/word_cloud_bject)并下载相应的源码和数据。






​
