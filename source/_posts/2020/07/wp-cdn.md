---
title: WordPress全站优化策略——CDN加速配置
category_bar: true
date: 2020-7-2 16:47:34
tags: ['wordpress', 'cdn']
categories:
- 技术
- Web
---

其实尝试过在WordPress使用CDN内容分发网络之后，会发现这并不是一件舒服的事，一会账户无法登陆了，一会文章修改了不刷新了......

造成这一系列问腿的主要还是WordPress是一个动态网站，主要靠数据库和php协调生成网页，而CDN分发网络仅仅适用于静态加速，所以WordPress在上CDN时缓存配置策略一定要好好研究。

### 我的CDN缓存策略

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/wp_cdn/cdn.png" style="zoom:80%;" /></div>

看图片我们知道了如何做，下面讲下为什么这么做。

#### 需要进行缓存的静态文件：

1. .woff2;.otf;.ot;.svg;.ttf;.woff是各类字体文件，我们将其缓存下来可以提高很多速度。

2. .jpg;.gif;.png;.bmp;.ico是各类图片文件。
3. .css;.js是网页中各种样式的文件类型，这个占比最大。

4. 所有文件就是其他我们下面没有包括在里面的文件类型，根据自己需求设置，一般30天就行。当然也可以像我一样每天刷新。

#### 不需要进行缓存的文件

1. /wp-admin是wordpress网站后台的地址，我们让其缓存设置为0天是为了防止打不开后台这种情况发生。

2. .gz;.xml;.php这个里面.php是我们网页的文件类型，防止更新文章后打开网站看不到新发表的文章。.xml是站点地图的文件，设置0是为了防止站点地图更新后搜索引擎抓取不到新更新的URL文件。.gz也是站点地图的文件，不同插件后缀不同，有的是xml有的是gz。

### 下面看下配置完CDN加速后的效果

第一次测试，文件还没完全分发到各个CDN服务器：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/wp_cdn/test1.png" style="zoom:80%;" /></div>

第二次测试，文件基本分发完成，速度有明显提升：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/wp_cdn/test2.png" style="zoom:80%;" /></div>

第三次测试，可以看到加载速度基本稳定在了零点几秒，也基本能达到我们的预期效果了：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/wp_cdn/test3.png" style="zoom:80%;" /></div>



为什么三次测速速度越来越快，是因为CDN的作用主要就是缓存网站的文件，这样访客访问时候就直接从CDN就给访客返回文件，不需要再走你的服务器。而且因为CDN节点多，避免了很多从你服务器到访客之间的网络线路问题。

以上仅提供一个缓存思路，如果有更好的方法欢迎一起交流。