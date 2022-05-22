## 最新机场\加密翻墙搭建教程，2022年V2ray服务器搭建（V2ray-ws协议）

本文是搭建V2ray服务器（ws+tls+web）的完整教程。V2ray是 Project V 下的一个工具。Project V 是一个包含一系列构建特定网络环境工具的项目，而V2ray属于最核心的一个。致力于让大家更好更快的科学上网。

**详细的机场\加密翻墙搭建教程请点击：[完整安装V2Ray服务器（V2Ray+ws+tls+web）零基础搭建VPN](https://www.xsdnmg.tk/2022/04/24/04/)**

工作原理
在配置V2ray之前，不妨先来看一下V2ray的工作原理，以下是单个V2ray进程的内部结构示意图。多个V2ray之间互相独立，互不影响。

![image](https://github.com/diausek/V2ray/raw/8a0673c8904090b76a801c4cec683cc7134f7efe/1-1.jpg)

需要配置至少一个入站协议（Inbound）和一个出站协议（Outbound）才可以正常工作。
入站协议负责与客户端（如浏览器）通信：如配置用户认证，如 ID 和密码等，收到数据之后，会交给分发器（Dispatcher）进行分发。
出站协议负责将数据发给服务器，如另一台主机上的 V2ray。
当有多个出站协议时，可以配置路由（Routing）来指定某一类流量由某一个出站协议发出。
路由会在必要时查询 DNS 以获取更多信息来进行判断。
一个 V2ray服务器可同时支持多台设备，使用不同的代理协议访问。同时，经过合理的配置，V2ray可以识别并区分需要代理和不需要代理的流量，直连的流量不需要绕路。

服务器端配置文件：
```{
  "inbounds": [{
    "port": 10086, // 服务器监听端口，必须和上面的一样
    "protocol": "vmess",
    "settings": {
      "clients": [{ "id": "b831381d-6324-4d53-ad4f-8cda48b30811" }]
    }
  }],
  "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  }]
}
```
客户端配置文件：
```{
  "inbounds": [{
    "port": 1080,  // SOCKS 代理端口，在浏览器中需配置代理并指向这个端口
    "listen": "127.0.0.1",
    "protocol": "socks",
    "settings": {
      "udp": true
    }
  }],
  "outbounds": [{
    "protocol": "vmess",
    "settings": {
      "vnext": [{
        "address": "server", // 服务器地址，请修改为你自己的服务器 ip 或域名
        "port": 10086,  // 服务器端口
        "users": [{ "id": "b831381d-6324-4d53-ad4f-8cda48b30811" }]
      }]
    }
  },{
    "protocol": "freedom",
    "tag": "direct",
    "settings": {}
  }],
  "routing": {
    "domainStrategy": "IPOnDemand",
    "rules": [{
      "type": "field",
      "ip": ["geoip:private"],
      "outboundTag": "direct"
    }]
  }
}
```
