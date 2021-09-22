---
layout: post
title:  远程连接环境搭建（可选）
author: Yiyuan
# description: This is just another page
---

# 开发环境配置
在上一节中，介绍了连接服务器所需要的最小配置及基本操作，配置一定的开发环境，可以更加方便的连接服务器。
## 使用远程登陆工具连接服务器
### 使用Xshell连接服务器
自行下载Xshell应用，在开始的界面点击“新建”，得到图示界面

<img src="/image/posts/20210922/3.jpg" alt="3"> 

名称一栏可以随意填写，其他按照红框部分填写即可。

随后进入"用户身份验证"，填写用户名和密码。

<img src="/image/posts/20210922/4.jpg" alt="4"> 

此时点击确定，配置完成，以后可以实现免密登录。

Xshell也可以实现本地与服务器之间传输文件，按下`Ctrl+Alt+F`打开文件互传页面：

<img src="/image/posts/20210922/5.jpg" alt="5"> 

相互拖拽就可以完成文件的传输。

### 使用 SSH Secure Shell Client连接服务器
SSH Secure Shell Client与Xshell软件功能相同，二者选择其一即可。配置界面如下图所示：

<img src="/image/posts/20210922/6.jpg" alt="6"> 
<img src="/image/posts/20210922/7.jpg" alt="7"> 

点击红框的标志可以启动文件传输器，可以通过拖拽互相传输文件。

<img src="/image/posts/20210922/8.jpg" alt="8"> 
<img src="/image/posts/20210922/9.jpg" alt="9"> 

### 配置免密登录
上述两个工具的实质都是使用ssh命令登录到服务器，并使用scp命令相互传输文件。在少量文件相互传输的情况下，使用软件与命令行没有差异，但当涉及到大量不同位置的文件夹/文件在本地与服务器之间相互传输时，命令行批处理的优势就会体现出来。本节介绍不使用任何外部软件的情况下，利用ssh公钥/私钥配置免密登录，并指定服务器的别名。配置完成后，可以在本地使用`powershell`输入自定义名称，免密登录服务器，并可以使用`scp`命令批量传输文件。

__重要提示：在配置过程中，务必注意公钥和私钥的区别，服务器端部署公钥，本地端部署私钥，不可交换。__

首先对配置过程中的术语进行说明：
 - 服务器端：       服务器集群
 - 本地端：         配置免密登录的个人PC/笔记本
 - 公钥：           ssh-keygen.exe生成的id_rsa.pub文件
 - 私钥：           ssh-keygen.exe生成的id_rsa文件

#### 生成公钥/私钥对
1. 在本地端按`Win+R`，输入`powershell`打开命令行界面。
2. 在本地端的`powershell`命令行中输入`ssh-keygen.exe`，一路回车选择默认设置即可。
```
PS C:\Users\SCES> ssh-keygen.exe
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\SCES/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\SCES/.ssh/id_rsa.
Your public key has been saved in C:\Users\SCES/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:*************** sces@DESKTOP-DQVLUVG
The key's randomart image is:
+---[RSA 2048]----+
******
+----[SHA256]-----+
```
#### 编辑ssh的配置文件 上传公钥到服务器
1. 在本地端进入第3行中给出的路径(默认为C:\Users\"用户名(需根据情况替换)"\.ssh)。
2. 新建一个名为config的文档(没有后缀名)，使用记事本打开。
3. 填入服务端的相关信息，例子如下所示：
```powershell
Host cluster
    HostName 10.30.13.118
    User yyzhao
    Port 22
    IdentityFile C:\Users\SCES\.ssh\id_rsa
```
其中Host是你设定的服务端别名，ssh配置完成后使用`ssh 别名`就可以直接登录服务器，无需输入ip和账户名称。User是账户名称，HostName是服务器端的ip地址，IdentityFile是ssh-add.exe生成的私钥的路径。

4. 在本地端的`powershell`命令行中输入`scp`命令，将公钥上传到服务器。例子为：
```powershell
scp "C:\Users\SCES\.ssh\id_rsa.pub" yyzhao@10.30.13.118:~/
```
上述代码表示将本地端的"C:\Users\SCES\.ssh\id_rsa.pub"文件上传到服务器端的用户根目录。

#### 服务器端配置
在本地端打开一个新的`powershell`，在命令行中使用`ssh`连接服务器。
由于ssh安全策略的限制，必须使用`chmod`命令修改ssh文件夹的读写权限，否则无法使用密钥登陆。
```shell
ssh yyzhao@10.30.13.118
mkdir .ssh
chmod 700 .ssh
cd .ssh
mv ~/id_rsa.pub ~/.ssh/
cat id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 authorized_keys
```
上述代码建立文件authorized_keys，并将公钥追加到authorized_keys文件中，并调整相关的读写权限。
#### ssh配置测试
经过上述操作，应该可以使用`ssh 别名`的方式实现免密登录。打开一个新的`powershell`，在本例中的测试代码为：
```powershell
ssh cluster
```
此处的cluster与《编辑ssh的配置文件 上传公钥到服务器》第3步中Host之后的自定义名称一致。如果此时显示登陆成功，不需要输入密码，则配置成功，成功登录的例子为：
```
PS C:\Users\SCES> ssh cluster
Last login: Sat Sep 26 18:54:52 2020 from 100.64.240.108
Rocks 6.1.1 (Sand Boa)
Profile built 08:26 14-May-2019

Kickstarted 17:00 14-May-2019
```
此时可以在本地端直接使用`scp`命令互传多个文件：
```powershell
PS C:\Users\SCES> scp cluster:~/swap/clusterVaspQuickStart/* "C:\temp\"
INCAR                                                          100% 4025    15.7KB/s   00:00
KPOINTS                                                        100%   25     1.6KB/s   00:00
POSCAR                                                         100%  688     0.7KB/s   00:00
POTCAR                                                         100%  444KB   5.6MB/s   00:00
run_vasp.sh                                                    100%  664     0.7KB/s   00:00
vdw_kernel.bindat                                              100%  153KB   9.6MB/s   00:00
```
如果此时仍然需要输入密码，请从两个方面排查：
 - 服务器端应该部署公钥，本地端应该部署私钥
 - 服务器端的读写设置是否正确

到此，免密登录配置完成。

[back](/blogIndex)