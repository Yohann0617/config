# 使用官方的 Ubuntu 基础镜像
FROM ubuntu:latest

# 将本地的 .deb 文件复制到容器中
COPY openssl.deb /tmp/openssl.deb
COPY ca-certificates.deb /tmp/ca-certificates.deb

# 安装容器内的 .deb 文件
RUN dpkg -i /tmp/openssl.deb && apt-get install -f
RUN dpkg -i /tmp/ca-certificates.deb && apt-get install -f

# 将编译好的 server 和 client 二进制文件复制到容器中
COPY tgstate /app/tgState

# 设置工作目录
WORKDIR /app

# 设置暴露的端口
EXPOSE 8088

# 设置容器启动时要执行的命令
CMD ["/app/tgState"]

