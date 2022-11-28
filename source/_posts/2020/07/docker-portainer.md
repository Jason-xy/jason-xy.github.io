---
title: Docker部署及Portainer可视化面板搭建
category_bar: true
date: 2020-07-07 22:04:17
tags: ['docker', 'portainer']
categories:
- 技术
- Web
---

**测试平台（理论上Linux下都大同小异）**：VMware Deepin 15.5 based on Debian

## Docker安装

推荐**官方安装脚本自动安装**

安装命令：

```bash
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

也可以使用国内 daocloud 一键安装命令：

```bash
curl -sSL https://get.daocloud.io/docker | sh
```

安装过程如下：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/docker/1.png" style="zoom:100%;" /></div>

## Portainer搭建可视化界面

## Portainer介绍

Portainer是Docker的图形化管理工具，提供状态显示面板、应用模板快速部署、容器镜像网络数据卷的基本操作（包括上传下载镜像，创建容器等操作）、事件日志显示、容器控制台操作、Swarm集群和服务等集中管理和操作、登录用户管理和控制等功能。功能十分全面，基本能满足中小型单位对容器管理的全部需求。

**下载Portainer镜像**
**查询当前有哪些Portainer镜像**

```bash
docker search portainer
```



<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/docker/2.png" style="zoom:100%;" /></div>

上图就是查询出来的有下载量的portainer镜像，我们下载第一个镜像：docker.io/portainer/portainer。

**下载镜像**

```bash
docker pull docker.io/portainer/portainer
```

### 单机部署

如果仅有一个docker宿主机，则可使用单机版运行，Portainer单机版运行十分简单，只需要一条语句即可启动容器，来管理该机器上的docker镜像、容器等数据。

```bash
docker run -d -p 9000:9000 \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
--name prtainer-test \
docker.io/portainer/portainer
```

该语句用宿主机9000端口关联容器中的9000端口，并给容器起名为portainer-test。执行完该命令之后，使用该机器IP:PORT即可访问Portainer。

访问方式：http://IP:9000

首次登陆需要注册用户，给admin用户设置密码：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/docker/3.png" style="zoom:100%;" /></div>

单机版这里选择local即可，选择完毕，点击Connect即可连接到本地docker：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/docker/4.png" style="zoom:100%;" /><div>


注意：该页面上有提示需要挂载本地 /var/run/docker.socker与容器内的/var/run/docker.socker连接。因此，在启动时必须指定该挂载文件。

首页：

<img align=center src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/docker/5.png" style="zoom:100%;" />

***DONE!***