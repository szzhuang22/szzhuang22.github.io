---
title: "Docker 使用指南"
date: 2025-11-03 10:00:00
categories: [服务器使用指南]
tags: [Docker, 容器, 镜像, 仓库]
---


# Docker 使用指南

## 基本概念
Docker 使用三个基本概念：

- **容器 (Containers)**：容器是应用级的抽象，能将代码和依赖打包在一起。
- **镜像 (Images)**：镜像是分层的只读文件，存储应用程序所需的状态。
- **仓库 (Registry)**：仓库是镜像的集合，通常包含同一个应用的不同版本。

## 安装
[](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#docker)
```bash
curl https://get.docker.com | sh \
&& sudo systemctl --now enable docker
```

## Docker 基础操作

### 1. 进入 Dockerfile 目录
```bash
cd /mnt/user-ssd/zhuangweiji/env/docker
```

NVIDIA 提供了多个版本的 CUDA Docker 镜像，以满足不同的开发和运行需求。
这些镜像主要分为以下几类：

| 镜像版本 | 基础系统 | 大小 | 包含内容 | 适用场景 |
| --- | --- | --- | --- | --- |
| 12.1.0-base-ubuntu22.04 | Ubuntu 22.04 | ~238MB | 基础 CUDA 运行时库（如 libcudart），无其他开发工具或库。 | 最小化镜像，用户自行安装所需库和工具。 |
| 12.1.0-runtime-ubuntu22.04 | Ubuntu 22.04 | ~3.36GB | 完整的 CUDA 运行时库，适合运行预构建的 CUDA 应用程序。 | 运行预构建的 CUDA 应用程序。 |
| 12.1.0-devel-ubuntu22.04 | Ubuntu 22.04 | ~7.03GB | 包含 CUDA 运行时库、头文件、静态库、编译器工具链和调试工具。 | 开发和编译 CUDA 应用程序。 |
| 12.1.0-cudnn8-runtime-ubuntu22.04 | Ubuntu 22.04 | ~3.36GB | 基于 runtime 镜像，额外包含 cuDNN 8 的共享库，支持深度学习框架的运行。 | 运行需要 cuDNN 的深度学习应用程序。 |
| 12.1.0-cudnn8-devel-ubuntu22.04 | Ubuntu 22.04 | ~7.03GB | 基于 devel 镜像，额外包含 cuDNN 8 的头文件、静态库、编译器工具链和调试工具。 | 开发和编译需要 cuDNN 的深度学习应用程序。 |

选择建议：
- 需要最小化镜像大小：选择 base 版本。
- 仅运行 CUDA 应用程序：选择 runtime 或 cudnn8-runtime。
- 开发或编译 CUDA 应用程序：选择 devel 或 cudnn8-devel。

### 2. 构建镜像
```bash
docker build -t appl/ubuntu2204-cuda121:0.1 .
docker build -t micr.cloud.mioffice.cn/appl/ubuntu2204-cuda121:0.3 .
```
- `-t` 指定镜像名称和标签，如 `my-custom-cuda:latest`。
- `.` 表示使用当前目录中的 Dockerfile。

### 3. 检查构建的镜像
```bash
docker images
```

### 4. 运行镜像并测试
```bash
docker container run --rm -it --gpus all appl/ubuntu2204-cuda121:0.1
```
- `--rm`：容器退出时自动删除。
- `--gpus all`：使用所有可用的 GPU 设备。
- `-it`：产生一个可互动的终端。

### 5. 推送镜像到 MICR
1. 登录
```bash
docker login micr.cloud.mioffice.cn -u zhuangweiji -p <password>
```
2. 打标
```bash
docker tag appl/ubuntu2204-cuda121:0.1 micr.cloud.mioffice.cn/appl/ubuntu2204-cuda121:0.1
```
3. 推送
```bash
docker push micr.cloud.mioffice.cn/appl/ubuntu2204-cuda121:0.1
```

### 6. 删除容器或镜像
- 删除容器
```bash
docker rm sweet_stonebraker
```
- 删除镜像
```bash
docker rmi appl/ubuntu2204-cuda121:0.1
```

### 7. 进入容器进行开发
```bash
docker run \
    --ipc=host \
    --cap-add=SYS_ADMIN \
    --privileged=true \
    --gpus all \
    -v /mnt:/mnt \
    -v /home/d1v7-00:/home/d1v7-00 \
    -it \
    -d \
    --name appl-dev \
    micr.cloud.mioffice.cn/appl/ubuntu2204-cuda121:0.1 \
    /bin/bash
```
- 背景运行容器
- 连接容器方法：
  - 方法 1：`docker attach <container_id>` 附着到正在运行的容器，进入其终端。
  - 方法 2：`docker exec -it <container_id> bash` 创建一个新的终端，而不是附着到正在运行的容器。

### 8. 查看容器列表
```bash
docker ps
docker ps -a
```
- 显示当前正在运行的容器列表。
- 不加参数只显示正在运行的容器，加 `-a` 显示所有容器（包括已停止的）。

### 9. 将更改过的容器保存为新镜像
```bash
docker commit <container_id> micr.cloud.mioffice.cn/appl/ubuntu2204-cuda121:dev
```

## Dockerfile 示例
```dockerfile
# 免除 apt 缓存
RUN apt-get update -y && \
    apt-get install -y wget && \
    rm -rf /var/lib/apt/lists/*
```

## 配置 Docker Daemon

查看 Docker Daemon 配置
```bash
cat /etc/docker/daemon.json
```

配置例如：
```json
{
    "runtimes": {
        "nvidia": {
            "args": [],
            "path": "nvidia-container-runtime"
        }
    },
    "registry-mirrors": [
        "https://docker-hub-mirror.d.xiaomi.net",
        "https://mirror.ccs.tencentyun.com",
        "https://registry.docker-cn.com",
        "https://docker.mirrors.ustc.edu.cn"
    ],
    "storage-driver": "overlay2"
}
```
说明：
- `runtimes.nvidia`：为 Docker 容器提供 NVIDIA GPU 支持。
- `registry-mirrors`：配置镜像加速器，提升拉取镜像的速度。
- `storage-driver`：指定存储驱动类型，一般使用 `overlay2`。

重载配置
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

测试网络连通性
```bash
ping https://docker-hub-mirror.d.xiaomi.net
ping docker-hub-mirror.d.xiaomi.net
```
这可以用来测试与镜像加速器（docker-hub-mirror.d.xiaomi.net）的网络连通性。

查看 Docker 信息
```bash
docker info
```

## 参考文档
- [Docker入门教程-阮一峰](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
- [Docker完全手册](https://chinese.freecodecamp.org/news/the-docker-handbook/)
- [也许是Dockerfile最佳实践](https://tech.xiaomi.com/#/pc/article-detail?id=7323)
- [官方的dockerfile文档](https://docs.docker.com/develop/develop-images/dockerfile_best-practices)
