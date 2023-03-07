# ubuntu22.04安装tidb集群

## tidb 集群架构



## tidb集群安装

这里使用的是云服务器ubuntu 22.04

### 本地部署tidb集群

#### TiUP安装

TiUP是TiDB4.0版本引入的集群运维工具，通过TiUP可以进行TiDB的日常运维工作，包括部署、启动、关闭、销毁、弹性扩容和升级TiDB集群，以及管理TiDB集群参数。

1. 下载并安装TiUP

```shell
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh
```

   TiUP会被安装在`~/.tiup`目录下，安装成功够会提示以下信息：

```shell
Successfully set mirror to https://tiup-mirrors.pingcap.com
Detected shell: bash
Shell profile:  /home/ubuntu/.bashrc
/home/ubuntu/.bashrc has been modified to add tiup to PATH
open a new terminal or source /home/ubuntu/.bashrc to use it
Installed path: /home/ubuntu/.tiup/bin/tiup
===============================================
Have a try:     tiup playground
===============================================
```

2. 声明全局变量，这样在任何目录下都能够使用tiup命令（上面安装成功的提示中，Shell profile即是我们要source的shell profile）

```shell
source /home/ubuntu/.bashrc
```

验证命令

```shell
ubuntu@ubuntu-22-04:~$ tiup -v
1.11.3 tiup
Go Version: go1.19.5
Git Ref: v1.11.3
GitHash: 7223ed50460785a2adf666d511a257aa03110294
```

3. 安装TiUP的cluster组件

```shell
tiup cluster
```

4. 如果机器已经安装TiUP cluster，更新软件版本

```shell
tiup update --self && tiup update cluster
```

#### 单机部署tidb集群

1. 集群实例列表

   | 实例    | 个数 | IP        | 端口  | 部署目录               |
   | ------- | ---- | --------- | ----- | ---------------------- |
   | TiKV    | 1    | 127.0.0.1 | 20160 | 使用global中配置的目录 |
   | TiDB    | 1    | 127.0.0.1 | 4000  | ～                     |
   | PD      | 1    | 127.0.0.1 | 2379  | ～                     |
   | Monitor | 1    | 127.0.0.1 |       | ～                     |
   | Grafana | 1    | 127.0.0.1 |       | ～                     |

2. 修改机器的sshd服务的最大链接数限制，默认是10

```shell
sudo vim /etc/ssh/sshd_config
```

   修改`MaxSessions`至30

   重启sshd服务

```shell
service sshd restart
```

3. 创建配置模板，`topo.yaml`

```yaml
# # Global variables are applied to all deployments and used as the default value of# # the deployments if a specific deployment value is missing.
# 路径是相对于~路径来创建的
global:
  user: "ubuntu"
  ssh_port: 22
  deploy_dir: "/home/ubuntu/opt/tidb/tidb-deploy"
  data_dir: "/home/ubuntu/opt/tidb/tidb-data"

# # Monitored variables are applied to all the machines.
monitored:
  node_exporter_port: 9100
  blackbox_exporter_port: 9115

server_configs:
  tidb:
    log.slow-threshold: 300
  tikv:
    readpool.storage.use-unified-pool: false
    readpool.coprocessor.use-unified-pool: true
  pd:
    replication.enable-placement-rules: true
    replication.location-labels: ["host"]
  tiflash:
    logger.level: "info"

pd_servers:
  - host: 127.0.0.1

tidb_servers:
  - host: 127.0.0.1

tikv_servers:
  - host: 127.0.0.1
    port: 20160
    status_port: 20180
    config:
      server.labels: { host: "logic-host-1" }

monitoring_servers:
  - host: 127.0.0.1

grafana_servers:
  - host: 127.0.0.1
```

4. 执行集群部署命令

```shell
#tiup cluster deploy <cluster-name> <version> ./topo.yaml --user root -p
tiup cluster deploy test-tidb-cluster v6.1.2 ./topo.yaml --user ubuntu -p
```

   - `<cluster-name>`：集群名称
   - `<version>`：集群版本，可以通过`tiup list tidb`命令查看当前部署的TiDB版本
   - `--user`：连接到目标机器时使用的用户名
   - `-p`：表示连接目标机器时使用的密码登陆

   查看部署目录

```shell
ubuntu@ubuntu-22-04:~/opt/tidb$ pwd
/home/ubuntu/opt/tidb
ubuntu@ubuntu-22-04:~/opt/tidb$ ll
total 20
drwxrwxr-x  4 ubuntu ubuntu 4096 Mar  5 18:50 ./
drwxrwxr-x  3 ubuntu ubuntu 4096 Mar  5 18:31 ../
drwxr-xr-x  8 ubuntu ubuntu 4096 Mar  5 18:50 tidb-data/
drwxr-xr-x 10 ubuntu ubuntu 4096 Mar  5 18:50 tidb-deploy/
-rw-rw-r--  1 ubuntu ubuntu 1174 Mar  5 18:49 topo.yaml
ubuntu@ubuntu-22-04:~/opt/tidb$ tree -L 2
.
├── tidb-data
│   ├── monitor-9100
│   ├── pd-2379
│   ├── prometheus-9090
│   ├── tikv-20160
│   ├── tikv-20161
│   └── tikv-20162
├── tidb-deploy
│   ├── grafana-3000
│   ├── monitor-9100
│   ├── pd-2379
│   ├── prometheus-9090
│   ├── tidb-4000
│   ├── tikv-20160
│   ├── tikv-20161
│   └── tikv-20162
└── topo.yaml

16 directories, 1 file
```

5. 启动集群

```shell
# 上面一个步骤有提示
tiup cluster start test-tidb-cluster --init

# 把生成的密码记录下来
+5^m6qaF29bB=X14G%
```

6. 查看集群状态

```shell
tiup cluster display test-tidb-cluster
```

7. 使用mysql 连接tidb

```shell
mysql -h 127.0.0.1 -P 4000 -u root -p+5^m6qaF29bB=X14G%
```

```shell
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| INFORMATION_SCHEMA |
| METRICS_SCHEMA     |
| PERFORMANCE_SCHEMA |
| mysql              |
| test               |
+--------------------+
5 rows in set (0.00 sec)
```

8. 其他命令

```shell
# 查看集群状况
tiup cluster display <cluster-name>

# 停掉集群
tiup cluster stop <cluster-name>

# 销毁集群
tiup cluster destroy <cluster-name> -y

# 删除集群数据
tiup cluster clean <cluster-name> --all

# 查看集群列表
tiup cluster list

# 修改集群配置
tiup cluster edit-config <cluster-name> 
```

#### 扩容-tidb-server

如果忘记安装了tidb-server，是无法通过sql语句来查询数据库中的内容的，可以通过`tiup cluster scale out` 命令进行扩容。

1. 编写配置文件

```shell
vim scale-out.yaml
```

```yaml
tidb_servers:
	- host: 127.0.0.1
	 ssh_port: 22
	 port: 4000
	 status_port: 10080
	 #deploy_dir: /data/deploy/install/deploy/tidb-4000
	 #log_dir: /data/deploy/install/log/tidb-4000
```

TiKV 配置文件参考：

```yaml
tikv_servers:
	- host: 10.0.1.5
	 ssh_port: 22
   port: 20160
   status_port: 20180
   deploy_dir: /data/deploy/install/deploy/tikv-20160
   data_dir: /data/deploy/install/data/tikv-20160
   log_dir: /data/deploy/install/log/tikv-20160
```

PD 配置文件参考：

```yaml
pd_servers:
	- host: 10.0.1.5
   ssh_port: 22
   name: pd-1
   client_port: 2379
   peer_port: 2380
   deploy_dir: /data/deploy/install/deploy/pd-2379
   data_dir: /data/deploy/install/data/pd-2379
   log_dir: /data/deploy/install/log/pd-2379
```

   可以使用 `tiup cluster edit-config <cluster-name>` 查看当前集群的配置信息，因为其中的 `global` 和 `server_configs` 参数配置默认会被 `scale-out.yaml` 继承，因此也会在 `scale-out.yaml` 中生效

2. 执行扩容命令
检查集群存在的潜在风险：

```shell
tiup cluster check <cluster-name> scale-out.yaml --cluster --user ubuntu [-p] [-i /home/root/.ssh/gcp_rsa]
```
   自动修复集群存在的潜在风险：

```sh
tiup cluster check <cluster-name> scale-out.yaml --cluster --apply --user ubuntu [-p] [-i /home/root/.ssh/gcp_rsa]
```
   执行 scale-out 命令扩容 TiDB 集群：

```sh
tiup cluster scale-out <cluster-name> scale-out.yaml --user ubuntu [-p] [-i /home/root/.ssh/gcp_rsa]
```

   以上操作示例中：

   - 扩容配置文件为 `scale-out.yaml`。
   - `--user root` 表示通过 root 用户登录到目标主机完成集群部署，该用户需要有 ssh 到目标机器的权限，并且在目标机器有 sudo 权限。也可以用其他有 ssh 和 sudo 权限的用户完成部署。
   - [-i] 及 [-p] 为可选项，如果已经配置免密登录目标机，则不需填写。否则选择其一即可，[-i] 为可登录到目标机的 root 用户（或 --user 指定的其他用户）的私钥，也可使用 [-p] 交互式输入该用户的密码。

   预期日志结尾输出 `Scaled cluster  <cluster-name> out successfully` 信息，表示扩容操作成功

### 离线安装tikv 集群

