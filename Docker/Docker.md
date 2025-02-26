# Docker安装

## Docker的基本组成

**镜像（image）：**

docker镜像就好比是一个模板，通过这个模板来创建容器服务，tomcat镜像===>run===>tomcat01容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）。

**容器（container）：**

Docker利用容器技术，运行一个或者一个组应用，通过镜像来创建的。

启动，停止，删除，基本命令！

目前就可以把这个容器理解为就是一个建议的linux系统

**仓库（repository）**

仓库就是存放镜像的地方！

仓库分为共有仓库和私有仓库！

Docker Hub、阿里云......都有容器服务器（配置镜像加速！）

## 安装Docker

> 环境准备

1、需要会一点点的Linux的基础

2、Linux服务器

3、我们使用Xshell连接远程服务器进行操作！

> 环境查看

```shell
# 系统内核是 3.10 以上
jianghaoyu@VM-24-9-ubuntu:/$ uname -r
5.4.0-90-generic
```

```shell
# 系统版本
jianghaoyu@VM-24-9-ubuntu:/$ cat /etc/os-release 
NAME="Ubuntu"
VERSION="20.04 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal
```

> 安装

帮助文档：https://docs.docker.com/engine/install/ubuntu/
```shell
# 可以使用命令快速安装
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh --dry-run
```
> 镜像源

镜像源地址仅供参考，可以自行百度当前最新的地址，镜像拉取成功后无需再次拉取，所以不需要太关注这里的配置，能用即可。

修改文件 /etc/docker/daemon.json（如果不存在则创建）
```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://docker.fxxk.dedyn.io"]  # 仅供参考，镜像站更新
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

Ubuntu环境下使用root角色操作。



```shell
# 查看docker版本
root@VM-24-9-ubuntu:/home/jianghaoyu# docker version
Client: Docker Engine - Community
 Version:           20.10.12
 API version:       1.41
 Go version:        go1.16.12
 Git commit:        e91ed57
 Built:             Mon Dec 13 11:45:33 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.12
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.12
  Git commit:       459d0df
  Built:            Mon Dec 13 11:43:42 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.12
  GitCommit:        7b11cfaabd73bb80907dd23182b9347b4245eb5d
 runc:
  Version:          1.0.2
  GitCommit:        v1.0.2-0-g52b36a2
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

```shell
# 运行docker镜像
root@VM-24-9-ubuntu:/home/jianghaoyu# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:507ecde44b8eb741278274653120c2bf793b174c06ff4eaa672b713b3263477b
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## HelloWorld流程

![image-20220129114015933.png](/image/aed8ccbd-2d79-4f79-a177-89a30cbce613.png)

## 底层原理

**Docker是怎么工作的？**

Docker是一个Client-Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问！

DockerServer接收到Docker-Client的指令，就会执行或者命令！

![image-20220129114801130.png](/image/a108f007-797a-406d-a74e-86abdd86b4c1.png)
**Docker为什么比VM快**

1、Docker有着比虚拟机更少的抽象层。

2、Docker利用的宿主机的内核，VM需要的是Guest OS。

![image-20220129114918768.png](/image/31e74b9c-b54d-4b70-ba02-e2fe7b823342.png)

所以说，新建一个容器的时候，Docker不需要像一个虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机是加载Guest OS，分钟级别的，而Docker利用宿主机的操作系统，省略了这个复杂的过程，秒级！

![image-20220129115258401.png](/image/121d1be7-5206-4a7c-9a8f-dd40e402e5c3.png)
# Docker的常用命令

## 帮助命令

```shell
docker version #显示docker的版本信息
docker info    #显示docker的系统信息，包括镜像和容器的数量
docker [命令] --help  #帮助命令
```

帮助文档的地址：https://docs.docker.com/engine/reference/commandline/docker/

## 镜像命令

**docker images 查看所有本地的主机上的镜像**

```shell
root@VM-24-9-ubuntu:/home/jianghaoyu# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   4 months ago   13.3kB

#解释
REPOSITORY 镜像的仓库源
TAG        镜像的标签
IMAGE ID   镜像的id
CREATED    镜像创建的时间
SIZE       镜像的大小

#可选项
-a, --all      #列出所有镜像
-q, --quiet    #只显示镜像的id
```

**docker search 搜索镜像**

```shell
root@VM-24-9-ubuntu:/home/jianghaoyu# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   12043     [OK]       
mariadb                           MariaDB Server is a high performing open sou…   4613      [OK] 

#可选项，通过收藏来过滤
--filter=STARS=3000  #搜索出来的镜像就是STARS大于3000的
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   12043     [OK]       
mariadb   MariaDB Server is a high performing open sou…   4613      [OK]     
```

**docker pull 下载镜像**

修改下载镜像源地址，默认为/etc/docker/daemon.json，在该文件中加入如下内容后重启docker

```json
{ 
"registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"] 
}
```

```shell
#下载镜像 docker pull 镜像名[:tag]
root@VM-24-9-ubuntu:/home/jianghaoyu# docker pull mysql
Using default tag: latest #如果不写 tag，默认就是lastest
latest: Pulling from library/mysql
6552179c3509: Pull complete #分层下载，docker image的核心 联合文件系统
d69aa66e4482: Pull complete 
3b19465b002b: Pull complete 
7b0d0cfe99a1: Pull complete 
9ccd5a5c8987: Pull complete 
2dab00d7d232: Pull complete 
64d3afdccd4a: Pull complete 
82148d50b16c: Pull complete 
8bb7d73a7d0c: Pull complete 
74778cd68a75: Pull complete 
d7e5f9309140: Pull complete 
f2e376ecd59f: Pull complete 
Digest: sha256:92d27b8222bbcf53bc42c70ca7cd1010d6c0527efc61f14980ce77c50932bef4 #签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest #真实地址

#等价于他
docker pull mysql
docker pull docker.io/library/mysql:latest

#指定版本下载
root@VM-24-9-ubuntu:/home/jianghaoyu# docker pull mysql:5.7
5.7: Pulling from library/mysql
72a69066d2fe: Already exists 
93619dbc5b36: Already exists 
99da31dd6142: Already exists 
626033c43d70: Already exists 
37d5d7efb64e: Already exists 
ac563158d721: Already exists 
d2ba16033dad: Already exists 
0ceb82207cd7: Pull complete 
37f2405cae96: Pull complete 
e2482e017e53: Pull complete 
70deed891d42: Pull complete 
Digest: sha256:f2ad209efe9c67104167fc609cca6973c8422939491c9345270175a300419f94
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

**docker rmi 删除镜像**

```shell
docker rmi -f 镜像id #删除指定的镜像
docker rmi -f 镜像id 镜像id 镜像id #删除多个镜像
docker rmi -f $(docker images -aq) #删除全部的镜像
```

## 容器命令

**说明：我们有个镜像才可以创建容器，linux，下载一个centos镜像**

```shell
docker pull centos
```

**新建容器并启动**

```shell
docker run [可选参数] image

#参数说明
--name=“Name” 容器名字 tomcat01 tomcat02
-d            后台方式运行
-it           使用交互方式运行，进入容器查看内容
-p            指定容器的端口 -p 8080:8080
	-p ip:主机端口：容器端口
	-p 主机端口:容器端口（常用）
	-p 容器端口
-P            随机指定端口

#测试，启动并进入容器
root@VM-24-9-ubuntu:/home/jianghaoyu# docker run -it centos /bin/bash
[root@c945f73482a5 /]# ls #查看容器内的centos 基础版本，很多命令都是不完善的！
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 从容器中退到主机
[root@c945f73482a5 /]# exit
exit
root@VM-24-9-ubuntu:/# ls
bin  boot  cdrom  data  dev  etc  home  lib  lib32  lib64  libx32  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

**列出所有的运行的容器**

```shell
#docker ps 命令
-a #列出当前正在运行的容器+历史运行过的容器
-n=? #显示最近创建的容器
-q #只显示容器的编号

root@VM-24-9-ubuntu:/# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
root@VM-24-9-ubuntu:/# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                     PORTS     NAMES
c945f73482a5   centos         "/bin/bash"   5 minutes ago   Exited (0) 2 minutes ago             lucid_wozniak
c9bbab37cfa3   feb5d9fea6a5   "/hello"      4 days ago      Exited (0) 4 days ago                reverent_fermat
root@VM-24-9-ubuntu:/# 

```

**退出容器**

```shell
exit #直接容器停止并退出
Ctrl + P + Q #容器不停止退出
```

**删除容器**

```shell
docker rm 容器id              #删除指定容器，不能删除正在运行的容器
docker rm -f $(docker ps -aq) #删除所有的容器
docker ps -a -q|xargs docker rm #删除所有的容器
```

**启动和停止容器的操作**

```shell
docker start 容器id      #启动容器
docker restart 容器id    #重启容器
docker stop 容器id       #停止容器
docker kill 容器id       #强制停止当前的容器
```

## 常用的其他命令

**后台启动容器**

```shell
#命令 docker run -d 镜像名！
root@VM-24-9-ubuntu:/# docker run -d  centos
99a1ca198550f48ddd7b543603131054434edbe30fda9ccecf7753a26e534868

#问题 docker ps，发现 centos 停止了

#常见的坑，docker 容器使用后台运行，就必须要有一个前台进程，docker 发现没有应用，就会自动停止
```

**查看日志**

```shell
#自己编写一段shell脚本
root@VM-24-9-ubuntu:/# docker run -d centos /bin/sh -c "while true;do echo kuangshen;sleep 1;done"
39eb6bcab72045c77fedbefc1e191388e15a687995a7741a022e4276756bac61

root@VM-24-9-ubuntu:/# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
39eb6bcab720   centos    "/bin/sh -c 'while t…"   3 seconds ago   Up 2 seconds             romantic_benz

#显示日志
-tf             #显示日志
--tail number   #要显示日志的条数
root@VM-24-9-ubuntu:/# docker logs -tf --tail 10 39eb6bcab720
2022-02-02T03:57:49.470469255Z kuangshen
2022-02-02T03:57:50.471980414Z kuangshen
2022-02-02T03:57:51.473142285Z kuangshen
2022-02-02T03:57:52.475288386Z kuangshen
2022-02-02T03:57:53.476816980Z kuangshen
2022-02-02T03:57:54.478250246Z kuangshen
2022-02-02T03:57:55.479790852Z kuangshen
2022-02-02T03:57:56.481333896Z kuangshen
2022-02-02T03:57:57.482846849Z kuangshen
2022-02-02T03:57:58.484338599Z kuangshen
```

**查看容器中的进程信息**

```shell
#docker top -容器id

root@VM-24-9-ubuntu:/# docker top 39eb6bcab720
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                1025547             1025518             0                   11:57               ?                   00:00:00            /bin/sh -c while true;do echo kuangshen;sleep 1;done
root                1030262             1025547             0                   12:03               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
```

**查看镜像的元数据**

```shell
#docker inspect 容器id

root@VM-24-9-ubuntu:/# docker inspect 39eb6bcab720
[
    {
        "Id": "39eb6bcab72045c77fedbefc1e191388e15a687995a7741a022e4276756bac61",
        "Created": "2022-02-02T03:57:37.097153051Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo kuangshen;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 1025547,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-02-02T03:57:37.449343219Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/39eb6bcab72045c77fedbefc1e191388e15a687995a7741a022e4276756bac61/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/39eb6bcab72045c77fedbefc1e191388e15a687995a7741a022e4276756bac61/hostname",
        "HostsPath": "/var/lib/docker/containers/39eb6bcab72045c77fedbefc1e191388e15a687995a7741a022e4276756bac61/hosts",
        "LogPath": "/var/lib/docker/containers/39eb6bcab72045c77fedbefc1e191388e15a687995a7741a022e4276756bac61/39eb6bcab72045c77fedbefc1e191388e15a687995a7741a022e4276756bac61-json.log",
        "Name": "/romantic_benz",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/d5d80f1458661a7ed41332d279df336c0e91204ecdc47f5fb9b66a2a46f9e5be-init/diff:/var/lib/docker/overlay2/4df2aa428a72b8a4794d30343591759c4bf48c5f1c713373a73ffd9daf07f1fa/diff",
                "MergedDir": "/var/lib/docker/overlay2/d5d80f1458661a7ed41332d279df336c0e91204ecdc47f5fb9b66a2a46f9e5be/merged",
                "UpperDir": "/var/lib/docker/overlay2/d5d80f1458661a7ed41332d279df336c0e91204ecdc47f5fb9b66a2a46f9e5be/diff",
                "WorkDir": "/var/lib/docker/overlay2/d5d80f1458661a7ed41332d279df336c0e91204ecdc47f5fb9b66a2a46f9e5be/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "39eb6bcab720",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo kuangshen;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "1b712453fd165ff36aed3769dd90df14e2b22f2c08ae357ae1d95eecaaf69b79",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/1b712453fd16",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "7e8046bf5d4683cc029d098e19ba3362efac1b27677afe8c6e3a5743fba3dd67",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "21b9e76f874c72af75ff09678dfe5258d92ff7ba58ed4a142e2603b9958fda8a",
                    "EndpointID": "7e8046bf5d4683cc029d098e19ba3362efac1b27677afe8c6e3a5743fba3dd67",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

**进入当前正在运行的容器 **

```shell
#我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

#命令
docker exec -it 容器id bashShell

#测试
root@VM-24-9-ubuntu:/# docker exec -it 39eb6bcab720  /bin/bash
[root@39eb6bcab720 /]# ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 03:57 ?        00:00:00 /bin/sh -c while true;do echo kuangshen;sleep 1;done
root         969       0  0 04:13 pts/0    00:00:00 /bin/bash
root         989       1  0 04:13 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
root         990     969  0 04:13 pts/0    00:00:00 ps -ef

#方式二
docker attach 容器id
#测试
root@VM-24-9-ubuntu:/# docker attach 39eb6bcab720
正在执行当前的代码...

#docker exec 进入容器后开启一个新的终端，可以在里面操作（常用）
#docker attach 进入容器正在执行的终端，不会启动新的进程
```

**从容器内拷贝文件到主机上**

```shell
docker cp 容器id:容器内路径 目的地主机路径

#运行容器并进入容器内部
root@VM-24-9-ubuntu:/home/jianghaoyu# docker run -it centos /bin/bash
#查看容器根目录
[root@2a7899c982f6 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
#创建test.java文件
[root@2a7899c982f6 /]# touch test.java
#查看容器根目录
[root@2a7899c982f6 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  test.java  tmp  usr  var
#退出容器
[root@2a7899c982f6 /]# exit
exit
#查看服务器目录
root@VM-24-9-ubuntu:/home/jianghaoyu# ls
#查看容器id
root@VM-24-9-ubuntu:/home/jianghaoyu# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED             STATUS                         PORTS     NAMES
2a7899c982f6   centos         "/bin/bash"   39 seconds ago      Exited (0) 12 seconds ago                eager_swanson
#复制容器文件至服务器（容器停止了但文件还在）
root@VM-24-9-ubuntu:/home/jianghaoyu# docker cp 2a7899c982f6:/test.java /home/jianghaoyu
#查看服务器目录
root@VM-24-9-ubuntu:/home/jianghaoyu# ls
test.java
#拷贝是一个手动过程，未来使用 -v 卷的技术，可以实现
```

## 命令总结

![image-20220202134213837.png](/image/fea9e452-d3c0-4aa0-9df0-8eee4444be1e.png)

```shell
attach    Attach to a running container  #当前shell下attach连接指定运行镜像
build     Build an image from a Dockerfile  #通过Dockerfile定制镜像
commit    Create a new image from a container's changes  #提交当前容器为新的镜像
cp    Copy files/folders from a container to a HOSTDIR or to STDOUT  #从容器中拷贝指定文件或者目录到宿主机中
create    Create a new container  #创建一个新的容器，同run 但不启动容器
diff    Inspect changes on a container's filesystem  #查看docker容器变化
events    Get real time events from the server#从docker服务获取容器实时事件
exec    Run a command in a running container#在已存在的容器上运行命令
export    Export a container's filesystem as a tar archive  #导出容器的内容流作为一个tar归档文件(对应import)
history    Show the history of an image  #展示一个镜像形成历史
images    List images  #列出系统当前镜像
import    Import the contents from a tarball to create a filesystem image  #从tar包中的内容创建一个新的文件系统映像(对应export)
info    Display system-wide information  #显示系统相关信息
inspect    Return low-level information on a container or image  #查看容器详细信息
kill    Kill a running container  #kill指定docker容器
load    Load an image from a tar archive or STDIN  #从一个tar包中加载一个镜像(对应save)
login    Register or log in to a Docker registry#注册或者登陆一个docker源服务器
logout    Log out from a Docker registry  #从当前Docker registry退出
logs    Fetch the logs of a container  #输出当前容器日志信息
pause    Pause all processes within a container#暂停容器
port    List port mappings or a specific mapping for the CONTAINER  #查看映射端口对应的容器内部源端口
ps    List containers  #列出容器列表
pull    Pull an image or a repository from a registry  #从docker镜像源服务器拉取指定镜像或者库镜像
push    Push an image or a repository to a registry  #推送指定镜像或者库镜像至docker源服务器
rename    Rename a container  #重命名容器
restart    Restart a running container  #重启运行的容器
rm    Remove one or more containers  #移除一个或者多个容器
rmi    Remove one or more images  #移除一个或多个镜像(无容器使用该镜像才可以删除，否则需要删除相关容器才可以继续或者-f强制删除)
run    Run a command in a new container  #创建一个新的容器并运行一个命令
save    Save an image(s) to a tar archive#保存一个镜像为一个tar包(对应load)
search    Search the Docker Hub for images  #在docker
hub中搜索镜像
start    Start one or more stopped containers#启动容器
stats    Display a live stream of container(s) resource usage statistics  #统计容器使用资源
stop    Stop a running container  #停止容器
tag         Tag an image into a repository  #给源中镜像打标签
top       Display the running processes of a container #查看容器中运行的进程信息
unpause    Unpause all processes within a container  #取消暂停容器
version    Show the Docker version information#查看容器版本号
wait         Block until a container stops, then print its exit code  #截取容器停止时的退出状态值
```
# Docker镜像讲解

## 镜像是什么

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来！

## Docker镜像加载原理

> UnionFS（联合文件系统）

UnionFS(联合文件系统）:Union文件系统( UnionFS )是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtualfilesystem)。Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。
特性:一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录

> Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。
bootfs(boot file system)主要包含bootloader和kernel, bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。
rootfs (root file system)，在bootfs之上。包含的就是典型Linux系统中的/dev,/proc, /bin, letc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu ,Centos等等。

![image-20220202225542203.png](/image/7c6f0026-6f49-434b-9863-b1ef177099ef.png)

对于一个精简的OS， rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel ,自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版, bootfs基本是一致的, rootfs会有差别,因此不同的发行版可以公用bootfs。

## 分层理解

> 分层的镜像

我们可以去下载一个镜像，注意观察下载的日志输出，可以看到是一层一层的在下载！

![image-20220202225903308.png](/image/09d11bbe-77ab-4e3e-b440-10d543f21b91.png)

思考为什么Docker镜像要采用这种分层的结构呢?
最大的好处，我觉得莫过于是资源共享了!比如有多个镜像都从相同的Base镜像构建而来，那么宿主机只需在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。
查看镜像分层的方式可以通过docker image inspect命令！

```shell
root@VM-24-9-ubuntu:/home/jianghaoyu# docker image inspect redis
[
    {
        "Id": "sha256:7614ae9453d1d87e740a2056257a6de7135c84037c367e1fffa92ae922784631",
        "RepoTags": [
            "redis:latest"
        ],
        "RepoDigests": [
            "redis@sha256:db485f2e245b5b3329fdc7eff4eb00f913e09d8feb9ca720788059fdc2ed8339"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2021-12-21T12:42:49.755107412Z",
        "Container": "13d25f53410417c5220c8dfe8bd49f06abdbcd69faa62a9b877de02464bb04a3",
        "ContainerConfig": {
            "Hostname": "13d25f534104",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "Image": "sha256:e093f59d716c95cfce82c676f099b960cc700432ab531388fcedf79932fc81ec",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "20.10.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "redis-server"
            ],
            "Image": "sha256:e093f59d716c95cfce82c676f099b960cc700432ab531388fcedf79932fc81ec",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 112691373,
        "VirtualSize": 112691373,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/e6ca4123b0871d0abc8f6e888b37fbadbb99059e13613e399cb059df464f4e9b/diff:/var/lib/docker/overlay2/078db09c8e616593f73916df2105b23285e2ecc476d00d2555c9fb9ccde1bc04/diff:/var/lib/docker/overlay2/1944435e36745f72925516fe1566376c33ea9bd128779a2f47b4296e160f7901/diff:/var/lib/docker/overlay2/d5c697baf8cac2de2c8f8f40ef083813f535128566b1e4cf99f8ce6aecc99e14/diff:/var/lib/docker/overlay2/c1f839fb36f6ddddc957c97f074d736ea92c2cbc1940c2e2bc823a71458573d7/diff",
                "MergedDir": "/var/lib/docker/overlay2/5b4da1b79e1191787e45796432b444a31f4f8e34bf176ee07e8f72693434ec6b/merged",
                "UpperDir": "/var/lib/docker/overlay2/5b4da1b79e1191787e45796432b444a31f4f8e34bf176ee07e8f72693434ec6b/diff",
                "WorkDir": "/var/lib/docker/overlay2/5b4da1b79e1191787e45796432b444a31f4f8e34bf176ee07e8f72693434ec6b/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:2edcec3590a4ec7f40cf0743c15d78fb39d8326bc029073b41ef9727da6c851f",
                "sha256:9b24afeb7c2f21e50a686ead025823cd2c6e9730c013ca77ad5f115c079b57cb",
                "sha256:4b8e2801e0f956a4220c32e2c8b0a590e6f9bd2420ec65453685246b82766ea1",
                "sha256:529cdb636f61e95ab91a62a51526a84fd7314d6aab0d414040796150b4522372",
                "sha256:9975392591f2777d6bf4d9919ad1b2c9afa12f9a9b4d260f45025ec3cc9b18ed",
                "sha256:8e5669d8329116b8444b9bbb1663dda568ede12d3dbcce950199b582f6e94952"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]

```

其中rootfs展示了分层信息

```json
"RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:2edcec3590a4ec7f40cf0743c15d78fb39d8326bc029073b41ef9727da6c851f",
                "sha256:9b24afeb7c2f21e50a686ead025823cd2c6e9730c013ca77ad5f115c079b57cb",
                "sha256:4b8e2801e0f956a4220c32e2c8b0a590e6f9bd2420ec65453685246b82766ea1",
                "sha256:529cdb636f61e95ab91a62a51526a84fd7314d6aab0d414040796150b4522372",
                "sha256:9975392591f2777d6bf4d9919ad1b2c9afa12f9a9b4d260f45025ec3cc9b18ed",
                "sha256:8e5669d8329116b8444b9bbb1663dda568ede12d3dbcce950199b582f6e94952"
            ]
        }
```

**理解：**

所有的Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。
举一个简单的例子，假如基于Ubuntu Linux 16.04创建一个新的镜像，这就是新镜像的第一层;如果在该镜像中添加Python包，就会在基础镜像层之上创建第二个镜像层;如果继续添加一个安全补丁，就会创建第三个镜像层。
该镜像当前已经包含3个镜像层，如下图所示(这只是一个用于演示的很简单的例子)。

![image-20220202230112855.png](/image/7e6fdec2-f128-40b9-8236-0550cd664750.png)

在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，理解这一点非常重要。下图中举了一个简单的例子，每个镜像层包含3个文件，而镜像包含了来自两个镜像层的6个文件。

![image-20220202232135642.png](/image/f68d6162-e26b-4057-9dad-5102e7650155.png)

上图中的镜像层跟之前图中的略有区别，主要目的是便于展示文件。
下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版本。

![image-20220202232320363.png](/image/0ec37306-376f-4a82-a516-abf4f5a27fee.png)

这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。Docker通过存储引擎(新版本采用快照机制)的方式来实现镜像层堆栈，并保证多镜像层对外展示为统一的文件系统。
Linux上可用的存储引擎有AUFS、Overlay2、Device Mapper、Btrfs以及ZFS。顾名思义，每种存储引擎都基于Linux中对应的文件系统或者块设备技术，并且每种存储引擎都有其独有的性能特点。
Docker在Windows上仅支持windowsfilter一种存储引擎，该引擎基于NTFS文件系统之上实现了分层和CoW[1]。下图展示了与系统显示相同的三层镜像。所有镜像层堆叠并合并，对外提供统一的视图。

![image-20220202232429653.png](/image/c3f6f5cd-5989-4879-9b60-6cceb510a5df.png)

> 特点

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层！

## commit镜像

```shell
docker commit 提交容器

docker commit -m="提交信息" -a="作者" 容器id 目标镜像名:[TAG]
```

实战测试

```shell
#启动一个默认的tomcat

#发现这个默认的tomcat没有webapps应用，官方默认镜像webapps目录为空

#自己拷贝进去基本文件

#将我们操作过的容器通过commit为一个镜像！我们以后就使用我们修改过的镜像即可，这就是我们自己的一个修改镜像。
```

![image-20220207160642889.png](/image/53650693-aa09-4b51-9f13-aacf7d0d32ab.png)
如果想要保存当前容器的状态，就可以通过commit提交，获得一个镜像。

到这里才算是入门Docker！

# 容器数据卷

## 什么是容器数据卷

**docker的理念回顾**

将应用和环境打包成一个镜像！

数据？如果数据都在容器中，那么我们容器删除，数据就会丢失！需求：数据可以持久化。

MySQL，容器删了，删库跑路！需求：MySQL数据可以储存在本地！	

容器之间可以有一个数据共享的技术！Docker容器中产生的数据，同步到本地！

这就是卷技术！目录的挂载，将我们容器内的目录，挂载到Linux上面！

![image-20220207161905189.png](/image/65f8f2fb-3e35-4212-8ec4-20f665529107.png)
总结一句话：容器的持久化和同步操作！容器间也是可以数据共享的！

## 使用数据卷

> 方式一：直接使用命令来挂载 -v

```shell
docker run -it -v 主机目录:容器内目录

#测试
root@VM-24-9-ubuntu:/home# docker run -it -v /home/ceshi:/home centos /bin/bash

#启动起来时候我们可以通过docker inspect 容器id
```

![image-20220207162720640.png](/image/a9bb6a8f-3d3f-4de4-943b-75fbf2380426.png)
测试文件的同步

![image-20220207162956421.png](/image/293480ae-b9e6-47b2-aac4-515a841faf99.png)
再来测试！

1、停止容器

2、宿主机上修改文件

3、启动容器

4、容器内的数据依旧是同步的

![image-20220207163225811.png](/image/11d9ccef-e6a2-4a31-add7-c4f2b98c8997.png)

# DockerFile

## DockerFile介绍

dockerfile是用来构建docker镜像的文件！命令参数脚本！

构建步骤：

1. 编写一个dockerfile文件
2. docker build 构建成为一个镜像
3. docker run 运行镜像
4. docker push 发布镜像（DockerHub、阿里云镜像仓库！）

很多官方的镜像都是基础包，很多功能没有，我们通常会自己搭建镜像！

官方既然可以制作镜像，那我们也可以！

## DokcerFile构建过程

**基础知识：**

1. 每个保留关键字（指令）都必须是大写字母
2. 执行从上到下的顺序执行
3. #表示注释
4. 每一个指令都会创建提交一个新的镜像层，并提交

![image-20220209203510714.png](/image/06b3750e-6c0b-4a26-9936-e74a50ce5528.png)

dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写这个dockerfile文件，这个文件十分简单！

Docker镜像逐渐成为了企业交付的标准，必须要掌握！

步骤：开发，部署，运维。。。缺一不可！

DockerFile：构建文件，定义了一切的步骤，源代码

DockerImages：通过DockerFile构建生成的镜像，最终发布和运行的产品！

Docker容器：容器就是镜像运行起来提供服务器

## DockerFile的指令：

以前的话我们就是使用别人的，现在我们知道了这些命令后，我们来练习自己写一个镜像！

```shell
FROM                #基础镜像,一切从这里开始构建
MAINTAINER			#镜像是谁写的，姓名+邮箱
RUN					#镜像构建时需要运行的命令
ADD 				#步骤：tomcat镜像，这个tomcat压缩包！添加内容
WORKDIR				#镜像的工作目录  
VOLUME				#挂载的目录
EXPOSE    			#暴露端口配置
RUN 				#运行
CMD 				#指定这个容器启动需要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT   		#指定这个容器启动需要运行的命令，可以追加命令
ONBUILD 			#当构一个被继承 DockerFile 这个时候运行ONBUILD的指令
COPY				#类似ADD，将我们的文件拷贝到镜像中
ENV					#构建的时候设置环境变量！
```




## 实战测试

Docker Hub 中 99% 镜像都是从这个基础镜像过来的 FROM scratch，然后配置需要的软件和配置来进行的构建.
![image-20220209202913700.png](/image/610b1dac-553c-4db6-a4c0-0110725e56f7.png)

> 创建一个自己的centos

```shell
# 1、编写DockerFile文件
root@VM-24-9-ubuntu:/home/dockerfile# cat mydockerfile 
FROM centos
MAINTAINER jianghaoyu<1332141191@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

#2022年1月31日需要更换yum源
RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash

# 2、通过文件构建镜像
# 命令 docker build -f dockerfile 文件路径 -t 镜像名:[tag]
Successfully built 28195e1ece8b
Successfully tagged mycentos:0.1

# 3、测试运行
```

对比：官方的centos

![image-20220210210710107.png](/image/79cfdf73-22e8-463a-b1fe-87871387e37a.png)

我们增加之后的镜像

![image-20220210210839873.png](/image/a4a96d4e-73bd-4d22-86fc-5b9ada119b6a.png)

我们可以列出本地镜像的变更历史

![image-20220210211007157.png](/image/70457ab7-a198-4c82-87f1-53de2b2ebef4.png)

我们平时拿到一个镜像，可以研究一下是怎么做的？

> CMD 和 ENTRYPOINT 区别

```shell
CMD        # 指定这个容器启动时候就要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT # 指定这个容器启动的时候运行的命令，可以追加命令
```

测试cmd

```shell
#编写dockerfile文件
root@VM-24-9-ubuntu:/home/dockerfile# vim docker-cmd-test
FROM centos:7
CMD ["ls","-a"]

#构建镜像
root@VM-24-9-ubuntu:/home/dockerfile# docker build -f docker-cmd-test -t cmdtest .

#run运行发现我们的 ls -a 生效了
root@VM-24-9-ubuntu:/home/dockerfile# docker run d932e8700b43
.
..
.dockerenv
anaconda-post.log
bin
dev
etc
home
lib
lib64

#想追加一个命令 -l ls -al
root@VM-24-9-ubuntu:/home/dockerfile# docker run d932e8700b43 -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:380: starting container process caused: exec: "-l": executable file not found in $PATH: unknown.

#cmd的情况下 -l 替换了CMD ["ls","-a"] 命令，-l 不是命令所以报错！
```

测试ENRTRYPOINT

```shell
root@VM-24-9-ubuntu:/home/dockerfile# vim dockerfile-entrypoint-test
FROM centos:7
ENTRYPOINT ["ls","-a"]

root@VM-24-9-ubuntu:/home/dockerfile# docker build -f dockerfile-entrypoint-test -t entryppoint-test .
Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM centos:7
 ---> eeb6ee3f44bd
Step 2/2 : ENTRYPOINT ["ls","-a"]
 ---> Running in 7cfedf091091
Removing intermediate container 7cfedf091091
 ---> 0fc57475f6d4
Successfully built 0fc57475f6d4
Successfully tagged entryppoint-test:latest

root@VM-24-9-ubuntu:/home/dockerfile# docker run 0fc57475f6d4
.
..
.dockerenv
anaconda-post.log
bin
dev
etc
home
lib
lib64

#我们的追加命令是直接拼接在我们的ENTRYPOINT后面
root@VM-24-9-ubuntu:/home/dockerfile# docker run 0fc57475f6d4 -l
total 64
drwxr-xr-x   1 root root  4096 Feb 16 11:21 .
drwxr-xr-x   1 root root  4096 Feb 16 11:21 ..
-rwxr-xr-x   1 root root     0 Feb 16 11:21 .dockerenv
-rw-r--r--   1 root root 12114 Nov 13  2020 anaconda-post.log
lrwxrwxrwx   1 root root     7 Nov 13  2020 bin -> usr/bin
drwxr-xr-x   5 root root   340 Feb 16 11:21 dev
drwxr-xr-x   1 root root  4096 Feb 16 11:21 etc
drwxr-xr-x   2 root root  4096 Apr 11  2018 home
lrwxrwxrwx   1 root root     7 Nov 13  2020 lib -> usr/lib
lrwxrwxrwx   1 root root     9 Nov 13  2020 lib64 -> usr/lib64


```

Dockerfile中很多命令都十分相似，我们需要了解他们的区别，我们最好的学习就是对比他们然后测试结果！

# Docker网络

## 理解Docker网络

清空所有环境

> 测试
![image-20220217191822239.png](/image/ad94b0b1-1ecc-4ef1-8e8b-d17b44c17b74.png)

![image-20220217193528799.png](/image/a5b6b9cc-7a0e-4cc1-88f6-7b59e5245bf3.png)

三个网络

```shell
# 问题，docker是如何处理容器网络访问的
root@VM-24-9-ubuntu:/home/tomcat/tomcatlogs# docker run -d -P --name tomcat01 tomcat

# 查看容器的内部网络地址，发现容器启动的时候会得到一个 ip地址，docker分配的！
root@VM-24-9-ubuntu:/home/tomcat/tomcatlogs# docker exec -it tomcat01 /bin/bash
root@07ba7a097287:/usr/local/tomcat# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      07ba7a09728

# 思考，linux能不能ping通容器内部！
root@VM-24-9-ubuntu:/home/tomcat# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.039 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.042 ms

# linux可以ping通docker容器内部
```

> 原理

1、我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0，桥接模式，使用的技术是veth-pair技术！

再测试ip addr

![image-20220217193919936.png](/image/345e6d2e-b5ec-4af3-8a91-c2147b8caa97.png)
2、再启动一个容器测试，发现又多了一对网卡

![image-20220217194107369.png](/image/4497f84b-b6ad-4ec3-98fa-5d5f63940671.png)
```shell
#我们发现这个容器带来的网卡，都是一对一对的
#veth-pair就是一对虚拟设备接口，他们都是成对出现的，一段连接着协议，一段彼此连接
#正因为有这个特性，veth-pair充当一个桥接，连接各种虚拟网络设备的
#OpenStac、Docker容器之间的连接，ovs的连接都是使用veth-pair技术
```

3、我们来测试一下tomcat01和tomcat02是否能ping通

结论：容器直接是可以互相ping通的！

绘制一个网络模型图

![image-20220217195122891.png](/image/49f8f7ee-50d3-4af6-846d-3c9abfb7fd2a.png)

结论：tomcat01 和 tomcat01 是公用的一个路由器，docker0。

所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认可用的IP。

> 小结

Docker使用的是linux的桥接，宿主机中是一个Docker容器的网桥docker0。

![image-20220217195638582.png](/image/02d1a08b-e2ec-4efc-b606-519efe141f8b.png)

Docker中的所有的网络接口都是虚拟的，虚拟的转发效率高！

只要容器删除，对应的网桥一对就没了。

## --link

> 思考一个场景，我们编写了一个微服务，database url=ip，项目不重启，数据库ip换掉了，我们希望可以处理这个问题，可以用名字来访问容器？

本质探究：--link 就是我们在hosts配置中增加了一个绑定配置

我们现在玩Docker已经不建议使用--link了。

自定义网络！不适用docker0！

docker0问题：不支持容器名连接访问！

## 自定义网络

> 查看所有的docker网络

![image-20220217201758149.png](/image/f20f23bc-d164-4176-9cd5-ede7306b8a45.png)

**网络模式**

bridge：桥接docker搭桥

none：不配置网络

host：和宿主机共享网络

container：容器内网络连通

**测试**

```shell
#我们直接启动的命令，默认 --net bridge

#我们可以自定义一个网络
#--driver bridge 
#--subnet 192.168.0.0/16
#--gateway 192.168.0.1

root@VM-24-9-ubuntu:/home/tomcat/tomcatlogs# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
56e87d92175bf57c272e3a85ed3a0351f45272682204a186ba7cd7445c12fbd6
root@VM-24-9-ubuntu:/home/tomcat/tomcatlogs# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
21b9e76f874c   bridge    bridge    local
d923f9359e76   host      host      local
56e87d92175b   mynet     bridge    local
be3b942af1fe   none      null      local
```

我们自己的网络就创建好了

![image-20220217202718641.png](/image/8fb13977-21f5-4700-8f95-aa9cd6b56c4f.png)

我们自定义的网络docker都已经帮我们维护好了对应关系，推荐我们平时这样使用网络！

好处：

redis - 不同的集群使用不同的网络，保证集群安全和健康的

mysql - 不同的集群使用不同的网络，保证集群安全和健康的

## 网络连通

![image-20220217203655640.png](/image/d52c7e3d-815a-46c8-9fc3-4814eeee12c2.png)

![image-20220217203748425.png](/image/5c896d41-af2c-4875-a863-4f05b3c0dfe8.png)

```shell
# 测试打通 tomcat01 - mynet

# 连通之后就是将 tomcat01 放到了 mynet 网络下

#一个容器两个ip地址
```

![image-20220217204148772.png](/image/8cc15554-d7af-44d3-ac73-d9aa52b200e3.png)

假设：假设

要跨网络操作别人，就需要使用docker network connect连通！