---
title: 使用VS Code远程开发树莓派
category_bar: true
date: 2020-07-20 13:03:54
tags: ['raspberry-pi', 'vscode']
categories:
- 技术
- Linux
- ARM
---

VS Code是微软推出的一款非常好用的文本编辑器，更是微软为数不多的良心开源产品！！！得益于丰富的开源生态，合理利用VScode甚至可以在一定程度上替代IDE。这里主要介绍一款前几天发现的宝藏插件，对于我这种用不惯vim的菜鸟，简直让我知乎VScode，yyds！

## 安装

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/vsc_ssh/1.png" style="zoom:80%;" />

点击extensions，如上图方框所示，搜索remote ssh并安装。安装完成后，使用组合键`ctrl+shift+p`打开输入框，输入remote ssh，你将看到类似于下图的结果：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/vsc_ssh/2.png" style="zoom:80%;" />

选中上图中高亮的选项，即添加新主机的选项。你将看到如下图所示的选项：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/vsc_ssh/7.png" style="zoom:100%;" />

这个地方和Linux中的SSH命令用法一样，按照如下格式即可连接你的主机：

```bash
ssh user@IP/domain_name -p Port
```

此处的Port如果没有改过的话默认是22，完成后会让你选择密钥存储位置，这个就各自随意了，但是最好每次加入新主机的时候都选同一个位置，方便以后查找。

完成后，VS Code的侧边栏会出现一个新的图标，如下图所示：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/vsc_ssh/8.png" style="zoom:80%;" />

点击图标，就能看到刚才加入的主机，在主机名上右键便能看到连接的选项，选择在新窗口打开，此时会要求输入远程主机的密码，输入后回车即可。

完成后点击左侧边栏的文件图标，选择打开文件夹，如下图所示：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/vsc_ssh/5.png" style="zoom:80%;" />

在弹出的下拉菜单中选择你要打开的文件夹，然后就可以像在本机使用那样新建文件等。

同时，你也可以在VS Code自带的终端中输入命令，如下图所示：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/vsc_ssh/6.png" style="zoom:80%;" />

Done!!!

开始愉快的远程开发之旅吧~