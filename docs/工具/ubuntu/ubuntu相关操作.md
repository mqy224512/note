# <font style="color:black;font-weight:bolder">Ubuntu相关操作</font>

## <font style="color:black;font-weight:bolder">常规操作</font>

### <font style="color:black;font-weight:bolder">换源</font>

使用清华的源[https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)

先备份源文件

```shell
sudo cp /etc/apt/sources.list  /etc/apt/sources.list.bk
```

修改源文件，删除文件中之前的内容，写入新的源

```shell
sudo rm /etc/apt/sources.list
sudo vim /etc/apt/sources.list
```

仅包含 32/64 位 x86 架构处理器的软件包

```shell
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
# deb-src http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
```

在 ARM(arm64, armhf)、PowerPC(ppc64el)、RISC-V(riscv64) 和 S390x 等架构的设备上（对应官方源为 ports.ubuntu.com）使用：

```shell
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-backports main restricted universe multiverse

# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-security main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-security main restricted universe multiverse

deb http://ports.ubuntu.com/ubuntu-ports/ jammy-security main restricted universe multiverse
# deb-src http://ports.ubuntu.com/ubuntu-ports/ jammy-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-proposed main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-proposed main restricted universe multivers
```

更新源

```shell
sudo apt-get update
```



### <font style="color:black;font-weight:bolder">open too many files</font>

打开的文件描述符超限了

```shell
vim /etc/security/limits.conf

* soft nproc 1000000
* hard nproc 1000000
* soft nofile 1000000
* hard nofile 1000000
```

重新登录即可

查看所允许最大文件数

```shell
[root@localhost ~]# cat /proc/sys/fs/file-max
2000000
```

### <font style="color:black;font-weight:bolder">ssh连接Permission denied (publickey)</font>

可能是Linux上ssh服务没有开密码登录

```shell
sudo vim /etc/ssh/sshd_config
```

找到`PasswordAuthentication`,将`no`改为`yes` 

```shell
PasswordAuthentication yes
```

然后重启服务

```shell
sudo systemctl restart sshd
```

### <font style="color:black;font-weight:bolder">创建一个新用户</font>

创建一个新用户

```shell
sudo adduser <username>
```

将用户添加到sudo组中，以便拥有管理员权限

```shell
sudo usermod -aG sudo <username>
```

### <font style="color:black;font-weight:bolder">修改目录所有者</font>

递归修改目录及其目录中的文件的所有者

```shell
sudo chown -R <用户名>:<用户组> 目标目录
```



## <font style="color:black;font-weight:bolder">Git</font>

### <font style="color:black;font-weight:bolder">生成公钥</font>

```shell
# 一路回车
ssh-keygen -t rsa -C "your email"
```

### <font style="color:black;font-weight:bolder">设置global参数</font>

```shell
git config --global  user.name "your user name"
git config --global user.email "your email"

# 查看所有配置
git config --global --list
```

### <font style="color:black;font-weight:bolder">代码拉取报错</font>

仓库配置了ssh公钥，拉取代码报错：`Their offer: ssh-rsa fatal: Could not read from remote repository.`

```shell
vim ~/.ssh/config

Host *
HostkeyAlgorithms +ssh-rsa
PubkeyAcceptedKeyTypes +ssh-rsa
```



## <font style="color:black;font-weight:bolder">shell</font>

### <font style="color:black;font-weight:bolder">查看所有shell</font>

```shell
cat /etc/shells
```

### <font style="color:black;font-weight:bolder">查看当前sell</font>

```shell
echo $SHELL
```

### <font style="color:black;font-weight:bolder">zsh安装</font>

#### <font style="color:black;font-weight:bolder">安装zsh</font>

```shell
sudo apt install zsh
```

将默认的shell改为zsh

```shell
# 使用zsh
zsh
```

将zsh设置成默认shell

```shell
chsh -s /usr/bin/zsh
```

#### <font style="color:black;font-weight:bolder">安装oh-my-zsh</font>

前提已经安装了gt

```shell
# github安装
wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh

# giteea安装
sh -c "$(curl -fsSL https://gitee.com/shmhlsy/oh-my-zsh-install.sh/raw/master/install.sh)"
```

#### <font style="color:black;font-weight:bolder">配置zsh</font>

```shell
vim ~/.zshrc
```

```shell
#防止中文乱码
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8

# 默认的路径
ZSH="～/.oh-my-zsh"
# 设置字体模式以及配置命令行的主题 
#ZSH_THEME="robbyrussell" #默认主题
# 主题：https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
ZSH_THEME="gallois" # 目前使用主题
```



## <font style="color:black;font-weight:bolder">GO</font>

官网：[https://golang.google.cn/](https://golang.google.cn/)

### <font style="color:black;font-weight:bolder">Go安装(arm64)</font>

下载

```shell
wget https://golang.google.cn/dl/go1.17.13.linux-arm64.tar.gz
```

解压

```shell
tar -zxvf go1.17.13.linux-arm64.tar.gz -C ~/opt/programming/Go
```

创建目录

```shell
mkdir ~/opt/programming/Go/go_workspace
mkdir ~/opt/programming/Go/GOCACHE
touch ~/opt/programming/Go/ENV
```

```shell
[~/opt/programming/Go]$ ll ~/opt/programming/Go        
total 12K
-rw-rw-r-- 1 ubuntu ubuntu   0 Mar 12 17:46 ENV
drwxr-xr-x 1 ubuntu ubuntu 608 Jul 30  2022 go
drwxrwxr-x 1 ubuntu ubuntu  64 Mar 12 17:46 GOCACHE
drwxrwxr-x 1 ubuntu ubuntu  64 Mar 12 17:46 go_workspace
```

配置环境变量

```shell
vim ~/.zshrc
```

```shell
export OPT_DIR=$HOME/opt

# Go
export GO_HOME=$OPT_DIR/programming/Go
export GOROOT=$GO_HOME/go
export GOPATH=$GO_HOME/go_workspace
export GOENV=$OPT_DIR/programming/Go/ENV
export GOCACHE=$OPT_DIR/programming/Go/GOCACHE
export PATH=$PATH:$GOROOT/bin::$GOPATH
export GO111MODULE=on
export GOPROXY=https://goproxy.io,direct
```

```shell
source ~/.zshrc
```

查看版本

```shell
[~/opt/programming/Go]$ go version                                                                                                       
go version go1.17.13 linux/arm64
```







## <font style="color:black;font-weight:bolder">Rust</font>

### <font style="color:black;font-weight:bolder">Rust 安装</font>
