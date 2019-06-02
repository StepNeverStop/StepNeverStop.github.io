---
title: Ubuntu16.04配置Shadowsocks服务器
copyright: true
mathjax: false
top: 1
date: 2019-06-01 22:03:10
categories: 小知识
tags:
- ubuntu
- note
password: ss
keywords:
description:
---

科学上网，你懂的。

<!--more-->

# 购买VPS

[VULTR](https://www.vultr.com/)

纽约节点，3.5$一个月，ubuntu 16.04，enable IPV6

更改root密码

```
sudo passwd
```

# shadowsocks 服务器

更新软件源

```
sudo apt-get update
```

安装PIP

```
sudo apt-get install python-pip
sudo apt-get install python3-pip
```

安装shadowsocks

```
pip3 install https://github.com/shadowsocks/shadowsocks/archive/master.zip
```

查看shadowsocks版本，显示"Shadowsocks 3.0.0"

```
sudo ssserver --version
```

创建配置文件夹及文件

```
sudo mkdir /etc/shadowsocks
sudo nano /etc/shadowsocks/config.json
```

复制并修改配置内容，然后`ctrl+x`，`y`，回车，

```
{
    "server":"::",
    "port_password": {
        "端口1": "密码1",
        "端口2": "密码2"
    },
    "timeout":300,
    "method":"rc4-md5",
    "fast_open": false
}
```

赋予权限

```
sudo chmod 755 /etc/shadowsocks/config.json
```

为了支持这些加密方式，也许需要安装

```
sudo apt-get install python-dev
sudo apt-get install python–m2crypto
```

服务端后台启停

```
sudo ssserver -c /etc/shadowsocks/config.json -d start
sudo ssserver -c /etc/shadowsocks/config.json -d stop
```

配置Systemd管理Shadowsocks，新建Shadowsocks管理文件

```
sudo nano /etc/systemd/system/shadowsocks-server.service
```

复制粘贴，`ctrl+x`，`y`，回车

```
[Unit]
Description=Shadowsocks Server
After=network.target

[Service]
ExecStart=/usr/local/bin/ssserver -c /etc/shadowsocks/config.json
Restart=on-abort

[Install]
WantedBy=multi-user.target
```

启动Shadowsocks

```
sudo systemctl start shadowsocks-server
```

设置开机自启动Shadowsocks

```
sudo systemctl enable shadowsocks-server
```

查看运行状态

```
sudo systemctl status shadowsocks-server
```

## 优化

查看linux内核

```
uname -r
如果其显示版本在4.9.0之下，则需要升级Linux内核，否则请忽略下文

sudo apt update
查看可用的Linux内核版本
sudo apt-cache showpkg linux-image
找到一个你想要升级的Linux内核版本，如“linux-image-4.10.0-22-generic”
sudo apt install linux-image-4.10.0-22-generic
重启
sudo reboot
删除旧的内核
sudo purge-old-kernels
```

开启BBR

```
modprobe tcp_bbr
echo "tcp_bbr" >> /etc/modules-load.d/modules.conf
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
```

运行下两句，均有"bbr"则开启BBR成功

```
sysctl net.ipv4.tcp_available_congestion_control
sysctl net.ipv4.tcp_congestion_control
```

优化吞吐量，新建配置文件

```
sudo nano /etc/sysctl.d/local.conf
```

复制粘贴，`ctrl+x`，`y`，回车

```
# max open files
fs.file-max = 51200
# max read buffer
net.core.rmem_max = 67108864
# max write buffer
net.core.wmem_max = 67108864
# default read buffer
net.core.rmem_default = 65536
# default write buffer
net.core.wmem_default = 65536
# max processor input queue
net.core.netdev_max_backlog = 4096
# max backlog
net.core.somaxconn = 4096

# resist SYN flood attacks
net.ipv4.tcp_syncookies = 1
# reuse timewait sockets when safe
net.ipv4.tcp_tw_reuse = 1
# turn off fast timewait sockets recycling
net.ipv4.tcp_tw_recycle = 0
# short FIN timeout
net.ipv4.tcp_fin_timeout = 30
# short keepalive time
net.ipv4.tcp_keepalive_time = 1200
# outbound port range
net.ipv4.ip_local_port_range = 10000 65000
# max SYN backlog
net.ipv4.tcp_max_syn_backlog = 4096
# max timewait sockets held by system simultaneously
net.ipv4.tcp_max_tw_buckets = 5000
# turn on TCP Fast Open on both client and server side
net.ipv4.tcp_fastopen = 3
# TCP receive buffer
net.ipv4.tcp_rmem = 4096 87380 67108864
# TCP write buffer
net.ipv4.tcp_wmem = 4096 65536 67108864
# turn on path MTU discovery
net.ipv4.tcp_mtu_probing = 1

net.ipv4.tcp_congestion_control = bbr
```

运行

```
sysctl --system
```

编辑之前的shadowsocks-server.service文件

```
sudo nano /etc/systemd/system/shadowsocks-server.service
```

在`ExecStart`前插入一行

```
ExecStartPre=/bin/sh -c 'ulimit -n 51200'
```

重载shadowsocks-server.service

```
sudo systemctl daemon-reload
```

重启Shadowsocks

```
sudo systemctl restart shadowsocks-server
```

开启TCP Fast Open，降低Shadowsocks服务器和客户端的延迟，将`fast_open`的值由`false`修改为`true`

```
sudo nano /etc/shadowsocks/config.json
```

重启服务

```
sudo systemctl restart shadowsocks-server
```

