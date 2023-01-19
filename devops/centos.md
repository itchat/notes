# Centos 7.9

本教程记载 Centos 7.9 装机以及双网卡配置和 VMware 虚拟机配置过程

## Centos

refus.exe 挑选 U 盘制作镜像后插入机器，重启 delete 或者 F11 或者 F12 键进入 BIOS 模式调整启动顺序

设置系统分区分别为

```shell
swap  48 GiB
/home 300 GiB
/boot 1024 MiB
/     
```

最后的根目录会自动计算，然后选择 GHOME 图形化界面安装，配置大容量 U 盘储存兼容

```shell
yum install exfat-utils fuse-exfat
# 查看硬盘位置
lsblk
# 可能需要手动挂载
mkdir sdb
mount -t exfat /dev/sdb1 sdb
```

## Network

常用网络配置检查工具为：`ifconfig` `route -n` 与 `ip a`

检查确定网卡名字后开始手动编辑网卡配置

- 设置 enp2s0f0 为静态 IP 用于 ssh 连接

```shell
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp2s0f0
UUID=96b50dd6-5241-4b51-a486-c15091cd83c4
DEVICE=enp2s0f0
ONBOOT=yes
IPADDR=192.168.10.191
NETMASK=255.255.255.0
#GATEWAY=192.168.10.182
```

- 设置 enp2s0f1 为 dhcp 用于联网

```shell
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
#BOOTPROTO=static
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp2s0f1
#UUID=d7079ed0-a050-4c8d-99be-25a3c4f30287
DEVICE=enp2s0f1
ONBOOT=yes
#IPADDR=192.168.11.185
NETMASK=255.255.255.0
```

然后修改 dns 配置 `/etc/resolv.conf`

```shell
nameserver 192.168.0.1
nameserver fe80::2ad1:27ff:fe17:ead9%enp2s0f1
nameserver fe80::5%enp2s0f1
```

重启 network 后 `ping www.baidu.com` 测试是否联网

## SELinux

SELinux 是 Security Enhanced Linux 的缩写，字面上的意思就是安全强化的 Linux，它是由美国国家安全局 (NSA) 开发的，整合到 Linux 核心的一个模块，是对于强制访问控制（MAC）的实现，是 Linux 历史上最杰出的新安全子系统，提供了比传统的 UNIX 权限更好的访问控制。在 SELinux 的访问控制体系的限制下，进程只能访问那些在他的任务中所需要文件。

常见的命令为 `sestatus` `getenforce` `setenforce`

- enforcing：强制模式，表示 SELinux 运行中，且已经正确的开始限制 domain/type 了
- permissive：宽容模式，表示 SELinux 运行中，不过仅会有警告信息，并不会实际限制 domain/type 的存取。这种模式可以运来作为 SELinux 的 debug 之用
- disabled：关闭，SELinux 没有运行

```shell
vim /etc/selinux/config
```

`SELINUX=enforcing` 改成 disabled

## VNCServer

```shell
yum install tigervnc-server 
vncpasswd
vncserver
vncserver -kill :1
systemctl start vncserver@:1.service
```

## VMware

用 winscp 上传 vmware 安装包后安装依赖

```shell
yum update kernel -y
yum install kernel-headers kernel-devel gcc make -y
init 6
```

安装 vmware 安装包以及激活码激活

```shell
chmod +x VMware-Workstation-Full-17.0.0-20800274.x86_64.bundle
./VMware-Workstation-Full-17.0.0-20800274.x86_64.bundle --console --eulas-agreed --required -s vmware-workstation serialNumber MC60H-DWHD5-H80U9-6V85M-8280D
```

备份的 Windows 工业协议操作环境上传到 `/opt/soft` 目录解压，开始用 VMware 恢复镜像

恢复 VMware 的网络设置

```txt
# vmnet1 host-only 
Host-only 勾选
Use local DHCP 勾选
Connect a host virtual adapter 勾选
Subnet IP: 172.168.114.0 Subnet mask: 255.255.255.0

# vmnet8 NAT 这个有 NAT Settings 要设置
NAT 勾选
Use local DHCP 勾选
Connect a host virtual adapter 勾选
Subnet IP: 172.16.70.0 Subnet mask: 255.255.255.0

NAT 中
Gateway IP: 172.16.70.2
Allow active FTP 以及 Allow any organizationally 勾选其他默认
```