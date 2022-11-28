---
title: Typora + Chevereto图床Markdown图片自动上传
category_bar: true
date: 2021-06-23 14:36:45
tags: ['Typora', 'Chevereto', 'Markdown']
categories:
- 生活
- IT小技巧
---

## Typora 配置

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/06/23/image-20210623142424581.png)

## 编写上传脚本

### upload.py图床上传API调用程序

```python
# -*- encoding: utf-8 -*-

import requests
import json
import mimetypes
import argparse
import sys

APP_DESC = """
一个上传图片到chevereto图床的命令行工具
"""

print(APP_DESC)
if len(sys.argv) == 1:
    sys.argv.append('--help')

parser = argparse.ArgumentParser()
parser.add_argument('-s', '--source', type=str, nargs='+', help="", required=True)
parser.add_argument('-c', '--config', default="./config.json", help="读取配置文件", required=True)
args = parser.parse_args()

# 从参数中获取要上传的文件列表
img_list = args.source

def read_conf(path):
    with open(path,"r",encoding="utf-8") as f:
        confstr = f.read()
        conf = json.loads(confstr)
        #print(conf)
    return conf

def up_to_chevereto(img_list):
    # 获得本地图片路径后，上传至图床并记录返回的json字段
    for img in img_list:
        # 先判断传过来的是本地路径还是远程图片地址
        if "http" == img[:4]:
            # 非本地图片的话可以考虑下载到本地再上传，但是没这个必要
            print(img)
            continue
        else:
            try:
                res_json = upload(formatSource(img))
                parse_response_url(res_json,img)
            except:
                print(img+"\t上传失败")

def upload(files):
    # 图床api
    APIKEY = "你的key"
    conf = read_conf(args.config)
    url = conf['url'] + "?key=" + APIKEY
    r = requests.post(url, files=files)
    return json.loads(r.text)

def formatSource(filename):
    imageList = []
    mime_type = mimetypes.guess_type(filename)[0]
    imageList.append(
        ('source', (filename, open(filename, 'rb'), mime_type))
    )
    return imageList

def parse_response_url(json, img_path):
        # 从返回的json中解析字段
    if json['status_code'] != 200:
        print("{}\tweb端返回失败,可能是APIKey不对. status_code {} .".format(
            img_path, json['status_code'])
        )
    else:
        img_url = json["image"]["url"]
        print(img_url)

up_to_chevereto(img_list)
```

### config.json配置文件

```json
{
    "APIKEY": "你的key", 
    "url": "https://你的域名/api/1/upload/"
}
```

### Windows下上传命令

```powershell
python -u "脚本路径\upload.py" -c "C:配置文件路径\config.json" -s
# python -u "C:\Program Files\Typora\upload.py" -c "C:\Program Files\Typora\config.json" -s
```

### 工作效果

**上传测试**

![image-20210623143140288](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/06/23/image-20210623143140288.png)

**实际测试**

![image-20210623143228624](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/06/23/image-20210623143228624.png)

**图片链接已自动更新**

![image-20210623143315195](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/06/23/image-20210623143315195.png)

