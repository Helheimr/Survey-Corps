---
title: 酸酸乳
urlname: ssr
date: 2019-09-14 15:33:50
tags: ssr
categories: [Notes]
description: 喝茶了，逗比也倒了，她不香了.V2ray老大哥替你的位置。
cover: https://zyue-pic.oss-cn-shenzhen.aliyuncs.com/XFOAdH.png
top_img:  
---
原冲浪手段，随笔记录，现已放弃使用。
<!--more-->

# __弃SSR,转v2ray.__

# ~~秋神一键脚本安装SSR~~
 随时可能失效
```bash
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
```
```bash
chmod +x shadowsocks-all.sh
```
```bash
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

卸载方法
```bash
./shadowsocks-all.sh uninstall
```

配置文件路径：/etc/shadowsocks.json


启动：/etc/init.d/shadowsocks start
停止：/etc/init.d/shadowsocks stop
重启：/etc/init.d/shadowsocks restart
状态：/etc/init.d/shadowsocks status

------
# ~~防火墙策略~~
1. vi /etc/sysconfig/iptables
2. 修改（红色为新增行）
```bash
iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport 20192 -j ACCEPT
```
![iptables](https://zyue-pic.oss-cn-shenzhen.aliyuncs.com/iptables.png)

重启
```bash
service iptables restart
/etc/init.d/iptables restart

/sbin/iptables -I INPUT -p udp --dport 19030 -j ACCEPT
/sbin/iptables -I INPUT -p tcp --dport 19030 -j ACCEPT

iptables -F 清空策略 
/etc/init.d/iptables save 保存现有 
/etc/init.d/iptables restart 
/etc/init.d/iptables status(正常了) 
```

# ~~~锐速脚本~~~

## centos7

1.更换内核
```bash
wget --no-check-certificate -O rskernel.sh https://raw.githubusercontent.com/uxh/shadowsocks_bash/master/rskernel.sh && bash rskernel.sh
```

2.安装锐速
```bash
yum install net-tools -y && wget --no-check-certificate -O appex.sh https://raw.githubusercontent.com/0oVicero0/serverSpeeder_Install/master/appex.sh && bash appex.sh install
```


## centos6
```bash
wget --no-check-certificate -O appex.sh https://raw.githubusercontent.com/0oVicero0/serverSpeeder_Install/master/appex.sh && bash appex.sh install '2.6.32-642.el6.x86_64'
```


可选用BBR,魔改BBR,KCP其他策略, 其他可戳 此网站 `ssr.tools`