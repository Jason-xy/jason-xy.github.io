---
title: 使用docker部署WordPress
date: 2020-06-18 19:16:52
category_bar: true
tags: ['docker', 'blog']
categories:
- 技术
- Web
---

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/wordpress_docker/34173314725397cecc026b7371fa78ee-1024x640.jpg)
## 基于Docker的部署方案

容器部署在Docker下部署WordPress的难点在于**数据库的连接**，下面给出一种可行的方案。

### 前提条件：

- 任意可以运行的Linux发行版
- 已安装完成Docker相关组件
- 完成了Docker镜像源相关的设置

### 安装步骤：

#### 1. 数据库容器配置

```
docker pull MariaDB //下载mysql镜像
docker volume create mysql1 //创建数据卷
docker run -d --privileged=true --name my-mysql -v /data/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -p 33306:3306 mariadb
```

参数解释:

- -p: 端口映射，33306表示宿主，3306表示容器中的端口。 这里表示将宿主机的33306映射给镜像的3306。
- -e: 环境变量， 环境变量和具体的Docker容器制作时设置有关，这里表示设置镜像中MySQL的root 密码时123456
- -v: 指定数据卷，也就是将我们MySQL容器的/var/lib/mysql映射到宿主机的/data/mysql
- –privileged=true: CentOS系统下的安全Selinux禁止了一些安全权限，导致MySQL容器在运行时会因为权限不足而报错，所以需要增加该选项

#### 2. WordPress容器配置

```
docker pull wordpress //下载镜像
docker run -d --name my-wp -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_PASSWORD=123456 -p 8080:80 --link my-mysql:mysql wordpress //运行镜像创建容器
```

参数解释：

- “WORDPRESS_DB_HOST”: 链接的docker的MySQL的IP地址和端口，一般设置成mysql表示用默认的设置
- “WORDPRESS_DB_USER”: 以什么用户使用MySQL，默认是root
- “WORDPRESS_DB_PASSWORD” 这设置MySQL的登陆用户密码，由于上一项是默认的root，所以这一项和之前的MYSQL_ROOT_PASSWORD“要相同。
- WORDPRESS_DB_NAME”: 数据库的表名，不需要修改，用默认的”wordpress就行

#### 3. 进入Web界面设置

浏览器地址栏输入IP:Port访问即可