---
title: about
layout: about
date: 2022-11-27 21:33:22
---
## 站点日志

2020-6-20  Jason的小站终于从内网搬到服务器啦！！！

2020-6-21   暂定域名https://uestcpi.cn | https://www.uestcpi.cn

2020-6-22  装修了一下主页，加了亿点点特效噢！COS对象存储服务部署完成。

2020-6-23  博客现在可以自己发送邮件啦！

2020-6-26  给网站注册了新的域名jason-xy.cn，现在正在备案呢。

2020-6-28  添加了后台统计功能，统计访客浏览信息。

2020-7-1    网站服务器更换为Debian LAMP框架。自动静态缓存部署完成。

2020-7-2    新域名https://jason-xy.cn | https://jason-xy.cn上线 。CDN内容分发网络部署完成。评论与回复模块添加完成。

2020-7-3    统计面板功能实现。社交分享与赞赏功能实现。修复Markdown语法高亮显示问题。

2020-7-20   移植Pi-Dashboard项目，给网站服务器添加了实时监控仪表盘。

2020-7-21   图床部署完成，所有图片及媒体完全移至腾讯云COS对象存储服务器。

2020-7-22   https://gallery.jason-xy.cn 图床网站上线。完成动态网站的CDN静态缓存优化，解决账户登录实不及时刷新缓存问题。

2020-7-23   https://media.jason-xy.cn 音乐小站上线。

2020-7-24   http://ebook.jason-xy.cn 书库网站上线。爆肝一周丰富了网站资源，该认真预习期末考试了。

2020-7-25   由于带宽问题将书库搬到了内网，走纽约机房用frp内网穿透，理论100兆带宽，实测600ms、50Mbps。

2020-7-26   站点健康状态监控与提醒功能上线。

2020-8-11    由于网络连通性问题，frp代理服务器搬到了NJ，目前测试下载带宽500Mbps，上传带宽10Mbps。

2020-9-16   邮件服务器维修完成，感谢ROYLEO发现的BUG。

2022-12-11  腾讯云的三年学生优惠用完了，不打折实在太贵了，所以决定搬家到Github。

            更新：
                1. 框架由wordpress换为hexo。
                2. 服务器就直接用GitHub pages，正好还可以搭一个CI自动发布。
                3. 考虑到GitHub国内访问体验不太好，所以还是做了CDN全球加速。
                4. 域名用CNAME解析到jason-xy，尽量保持之前的文章链接不变。
            下线：
                1. https://gallery.jason-xy.cn图床下线 ，前端没了PicGo直连COS服务。
                2. https://media.jason-xy.cn音乐小站下线 ，免费的API太少，之前的基本用不了，懒得维护了。
                3. http://ebook.jason-xy.cn书库网站下线 ，书现在都放Goodnotes和zotero里面了，可以用iCloud和OneDrive同步，比放我的服务器更稳妥。
                4. 站点监控功能下线，uptime定格在了1000多天，云服务器稳定性确实🐂🍺，现在换GitHub了只要CI不挂应该就不太需要监控了。

<script src="https://utteranc.es/client.js"
        repo="Jason-xy/jason-xy.github.io"
        issue-term="pathname"
        label="💬"
        theme="github-dark"
        crossorigin="anonymous"
        async>
</script>