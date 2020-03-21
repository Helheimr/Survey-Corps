# 调查兵团第57次墙外调查—v2ray篇

本文只是对个人搭建实例的一些收录，不算教程，详情可去下方相关链接官网查看。
![google](https://zyue-pic.oss-cn-shenzhen.aliyuncs.com/google.png)

# 准备工作

## 时间是否准确

V2Ray 对于时间有比较严格的要求，要求服务器和客户端时间差绝对值不能超过 2 分钟，所以一定要保证时间足够准确。还好 V2Ray 并不要求时区一致。

对于 V2Ray，它的验证方式包含时间，就算是配置没有任何问题，如果时间不正确，也无法连接 V2Ray 服务器的，服务器会认为你这是不合法的请求。所以系统时间一定要正确，只要保证时间误差在90秒之内就没问题。

对于 VPS(Linux) 可以执行命令 date -R 查看时间：
```
$ date -R
Sun, 22 Jan 2017 10:10:36 -0500
```
输出结果中的 -0500 代表的是时区为西 5 区，如果转换成东 8 区时间则为 2017-01-22 23:10:36。

如果时间不准确，可以使用 date --set 修改时间：
```
$ sudo date --set="2017-01-22 16:16:23"
Sun 22 Jan 16:16:23 GMT 2017
```
如果你的服务器架构是 OpenVZ，那么使用上面的命令有可能修改不了时间，直接发工单联系 VPS 提供商的客服吧，就说你在 VPS 上运行的服务对时间有要求，要他们提供可行的修改系统时间的方法。

无论是 VPS 还是个人电脑，时区是什么无所谓，因为 V2Ray 会自动转换时区，但是时间一定要准确

## __启动问题__
使用脚本新安装 V2Ray 后不会自动运行，而是要自己手动运行。另外如果修改了配置文件，要重新启动 V2Ray 新的配置才会生效。
```
systemctl restart v2ray
```

## 配置文件的格式问题
因为 V2Ray 的配置文件比较长，层级也多，导致编辑时很容易出错，也难检查。如果使用在线的 JSON 工具（当然也有离线 的），可以检查文件格式是否正确。

## 代理设置问题
- 浏览器可以设置浏览器代理。
- 在系统选项中设置代理。
- 或者也可以使用浏览器插件，如 SwitchyOmega 等。

## 最后
- 善用搜索引擎搜索解决。
- 查看相关文档解决。如果看不懂，看不明白，那么只能说明你的综合能力还需提高（比如查资料的能力、阅读理解能力的）。
- 一个提问的智慧。到社区里提问是迫不得已的办法。

# GCP(非GCP可忽略)
- [防火墙设置](https://233blog.com/post/30/)
- __不建议！！不推荐！！__ __推荐使用GCP网页SSH连接。__[SSH连接GCP](https://blog.csdn.net/datadev_sh/article/details/79593360)

# 安装

## 脚本安装
在 Linux 操作系统， V2Ray 的安装有脚本安装、手动安装、编译安装 3 种方式。

现默认使用 Debian 8.7 系统脚本安装作为示范。

确保root用户下
```
bash <(curl -L -s https://install.direct/go.sh)
```
此脚本会自动安装以下文件：
```
/usr/bin/v2ray/v2ray：V2Ray 程序；
/usr/bin/v2ray/v2ctl：V2Ray 工具；
/etc/v2ray/config.json：配置文件；
/usr/bin/v2ray/geoip.dat：IP 数据文件
/usr/bin/v2ray/geosite.dat：域名数据文件
```
此脚本会配置自动运行脚本。自动运行脚本会在系统重启之后，自动运行 V2Ray。目前自动运行脚本只支持带有 Systemd 的系统，以及 Debian / Ubuntu 全系列。

运行脚本位于系统的以下位置：
```
- /etc/systemd/system/v2ray.service: Systemd
- /etc/init.d/v2ray: SysV
```
脚本运行完成后，你需要：
```
1. 编辑 /etc/v2ray/config.json 文件来配置你需要的代理方式；
2. 运行 service v2ray start 来启动 V2Ray 进程；
3. 之后可以使用 service v2ray start|stop|status|reload|restart|force-reload 控制 V2Ray 的运行。
```
# 配置加速
## 魔改BBR(推荐)

> 关于暴力魔改BBR BBR是Google出品的TCP拥塞控制算法，目前集成在最新的Linux内核中。
国外VPS服务器上安装BBR后，可以明显提高连接速度，降低丢包。  BBR对SS/SSR有明显的加速作用，看Youbube视频时更为明显。另外如果在国外VPS服务器上架设网站，BBR也可以加速网站的加载速度。

Debian:
```
wget --no-check-certificate https://github.com/tcp-nanqinlang/general/releases/download/3.4.2.1/tcp_nanqinlang-fool-1.3.0.sh
```
```
bash tcp_nanqinlang-fool-1.3.0.sh
```

CentOS:
```
wget --no-check-certificate https://raw.githubusercontent.com/tcp-nanqinlang/general/master/General/CentOS/bash/tcp_nanqinlang-1.3.2.sh
```
```
bash tcp_nanqinlang-1.3.2.sh
```

## 配置mkcp加速(不推荐,酌情使用)

>V2Ray 引入了 KCP 传输协议，并且做了一些不同的优化，称为 mKCP。如果你发现你的网络环境丢包严重，可以考虑一下使用 mKCP。由于快速重传的机制，相对于常规的 TCP 来说，mKCP 在高丢包率的网络下具有更大的优势，也正是因为此， mKCP 明显会比 TCP 耗费更多的流量，所以请酌情使用。要了解的一点是，mKCP 与 KCPTUN 同样是 KCP 协议，但两者并不兼容。

>在此我想纠正一个概念。基本上只要提起 KCP 或者 UDP，大家总会说”容易被 Qos“。Qos 是一个名词性的短语，中文意为服务质量，试想一下，你跟人家说一句”我的网络又被服务质量了“是什么感觉。其次，哪怕名词可以动词化，这么使用也是不合适的，因为 Qos 区分网络流量优先级的，就像马路上划分人行道、非机动车道、快车道、慢车道一样，哪怕你牛逼到运营商送你一条甚至十条专线，是快车道中的快车道，这也是 Qos 的结果。

### 服务器配置
mKCP 的配置比较简单，只需在服务器的 inbounds 和 客户端的 outbounds 添加一个 streamSettings 并设置成 mkcp 即可。
```json
{
  "inbounds": [
    {
      "port": 16823,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "b831381d-6324-4d53-ad4f-8cda48b30811",
            "alterId": 64
          }
        ]
      },
      "streamSettings": {
        "network": "mkcp", //此处的 mkcp 也可写成 kcp，两种写法是起同样的效果
        "kcpSettings": {
          "uplinkCapacity": 5,
          "downlinkCapacity": 100,
          "congestion": true,
          "header": {
            "type": "none"
          }
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```

### 客户端配置
```json
{
  "inbounds": [
    {
      "port": 1080,
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
    },
      "settings": {
        "auth": "noauth"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "serveraddr.com",
            "port": 16823,
            "users": [
              {
                "id": "b831381d-6324-4d53-ad4f-8cda48b30811",
                "alterId": 64
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "mkcp",
        "kcpSettings": {
          "uplinkCapacity": 5,
          "downlinkCapacity": 100,
          "congestion": true,
          "header": {
            "type": "none"
          }
        }
      }
    }
  ]
}
```

### 关于BBR能否加速使用kcp协议的V2Ray的问题
![BBR_KCP](https://zyue-pic.oss-cn-shenzhen.aliyuncs.com/BBR_KCP.png)

LINK : https://github.com/v2ray/v2ray-core/issues/549
## 其他加速脚本
- 锐速 
- 原版BBR 

左转LINK : https://ssr.tools/674

# 默认config.json配置

## 客户端配置
以下是客户端配置，将客户端的 config.json 文件修改成下面的内容，__修改完成后要重启 V2Ray 才会使修改的配置生效。__
```json
{
  "inbounds": [
    {
      "port": 1080, // 监听端口
      "protocol": "socks", // 入口协议为 SOCKS 5
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth"  //socks的认证设置，noauth 代表不认证，由于 socks 通常在客户端使用，所以这里不认证
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess", // 出口协议
      "settings": {
        "vnext": [
          {
            "address": "serveraddr.com", // 服务器地址，请修改为你自己的服务器 IP 或域名
            "port": 16823,  // 服务器端口
            "users": [
              {
                "id": "b831381d-6324-4d53-ad4f-8cda48b30811",  // 用户 ID，必须与服务器端配置相同
                "alterId": 64 // 此处的值也应当与服务器相同
              }
            ]
          }
        ]
      }
    }
  ]
}
```
## 服务器配置
以下是服务器配置，将服务器 /etc/v2ray 目录下的 config.json 文件修改成下面的内容，__修改完成后要重启 V2Ray 才会使修改的配置生效。__
```json
{
  "inbounds": [
    {
      "port": 16823, // 服务器监听端口
      "protocol": "vmess",    // 主传入协议
      "settings": {
        "clients": [
          {
            "id": "b831381d-6324-4d53-ad4f-8cda48b30811",  // 用户 ID，客户端与服务器必须相同
            "alterId": 64
          }
        ]
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",  // 主传出协议
      "settings": {}
    }
  ]
}
```

# 工具及使用 
* 修改 `uid` 和 `端口号` 即可

* 测速 `speedtest` : https://www.speedtest.net

* 电脑建议使用 `v2rayN ` : https://github.com/2dust/v2rayN/releases

  手机建议使用 `BifrostV` : https://apkpure.com/bifrostv/com.github.dawndiy.bifrostv

![youtube-mangzhong](https://zyue-pic.oss-cn-shenzhen.aliyuncs.com/youtube-mangzhong.png)

# SwitchyOmega配置

Official mirror URLs:

Pagure: https://pagure.io/gfwlist/raw/master/f/gfwlist.txt

Repo.or.cz: http://repo.or.cz/gfwlist.git/blob_plain/HEAD:/gfwlist.txt

Bitbucket: https://bitbucket.org/gfwlist/gfwlist/raw/HEAD/gfwlist.txt

Gitlab: https://gitlab.com/gfwlist/gfwlist/raw/master/gfwlist.txt

TuxFamily: https://git.tuxfamily.org/gfwlist/gfwlist.git/plain/gfwlist.txt

__`建议个人备份SwitchOmega配置。`__


> # 相关链接
>
> -  [__v2ray官网__](https://www.v2ray.com/)
> -  [v2ray文档](https://toutyrater.github.io)
> -  [新V2Ray 白话文指南](https://guide.v2fly.org)
> -  [逗比根据地](https://doubibackup.com)
> -  [SSR中文网](https://ssr.tools)



