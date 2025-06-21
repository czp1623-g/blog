---
title: 折腾一下我的笔记本
date: 2023-04-04 00:14:52
categories:
    - cv
tags: 
    - 瞎折腾

---



# 笔记本安装U

1. 制作启动盘：下载好一个Ubuntu镜像，用rufus工具写到U盘

2. bios，U盘启动，安装...

    可以自定义一下分区，以下都设置为主分区，主分区（4）不够再设置逻辑分区

    - 根分区/：可以大点
    - swap：选择类型交换分区，一般大小为内存的两倍
    - /home
    - /boot：ext4，给500MB就好啦
    - /boot/efi：选择EFI System分区类型，500MB就好了

如果要登录root账号，可以用：

```shell
sudo passwd root
# 输入当前用户的密码
# 输入新的root密码
```

不然为了安全着想，每次重启root都是不同的随机密码





# U安装openssh-server

```shell
  sudo apt-get install openssh-server
  sudo /etc/init.d/ssh start
```





# U安装xrdp+xubuntu-desktop

搬运自 https://blog.csdn.net/NeoZng/article/details/123505127

怕博文没了，所以cv一下

--1941508951@qq.com

```emm
由于Ubuntu自带的屏幕分享软件是VNC，而VNC在远程连接的时候，是实时截屏将一帧帧图片传输到控制端，显然效率非常低下，即使使用千兆路由器桥接甚至直接连接网线，延迟都还是很大。RDP对于视频解码的支持更加友好，本文将介绍利用xrdp连接Ubuntu远程桌面。配置好之后，可以无须屏幕直接连接，而且也不会触发显卡休眠出现连接黑屏的情况
```

```emm
2022/07/07更新： 虽然xrdp的分辨率是最高的，但是压缩后的码率还是有些高，导致帧率只有十几二十帧。NoMachine是一款远程桌面连接软件，还支持局域网内自动搜索ip，强烈推荐使用！虽然画质略有一点点点点的损失但是在1920x1080分辨率下还可以达到50-60fps的程度（网络好的情况下）。配置起来也非常非常简单，装好基本就能用。调试的时候有需要还可以直接将你的电脑用网线和目标电脑进行连接。
```



官网链接：https://www.nomachine.com

xrdp不太兼容Ubuntu默认的gnome桌面，所以我们需要安装xubuntu-desktop

## 安装xubuntu-desktop

```shell
sudo apt install xubuntu-desktop
# 碰到需要选择的地方直接回车
```



安装xubuntu-desktop 如果失败，那么可以选择换源

## 换源方法如下：

[Ubuntu清华源地址](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/) 选择合适的Ubuntu版本

Ubuntu 的软件源配置文件是 /etc/apt/sources.list

```shell
# 先备份原始文件:
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
# 打开软件源配置文件，把软件源配置文件中的内容换成其他源：
sudo gedit /etc/apt/sources.list
# 随后更新源：
sudo apt-get update
#当然也可以直接在 软件与更新 中切换安装源。
```



## 安装和配置xrdp

```shell
sudo apt install xrdp
# 验证是否安装成功
sudo systemctl status xrdp
```

成功会出现类似如下内容,按q键退出

```shell
● xrdp.service - xrdp daemon
     Loaded: loaded (/lib/systemd/system/xrdp.service; enabled; vendor preset: >
     Active: active (running) since Tue 2022-03-15 14:05:08 CST; 41min ago
       Docs: man:xrdp(8)
             man:xrdp.ini(5)
   Main PID: 810 (xrdp)
      Tasks: 1 (limit: 18748)
     Memory: 1.4M
     CGroup: /system.slice/xrdp.service
             └─810 /usr/sbin/xrdp

3月 15 14:05:07 zzy-ThinkPad-T490s xrdp[800]: (800)(139792143402816)[INFO ] add>
3月 15 14:05:07 zzy-ThinkPad-T490s xrdp[800]: (800)(139792143402816)[INFO ] lis>
3月 15 14:05:07 zzy-ThinkPad-T490s xrdp[800]: (800)(139792143402816)[INFO ] xrd>
3月 15 14:05:07 zzy-ThinkPad-T490s xrdp[800]: (800)(139792143402816)[DEBUG] Clo>
3月 15 14:05:07 zzy-ThinkPad-T490s systemd[1]: xrdp.service: Can't open PID fil>
3月 15 14:05:08 zzy-ThinkPad-T490s systemd[1]: Started xrdp daemon.
3月 15 14:05:09 zzy-ThinkPad-T490s xrdp[810]: (810)(139792143402816)[INFO ] sta>
3月 15 14:05:09 zzy-ThinkPad-T490s xrdp[810]: (810)(139792143402816)[INFO ] add>
3月 15 14:05:09 zzy-ThinkPad-T490s xrdp[810]: (810)(139792143402816)[INFO ] lis>
3月 15 14:05:09 zzy-ThinkPad-T490s xrdp[810]: (810)(139792143402816)[INFO ] xrd>
lines 1-21/21 (END)
```



xrdp 使用 /etc/ssl/private/ssl-cert-snakeoil.key

所以将xrdp添加到ssl-cert用户组中：

```shell
sudo adduser xrdp ssl-cert
# 不放心的话使用这个命令查看一下:
groups xrdp
# 正常会出现：
xrdp : xrdp ssl-cert
```

由于安装了xubuntu-desktop ，Ubuntu中存在多个桌面管理器，所以我们需要进行选择。

输入下面的命令将修改写入配置文件，

```shell
echo "xfce4-session" >~/.xsession
#选择xubuntu-desktop即可。
```

重启xrdp使配置生效：

```shell
sudo systemctl restart xrdp
```

## 配置防火墙

如果想从某个IP或者某个范围的IP访问xrdp，例如从192.168.1.77访问xrdp，使用如下命令：

```shell
sudo ufw allow from 192.168.1.77 to any port 3389 proto tcp
```

如果想从任何IP访问则：

```shell
xrdp sudo ufw allow 3389
```



## 连接

```shell
# 安装完成后，启动xrdp程序，并打开防火墙端口3389，这是RDP协议的默认端口
sudo systemctl enable --now xrdp
```





## 可能会遇到的问题

### win remote连接闪退

同一账号不能同时在本地主机上登录和在远程主机上登录

### 远程桌面时出现蓝屏，电脑重启后鼠标键盘无法使用

首先重启时进入恢复模式获得root权限

输入：

```shell
sudo apt-get install xserver-xorg-input-all
# 完成再重启即可使用鼠标键盘
```



再使用如下命令，就应该可以解决蓝屏问题

sudo apt-get install xserver-xorg-core

sudo apt-get install xserver-xorg-input-all

sudo apt-get install xorgxrdp

### 远程桌面时多次出现要输入密码验证

最好是创建一个特定的用户，专门用来远程

```shell
adduser win
```



输入：

```shell
sudo gedit /etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla
```

把下面这段内容复制粘贴并保存

```shell
[Allow Colord all Users]
Identity=unix-user:*
Action=org.freedesktop.color-manager.create-device;org.freedesktop.color-manager.create-profile;org.freedesktop.color-manager.delete-device;org.freedesktop.color-manager.delete-profile;org.freedesktop.color-manager.modify-device;org.freedesktop.color-manager.modify-profile
ResultAny=no
ResultInactive=no
ResultActive=yes
```

再输入

```shell
sudo gedit /etc/polkit-1/localauthority/50-local.d/46-allow-packagekit.pkla
```

再把下面这段内容复制粘贴并保存

```shell
[Allow Package Management all Users]
Identity=unix-user:*
Action=org.freedesktop.packagekit.system-sources-refresh
ResultAny=yes
ResultInactive=yes
ResultActive=yes 
```

即可完成配置。



### 优化配置

https://blog.csdn.net/wu_weijie/article/details/116158271

https://cloud.tencent.com/developer/article/2129798