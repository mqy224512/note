# <font style="color:black;font-weight:bolder">Multipass安装与使用</font>

## <font style="color:black;font-weight:bolder">概述</font>

Multipass 是一个轻量虚拟机管理器，是由 Ubuntu 运营公司 Canonical 所推出的开源项目。运行环境支Linux、Windows、macOS。在不同的操作系统上，使用的是不同的虚拟化技术。在 Linux 上使用的是 KVM、Window 上使用 Hyper-V、macOS 中使用 HyperKit 以最小开销运行VM，支持在笔记本模拟小型云。

同时，Multipass提供了一个命令行界面来启动和管理 Linux 实例。下载一个全新的镜像需要几秒钟的时间，并且在几分钟内就可以启动并运行 VM。大大降低安装虚拟机的成本了。

官网：[https://multipass.run](https://multipass.run)



## <font style="color:black;font-weight:bolder">Mac安装Multipass</font>

```shell
brew cask install multipass
```

版本查看

```shell
multipass --version
```

## <font style="color:black;font-weight:bolder">Ubuntu安装Multipass</font>

```shell
sudo snap install multipass
```

版本查看

```shell
sudo multipass --version
```

## <font style="color:black;font-weight:bolder">Multipass使用</font>

### <font style="color:black;font-weight:bolder">获取版本信息</font>

```
multipass version
```

### <font style="color:black;font-weight:bolder">帮助</font>

```shell
multipass help
```

### <font style="color:black;font-weight:bolder">查找镜像</font>

```shell
multipass find
```

```shell
➜  ~ multipass find
Image                       Aliases           Version          Description
18.04                       bionic            20230303         Ubuntu 18.04 LTS
20.04                       focal             20230209         Ubuntu 20.04 LTS
22.04                       jammy,lts         20230302         Ubuntu 22.04 LTS
22.10                       kinetic           20230302         Ubuntu 22.10
anbox-cloud-appliance                         latest           Anbox Cloud Appliance
charm-dev                                     latest           A development and testing environment for charmers
docker                                        0.4              A Docker environment with Portainer and related tools
jellyfin                                      latest           Jellyfin is a Free Software Media System that puts you in control of managing and streaming your media.
minikube                                      latest           minikube is local Kubernetes
ros-noetic                                    0.1              A development and testing environment for ROS Noetic.
ros2-humble                                   0.1              A development and testing environment for ROS 2 Humble.
```

### <font style="color:black;font-weight:bolder">创建虚拟机</font>

语法：`multipass launch 镜像名称 -n 虚拟机名称`

- -n, --name: 名称
- -c, --cpus: cpu核心数, 默认: 1
- -m, --mem: 内存大小, 默认: 1G
- -d, --disk: 硬盘大小, 默认: 5G

```shell
# 以ubuntu 22.04的镜像创建一个虚拟机，分配4core，4G内存，40G硬盘
multipass launch 22.04 -n ubuntu2204 -c 4 -m 8G -d 40G
```

### <font style="color:black;font-weight:bolder">查看虚拟机实例信息</font>

```shell
multipass info ubuntu2204
```

```shell
➜  ~ multipass info ubuntu2204             
Name:           ubuntu2204
State:          Running
IPv4:           192.168.64.2
Release:        Ubuntu 22.04.2 LTS
Image hash:     f6b5b3a980f2 (Ubuntu 22.04 LTS)
CPU(s):         4
Load:           0.18 0.08 0.03
Disk usage:     1.4GiB out of 38.6GiB
Memory usage:   162.3MiB out of 7.7GiB
Mounts:         --
```

### <font style="color:black;font-weight:bolder">查看虚拟机实例列表</font>

```shell
multipass ls
multipass list
```

```shell
➜  ~ multipass list
Name                    State             IPv4             Image
ubuntu2204              Running           192.168.64.2     Ubuntu 22.04 LTS

➜  ~ multipass ls  
Name                    State             IPv4             Image
ubuntu2204              Running           192.168.64.2     Ubuntu 22.04 LTS
```

### <font style="color:black;font-weight:bolder">直接执行虚拟机命令</font>

```shell
# '--'后面跟命令
multipass exec <vm-name> -- <command>
```

```shell
➜  ~ multipass exec ubuntu2204 -- ls -la /home/ubuntu
total 28
drwxr-x--- 4 ubuntu ubuntu 4096 Mar 11 20:28 .
drwxr-xr-x 3 root   root   4096 Mar 11 20:28 ..
-rw-r--r-- 1 ubuntu ubuntu  220 Jan  7  2022 .bash_logout
-rw-r--r-- 1 ubuntu ubuntu 3771 Jan  7  2022 .bashrc
drwx------ 2 ubuntu ubuntu 4096 Mar 11 20:28 .cache
-rw-r--r-- 1 ubuntu ubuntu  807 Jan  7  2022 .profile
drwx------ 2 ubuntu ubuntu 4096 Mar 11 20:28 .ssh
```

### <font style="color:black;font-weight:bolder">进入到虚拟机</font>

```shell
multipass shell <vm-name>
```

### <font style="color:black;font-weight:bolder">启动虚拟机</font>

```shell
multipass start <vm-name>
```

### <font style="color:black;font-weight:bolder">停止虚拟机</font>

```shell
multipass stop <vm-name>
```

### <font style="color:black;font-weight:bolder">重启虚拟机</font>

```shell
multipass restart <vm-name>
```

### <font style="color:black;font-weight:bolder">删除虚拟机</font>

```shell
multipass delete <vm-name>
# 彻底删除
multipass delete --purge <vm-name> 
```

### <font style="color:black;font-weight:bolder">恢复被删除虚拟机</font>

```
multipass recover <vm-name> 
```

### <font style="color:black;font-weight:bolder">宿主机挂载</font>

`multipass mount 宿主机目录 虚拟机名称：虚拟机目录`

如：

```shell
multipass mount /mnt ubuntu2204
multipass mount /mnt ubuntu2204:/mnt
```

### <font style="color:black;font-weight:bolder">宿主机解除挂载</font>

`multipass unmount 虚拟机名称`

```shell
multipass unmount ubuntu2204
```

### <font style="color:black;font-weight:bolder">挂起虚拟机</font>

```
multipass suspend <vm-name>
```

