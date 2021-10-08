---
layout: project
title:  开源库MLQM的Docker容器实现
# code_url: "https://github.com/YiyuanZhao/ProfileProjectSourceCode/tree/main/shellScriptAutomation"
---

# Install CPU version of MLQM with Anaconda

## Install Anaconda
### Download and Install Anaconda
[Instructions here](https://www.anaconda.com/products/individual)

For Python 3.8, 64-Bit (x86) Installer (544 MB), the shell codes looks like:

```shell
wget https://repo.anaconda.com/archive/Anaconda3-2021.05-Linux-x86_64.sh
chmod +x Anaconda3-2021.05-Linux-x86_64.sh
./Anaconda3-2021.05-Linux-x86_64.sh
```

For changes to take effect, close and re-open your current shell.

If you'd prefer that conda's base environment not be activated on startup,
   set the auto_activate_base parameter to false:

```shell
conda config --set auto_activate_base false
```

### Create Conda Environments
These codes create a conda environment named MLQM, and install required libraries.
```shell
conda create -n MLQM python=3.8 tensorflow=2.6.0 -y
conda activate MLQM
```

### Install Library Dependency
The `cmake`, `horovod`

```shell
conda install -c conda-forge hydra-core
sudo apt install cmake
pip install horovod
```
                                                                                 
## Build & Run MLQM

### Clone and Run Test Case
Cloning the MLQM repository using 

```shell
git clone https://github.com/Nuclear-Physics-with-Machine-Learning/MLQM.git
```
Then run the test file to validate the setup.

```shell
cd ./MLQM
python bin/stochastic_reconfiguration.py run_id=MyTestRun
```

## Install Torch (optional)
```shell
pip install torch
```

# Install GPU Version of MLQM with Docker

## Install CUDA
[Instructions here](https://developer.nvidia.com/cuda-downloads?target_os=Linux)

For __Ubuntu 18.04 Release__, the code shows like:

```shell
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/11.4.2/local_installers/cuda-repo-ubuntu1804-11-4-local_11.4.2-470.57.02-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1804-11-4-local_11.4.2-470.57.02-1_amd64.deb
sudo apt-key add /var/cuda-repo-ubuntu1804-11-4-local/7fa2af80.pub
sudo apt-get update
sudo apt-get -y install cuda
```

## Install Docker

Type`docker -v`, if it shows information of version, then this step could be skipped. If the command returns `Command not found`, then following this guide:

[Instructions here](https://docs.docker.com/engine/install/)

For example, on Ubuntu 18.04, 
```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update
sudo apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg \
     lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Then start docker service:

```shell
service docker start
```

You may need to type your account password during setup. Note that this step is necessary for Linux (e.g. Ubuntu). For Mac/Windows users you should start Docker App instead.


After installing docker, type `docker ps -a`, if it works fine, this step is completed.

If the docker returns messages like:

```
docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.35/containers/create: dial unix /var/run/docker.sock: connect: permission denied. See 'docker run --help'.
```

You should add your current account to the docker group to get access:

```shell
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

This issue could be found on [stackoverflow](https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue). Run `docker ps -a` again to validate modification.

## Build the Docker Image

First, pull the official `Tensorflow` docker image:
```shell
docker pull tensorflow/tensorflow:latest-gpu
```

Create a empty directory and clone MLQM repository, the directory name is arbitary and here we use `mlqmImage`.

```shell
cd ~
mkdir mlqmImage
cd mlqmImage
git clone https://github.com/Nuclear-Physics-with-Machine-Learning/MLQM.git
```

Then, build our own MLQM image with Dockerfile. Copy the following codes into `Dockerfile`, this script will build image as root account.

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

After that, run 
```shell
docker build -t mlqm:latest .
```

to finish image building.

## Create Docker Volume

Everything in a docker container will be lost if the container is stopped and removed (using `docker rm <Container Name>`) by default. If you want to save your data in a container, it would be possible to create a docker volume and mount the volume to a specific container.

```shell
docker volume create mlqmVolume
```

The exactly saving position on your disk coult be seen by command `docker volume inspect mlqmVolume`, which gives output like:

```shell
[
    {
        "CreatedAt": "2021-10-06T14:06:16+08:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/mlqmVolume/_data",
        "Name": "mlqmVolume",
        "Options": {},
        "Scope": "local"
    }
]
```
The `Mountpoint` is `/var/lib/docker/volumes/mlqmVolume/_data` in this case.

## Run Docker Container
After build image successfully, one could type 
```shell
docker run -itd -v mlqmVolume:/mlqmVolume --name mlqmContainer mlqm:latest
docker exec -it mlqmContainer /bin/bash
```
to start a container. Then the setup of MLQM is completed.

## Validate setup

The setup can be verified by: 

```shell
cd ./MLQM
python bin/stochastic_reconfiguration.py run_id=MyTestRun
```

If it returns no error and exit normally, the environment is installed properly.

__Important Notes__: All data in the container will be lost if the container is removed by `docker rm <Container Name>` except data in the `/mlqmVolume/` folder. Make sure to save important data in this folder.

### [back](/)