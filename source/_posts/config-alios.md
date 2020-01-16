---
title: 配置阿里云上的服务器
copyright: true
mathjax: false
top: 1
date: 2020-01-13 09:22:24
categories: Docker
tags:
- docker
- conda
keywords:
description:
---

本文记录了在阿里云服务器上配置自己训练环境的过程。

<!--more-->

# 安装Miniconda

`wget https://repo.continuum.io/miniconda/Miniconda3-4.6.14-Linux-x86_64.sh`

`sh Miniconda3-4.6.14-Linux-x86_64.sh`
一路`ENTER`和`yes`

`source ~/.bashrc`

更新

```
conda update conda
conda update --all
apt-get update
```


# 安装Git

```
apt-get install git
mkdir ~/keavnn
cd ~/keavnn/
git clone https://github.com/StepNeverStop/RLt.git
conda create -n tf2 python=3.6
conda activate tf2
conda install -y docopt numpy pillow yaml pyyaml pandas openpyxl

apt-get install apt-file
apt-file update
apt-file search libSM.so.6
apt-get install libsm6
apt-file search libXrender.so.1
apt-get install libxrender1

pip install protobuf grpcio grpcio-tools tensorflow tensorflow_probability
```

# 安装MuJoCo



```
mkdir ~/.mujoco
cd .mujoco/
wget url https://www.roboti.us/download/mujoco200_linux.zip
apt-get install zip
unzip mujoco200_linux.zip
wget url https://www.roboti.us/getid/getid_linux
chmod +x getid_linux
./getid_linux
```

接着把获取到的计算机ID用于注册30天免费的Mujoco引擎https://www.roboti.us/license.html

把邮箱里的mjkey.txt通过ssh连接或者sftp软件File Zilla放置云服务器上

放在`~/.mujoco`与`~/.mujoco/mujoco200_linux/bin`文件夹下

```
mv ~/.mujoco/mujoco200_linux ~/.mujoco/mujoco200
apt-get install nano
nano ~/.bashrc
```

在尾部添加`export LD_LIBRARY_PATH=$HOME/.mujoco/mujoco200/bin`，` export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/root/.mujoco/mujoco200/bin`然后保存退出

```
source ~/.bashrc
apt install python3-pip
pip install --upgrade pip
```

注意：在配置完Git之后：

```
conda activate tf2
apt install libosmesa6-dev
apt-get install python3 python-dev python3-dev \
     build-essential libssl-dev libffi-dev \
     libxml2-dev libxslt1-dev zlib1g-dev \
     python-pip libgl1-mesa-dev patchelf libglfw3 libglfw3-dev
pip install fasteners
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple -U 'mujoco-py<2.1,>=2.0'

cd ~/keavnn/RLt/gym
pip install -e '.[all]'
```

```
Successfully built mujoco-py
Installing collected packages: mujoco-py
Successfully installed mujoco-py-2.0.2.9
```

测试一下：

```
$ python3
import mujoco_py
import os
mj_path, _ = mujoco_py.utils.discover_mujoco()
xml_path = os.path.join(mj_path, 'model', 'humanoid.xml')
model = mujoco_py.load_model_from_path(xml_path)
sim = mujoco_py.MjSim(model)

print(sim.data.qpos)
# [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]

sim.step()
print(sim.data.qpos)
# [-2.09531783e-19  2.72130735e-05  6.14480786e-22 -3.45474715e-06
#   7.42993721e-06 -1.40711141e-04 -3.04253586e-04 -2.07559344e-04
#   8.50646247e-05 -3.45474715e-06  7.42993721e-06 -1.40711141e-04
#  -3.04253586e-04 -2.07559344e-04 -8.50646247e-05  1.11317030e-04
#  -7.03465386e-05 -2.22862221e-05 -1.11317030e-04  7.03465386e-05
#  -2.22862221e-05]
```


