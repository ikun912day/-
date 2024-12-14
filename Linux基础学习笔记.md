## Linux基础

| 顺序 | 内容             | 说明                                       |
| ---- | ---------------- | ------------------------------------------ |
| 01   | 加电自检         | 通电后加载BIOS，检查硬件                   |
| 02   | 选择启动顺序     | 根据bios预先设置，依次从不同的设备启动系统 |
| 03   | 主引导记录MBR    | 引导系统读取磁盘信息                       |
| 04   | /boot驱动，grub  |                                            |
| 05   | 内核文件         | 选择操作系统和内核版本，加载内核信息       |
| 06   | systemd进程      | 系统进程启动运行起来                       |
| 07   | 运行级别         | 多种运行级别                               |
| 08   | 输入用户名和密码 |                                            |



### 一、基本语句以及常用参数

##### 1.cd

| 命令 | 作用           |
| ---- | -------------- |
| cd   | 切回家目录     |
| cd - | 切回上一个目录 |
| cd.  | 切回上级目录   |

##### 2.ls

| 参数 | 作用                 |
| ---- | -------------------- |
| -a   | 显示所有文件目录     |
| -l   | 长格式显示           |
| -h   | 带单位的文件大小     |
| -r   | 反向排序             |
| -t   | 按时间排序，从新到旧 |
| -S   | 按大小排序，从大到小 |

##### 3.pwd

查看当前路径，易于复制

##### 4.touch

创建文件

-t(指定时间戳)

##### 5.mkdir

-p(可创建多级目录)

##### 6.mv和cp

| 命令 | 参数示例          | 作用                             |
| ---- | ----------------- | -------------------------------- |
| mv   | mv  file1 file2   | 重命名/移动file1到file2          |
|      | mv -v file1 file2 | 显示过程                         |
|      | mv -i file1 file2 | 交互式操作                       |
| cp   | cp  file1 file2   | 赋值file1到file2                 |
|      | cp -r file1 file2 | 递归复制（复制才需要-r复制目录） |
|      | cp -v file1 file2 | 显示过程                         |
|      | cp -i file1 file2 | 交互式操作                       |

##### 7.cat

| 参数 | 说明         |
| ---- | ------------ |
| -n   | 显示行号     |
| -b   | 显示空白行号 |
| -s   | 压缩空白行   |

##### 8.echo

##### 9.tail

| 参数 | 说明      |
| ---- | --------- |
| -n   | 显示后n行 |
| -f   | 实时输出  |

##### 10.head

-n(显示前n行)

##### 11.tar

tar -czvf 1.tar file1 file2 file3

tar -xzvf 1.tar

| 参数 | 作用              |
| ---- | ----------------- |
| -c   | 创建归档文件tar   |
| -x   | 解开归档文件tar   |
| -z   | 压缩gz            |
| -v   | 显示压缩/解压过程 |
| -f   | 指定归档文件名称  |

##### 12.zip和unzip

zip 1.zip file1 file2 file3(如果要压缩目录需要-r)

unzip 1.zip(解压到...需要-d)

##### 13.rm

-f（强制删除）

-i（交互式删除）

-r 或 -R（递归删除）

##### 14.find

-size(按大小)+10m(大于10m的文件)

-atime(按访问时间)-mtime(修改时间)-ctime(改变时间)

##### 15.alias

vim ~/.bashrc

alias ll='ls -lha'

##### 16.which

用于查找可执行文件位置的命令

which -ls

##### 17.reboot

重启机器

##### 18.shutdown

-h now 立即关闭

-r now 立即重启

-h +30 30分钟后重启 14:00 14:00重启

##### 19.uname

-a（全部信息）

-s（内核名称）

-m（硬件平台）

##### 20.passwd

修改密码

##### 21.hostnamectl

hostnamectl set-hostname [新主机名] (修改主机名)

##### 22.grep

| 命令    | 作用                                  |
| ------- | ------------------------------------- |
| grep -i | 忽略大小写                            |
| grep -v | 输出的内容反转                        |
| grep -n | 带行号输出                            |
| grep -r | 文件递归搜索                          |
| grep -c | 输出内容的行数（在第几行）            |
| grep -E | 正则表达式                            |
| grep -A | 检索到的内容以及之后（after）         |
| grep -B | 检索到的内容以及之前（before）        |
| grep -C | 检索到的内容的前后（三个皆可带-数字） |
| grep -q | 布尔值，搜到了就是true                |

| 进阶命令                        | 作用         | 示例                                           |
| ------------------------------- | ------------ | ---------------------------------------------- |
| grep \[^\]                      | 单字符取反   | grep \[^abc\] a.txt #只有abc的行就不输出       |
| grep \[a-z\]                    | 匹配单字符   | grep  \[a-z\] a.txt #输出含有a-z任一字母的行   |
| grep [[:alnum:]]或者[0-9a-zA-Z] | 匹配字母数字 | grep [[:alnum:]] a.txt或grep [0-9A-Za-z] a.txt |
| [[:alpha:]] 或 [a-zA-Z]         | 字母         |                                                |
| [[:upper:]] 或 [A-Z]            | 大写         |                                                |
| [[:lower:]] 或 [a-z]            | 小写         |                                                |
| [[:space:]]                     | 空格         |                                                |
|                                 |              |                                                |
|                                 |              |                                                |



##### 23.useradd创建用户

| 命令                     | 作用                       |
| ------------------------ | -------------------------- |
| useradd -m user          | 创建用户时为用户创建家目录 |
| useradd -d 目录 user     | 指定目录                   |
| useradd -s /sbin/nologin | 创建一个不允许登录的用户   |

##### 24.tr替换

| 命令                      | 作用                       |
| ------------------------- | -------------------------- |
| tr 'a-z' 'A-Z' < file.txt | 把file中的小写字母转为大写 |
| tr -d 'a-d' < file.txt    | 把file中a-d删除            |
| tr -s ' ' < file.txt      | 压缩file中的空格           |

##### 25.sort排序

| 命令               | 作用                     |
| ------------------ | ------------------------ |
| sort file.txt      | 按字母顺序对文件的行排序 |
| sort -r file.txt   | 逆序                     |
| sort -n file.txt   | 当内容是数字时按数字排序 |
| sort -k 2 file.txt | 按每行第二个字段排序     |
| sort -f file.txt   | 忽略大小写排序           |

##### 26.uniq去重

| 命令             | 作用                 |
| ---------------- | -------------------- |
| uniq file.txt    | 去重                 |
| uniq -c file.txt | 显示每行出现的次数   |
| uniq -u file.txt | 只显示出现过一次的行 |

##### 27.cut提取

| 命令                     | 作用                                           |
| ------------------------ | ---------------------------------------------- |
| cut -b 1-5 file.txt      | 按字节提取每行第1-5个字节                      |
| cut -c 1-5 file.txt      | 按字符提取每行第1-5个字节                      |
| cut -d ' ' -f 2 file.txt | 按字段提取，以空格为分隔符，提取每行第二个字段 |

##### 28.磁盘查看命令

| 命令                  | 作用                                             |
| --------------------- | ------------------------------------------------ |
| df -Th                | 看使用情况、文件系统类型、挂载点                 |
| lsblk                 | 看块设备                                         |
| fdisk -l /dev/sdb     | 查看指定分区信息，磁盘大小、扇区数量、分区类型等 |
| parted /dev/sdb print | 功能同上，主要是更好的支持GPT                    |
| du -h                 | 会显示目录以及子目录、文件磁盘占用大小           |

##### 29.内存查看命令

| 命令    | 作用                                           |
| ------- | ---------------------------------------------- |
| free -h | 查看内存大小，使用情况，以更容易理解的方式展示 |
| lsmem   | 查看内存可用区域、大小、偏移量等信息           |

##### 30.查看网络连接套接字（基于tcp协议）

| 命令      | 作用                                                         |
| --------- | ------------------------------------------------------------ |
| ss -tunlp | 查看tcp连接、udp连接、不域名解析、处于监听状态的套接字、相关进程号和进程名称 |
| netstat   | 功能同上                                                     |

##### 31.ps查看进程

| 命令    | 作用                                                         |
| ------- | ------------------------------------------------------------ |
| ps -ef  | 以全格式显示进程信息，包括uid、pid、ppid、cpu使用率等等      |
| ps -aux | 更详细的显示包括用户名、虚拟内存大小、内存使用率、进程状态、命令行等等 |

##### 32.kill停止运行

| 命令        | 作用                        |
| ----------- | --------------------------- |
| kill pid    | 优雅停止pid这个进程号的进程 |
| kill -9 pid | 强制杀死pid这个进程号的进程 |

##### 33.curl与服务器数据传输（基于http协议）

| 命令                                                        | 作用                               |
| ----------------------------------------------------------- | ---------------------------------- |
| curl www.baidu.com                                          | 获取网页内容                       |
| curl -X POST www.baidu.com/api                              | 发送POST请求                       |
| curl -X PUT www.baidu.com/api                               | 发送PUT请求                        |
| curl -H "Content-Type: application/json" http://example.com | 携带特定请求头，设置特定请求头信息 |
| curl -F 'file=@/path/to/file.txt' http://example.com        | 上传文件                           |
| curl -d '{"key": "value"}' http://example.com               | 以POST的形式发送JSON数据           |

##### 34.telnet测试网络连接工具（基于tcp协议）

| 命令                  | 作用                                 |
| --------------------- | ------------------------------------ |
| telnet example.com 80 | 测试与`example.com` 的 80 端口的连接 |

##### 35.scp（主机间文件完整传输）

| 命令                               | 作用                           |
| ---------------------------------- | ------------------------------ |
| scp 文件 用户名@主机号:目录        | 把文件传输给远程主机的某个目录 |
| scp 用户名@主机号:文件 本地目录    | 把文件从远程主机拷贝到本地目录 |
| scp -r 目录 用户名@主机号:目录     | 把目录传输给远程主机的某个目录 |
| scp -r 用户名@主机号:目录 本地目录 | 把目录从远程主机拷贝到本地目录 |

##### 36.rsync（主机间文件变化传输）

##### 37.tcpdump抓包

| 命令            | 作用           |
| --------------- | -------------- |
| tcpdump -i eth0 | 监听 eth0 网卡 |



### 二、重要文件位置

#### /etc

/etc/os-release系统版本

/etc/fstab挂载列表

/etc/hosts域名劫持 ip别名

/etc/ssh/sshd_config ssh配置文件

/etc/resolv.conf dns配置

/etc/selinux/config selinux配置文件

### 三、磁盘管理

#### 1.固态/机械

固态硬盘：轻、快、噪音小、贵

机械硬盘：重、慢、噪音大、便宜

```Bash

#-d只显示设备而不显示其分区
#-o选项后面跟的是要显示的字段
#rota为1表示机械硬盘，为0表示固态硬盘
lsblk -d -o name,rota
```

#### 2.磁盘分区

##### MBR分区 4/2TB

##### GPT分区 128/18EB

#### 3.BIOS和UEFI

BIOS是传统的引导程序，历史比较悠久，UEFI是BIOS的继任者，提供了更先进的功能

```Bash
#判断是否是uefi启动
ls /sys/firmware/efi
```

#### 4.磁盘查看命令

##### fdisk 命令（主要用于管理MBR）

fdisk -l /dev/sda

##### parted 命令（很好地支持GPT）

parted /dev/sdb print

##### lsblk 命令（看块设备以及挂载点信息）

##### df 命令（看使用情况）

df -Th

#### 5.文件系统

管理计算机存储设备上的数据，提供用户和应用程序与存储设备之间的接口

分类：

| 文件系统 | 备注                                                         |
| -------- | ------------------------------------------------------------ |
| ext2     | 不支持日志，单个文件最大支持2TB                              |
| ext3     | 支持日志                                                     |
| ext4     | 支持日志+单个文件最大支持16TB，更高性能，更大容量            |
| xfs      | 支持日志+单个文件最大支持16EB，更高性能，更大容量，适合大规模存储和高性能场景 |
| swap     | 交换分区专用的文件系统                                       |

#### 6.RAID阵列

##### RAID-0 分布读写，速度快，无冗余

##### RAID-1 双份读写，读性能增加，写性能降低，有冗余

##### RAID-4 加一块存校验位的盘（容易坏）

##### RAID-5 加一块盘，校验位分布到每个盘上，允许坏一块盘

##### RAID-6 加两块盘，2位校验位分布到每个盘上，允许快两块盘

##### RAID-10

##### RAID-01

#### 7.lvm逻辑卷管理器

### 二、网络

#### centos7

vim /etc/sysconfig/network-scripts/ifcfg-eth0

```
BOOTPROTO=static #静态ip配置
NAME=eth0 #网络接口名称
DEVICE=eth0 #设备名称
IPADDR=10.0.0.117 #静态ip
NETMASK=255.255.255.0 #子网掩码
GATEWAY=10.0.0.2 #网关地址
DNS1=202.96.128.166
DNS2=202.96.128.86
ONBOOT=yes #表示系统启动时激活该网络接口
```



#重启网络

systemctl restart network

#### ubuntu

cd /etc/netplan/01-network-manager-all.yaml

```yaml
# This file is generated from information provided by

# the datasource.  Changes to it will not persist across an instance.

# To disable cloud-init's network configuration capabilities, write a file

# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:

network:

 version: 2

 renderer: networkd

 ethernets:

  ens33: #网络接口名称

   dhcp4: no #不适用ipv4动态主机配置协议（DHCP）获取地址

   addresses: [10.0.0.100/24] #为接口手动配置ipv4地址和子网掩码

   routes:

​    \- to: 0.0.0.0/0 #任何地址的流量

​     via: 10.0.0.2 #都通过这个网关

   nameservers:

​    addresses: [8.8.8.8, 8.8.4.4] #指导dns服务器地址
```

sudo netplan apply重启网络

#### 1.配置dns

#vim /etc/resolv.conf

nameserver 127.0.0.53

 
