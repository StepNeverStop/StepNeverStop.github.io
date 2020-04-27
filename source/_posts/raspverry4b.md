---
title: 记录在树莓派4B上的配置命令
copyright: true
mathjax: false
top: 1
date: 2020-04-24 21:20:56
categories: Raspberry
tags:
- os
- raspberry
keywords:
description:
---

学习在树莓派4B上通过命令行配置各个模块。官方文档：[docs](https://www.raspberrypi.org/documentation/)。

<!--more-->

打开配置窗口

`sudo raspi-config`

可以通过这个配置窗口打开摄像机模块

# 换源

```shell
sudo -s
echo -e "deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi \n deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi" > /etc/apt/sources.list
exit
sudo apt update && sudo apt -y upgrade
```

# 更新库

```shell
sudo apt-get update
sudo apt-get upgrade
sudo apt full-upgrade
```

# 安装 BerryConda

```shell
wget https://github.com/jjhelmus/berryconda/releases/download/v2.0.0/Berryconda3-2.0.0-Linux-armv7l.sh
chmod a+x Berryconda3-2.0.0-Linux-armv7l.sh
./Berryconda3-2.0.0-Linux-armv7l.sh
```

一路`ENTER`+`yes`就ok了。

`source ~/.bashrc`

```shell
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
conda config --show
```

# 使用摄像机模块拍照

在终端使用命令`raspistill`，点击[这里](https://www.raspberrypi.org/documentation/usage/camera/raspicam/raspistill.md)查看详细API。

`raspistill -o 路径/image.jpg -w 640 -h 480`

`raspistill -vf -hf -o cam2.jpg`这个可以指定水平和垂直翻转图像

录像的命令是`raspivid`，点击[这里](https://www.raspberrypi.org/documentation/usage/camera/raspicam/raspivid.md)查看详细API，比如

`raspivid -o 路径/video.h264`

如要录制`.mp4`格式，需要先安装MP4BOX：

`sudo apt install -y gpac`

```shell
# Capture 30 seconds of raw video at 640x480 and 150kB/s bit rate into a pivideo.h264 file:
raspivid -t 30000 -w 640 -h 480 -fps 25 -b 1200000 -p 0,0,640,480 -o pivideo.h264
# Wrap the raw video with an MP4 container:
MP4Box -add pivideo.h264 pivideo.mp4
# Remove the source raw file, leaving the remaining pivideo.mp4 file to play
rm pivideo.h264
```

## 使用python拍照

创建一个`.py`文件。

安装`picamera`：

```shell
pip install picamera
# If you wish to use the classes in the picamera.array module then specify the “array” option which will pull in numpy as a dependency:
pip install "picamera[array]"
sudo pip install -U picamera # 更新
```

下面是我写的一个测试各项功能的脚本：

```python
import os
from time import sleep
from picamera import PiCamera, Color

BASE_FOLD = './'

class Cam(object):

    def __init__(self, camera, path='./', alpha=0):
        '''
        params:
            camera: PiCamera
            alpha: the degree of transparency, [0, 255]
        '''
        self.camera = camera
        self.path = path
        self.create_date_folder()
        self.alpha = alpha
        self.start()

    def create_date_folder(self):
        '''
        创建存放图片和视频的文件夹
        '''
        if not os.path.exists(self.path+'images'):
            os.makedirs(self.path+'images')
        if not os.path.exists(self.path+'vedios'):
            os.makedirs(self.path+'vedios')

    def start(self, alpha=-1):
        if alpha<0:
            alpha = self.alpha
        self.camera.start_preview(alpha=alpha)

    def close(self):
        self.camera.stop_preview()

    def review(self, time):
        '''
        检视
        params:
            time: sleep time
            rot: rotation angle, within [0, 90, 180, 270, 360]
        '''
        sleep(time)

    def capture(self, time, name):
        '''
        拍摄单张图片
        '''
        sleep(time)
        self.camera.capture(self.path+f'images/{name}.jpg')

    def capture_batch(self, time, name, n):
        '''
        连续拍照
        '''
        for i in range(n):
            self.capture(time, name=name+f'-{i}')

    def record(self, time, name):
        '''
        录制视频
        '''
        self.camera.start_recording(self.path+f'vedios/{name}.h264')
        sleep(time)
        self.camera.stop_recording()

    def set_camera_attrs(self, d):
        '''
        批量设置相机属性
        '''
        # self.close()
        for k, v in d.items():
            self.set_camera_attr(k, v)
        # self.start()

    def set_camera_attr(self, k, v):
        '''
        设置相机某个属性
        '''
        setattr(self.camera, k, v)
    
    def traverse_effects(self, time, name):
        '''
        遍历图像特效
        '''
        for effect in self.camera.IMAGE_EFFECTS:
            self.camera.image_effect = effect
            self.camera.annotate_text = "Effect: %s" % effect
            self.capture(time, name+'-'+str(effect))
        self.camera.image_effect = 'none'
        self.camera.annotate_text = ''

    def traverse_exposure_mode(self, time, name):
        '''
        遍历曝光模式
        '''
        for exposure_mode in self.camera.EXPOSURE_MODES:
            self.camera.exposure_mode = exposure_mode
            self.camera.annotate_text = "Exposure Mode: %s" % exposure_mode
            self.capture(time, name+'-'+str(exposure_mode))
        self.camera.exposure_mode = 'auto'
        self.camera.annotate_text = ''

    def traverse_white_balance(self, time, name):
        '''
        遍历白平衡模式
        '''
        for awb_mode in self.camera.AWB_MODES:
            self.camera.awb_mode = awb_mode
            self.camera.annotate_text = "Write Balamce: %s" % awb_mode
            self.capture(time, name+'-'+str(awb_mode))
        self.camera.awb_mode = 'auto'
        self.camera.annotate_text = ''


def run():
    camera_config={
        'rotation': 180,    # rotation angle, within [0, 90, 180, 270, 360]
        'resolution': (800, 600), # The maximum resolution is 2592×1944 for still photos, and 1920×1080 for video recording. The minimum resolution is 64×64.
        'framerate': 15,
        'annotate_text': "Hello world!", 
        'annotate_text_size': 50,   #[6, 160], default 32
        'annotate_background': Color('blue'),
        'annotate_foreground': Color('red'),
        'brightness': 50,   # 亮度  [0, 100], default 50
        'contrast': 0   # 对比度
    }
    camera = Cam(PiCamera(), path=BASE_FOLD)
    camera.set_camera_attrs(camera_config)
    camera.review(2)
    camera.capture(2, 'test_capture')
    camera.capture_batch(2, 'test_capture_batch', 4)
    camera.record(2, 'test_record')
    camera.traverse_effects(2, 'test_traverse_effects')
    camera.traverse_exposure_mode(2, 'test_traverse_exposure_mode')
    camera.traverse_white_balance(2, 'test_traverse_white_balance')
    camera.close()

if __name__ == "__main__":
    run()
```

更多复杂操作，请看官方[API文档](https://picamera.readthedocs.io/en/release-1.13/recipes1.html)。

# GPIO

这个库好像是用来与树莓派的引脚进行交互的。

# 参考

1. [树莓派 更换国内源，安装vim，berryconda，opencv](https://blog.csdn.net/xuzhexing/article/details/99404943)