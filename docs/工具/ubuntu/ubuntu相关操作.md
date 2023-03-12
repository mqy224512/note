# <font style="color:black;font-weight:bolder">Ubuntu相关操作</font>

## <font style="color:black;font-weight:bolder">SSH</font>

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



## <font style="color:black;font-weight:bolder">用户操作</font>

### <font style="color:black;font-weight:bolder">创建一个新用户</font>

创建一个新用户

```shell
sudo adduser <username>
```

将用户添加到sudo组中，以便拥有管理员权限

```shell
sudo usermod -aG sudo <username>
```

