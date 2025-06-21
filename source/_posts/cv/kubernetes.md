---
title: kubernetes
date: 2023-04-04 00:14:52
categories:
    - cv
tags: 
    - 可观测性
    - 日志
    - 云原生
    - grafana	
    - Cloud Native
---

--

# 概念

## 介绍

Kubernetes也成为k8s，是用于自动部署、扩缩容和管理容器化应用程序的开源系统。

-- 将组成应用程序的容器组合成逻辑单元，以便于管理和服务发现。

前身是Google的borg，后捐赠给CNCF。

## 一些特性

#### [自动化上线和回滚](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/deployment/)

Kubernetes 会分步骤地将针对应用或其配置的更改上线，同时监视应用程序运行状况以确保你不会同时终止所有实例。如果出现问题，Kubernetes 会为你回滚所作更改。你应该充分利用不断成长的部署方案生态系统。

#### [服务发现与负载均衡](https://kubernetes.io/zh-cn/docs/concepts/services-networking/service/)

无需修改你的应用程序去使用陌生的服务发现机制。Kubernetes 为容器提供了自己的 IP 地址和一个 DNS 名称，并且可以在它们之间实现负载均衡。

#### [自我修复](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/replicaset/#replicationcontroller-如何工作)

重新启动失败的容器，在节点死亡时替换并重新调度容器， 杀死不响应用户定义的健康检查的容器， 并且在它们准备好服务之前不会将它们公布给客户端。

#### [存储编排](https://kubernetes.io/zh-cn/docs/concepts/storage/persistent-volumes/)

自动挂载所选存储系统，包括本地存储、诸如 [AWS](https://aws.amazon.com/products/storage/) 或 [GCP](https://cloud.google.com/storage/) 之类公有云提供商所提供的存储或者诸如 NFS、iSCSI、Ceph、Cinder 这类网络存储系统。

#### [Secret 和配置管理](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/)

部署和更新 Secret 和应用程序的配置而不必重新构建容器镜像， 且不必将软件堆栈配置中的秘密信息暴露出来。

#### [自动装箱](https://kubernetes.io/zh-cn/docs/concepts/configuration/manage-resources-containers/)

根据资源需求和其他限制自动放置容器，同时避免影响可用性。 将关键性的和尽力而为性质的工作负载进行混合放置，以提高资源利用率并节省更多资源。

#### [批量执行](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/)

除了服务之外，Kubernetes 还可以管理你的批处理和 CI 工作负载，在期望时替换掉失效的容器。

#### [IPv4/IPv6 双协议栈](https://kubernetes.io/zh-cn/docs/concepts/services-networking/dual-stack/)

为 Pod 和 Service 分配 IPv4 和 IPv6 地址

#### [水平扩缩](https://kubernetes.io/zh-cn/docs/tasks/run-application/horizontal-pod-autoscale/)

使用一个简单的命令、一个 UI 或基于 CPU 使用情况自动对应用程序进行扩缩。

#### [为扩展性设计](https://kubernetes.io/zh-cn/docs/concepts/extend-kubernetes/)

无需更改上游源码即可扩展你的 Kubernetes 集群。

# 一些实践

## 常用命令

```shell
# start k3s server
sudo nohup k3s server --docker &
# kill all
sh /usr/local/bin/k3s-killall.sh
# 进入容器
kubectl exec -it <pod_name> -c <container_name> -- <command>
k exec -it nginx-deployment-6d945c9c6b-b8b6z -c mynginx /bin/bash
# /bin/sh

# 在node节点上查看iptable配置
sudo iptables -L -v -n -t nat


```



```shell
# 如定义了三个pod，一个service抽象
 % k get pods -o wide
NAME                             READY   STATUS    RESTARTS   AGE   IP           NODE         NOMINATED NODE   READINESS GATES
demo-deployment-866496cd-rnml9   1/1     Running   0          23m   10.42.0.11   zp-desktop   <none>           <none>
demo-deployment-866496cd-td9q9   1/1     Running   0          23m   10.42.0.10   zp-desktop   <none>           <none>
demo-deployment-866496cd-kxc6z   1/1     Running   0          23m   10.42.0.9    zp-desktop   <none>           <none>
 % k get svc -o wide
NAME           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                           AGE   SELECTOR
demo-service   NodePort    10.43.217.37   <none>        30000:30001/TCP,30010:30011/TCP   21m   app=demo-app
# iptables的chain如下
Chain KUBE-SVC-63XIDVX3FQTFCQ6L (2 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 KUBE-MARK-MASQ  tcp  --  *      *      !10.42.0.0/16         10.43.217.37         /* default/demo-service:spring-svc cluster IP */ tcp dpt:30000
    0     0 KUBE-SEP-YJLR2SS2M4E7KIG7  all  --  *      *       0.0.0.0/0            0.0.0.0/0            /* default/demo-service:spring-svc -> 10.42.0.10:6000 */ statistic mode random probability 0.33333333349
    0     0 KUBE-SEP-664WUM5UBXGPHS7X  all  --  *      *       0.0.0.0/0            0.0.0.0/0            /* default/demo-service:spring-svc -> 10.42.0.11:6000 */ statistic mode random probability 0.50000000000
    0     0 KUBE-SEP-2NKQLS73Z4PYUJBC  all  --  *      *       0.0.0.0/0            0.0.0.0/0            /* default/demo-service:spring-svc -> 10.42.0.9:6000 */
# 后面跟着的小数表示：选择该pod的几率，按链条上的顺序一个个判断
```



## wsl搭建k3s环境

https://www.guide2wsl.com/

或见另外的post

## k3s集群搭建

```shell
# 装好了master节点后
# 查看k3s token
sudo cat /var/lib/rancher/k3s/server/node-token
# 新节点执行如下命令
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=K10f0babbc6cac44560f6e3271aa0af817910b1b61712a1fbde9dee4811026fbaa8::server:157a7f73685391377e14699136b1a121 INSTALL_K3S_EXEC="--docker" sh -
# https://blog.csdn.net/weixin_43960618/article/details/108106020
sudo vim /etc/systemd/system/multi-user.target.wants/k3s-agent.service
# 修改ExecStart的值，加上 --docker启动参数
sudo systemctl daemon-reload
# restart
service k3s-agent restart
# 或者手动启动参考https://mmdjiji.com/2022/09/1201/
# 或许要开通防护墙，sudo ufw allow from 192.168.31.22 to any port 6443 proto tcp
k3s agent --server <K3s服务器地址>:<端口> --token <节点令牌>
sudo /usr/local/bin/k3s agent --server https://192.168.31.22:6443 --token K10f0babbc6cac44560f6e3271aa0af817910b1b61712a1fbde9dee4811026fbaa8::server:157a7f73685391377e14699136b1a121 --docker
```





## yaml样例

### Dockerfile

#### nginx

Dockerfile

```shell
# 使用官方的nginx镜像作为基础镜像
FROM nginx:latest
# 复制自定义的index.html文件到nginx的默认目录下
COPY ./index.html /usr/share/nginx/html/index.html
# 暴露80端口，用于接收HTTP请求
EXPOSE 80
# 启动nginx服务
CMD ["nginx", "-g", "daemon off;"]
```

```html
<html>
<body>
    <h1>Hello World!</h1>
</body>
</html>
```

```shell
# build image
docker build -t mynginx:v1 .
```

```shell
# run
docker run --name mynginx -p 8888:80 -d mynginx:v1
```



### k8s

部署自己tag的nginx镜像，并挂载一个目录到node节点

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo-app
  template:
    metadata:
      labels:
        app: demo-app
    spec:
      containers:
      - name: mynginx
        image: mynginx:v1
          #imagePullPolicy: Never
        volumeMounts:
          - name: demo-storage
            mountPath: /var/log/myapp
      volumes:
        - name: demo-storage
          hostPath: # 挂载到所在node节点
            path: /var/log/myapp/nginx
```





# 遇到的一些问题

## 找不到docker image

咋回事呢，镜像名和tag都写错啊

```shell
302 ~/k3s  » k create -f deployment-demo.yaml
deployment.apps/demo-deployment created
306 ~/k3s  » k get pods
NAME                              READY   STATUS         RESTARTS   AGE
demo-deployment-fc474df9f-hcxfz   0/1     ErrImagePull   0          6s
demo-deployment-fc474df9f-z9tbq   0/1     ErrImagePull   0          6s
demo-deployment-fc474df9f-hzd6g   0/1     ErrImagePull   0          6s
```

### 指定仅从本地拉镜像，Failed

```yaml
 spec:
  containers:
  - name: <name>
    image: <local-image-name>
    imagePullPolicy: Never
```

```shell
313 ~/k3s  » k get pods
NAME                             READY   STATUS              RESTARTS   AGE
demo-deployment-866496cd-whg25   0/1     ErrImageNeverPull   0          4s
```



### 指定docker镜像仓库，Success

[根据我的搜索结果](https://blog.csdn.net/xs20691718/article/details/106515605)[1](https://bing.com/search?q=k3s+默认+镜像源)[2](https://blog.csdn.net/xs20691718/article/details/106515605)[，k3s 默认是使用 containerd 作为容器运行时，并从 docker.io 拉取镜像。](https://docs.k3s.io/zh/installation/private-registry)

即启动k3s server时要这样子

```shell
# /usr/local/bin/k3s-killall.sh
sudo k3s server --docker &
```



或直接指定

```yaml
# /etc/rancher/k3s/registries.yaml
mirrors:
  docker.io:
    endpoint:
      - "http://localhost:5000" # docker默认端口是5000

```

