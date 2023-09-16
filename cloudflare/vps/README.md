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

# 自己搭建的
docker run -itd --network=host \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --name x-ui --restart=unless-stopped \
	yohannfan/yohann-x-ui

```
```bash
# 将容器内容导出为压缩包 x-ui为容器名
docker export -o container_snapshot.tar x-ui

vim Dockerfile

# 使用一个适当的基础镜像
# FROM ubuntu:20.04
FROM centos:7

# 复制导出的容器快照到镜像中
COPY container_snapshot.tar /

# 创建一个可写的目录，用于解压文件
RUN mkdir /data

# 解压快照文件
RUN tar -xf /container_snapshot.tar -C /data

# 其他构建步骤
# ...

# 清理临时文件
RUN rm /container_snapshot.tar

# 定义容器启动命令或入口点
CMD ["/bin/bash"]


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

