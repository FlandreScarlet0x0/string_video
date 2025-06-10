# 字符动画介绍

# 本代码为纯python设计，且使用了第三方库pillow以及Opencv,Moviepy
-  在运行代码时，请首先确保您的计算机上有一个python
其次，您还需要准备你所需转字符的视频文件和音频文件
（如果是在b站下视频源，要通过手机将视频缓存并在本机文件中找到该视频文件）
之后，需要通过打开控制台（win+cmd）使用pip安装相应的第三方库
- 输入如下：（推荐使用清华同方的镜像站）
```
pip install pillow  -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install opencv-python  -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install moviepy  -i https://pypi.tuna.tsinghua.edu.cn/simple 1

```

- 当然可以直接通过计算机下载安装
```
pip install moviepy

```
- 如果安装出错建议在网上搜索相应内容
最后就可以运行该代码了
# （附：建议使用短视频（1分钟30秒内）作为源文件，或者裁剪原视频
# 如果您想导出长视频，在导出音频文件和完整的字符动画过程中觉得过慢，请手动按ENTER键加速缓存传输，基于4分钟视频可能需要一个下午（大概3，4个小时））
# 以下是代码实例
```

import cv2
from PIL import Image, ImageDraw, ImageFont
import numpy as np
from moviepy import VideoFileClip, CompositeVideoClip, AudioFileClip
ascii_char =list("B%8&WM#*oahkbdpqwmZO0QLCJUYXzcv2unxrjft/\|()1{}[]?-_+~<>i!lI;:,\"^`'. ")
#打开原视频
#路径自行修改
cap=cv2.VideoCapture('D:/Lucky Star/Lucky Star.mp4')
#设置文件名
output_file='lucky_star.mp4'
#设置编码方式
fourcc = cv2.VideoWriter_fourcc(*'mp4v')

def get_video_properties_opencv(cap):
    if cap.isOpened():
        fps = cap.get(cv2.CAP_PROP_FPS)  # 获取帧率
        frame_count = int(cap.get(cv2.CAP_PROP_FRAME_COUNT))  # 获取总帧数
        duration = frame_count / fps  # 计算视频长度（秒）
        f = fps * duration / 100
        return f
    else:
        return None
p = get_video_properties_opencv(cap)

#获取原视频帧宽帧高
w = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
h = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))
color=(0,0,0)
#创建空白视频实例
out=cv2.VideoWriter(output_file, fourcc, 30.0, (int(w*1.7), int(h*1.7)))
n=0
t=-1
while True:
    #读取每一帧
    a,b=cap.read()
    if not a:
        break
    #获取视频的长宽
    width=b.shape[0]
    height=b.shape[1]
    #把每一帧转灰度图
    gray_cap=cv2.cvtColor(b,cv2.COLOR_BGR2GRAY)
    #缩小图片，图片尺寸也请根据视频图画大小自行调整
    gray_cap1=cv2.resize(gray_cap,(width//2,height//19))
    #创建空字符串，作为字符画的载体
    text=''
    #遍历图片像素点
    for row in gray_cap1:
        for i in row:
            text+=ascii_char[int(i/256*len(ascii_char))]
        text+='\n'
    #创建白色底图
    img = Image.new('RGB', (int(w*1.7) , int(h*1.7)), color='white')
    #将底图创建实例
    draw = ImageDraw.Draw(img)
    #设置字体和尺寸
    font = ImageFont.truetype('Inconsolata.otf',size=12)
    #将字符画绘制在底图实例上
    draw.text((0,20), text, font=font, fill=(0, 0, 0))
    # 将 PIL 图像转换为 NumPy 数组
    c= np.array(img)
    #将数组（即每一帧）加入到空白视频实例中
    out.write(c)
    #视频进度要根据视频长短自行调整
    if (n//p-t)==1:
        print(f'视频生成进度为{n/p:.0f}%')
    t = n // p
    n+=1
#释放视频实例
out.release()
cap.release()
print('视频生成完毕')
video = VideoFileClip(output_file)
#导出音频和完整合并视频，请在在测试时根据自身需要选择关闭
#audio = AudioFileClip('D:/Lucky Star/audio.mp3')
#audio=audio.subclipped(0,257)
#final_clip = video.with_audio(audio)
#print('合成中...')
#threads代表进程数，请根据电脑本机cpu等填入数值(最大为2.14748e+09)
#final_clip.write_videofile('Lucky Star字符动画.mp4', codec='libx264', audio_codec='aac',threads = 2.14748e+09)
#print('合成完毕!')

```
