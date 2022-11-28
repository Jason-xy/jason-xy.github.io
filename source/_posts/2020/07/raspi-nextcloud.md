---
title: 使用树莓派搭建私有云盘
category_bar: true
date: 2020-07-26 12:40:59
tags: ['raspberry-pi', 'cloud', 'docker']
categories:
- 技术
- Web
---

1. 通过内网穿透实现外网访问。

   [frp内网穿透方案](https://jason-xy.cn/2020/07/frp/)

2. 部署NextCloud-Docker

## 内网穿透

请参考文章：frp内网穿透方案。

## NextCloud-Docker

选择docker部署的主要原因还是相对来说方便部署，一行代码即可完成傻瓜式部署。

### 服务部署

前提条件：

1. 你已经安装好了`docker`和`docker-compose`

   [Docker部署及Portainer可视化面板搭建](https://jason-xy.cn/2020/07/docker_portainer/)

   [Docker Compose部署](https://jason-xy.cn/2020/07/docker-compose/)

2. 创建`nextcloud`文件夹，然后在文件夹中创建`docker-compose.yml`（相当于Docker镜像部署脚本）

   `docker-compose.yml`文件内容如下：

   ```yml
   version: '2'
   
   services:
     db:
       image: ibex/debian-mysql-server-5.7
       restart: always
       volumes:
         - ./cloud/db:/var/lib/mysql
       environment:
         - MYSQL_ROOT_PASSWORD=ROOT密码 # 根据需要替换
         - MYSQL_PASSWORD=数据库密码 # 根据需要替换
         - MYSQL_DATABASE=nextcloud # 根据需要替换
         - MYSQL_USER=nextcloud # 根据需要替换
   
     app:
       image: nextcloud
       ports:
         - 8888:80  # 端口映射，将Docker的80端口，映射成主机的8888端口。根据需要可以自行修改。
       links:
         - db
       volumes:
         - ./cloud/config:/var/www/html/config
         - /home/pi/disk/nextCloud:/var/www/html/data # 因为树莓派本身存储太小，这里是映射到一个外置2T硬盘
         - ./cloud/apps:/var/www/html/apps
       restart: always
   ```

   

执行脚本：

在当前目录下（`nextcloud`文件夹）执行下面命令，Docker会根据我们的配置，自动下载镜像并启动程序。

```bash
docker-compose up -d
```

### 初始配置

如果一切正常，这时候，访问`IP:8888` 就会出现 NextCloud界面。
第一次启动，需要配置`数据库`和`账户`才可以正常使用。
配置完毕，网盘就可以正常使用了。

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/docker_nextcloud/1.jpg" style="zoom:80%;" />





### 扩展资料：

>#### 关于外置硬盘问题：

>树莓派空间实在太小，不适合做存储。
>这里我使用了一个2T 的移动硬盘作为存储设备。
>关于挂载硬盘，并且开机自动挂载，可以参考Linux移动硬盘挂载

> #### 添加不受信任的域名
>
> Nextcloud初始化完毕后，会生成  `/var/www/nextcloud/config/config.php`  配置文件，里面的 `trusted_domains`  配置项为信任域名，初始化完毕后只有一项，为主机的IP地址。可以修改该配置项，添加绑定的域名。
>
> 
>
> ```php
> 'trusted_domains' => 
> array (
> 0 => '127.0.0.1',
> 1 => 'xxx.yyyyy.com',  # 将这个自定义域名添加到配置中
> ),
> ```

> #### 使用SqLite数据库
>
> 不想使用MySQL，或者个人使用时，可以使用Sqlite数据库，配置更简单。
> docker-compose.yml 文件内容修改如下：
>
> 
>
> ```yml
> version: '2'
> 
> app:
>  image: nextcloud
>  ports:
>       - 8888:80
>     volumes:
>       - ./cloud/config:/var/www/html/config
>       - /home/pi/disk/nextCloud:/var/www/html/data
>       - ./cloud/apps:/var/www/html/apps
>     restart: always
> ```

