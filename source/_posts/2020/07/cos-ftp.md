---
title: 腾讯云COS-FTP部署
category_bar: true
date: 2020-07-22 12:10:42
tags: ['wordpress', 'cos']
categories:
- 技术
- Web
---

最近在用Chevereto做图床平台，但是惊奇的发现外部存储中有Aliyun OSS但是没有腾讯云的COS，这就意味着无法直接用COS与图床对接啦/(ㄒoㄒ)/~~

腾讯云太没有排面了↓

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/cos_ftp/1.png" style="zoom:80%;" />

但是我一下看到了FTP也有接口呀！但是查了一圈资料发现...COS原生不支持FTP┭┮﹏┭┮。抱着试一试的态度继续翻了翻COS的文档，突然发现了COS居然有FTP流式传输的Python官方脚本！那不妨试试用腾讯云服务器当跳板，将FTP根目录对接到COS中。

## 操作方法

#### 安装运行

FTP Server 工具下载地址为：[cos-ftp-server](https://github.com/tencentyun/cos-ftp-server-V5)。安装步骤如下：

1. 进入 FTP Server 目录，运行 setup.py 安装 FTP Server 及其相关的依赖库（需要联网）：

   ```bash
   python setup.py install   # 这里可能需要您的账号 sudo 或者拥有 root 权限。
   ```

2. 将配置示例文件`conf/vsftpd.conf.example`复制命名为`conf/vsftpd.conf`，参考本文下方章节 ，正确配置 bucket 和用户信息。

3. 运行 ftp_server.py 启动 FTP Server：

   ```bash
   python ftp_server.py
   ```

   此外还有两种方式启动 FTP Server，如下：

   - 使用 nohup 命令，以后台进程方式启动：

     ```bash
     nohup python ftp_server.py >> /dev/null 2>&1 &
     ```

   - 使用 screen 命令放入后台运行（需要安装 screen 工具)：

     ```bash
     screen -dmS ftp
     screen -r ftp
     python ftp_server.py
     #使用快捷键 Ctrl+A+D ，切回主 screen 即可。
     ```

#### 停止运行

- 若您是直接运行，或 screen 方式放在后台运行的 FTP Server，您可以使用快捷键`Ctrl+C`停止 FTP Server 运行。

- 若您是通过 nohup 命令启动，可以使用下面方式停止：

  ```bash
  ps -ef | grep python | grep ftp_server.py | grep -v grep | awk '{print $2}' | xargs -I{} kill {}
  ```



## 配置文件

FTP Server 工具的配置示例文件为`conf/vsftpd.conf.example`，请复制命名为 vsftpd.conf，并按照以下的配置项进行配置：

```conf
[COS_ACCOUNT_0]
cos_secretid = COS_SECRETID    # 替换为您的 SECRETID
cos_secretkey = COS_SECRETKEY  # 替换为您的 SECRETKEY
cos_bucket = examplebucket-1250000000
cos_region = region   # 替换为您的存储桶地域
cos_protocol = https
#cos_endpoint = region.myqcloud.com
home_dir = /home/user0
ftp_login_user_name=user0   #替换为用户自定义的账号
ftp_login_user_password=pass0   #替换为用户自定义的密码
authority=RW                # 设置该用户的读写权限，R 表示读权限，W 表示写权限，RW 表示同时具备读写权限
delete_enable=true         # true 为允许该 ftp 用户进行删除操作（默认），false 为禁止该用户进行删除操作

[COS_ACCOUNT_1]
cos_secretid = COS_SECRETID    # 替换为您的 SECRETID
cos_secretkey = COS_SECRETKEY  # 替换为您的 SECRETKEY
cos_bucket = examplebucket-1250000000
cos_region = region   # 替换为您的存储桶地域
cos_protocol = https
#cos_endpoint = region.myqcloud.com
home_dir = /home/user1
ftp_login_user_name=user1   #替换为用户自定义的账号
ftp_login_user_password=pass1   #替换为用户自定义的密码
authority=RW               # 设置该用户的读写权限，R 表示读权限，W 表示写权限，RW 表示同时具备读写权限
delete_enable=false        # true 为允许该 ftp 用户进行删除操作（默认），false 为禁止该用户进行删除操作

[NETWORK]
# 如果 FTP Server 处于某个网关或 NAT 后，可以通过该配置项将网关的 IP 地址或域名指定给 FTP
masquerade_address = XXX.XXX.XXX.XXX
# FTP Server 的监听端口，默认为2121，注意防火墙需要放行该端口（例如您是将 FTP Server 工具部署在腾讯云 CVM，则需要在 CVM 安全组放行该端口）
listen_port = 2121            
# passive_port 可以设置 passive 模式下，端口的选择范围，默认在 [60000, 65535] 区间上选择，注意防火墙（例如 CVM 安全组）需要放行此区间端口
passive_port = 60000,65535      


[FILE_OPTION]
# 默认单文件大小最大支持到200G，不建议设置太大
single_file_max_size = 21474836480

[OPTIONAL]
# 以下设置，如无特殊需要，建议保留 default 设置。如需设置，请合理填写一个整数
min_part_size       = default
upload_thread_num   = default
max_connection_num  = 512
max_list_file       = 10000                # ls 命令最大可列出的文件数目，建议不要设置太大，否则 ls 命令延时会很高
log_level           = INFO                 # 设置日志输出的级别
log_dir             = log                  # 设置日志的存放目录，默认是在 FTP Server 目录下
```

**在服务器上执行程序如果出现以下提示就说明成功了：**

直接启动调试：

```bash
root@VM-0-12-debian:/ftp/cos-ftp-server-V5# python ftp_server.py
starting  ftp server...
```

后台启动运行：

```bash
^Croot@VM-0-12-debian:/ftp/cos-ftp-server-V5# nohup python ftp_server.py >> /dev/null 2>&1 &
[1] 29981
```

