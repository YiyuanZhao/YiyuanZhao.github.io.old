---
layout: project
title:  开源库MLQM的Docker容器实现
# code_url: "https://github.com/YiyuanZhao/ProfileProjectSourceCode/tree/main/shellScriptAutomation"
---

# 背景
机器学习量子蒙特卡洛(Machine Learning Quantum Montecarlo, MLQM)开源项目[https://github.com/Nuclear-Physics-with-Machine-Learning/MLQM/tree/master](https://github.com/Nuclear-Physics-with-Machine-Learning/MLQM/tree/master)在科研上具有很高的价值，但项目不支持包管理器的安装方式，且相关环境配置复杂。项目希望解决下列问题：

 - 简化运行环境、包依赖关系的配置。
 - 跨平台可迁移性/扩展性。
 - 多个MLQM实例并行的能力。
 - Python, Tensorflow环境的隔离。

可以使用Docker的容器化技术来实现环境的标准化、并行化、隔离化的需求，同时挂载Volume来解决计算数据的传递问题。

# 源代码
使用简单的Makefile就可以实现上述功能，具体请参见演示Demo部分。

# 演示Demo
环境需求: Docker

## 构建MLQM库的Dcoker镜像

```shell
docker pull tensorflow/tensorflow:latest-gpu
cd ~
mkdir mlqmImage
cd mlqmImage
git clone https://github.com/Nuclear-Physics-with-Machine-Learning/MLQM.git
```

在该目录下构建以下的Dockerfile：

```Dockerfile
cat >Dockerfile <<EOF
FROM tensorflow/tensorflow:latest-gpu
WORKDIR /
COPY ./MLQM/ ./MLQM/
ENV PYTHONPATH=/MLQM/
RUN pip install hydra-core cmake\
    && pip install horovod torch
EOF
```

接下来构建镜像：

```shell
docker build -t mlqm:latest .
```

## 创建Docker Volume

```shell
docker volume create mlqmVolume
```

## 运行Container
使用下面的命令来启动MLQM容器，并将Volume挂载到容器下：

```shell
docker run -itd -v mlqmVolume:/mlqmVolume --name mlqmContainer mlqm:latest
docker exec -it mlqmContainer /bin/bash
```

## 验证安装
你可以在容器中使用下面的命令来验证安装：

```shell
cd ./MLQM
python bin/stochastic_reconfiguration.py run_id=MyTestRun
```

你可以[在这里](./dockerImageMlqmInstallInstruction)找到更加详细的安装指南。

* * *

# 技术栈
shell，powershell

### [back](/)