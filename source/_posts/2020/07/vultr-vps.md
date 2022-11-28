---
title: Vultr VPS-服务器地址（机房）选择方法
category_bar: true
date: 2020-07-15 10:42:30
tags: ['vps', 'cloud']
categories:
- 技术
- Web
---

> 如何购买vps以及后续服务的搭建不在本文讨论范围，如需帮助可私信联系。
> 若有帮助请按个赞。

------

> 更新：有的网络不管怎么操作怎么选择节点，还是会经常timeout，或者连上也卡，比如教育网。 自己搭的还是还简陋了，唯一优点是便宜，追求良好体验的看评论，我现在已经放弃vultr了。

1. 不同地区的服务器在价格上可能有一定差异，以vultr为例，小部分服务器可以选择2.5$/month 的价格，大部分最低为5$/moth，如果要选择最便宜的2.5$/month，那选择范围就非常小了，基本没有选择的余地，看哪个服务器支持就选哪个好了。
2. 选择正常价格的服务器。唯一需要考虑的就是本地与服务器的连通性，通常推荐最多的是日本，不过到底适不适合自己的网络，需要实际测试才知道。下表给出了各个地区服务器的地址，通过Ping就可以检测本地与服务器的连通性了。

|         服务器地区         |        服务器地址        |
| :------------------------: | :----------------------: |
|       Frankfurt, DE        |  fra-de-ping.vultr.com   |
|       Paris, France        |  par-fr-ping.vultr.com   |
|       Amsterdam, NL        |  ams-nl-ping.vultr.com   |
|         London, UK         |  lon-gb-ping.vultr.com   |
|         Singapore          |    sgp-ping.vultr.com    |
|       New York (NJ)        |   nj-us-ping.vultr.com   |
|        Tokyo, Japan        |  hnd-jp-ping.vultr.com   |
|     Chicago, Illinois      |   il-us-ping.vultr.com   |
|      Atlanta, Georgia      |   ga-us-ping.vultr.com   |
|       Miami, Florida       |   fl-us-ping.vultr.com   |
|    Seattle, Washington     |   wa-us-ping.vultr.com   |
|       Dallas, Texas        |   tx-us-ping.vultr.com   |
| Silicon Valley, California | sjo-ca-us-ping.vultr.com |
|  Los Angeles, California   | lax-ca-us-ping.vultr.com |
|     Sydney, Australia      |  syd-au-ping.vultr.com   |

1. 打开命令行窗口：按![win](https://math.jianshu.com/math?formula=win)键+![R](https://math.jianshu.com/math?formula=R)，输入![cmd](https://math.jianshu.com/math?formula=cmd)按回车，然后将下面代码复制进去按回车，就会依次ping上表中的每个节点，记录下结果后，选择无丢失且延迟最小的节点。



```bash
ping fra-de-ping.vultr.com    
ping par-fr-ping.vultr.com    
ping ams-nl-ping.vultr.com    
ping lon-gb-ping.vultr.com    
ping sgp-ping.vultr.com    
ping nj-us-ping.vultr.com    
ping hnd-jp-ping.vultr.com    
ping il-us-ping.vultr.com    
ping ga-us-ping.vultr.com    
ping fl-us-ping.vultr.com    
ping wa-us-ping.vultr.com    
ping tx-us-ping.vultr.com    
ping sjo-ca-us-ping.vultr.com    
ping lax-ca-us-ping.vultr.com    
ping syd-au-ping.vultr.com
^
```

1. 实际运行时，单次结果可能不太可靠，最好多次选择不同时间节点测试，出现丢失的节点就不用看了。下表是我多次测试的结果，本地网络为西安电信教育网。1、2、3次分别是上午、下午、晚上测试的数据，可以看出最好的是**Los Angeles**的节点，其次是**Paris**和**Atlanta**。而**Tokyo**确实是延迟最低的服务器，但是下午和晚上延迟就变高了，而且丢包严重，所以并不适合我。

|           服务器地区           | 1st ping(ms)  |   2nd ping   |   3rd ping   |
| :----------------------------: | :-----------: | :----------: | :----------: |
|       ~~Frankfurt, DE~~        |      230      |     238      | 50% lost 240 |
|         Paris, France          |      198      |     240      |     244      |
|       ~~Amsterdam, NL~~        |      199      | 25% lost 388 |     289      |
|         ~~London, UK~~         |      289      | 25% lost 351 | 25% lost 253 |
|         ~~Singapore~~          |   100% lost   | 50% lost 191 | 50% lost 194 |
|       ~~New York (NJ)~~        | 25% lost，312 |     332      |     262      |
|        ~~Tokyo, Japan~~        | 50% lost 124  | 25% lost 210 |   25% 219    |
|     ~~Chicago, Illinois~~      |   100% lost   |     340      |     241      |
|        Atlanta, Georgia        |      251      |     256      |     259      |
|       ~~Miami, Florida~~       |   100% lost   |     381      |     334      |
|      Seattle, Washington       |      307      |     361      |     289      |
|         Dallas, Texas          |      312      |     309      |     208      |
| ~~Silicon Valley, California~~ |      241      |     363      | 25% lost 297 |
|    Los Angeles, California     |      175      |     189      |     190      |
|     ~~Sydney, Australia~~      |   100% lost   |     263      |     366      |

V2ray一键脚本
`bash <(curl -s -L https://git.io/v2ray.sh)`

Trojan一键脚本
`wget -N --no-check-certificate -q -O trojan_install.sh "https://raw.githubusercontent.com/V2RaySSR/Trojan/master/trojan_install.sh" && chmod +x trojan_install.sh && bash trojan_install.sh`

