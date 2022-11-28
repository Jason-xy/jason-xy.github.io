---
title: WordPress全站优化策略——COS配置策略
category_bar: true
date: 2020-7-22 9:46:39
tags: ['wordpress', 'cos', 'ftp']
categories:
- 技术
- Web
---

COS的部署可以极佳地解决WordPress图像文件、.css、.js等文件加载慢的问题，其原理就跟CDN相似，只不过是在服务器直接将内容的链接指向COS对象存储服务器，就相当于将COS做为内容分发服务器。那么按照这个思路，我们还可以把主题加载的相关图片、字体等全部托管在COS对象存储服务器。那么看看是怎么部署的吧。

## 腾讯云COS配置

### 创建储存桶

进步腾讯云控制台，按照指引添加新的储存桶：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/wp_cos/1.png" style="zoom:80%;" />

选择储存桶权限为：**公有读私有写**

### 获取信息

记录下以下内容：

- 存储桶名称
- 存储桶地域
- APP ID
- SecretID
- SecretKey

前两个在储存桶详细信息中获取，后三个在密钥管理中获取。

### COS管理

可以选择腾讯官方的COSBrowser

| Windows                                                      | Mac                                                          | Linux                                                        | Android                                                      | IOS                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------- |
| [下载](https://cosbrowser-1253960454.cos.ap-shanghai.myqcloud.com/releases/cosbrowser-setup-2.2.3.exe) | [下载](https://cosbrowser-1253960454.cos.ap-shanghai.myqcloud.com/releases/cosbrowser-2.2.3.dmg) | [下载](https://cosbrowser-1253960454.cos.ap-shanghai.myqcloud.com/releases/cosbrowser-1.5.3-linux.zip) | [下载](https://cosbrowser-1253960454.cos.ap-shanghai.myqcloud.com/mobile/Android/release/latest/cosbrowser-latest.apk) | [App Store](https://apps.apple.com/cn/app/id1469323992) |



## WordPress插件配置

下载插件：**[腾讯云COS](https://qq52o.me/2518.html)**（[下载](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/wp_cos/wordpress-qcloud-cos-1.8.4.zip)）

按照插件提示进行设置：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/wp_cos/2.png" style="zoom:80%;" />

参数说明：

1. 前五项就是刚刚创建记录的信息，填上去就好。
2. 本地文件夹一般选**wp-content/uploads**，其中包含了：媒体库、主题css、字体等页面美化有关的资源。
3. URL前缀就是**wp-content/uploads**在你COS中的直链。

图像处理方式请参考[**数据万象文档**](https://cloud.tencent.com/document/product/460/6936)

之后点击**保存更改**。

## 文件迁移

不建议用自带的同步历史附件，有点玄学，我试了几次都失败了，然后网站就挂了。

这里只需进入服务器网站文件夹，将**wp-content/uploads**文件夹全部下载下来，然后整包上传至COS对应文件夹就可以实现同步。

在宝塔面板中可以看到服务器文件夹下的情况。

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/wp_cos/3.png" style="zoom:80%;" />

## 查看验证

先访问一下网站看能否正常显示。然后进入媒体库随便查看一个媒体的链接，看一下是否替换成了相应的COS链接：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/wp_cos/4.png" style="zoom:80%;" />

如果成功替换，并且网页能够正常显示，那么COS部署就基本成功了！

如果有用的话记得三连噢~