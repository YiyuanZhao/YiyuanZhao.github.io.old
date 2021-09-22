---
layout: post
title:  集群使用/配置指南
author: Yiyuan
# description: This is just another page
---

# 快速开始
## 最小化运行环境
在连接服务器前，需要保证以下环境：
 - 处在校园宽带/校园WIFI/使用同济VPN连接到校园网。
 - 电脑有可用的命令行界面（windows下的`powershell`，`cmd` 等）。


## 使用`powershell`连接服务器
 - 按下`Win+R`，输入powershell，点击“确定”。

<img src="/image/posts/20210922/1.jpg" alt="1">

 - 在打开的界面中，输入

``` shell
ssh UserName@10.30.13.118
```
其中，UserName是集群管理员分配的账号名称。
 - 在命令行中输入密码（输入的密码不会回显），完成后按回车，登录服务器。
 - 如果不再使用服务器，可以使用`logout`命令退出连接。

```shell
PS C:\Users\SCES> ssh yyzhao@10.30.13.118
Last login: Sat Sep 26 14:43:04 2020 from 100.64.240.108
Rocks 6.1.1 (Sand Boa)
Profile built 08:26 14-May-2019

Kickstarted 17:00 14-May-2019
[yyzhao@cluster ~]$ logout
Connection to 10.30.13.118 closed.
PS C:\Users\SCES> exit
```

## 使用命令行操作服务器

 - 使用`cd`命令改变所处文件夹位置。

```shell
cd ./folderName
cd folderName                       #进入当前目录下的folderName文件夹
cd ../                              #返回上一级目录
cd ~                                #定位到用户根目录
```
 - 使用`ls`命令显示当前文件夹下的文件

```shell
ls -a                               #同时显示隐藏文件
ls -l                               #显示更加详细的信息
ls -alh                             #等同于ls -a -l -h，显示包含隐藏文件的更详细信息，而且智能转换文件大小的相关信息
pwd                                 #显示当前所在目录
```

<img src="/image/posts/20210922/2.jpg" alt="2">

 - 使用`cat`来查看文件内容

```shell
cat ./output.dat                    #查看当前目录下output.dat文件的内容
head -10 ./output.dat               #查看当前目录下output.dat文件的内容的前10行
tail -10 ./output.dat               #查看当前目录下output.dat文件的内容的最后10行
```
 - <a href = 'https://www.runoob.com/linux/linux-vim.html'>使用vim</a>来编辑文件内容
 - 文件的创建、移动、复制

```shell
mkdir folderName                    #创建名称为folderName的文件夹
mv folderName folderName2           #将文件夹名称改为folderName2
mv folderName ../                   #将文件夹移动到上一级目录
cp file1 ../file2                   #将file1复制到上一级目录中，名称为file2
cp -r ./folderName ./newFolder      #将本目录下的文件夹folderName复制到newFolder
cp -r ./* ../folder/                #将本目录下的所有文件（包含文件夹）复制到上一级目录的folder文件夹中
```
 - 文件的删除
__注意：文件删除操作应该小心进行，尤其使用递归方式(-r参数时)，并尽量避免使用绝对路径( / 开头的路径)，以免误删除，linux中的删除操作无法恢复！__

```shell
rm ./*                              #删除本目录下的所有文件（不包括文件夹）
rm -r ./folderName                  #删除本目录下的folderName文件夹
```
 - 使用重定向操作符来写入/读取文件
对于一般的命令，命令的输入来自于标准输入(stdin)，一般情况下是你的键盘；命令的输出显示到标准输出(stdout)，错误信息显示到标准错误(stderr)，一般情况下都是你的屏幕。但是可以通过重定向操作符来将输入、输出重定向到其他文件。

```shell
ls -al > childItem                  #将ls -al的命令输出内容写入childItem文件中，如果childItem文件已存在，将覆盖原文件的内容。
ls -al >> childItem                 #将ls -al的命令输出内容写入childItem文件中，如果childItem文件已存在，将在源文件后面追加写入。
```
以下实例演示了重定向输入符号的使用，在此使用wc命令计算输入的行数：

```shell
wc -l << EOF
    Welcome to 
    SCES
    cluster
EOF
```
 - 使用`qstat`命令来查看当前正在计算的任务

```shell
qstat                               #查看正在计算的任务
qstat -j 26711                      #查看jobID为26711的任务详情
```
以下是一个使用qstat获取当前计算状态的例子：
```
[yyzhao@cluster clusterVaspQuickStart]$ qstat
job-ID  prior   name       user         state submit/start at     queue                          slots ja-task-ID 
-----------------------------------------------------------------------------------------------------------------
  26711 0.60500 VSe2       yyzhao       dr    02/24/2020 13:55:55 all.q@compute-0-3.local           12
  28017 0.60500 VSe2       yyzhao       dr    05/16/2020 10:45:55 all.q@compute-0-0.local           12
```
其中`job-ID`为当前任务的ID，prior为当前任务的优先级，name为提交脚本中指定的任务名，user为用户名，state为当前执行状态（'r'为正在计算，'qw'为正在等待提交节点上一个计算任务完成，'dr'为正在删除任务），submit/start at表示提交时间，queue表示所选取的计算节点名称，slots为计算所使用的CPU核心数目。

```shell
qstat -f                            #查看所有计算节点的状态
```
以下是一个使用`qstat -f`查看服务器状态的例子
```
[yyzhao@cluster clusterVaspQuickStart]$ qstat -f
queuename                      qtype resv/used/tot. load_avg arch          states
---------------------------------------------------------------------------------
all.q@compute-0-0.local        BIP   0/12/12        -NA-     linux-x64     au
  28017 0.60500 VSe2       yyzhao       dr    05/16/2020 10:45:55    12
---------------------------------------------------------------------------------
all.q@compute-0-1.local        BIP   0/5/12         -NA-     linux-x64     au
---------------------------------------------------------------------------------
all.q@compute-0-10.local       BIP   0/0/12         0.04     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-11.local       BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-12.local       BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-13.local       BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-14.local       BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-15.local       BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-16.local       BIP   0/0/12         1.06     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-17.local       BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-18.local       BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-19.local       BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-2.local        BIP   0/0/12         -NA-     linux-x64     au
---------------------------------------------------------------------------------
all.q@compute-0-20.local       BIP   0/0/12         2.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-21.local       BIP   0/0/12         -NA-     linux-x64     au
---------------------------------------------------------------------------------
all.q@compute-0-22.local       BIP   0/0/12         0.03     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-23.local       BIP   0/0/12         0.04     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-24.local       BIP   0/12/12        -NA-     linux-x64     au
---------------------------------------------------------------------------------
all.q@compute-0-25.local       BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-27.local       BIP   0/0/12         0.01     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-28.local       BIP   0/0/12         -NA-     linux-x64     au
---------------------------------------------------------------------------------
all.q@compute-0-3.local        BIP   0/12/12        -NA-     linux-x64     au
  26711 0.60500 VSe2       yyzhao       dr    02/24/2020 13:55:55    12
---------------------------------------------------------------------------------
all.q@compute-0-30.local       BIP   0/0/12         0.01     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-4.local        BIP   0/0/12         0.00     linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-5.local        BIP   0/12/12        12.00    linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-6.local        BIP   0/0/12         -NA-     linux-x64     au
---------------------------------------------------------------------------------
all.q@compute-0-7.local        BIP   0/12/12        -NA-     linux-x64     au
---------------------------------------------------------------------------------
all.q@compute-0-8.local        BIP   0/12/12        12.01    linux-x64
---------------------------------------------------------------------------------
all.q@compute-0-9.local        BIP   0/1/12         1.78     linux-x64
```
其中queuename表示计算节点名称，resv/used/tot表示可用/已用/总核心数目，load_avg表示节点当前负载(-NA-表示失去连接)，states表示节点当前状态(au表示失去连接)
 - 使用`qsub`和`qdel`来提交和删除任务

```shell
qsub run_vasp.sh                    #提交任务，启动脚本的名称为run_vasp.sh
qdel 26711                          #删除jobID为26711的任务
```
以下是一个使用`qsub`提交任务，并使用`qdel`删除任务的例子：
```
[yyzhao@cluster clusterVaspQuickStart]$ qsub run_vasp.sh
Your job 33132 ("VSe2") has been submitted
[yyzhao@cluster clusterVaspQuickStart]$ qstat
job-ID  prior   name       user         state submit/start at     queue                          slots ja-task-ID
-----------------------------------------------------------------------------------------------------------------
  26711 0.60500 VSe2       yyzhao       dr    02/24/2020 13:55:55 all.q@compute-0-3.local           12
  28017 0.60500 VSe2       yyzhao       dr    05/16/2020 10:45:55 all.q@compute-0-0.local           12
  33132 0.60500 VSe2       yyzhao       r     09/26/2020 17:14:56 all.q@compute-0-10.local          12
[yyzhao@cluster clusterVaspQuickStart]$ qdel 33132
yyzhao has registered the job 33132 for deletion
```

## 启动VASP计算
本节通过一个简单的VSe$_2$算例，给出VASP计算的启动方式。

首先执行以下命令，复制VASP计算所需文件到用户根目录：

```shell
cp -r /home/yyzhao/swap/clusterVaspQuickStart ~/
cd ~/clusterVaspQuickStart/
```
接下来使用`qstat -f`命令寻找空节点(0/0/12类型的节点)，记下节点编号，填充在下方代码的`15`处，此处编号15仅作参考。

```shell
num=15;
sed -i "s/\(.*compute-0-\)[0-9]\+/\1$num/g" ./run_vasp.sh
```
在这里采用了使用正则表达式替换的方式来修改节点编号，当然你也可以使用`vim ./run_vasp.sh`命令来修改节点。如果在上一步选择的节点编号为15，那么使用`vim ./run_vasp.sh`看到的文件应该与下面一致：

```shell
#!/bin/bash
# bash started with bin/bash#

#$ -S /bin/bash
#$ -N VSe2
# The Name of the exe file #

#$ -o stdout
# Standard output file name #

#$ -e stderr
# If error, recording file #

#$ -cwd
# Output file path #

#$ -j y
# Run instantly = yes #

#$ -V
# Synchronize environment to the calculation nodes #

#$ -pe mpich 12
# Number of CPU core in calculation #

#$ -l h=compute-0-15
# Name of cluster nodes in calculation #

ulimit -s unlimited

execmd=/home/shke/Ware/intel-2018/compilers_and_libraries_2018.3.222/linux/mpi/intel64/bin/mpirun

$execmd -machinefile $TMPDIR/machines -np $NSLOTS /home/shke/DFT_SOFTWARE/vasp/vasp.5.4.4/bin/vasp_std 1>output.dat
```
在这个启动脚本中，声明提交任务的名称是VSe2，并行计算使用了12个核心，选取的计算节点为compute-0-15，你也可以根据自己的计算需求动态调整这三个参数，其他参数除非你明确其具体意义，否则不建议调整。
最后，使用`qsub`命令提交任务。

```shell
qsub run_vasp.sh
```
此时VASP计算已经提交，你可以使用`qstat`命令来查看当前任务的运行状态，也可以使用`cat output.dat`和`vim OUTCAR`来查询计算结果。

[back](/blogIndex)