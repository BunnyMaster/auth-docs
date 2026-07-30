## Linux 常用命令

### 设置静态 IP 地址

#### 打开配置文件

```bash
# 文件路径
sudo vim /etc/netplan/你的文件.yaml

# 示例
sudo vim /etc/netplan/00-installer-config.yaml
```

#### 编辑配置文件

**源文件示例**

```yaml
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        eth0:
            dhcp4: true
    version: 2
```

**示例文件**

```yaml
network:
  ethernets:
    eth0:
      dhcp4: false
      addresses: [172.20.116.203/20]
      optional: true
      routes:
        - to: default
          via: 172.20.112.1
      nameservers:
        addresses: [8.8.8.8]
  version: 2
```

#### 应用配置文件

```bash
sudo netplan apply
sudo reboot
```

### 校准时间

#### 安装 `tzdata`

```bash
sudo apt install tzdata
```

#### 设置时区

```bash
sudo timedatectl set-timezone Asia/Shanghai
```

#### 验证时间

```bash
timedatectl
```

### 设置防火墙

```bash
# 查看详细规则
sudo ufw show added

# 查看防火墙日志
sudo tail -f /var/log/ufw.log

# 暂时禁用防火墙（调试用）
sudo systemctl stop ufw
sudo systemctl start ufw

# 完全重置防火墙
sudo ufw reset
```

#### 设置打开防火墙

```bash
# 启用防火墙
sudo ufw enable

# 查看防火墙状态
sudo ufw status verbose
```

#### 设置关闭防火墙

```bash
# 禁用防火墙
sudo ufw disable

# 查看状态确认已关闭
sudo ufw status
```

#### 只开放部分端口

```bash
# 开放 SSH 端口 (22)
sudo ufw allow 22

# 开放 HTTP 端口 (80)
sudo ufw allow 80

# 开放 HTTPS 端口 (443)
sudo ufw allow 443

# 开放特定端口给特定协议
sudo ufw allow 53/udp  # DNS
sudo ufw allow 53/tcp  # DNS

# 开放端口范围
sudo ufw allow 8000:9000/tcp  # 开放8000-9000的TCP端口
```

### 设置镜像源

#### 备份镜像源

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.back
```

#### 编辑镜像源

```bash
sudo vim /etc/apt/sources.list
```

**镜像源配置**

```sh
deb https://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

# deb https://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```

#### 更新软件包列表

```bash
# 首先更新软件包列表
sudo apt update

# 升级已安装的软件包
sudo apt upgrade

# 如果需要完整升级
sudo apt full-upgrade
```
