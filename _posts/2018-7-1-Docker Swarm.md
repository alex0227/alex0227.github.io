---
layout: post
title: Docker Swarm 让你事半功倍
categories: Docker Swarm  
description: Docker Swarm 让你事半功倍
keywords: Docker Swarm
---


2016 年 DockerCon 上展示的最重大的变革之一就是 1.12 版本引擎的 Swarm 模式。它意味着什么呢？它意味着：如果你在运行 Docker 1.12时 ，你就可以原生创建一个 Swarm 集群。

## 创建一个 swarm 集群
用这样一条简单的命令：
```  
$ docker swarm init  
```
就足够创建一个 Swarm 了（虽然是一个只有简易管理节点的 Swarm，但已经是 Swarm 集群的最简集合）。  
```
$ docker node ls
ID                           HOSTNAME  MEMBERSHIP  STATUS  AVAILABILITY  MANAGER STATUS
7sytb3zk0yswdfky6mbh7nzk2 *  moby      Accepted    Ready   Active        Leader
```
来看看多节点吧！只有一个节点的 Swarm，用途是有限的，所以让我们来创建一个有两个管理节点（manager nodes）和两个工作节点（worker nodes）的 Swarm。 首先，我们来创建 4 个 Docker 主机。Docker Machine 是完成此任务的理想工具，所以我们就用它。
```
$ docker-machine ls
NAME ACTIVE DRIVER STATE URL SWARM DOCKER ERRORS
manager1 - virtualbox Running tcp://192.168.99.100:2376 v1.12.0-rc3
manager2 - virtualbox Running tcp://192.168.99.101:2376 v1.12.0-rc3
worker1 - virtualbox Running tcp://192.168.99.102:2376 v1.12.0-rc3
worker2 - virtualbox Running tcp://192.168.99.103:2376 v1.12.0-rc3
```
如果你手头上没有别的用 Machine 创建的主机，那么在显示集群节点时，你的显示信息就需要与下文给出的范例比较接近。
```
$ docker-machine ls
NAME              ACTIVE   DRIVER         STATE     URL                         SWARM   DOCKER        ERRORS
manager1          -        virtualbox     Running   tcp://192.168.99.100:2376           v1.12.0-rc3
manager2          -        virtualbox     Running   tcp://192.168.99.101:2376           v1.12.0-rc3
worker1           -        virtualbox     Running   tcp://192.168.99.102:2376           v1.12.0-rc3
worker2           -        virtualbox     Running   tcp://192.168.99.103:2376           v1.12.0-rc3
```

## 初始化 Swarm
上文介绍了一种用 1.12 版引擎来创建 Swarm 的最简单的命令（提示：“docker swarm init”），但我们在这里还将调用几种附加选项，让集群主机可以互相通信，并在无需许可的情况下加入集群。
```
$ MANAGER1_IP=$(docker-machine ip manager1)
$ docker-machine ssh manager1 docker swarm init --auto-accept manager --auto-accept worker --listen-addr $MANAGER1_IP:2377
```
*注意*：–listen-addr 是 Swarm 内不同节点互相访问的地址

## 添加第二管理节点
Docker Swarm 命令中还需要添加一些选项：
* join：表明一个新的节点将被添加进 Swarm
* –manager：表明节点的性质（manager vs worker）
* –listen-addr：让一个新添加的节点可以访问 Swarm 内的其他节点
* 最后的参数就是第一管理节点的地址（即这一命令将被送到的那个节点）

*注意*：由于 –auto-accept manager 选项会在 Swarm 初始化的过程中被提供，所以第二管理节点会被自动接受。如果没有这一选项，那么第二管理节点需要被第一管理节点手动接受。
```
$ MANAGER2_IP=$(docker-machine ip manager2)
docker-machine ssh manager2 docker swarm join --manager --listen-addr $MANAGER2_IP:2377 $MANAGER1_IP:2377
```

## 添加工作节点
往集群内添加工作节点的方式，跟添加管理节点几乎相同：
```
$ WORKER1_IP=$(docker-machine ip worker1)
$ docker-machine ssh worker1 docker swarm join --listen-addr $WORKER1_IP:2377 $MANAGER1_IP:2377
$ WORKER2_IP=$(docker-machine ip worker2)
$ docker-machine ssh worker2 docker swarm join --listen-addr $WORKER2_IP:2377 $MANAGER1_IP:2377
```
*注意*：由于 –auto-accept worker 选项会在 Swarm 初始化的过程中被提供，所以工作节点会被自动接受。如果没有这一选项，那么工作节点需要被管理节点手动接受。
我们的 Swarm 看起来像什么呢？让我们来看看。
```
$ docker-machine ssh manager1 docker node ls
ID                           HOSTNAME  MEMBERSHIP  STATUS  AVAILABILITY  MANAGER STATUS
109a5ufy8e3ey17unqa16wbj7    manager2  Accepted    Ready   Active        Reachable
4chbn8uphm1tidr93s64zknbq *  manager1  Accepted    Ready   Active        Leader
8nw7g1q0ehwq1jrvid1axtg5n    worker2   Accepted    Ready   Active
8rrdjg4uf9jcj0ma2uy8rkw5v    worker1   Accepted    Ready   Active
```
现在每个节点都归属于 Swarm，并都处在了待机状态。管理节点 1 是领导者，一切都各居其位，井井有条,是什么使它如此特别呢？
这个 Swarm 是得到安全传输层协议（TLS）保证的，能在设备外自动认证升级。

同样，它也不再需要 Consul、Zookeeper 之类的键值存储，所有东西都在掌控之中。
