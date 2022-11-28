---
title: manjaro 切换国内源及中文输入法安装
category_bar: true
date: 2020-07-27 8:53:43
tags: ['linux', 'manjaro']
categories:
- 技术
- Linux
- ENV
---

# 配置国内源

1.配置镜像源:

```bash
sudo pacman-mirrors -i -c China -m rank
```

2.设置 archlinuxcn 源,antergos源,arch4edu源:

```bash
sudo nano /etc/pacman.conf
```

```conf
[archlinuxcn]#选一个就行了
SigLevel = Optional TrustedOnly
#中科大源
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
#清华源
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch

[antergos]
SigLevel = TrustAll
Server = https://mirrors.tuna.tsinghua.edu.cn/antergos/$repo/$arch

[arch4edu]
SigLevel = TrustAll
Server = https://mirrors.tuna.tsinghua.edu.cn/arch4edu/$arch
```

3.更新源列表

```bash
sudo pacman-mirrors -g
```

4.更新pacman数据库并全面更新系统

```bash
sudo pacman -Syyu
```

5.防止PGP签名错误

```bash
sudo pacman -S archlinuxcn-keyring
sudo pacman -S antergos-keyring
```

6.为了安装使用aur方便，也可以额外安装一下yaourt

```bash
sudo pacman -S yaourt
```

7.yaourt 用户
添加之前首先备份原文件

```bash
cp /etc/yaourtrc /etc/yaourtrc.backup
```

修改 /etc/yaourtrc配置文件

```bash
nano /etc/yaourtrc
```

去掉 # AURURL 的注释,并修改

```
AURURL=“https://aur.tuna.tsinghua.edu.cn”
```

8.yay 用户

执行以下命令修改 aururl :

```bash
yay --aururl “https://aur.tuna.tsinghua.edu.cn” --save
```

# 讯飞输入法

```bash
yay -S iflyime
```

```
//回到home目录
cd ~
vim .xprofile
//写入
export GTK_IM_MODULE=fcitx  
export QT_IM_MODULE=fcitx 
export XMODIFIERS=@im=fcitx
```

注销后登录，就可以愉快打字啦。