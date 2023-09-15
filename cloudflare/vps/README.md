## 安装x-ui

### 脚本一键安装

```bash
# 脚本一键安装
bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)

# 其他脚本
bash <(wget -qO- https://gitlab.com/rwkgyg/x-ui-yg/raw/main/install.sh 2> /dev/null)
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
 
# Build 自己的镜像 
docker build -t x-ui .
```

## 测试是否支持chatgpt等

```bash
# 一键测试Linux服务器IP是否支持解锁Netflix、TikTok、ChatGPT脚本
wget -qO- https://github.com/yeahwu/check/raw/main/check.sh | bash
```

## 配置WARP

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

