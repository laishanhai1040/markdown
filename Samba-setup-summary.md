---
title: samba setup summary
date: 2017/01/09
tags: samba
---

# samba 安装总结

## 需求
在工作中需要一个局域网文件共享服务器，日常使用Windows系统，使用Windows的smb服务，可以直接使用运行\\\192.168.1.100(局域网IP)连接到存储主机读写文件，可以配合Windows offices一起使用，直接读写文件。  
手上没有多余的PC，不过有一个树莓派2,在Linux中可以使用samba实现smb服务。

## 环境  
使用一个树莓派3，网线连接到路由器中，路由器设置mac地址与ip地址绑定。因为树莓派本身使用内存卡存储资料，遇到意外断电的情况容易导致无法正常开机，所以通过外挂U盘存储资料，即使意外断电导致树莓派无法正常启动还能把U盘插到电脑上正常读取资料。

## 配置树莓派开机自动挂载U盘

#### 查看U盘的盘符  

``` bash  
pi@raspberrypi:~ $ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        29G  4.3G   23G  16% /
devtmpfs        459M     0  459M   0% /dev
tmpfs           463M     0  463M   0% /dev/shm
tmpfs           463M  9.7M  454M   3% /run
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           463M     0  463M   0% /sys/fs/cgroup
/dev/mmcblk0p1   63M   20M   43M  32% /boot
/dev/sda1        30G  100M   30G   1% /mnt/samba
tmpfs            93M  4.0K   93M   1% /run/user/1000

```
上面的/dev/sda1就是U盘，挂载在/mnt/samba  

#### 进入parted后print/p查看U盘文件系统格式
``` bash
pi@raspberrypi:~ $ sudo parted /dev/sda1
GNU Parted 3.2
Using /dev/sda1
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print                                                            
Model: Unknown (unknown)
Disk /dev/sda1: 32.0GB
Sector size (logical/physical): 512B/512B
Partition Table: loop
Disk Flags:

Number  Start  End     Size    File system  Flags
 1      0.00B  32.0GB  32.0GB  ntfs
 (parted) quit
```
上面看出/dev/sda1的文件系统格式为ntfs.

#### 支持ntfs文件系统格式  
``` bash
pi@raspberrypi:~ $ sudo apt-get install -y ntfs-3g
```

#### 备份fstab文
``` bash
pi@raspberrypi:~ $ sudo cp /etc/fstab /etc/fstab_BK
```

#### 编辑fstab文件
``` bash
pi@raspberrypi:~ $ sudo vim /etc/fstab
```
添加以下内容:
``` bash
/dev/sda1 /mnt/samba ntfs-3g defaults,noexec,umask=0000 0 0
```
完整如下
``` bash
proc            /proc           proc    defaults          0       0
/dev/mmcblk0p1  /boot           vfat    defaults          0       2
/dev/mmcblk0p2  /               ext4    defaults,noatime  0       1
/dev/sda1       /mnt/samba  ntfs-3g defaults,noexec,umask=0000 0 0
# a swapfile is not a swap partition, no line here
#   use  dphys-swapfile swap[on|off]  for that

```

#### 重启后生效

## 安装并配置samba
#### apt-get 安装samba
``` bash
pi@raspberrypi:~ $ sudo apt-get intall -y samba
```
#### 进入/etc/samba
``` bash
pi@raspberrypi:~ $ cd /etc/samba
```
#### 备份smb.conf
``` bash
pi@raspberrypi:/etc/samba $ sudo cp smb.conf smb.conf_BK
```
#### 编辑smb.conf
``` bash
pi@raspberrypi:/etc/samba $ vim smb.conf
```
添加以下内容:

``` bash
[samba]
comment = samba
path = /mnt/samba
writeable = yes
create mode = 0664
directory mode = 0775
```

#### 检查samba.conf语法是否正确

``` bash
pi@raspberrypi:~ $ testparm
```
#### 添加samba用户

``` bash
root@raspberry:~ # adduser samba
```
#### 设定可使用samba的用户账号与密码

``` bash
pi@raspberrypi:~ $ sudo pdbedit -a -u samba
```
#### 检查已经存在的samba账号
``` bash
pi@raspberrypi:~ $ sudo pdbedit -L
```

#### 重启samba

``` bash
pi@raspberrypi:~ $ sudo /etc/init.d/smbd restart
```

#### 快速添加账号到sudo用户组

``` bash
root@raspberry:~ # usermod -aG sudo username
```

## 使用效果

在Windows使用运行(快捷键win+R)输入\\\192.168.1.100(目标主机IP),回车.
