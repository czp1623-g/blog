---
title: SetupWSL
date: 2023-06-23 12:56:52
categories:
    - cv
tags: 
    - wsl
    - linux
---

--

WSL： Windows Subsystem for Linux

有问题，找AIGC~

# 环境setup

## 安装WSL

[安装 WSL | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/install)

wsl --install -d 分发名（Ubuntu）



## ohmyzsh

1.需先安装zsh

```shell
sudo apt install zsh
```

2.安装ohmyzsh

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

3.设置random~~

编辑家目录下的 .zshrc 修改theme为random



## docker

安装

```shell
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
# 启动
sudo service docker start
```

好的，前面安装了docker，但是只能加sudo来操作emm

```shell
sudo docker ps
```

不过它为docker专门创建了一个用户组“docker”，可以将当前用户加进该用户组里

```shell
# 看看用户组
cat /etc/group | grep docker
# 把当前用户（k8s）加入docker用户组
sudo usermod -a -G docker k8s
# 验证用户组已经加入
groups k8s
# 重新进一下wsl，验证权限
docker ps
```



## 搞个分发模板

导出分发

```shell
 wsl --export Ubuntu  E:\WindowsSubsystemForLinux\导出的分发\uu
```

导入分发

```shell
 wsl --import k8s E:\WindowsSubsystemForLinux\k8s E:\WindowsSubsystemForLinux\导出的分发\uu
```

导入后，默认用root登录，要手动切一下用户

或者设置一下默认用户

```shell
vim /etc/wsl.conf
# 追加
[user]
default=k8s
# 重启一下该分发。注意假死状态
wsl --shutdown k8s
# 验证一下~
whoami
```



## k3s

https://k3s.io/

```shell
curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | INSTALL_K3S_MIRROR=cn sh -
# Check for Ready node, takes ~30 seconds 
sudo k3s kubectl get node 
```

配置一下别名

```shell
# .profile 淦，又是权限不足
alias k="sudo kubectl"
```





