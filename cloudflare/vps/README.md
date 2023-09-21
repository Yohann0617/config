- [安装x-ui](#安装x-ui)
  * [脚本一键安装](#脚本一键安装)
  * [docker安装x-ui](#docker安装x-ui)
- [测试是否支持chatgpt等](#测试是否支持chatgpt等)
- [配置WARP解锁奈非和ChatGPT](#配置WARP解锁奈非和ChatGPT)
- [配置代理节点解锁奈非和ChatGPT](#配置代理节点解锁奈非和ChatGPT)
- [配置Hysteria2](#配置Hysteria2)
  * [服务器配置文件](#服务器配置文件)
  * [Hysteria2下载](#Hysteria2下载)
  * [v2rayN配置文件](#v2rayN配置文件)



## 安装x-ui

### 脚本一键安装

```bash
# 脚本一键安装
bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/master/install.sh)

# 其他脚本
bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)
bash <(wget -qO- https://gitlab.com/rwkgyg/x-ui-yg/raw/main/install.sh 2> /dev/null)
# 或
bash <(curl -Ls https://gitlab.com/rwkgyg/x-ui-yg/raw/main/install.sh)
```

### docker安装x-ui
参考链接：
- [https://hub.docker.com/r/enwaiax/x-ui](https://hub.docker.com/r/enwaiax/x-ui)
- [https://www.hundun.live/?p=174](https://www.hundun.live/?p=174)

```bash
# 安装docker
curl -fsSL https://get.docker.com | sh

# 安装x-ui
mkdir x-ui && cd x-ui

docker run -itd --network=host \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --name x-ui --restart=unless-stopped \
    enwaiax/x-ui:alpha-zh

# 自己搭建的
docker run -itd --network=host \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --name x-ui --restart=unless-stopped \
    yohannfan/yohann-x-ui:1.0

```
以下骚操作，可直接忽略
```bash
# 编写Dockerfile
cat << EOF > $PWD/Dockerfile
# 使用一个适当的基础镜像
# FROM ubuntu:20.04
# FROM centos:7
FROM enwaiax/x-ui:alpha-zh

# 其他构建步骤
# ...

# 定义容器启动命令或入口点
CMD ["/bin/bash"]

EOF

# 构建镜像
docker build -t yohann-x-ui .

# 登录docker hub
docker login

# 打标签
docker tag yohann-x-ui:latest yohannfan/yohann-x-ui_repository:1.0

# 上传docker hub
docker push yohannfan/yohann-x-ui_repository:1.0
```

## 测试是否支持chatgpt等

```bash
# 一键测试Linux服务器IP是否支持解锁Netflix、TikTok、ChatGPT脚本
wget -qO- https://github.com/yeahwu/check/raw/main/check.sh | bash
```

## 配置WARP解锁奈非和ChatGPT
PS：IP会经常变，但是免费
```bash
# 自动配置 WARP WireGuard 双栈全局网络（所有出站流量走 WARP 网络）
bash <(curl -fsSL git.io/warp.sh) d

# 查看 WARP 脚本子命令列表
bash <(curl -fsSL git.io/warp.sh) help

# 重启 WARP WireGuard 网络接口
systemctl restart wg-quick@wgcf

# 禁用 WARP WireGuard 网络接口
systemctl disable wg-quick@wgcf --now
```

## 配置代理节点解锁奈非和ChatGPT
以下为Xray配置，修改`outbounds`中节点即可。PS：需要自费购买能够解锁的节点
```json
{
  "api": {
    "services": [
      "HandlerService",
      "LoggerService",
      "StatsService"
    ],
    "tag": "api"
  },
  "inbounds": [
	{
    "listen": "127.0.0.1",
    "port": 30000, 
    "protocol": "socks", 
    "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
		}
	}
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    },
	{
      "tag": "netflix_proxy",
      "protocol": "trojan",
      "settings": {
        "servers": [
          {
            "address": "kbawssg1.aiopen.cfd",
            "method": "chacha20",
            "ota": false,
            "password": "30c348eb-a17c-4a23-a379-d01a1e47ce25",
            "port": 443,
            "level": 1,
            "flow": ""
          }
        ]
      },
      "streamSettings": {
        "network": "tcp",
        "security": "tls",
        "tlsSettings": {
          "allowInsecure": false,
          "serverName": "4-193-105-141.nhost.00cdn.com"
        }
      },
      "mux": {
        "enabled": false,
        "concurrency": -1
      }
    },
    {
      "protocol": "blackhole",
      "settings": {},
      "tag": "blocked"
    }
  ],
  "policy": {
    "levels": {
      "0": {
        "handshake": 10,
        "connIdle": 100,
        "uplinkOnly": 2,
        "downlinkOnly": 3,
        "statsUserUplink": true,
        "statsUserDownlink": true,
        "bufferSize": 10240
      }
    },
    "system": {
      "statsInboundDownlink": true,
      "statsInboundUplink": true
    }
  },
  "routing": {
    "rules": [
	{
    "type": "field",
    "outboundTag": "netflix_proxy",
    "domain": [
        "geosite:netflix",
        "geosite:disney",
	"geosite:openai"
    ]
	},
      {
        "inboundTag": [
          "api"
        ],
        "outboundTag": "api",
        "type": "field"
      },
      {
        "ip": [
          "geoip:private"
        ],
        "outboundTag": "blocked",
        "type": "field"
      },
      {
        "outboundTag": "blocked",
        "protocol": [
          "bittorrent"
        ],
        "type": "field"
      }
    ]
  },
  "stats": {}
}
```

## 配置Hysteria2

```bash
#一键安装Hysteria2
bash <(curl -fsSL https://get.hy2.sh/)

#生成自签证书
openssl req -x509 -nodes -newkey ec:<(openssl ecparam -name prime256v1) -keyout /etc/hysteria/server.key -out /etc/hysteria/server.crt -subj "/CN=bing.com" -days 36500 && sudo chown hysteria /etc/hysteria/server.key && sudo chown hysteria /etc/hysteria/server.crt

#启动Hysteria2
systemctl start hysteria-server.service
#重启Hysteria2
systemctl restart hysteria-server.service
#查看Hysteria2状态
systemctl status hysteria-server.service
#停止Hysteria2
systemctl stop hysteria-server.service
#设置开机自启
systemctl enable hysteria-server.service
#查看日志
journalctl -u hysteria-server.service
```

### 服务器配置文件

```yaml
cat << EOF > /etc/hysteria/config.yaml
listen: :443 #监听端口

#使用CA证书
#acme:
#  domains:
#    - yo.yohann.buzz #你的域名，需要先解析到服务器ip
#  email: 852221040@qq.com

#使用自签证书
tls:
  cert: /etc/hysteria/server.crt
  key: /etc/hysteria/server.key

auth:
  type: password
  password: 123456 #设置认证密码
  
masquerade:
  type: proxy
  proxy:
    url: https://bing.com #伪装网址
    rewriteHost: true
EOF
```
### Hysteria2下载

[https://github.com/apernet/hysteria/releases](https://github.com/apernet/hysteria/releases)

### v2rayN配置文件

```yaml
server: 1.1.1.1:443
auth: 123456

bandwidth:
  up: 50 mbps
  down: 100 mbps
  
tls:
  sni: bing.com
  insecure: true #使用自签时需要改成true

socks5:
  listen: 127.0.0.1:1080
http:
  listen: 127.0.0.1:8080
```

