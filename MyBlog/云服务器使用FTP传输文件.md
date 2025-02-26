#  云服务器使用FTP传输文件

腾讯云服务器马上就要到期了，2核2G续费一年要500多块钱，相比之下京东云2核4G三年只要618块，所以果断买入并迁移数据到新云服务器中。旧服务器所有的数据都保存在/home路径中，打包之后有5.4个G，所以考虑通过FTP连接直接将文件传输到新服务器中。所以腾讯云旧主机作为客户端，用来发起连接；京东云作为服务端，用来接收FTP请求。

## 开启FTP服务

首先我们需要选择一款FTP服务器软件安装在服务端，vsftpd是一款开源免费的FTP服务程序，具有轻量、快速、安全等优点。

### 安装vsftpd

我的两个主机都是Ubuntu系统，使用apt下载安装软件：

```shell
sudo apt update
sudo apt install vsftpd
```

安装完成后服务会自动启动，使用以下命令查看服务运行情况

```shell
sudo systemctl status vsftpd
```

看见`active(running)`表示服务正常运行。

### 修改配置

我的目标是：在服务端使用ftp命令登录root用户传输home.tar文件，所以需要进行两个配置：

* 允许root用户登录

  /etc/ftpusers文件中配置了禁止使用ftp登录的用户，修改此配置文件，注释掉`root`

* 允许客户端上传文件

  /etc/vsftpd.conf文件中定义了vsftpd服务的运行规则，找到`write_enable=YES`并取消注释

修改并保存文件后重启服务使得配置生效：

```shell
sudo systemctl restart vsftpd
```

## 开通防火墙

防火墙控制服务器的入流量，出流量默认允许所有请求，可以设置允许或禁止公网或内网对轻量应用服务器实例的访问，未配置规则等同于禁止访问。

为了使客户端和服务端正常建立连接并传输文件，需要我们添加防火墙规则。

### 主动模式/被动模式

FTP有两种模式：主动模式（Active Mode）和被动模式（Passive Mode）。这两种模式的主要区别在于数据连接的建立方式，即如何在客户端和服务器之间传输文件数据。

#### 主动模式（Active Mode）

**连接过程：**

1. **客户端启动控制连接：** 客户端以任意的动态端口（N端口）连接到服务器的21号端口。
2. **客户端监听数据连接：** 客户端在一个指定的端口（通常是N+1端口）上监听。
3. **服务器启动数据连接：** 服务器从它的20号端口主动连接到客户端的N+1端口。

#### 被动模式（Passive Mode）

**连接过程：**

1. **客户端启动控制连接：** 客户端以任意的动态端口连接到服务器的21号端口。
2. **服务器监听数据连接：** 服务器在一个动态选择的端口上（通常高位端口）进行监听，并将此端口告知客户端。
3. **客户端启动数据连接：** 客户端从它的一个动态端口连接到服务器刚刚告知的端口。

### 防火墙配置

无论是主动模式还是被动模式，都需要配置一定的防火墙规则，为了方便我们尽快的传输文件，此处不考虑服务端到底是哪种模式，客户端和服务端都配置对对方开放全部的TCP端口。配置时可能会提示开放全部端口会存在安全风险，所以我们完成文件传输后要及时关闭FTP服务并关闭防火墙规则。io/) 实现DevOps，通过拉取git服务器代码完成项目构建及部署。配置git webhook实现提交代码后自动更新、部署项目，实现自动化。

![image-20241109153302112.png](/image/3d3acdd7-dac8-4e5a-ae50-9035065451a4.png)

服务端

![image-20241109153325091.png](/image/ddc88b95-dcc0-4a1a-a97b-0631deed8279.png)

京东云在配置IP时，地址后会自动添加/32，/32表示IP地址子网掩码是32位，代表IP地址必须32位全匹配才能命中策略。

## 传输文件

在客户端使用ftp命令完成文件上传。

```shell
root@VM-24-9-ubuntu:/# cd /backup
root@VM-24-9-ubuntu:/backup# ls
backup_home.sh  home.tar
root@VM-24-9-ubuntu:/backup# ftp 117.72.121.49
Connected to 117.72.121.49.
220 (vsFTPd 3.0.5)
Name (117.72.121.49:root): root
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
257 "/root" is the current directory
ftp> cd /backup
250 Directory successfully changed.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-------    1 0        0        5837998080 Nov 09 14:48 home.tar
226 Directory send OK.
ftp> put backup_home.sh 
local: backup_home.sh remote: backup_home.sh
200 PORT command successful. Consider using PASV.
150 Ok to send data.
226 Transfer complete.
103 bytes sent in 0.00 secs (201.5750 kB/s)
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-------    1 0        0             103 Nov 09 15:45 backup_home.sh
-rw-------    1 0        0        5837998080 Nov 09 14:48 home.tar
226 Directory send OK.
ftp> bye
221 Goodbye.
root@VM-24-9-ubuntu:/backup# 
```
