# CentOS7网络配置

## 配置IP地址

### 查看网卡文件

```bash
ls /etc/sysconfig/network-scripts
```

![image-20220709102535169](https://s2.loli.net/2022/12/08/gf8qPiIWQN7uAtJ.png)

### 编辑网卡配置文件

```bash
vim /etc/sysconfig/network-scripts/ifcfg-ens33

TYPE=Ethernet			# 网卡类型
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static		# [none|static|dhcp] 分别为[不使用协议|静态分配|动态主机配置]
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
NAME=ens33
DEVICE=ens33			# 物理设备名称
ONBOOT=yes				# 开机是否激活网卡设备
IPADDR=192.168.72.135	# IP地址
NETMASK=255.255.255.0	# 子网掩码
GATEWAY=192.168.72.1	# 网关
DNS1=114.114.114.114	# DNS解析地址
```

### 重启网卡

```bash
systemctl restart network
ip addr
```

![image-20220709103615965](https://s2.loli.net/2022/12/08/PdBoQhiycsRHUrY.png)

### 有关网络的相关命令

```bash
systemctl start|status|restart network		# 启动、查看、重启网络
ifconfig [网卡名]		# 查看IP地址(需要安装net-tools工具  yum install net-tools -y)
ip addr									  # 查看IP地址
ping IP地址|域名						    # 测试网络联通性
```



## 修改网卡名称

### 查看并修改配置文件

```bash
cat /etc/sysconfig/grub

# 修改前
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet"		# 修改这一行
GRUB_DISABLE_RECOVERY="true"

# 修改后
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet net.ifnames=0 biosdevname=0"
GRUB_DISABLE_RECOVERY="true"
```

### 执行命令使配置生效

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

![image-20220709104341436](https://s2.loli.net/2022/12/08/uG9d4NlSKCaHPc7.png)

### 重命名网卡配置文件名称，并修改DEVICE和NAME名称

```bash
mv /etc/sysconfig/network-scripts/ifcfg-ens33 /etc/sysconfig/network-scripts/ifcfg-eth0		# 第一个网卡
sed -i 's|NAME=ens33|NAME=eth0|g' /etc/sysconfig/network-scripts/ifcfg-eth0					# 修改NAME
sed -i 's|DEVICE=ens33|DEVICE=eth0|g' /etc/sysconfig/network-scripts/ifcfg-eth0				# 修改DEVICE

mv /etc/sysconfig/network-scripts/ifcfg-ens37 /etc/sysconfig/network-scripts/ifcfg-eth1		# 第二个网卡
sed -i 's|NAME=ens37|NAME=eth1|g' /etc/sysconfig/network-scripts/ifcfg-eth1					# 修改NAME
sed -i 's|DEVICE=ens37|DEVICE=eth1|g' /etc/sysconfig/network-scripts/ifcfg-eth1				# 修改DEVICE
```

![image-20220709105339443](https://s2.loli.net/2022/12/08/9GNrQXCOw2v4Mqt.png)

### 重启计算机查看网卡名是否修改成功

```bash
reboot
ip addr
```

![image-20220709105634555](https://s2.loli.net/2022/12/08/viFeabGtHJn6Ls8.png)

## 域名解析

### 查看域名解析

```bash
cat /etc/resolv.conf
```

### 配置域名解析

- 方法一

  从网卡配置文件上添加

- 方法二

  直接修改/etc/resolv.conf中dns的地址

## 配置桥接网络

说明：以桥接网卡ens33为例，创建的birdge网桥名称为bridge-01

**桥接前**

![image-20220717172746449](https://s2.loli.net/2022/12/08/m5PAVzBdbCR6qg1.png)

1. 安装创建桥接的工具

   ```bash
   yum install bridge-utils -y
   ```

2. 创建网桥，完成相关配置

   ```bash
   brctl addbr bridge-01			# 创建网桥
   brctl addif bridge-01 ens33		# 将桥接的网卡ens33与网桥进行绑定
   ifconfig ens33 0.0.0.0			# 清除ens33原来的IP(192.168.72.201)
   ifconfig bridge-01 192.168.72.201/24 up	 # 将原ens33的IP分配给网桥bridge-01
   echo 'nameserver 114.114.114.114' >>/etc/resolv.conf	   # 配置域名解析
   ip route add default via 192.168.72.1 dev bridge-01		   # 添加默认路由（访问外网时指定通过网桥bridge-01经网关192.168.72.1访问外网）
   brctl stp bridge-01 on			# 开启生成树协议，防止网桥环路等
   ```

3. 查看网桥信息

   ```bash
   brctl show [birdgename]		# 查看[指定]网桥（不指定网桥则查看所有创建的网桥）
   [root@test ~]# brctl show
   bridge name	bridge id		STP enabled	interfaces
   bridge-01		8000.000c29fe075c	yes		ens33
   
   # 查看路由
   [root@test ~]# route -n
   Kernel IP routing table
   Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
   0.0.0.0         192.168.72.1    0.0.0.0         UG    0      0        0 bridge-01
   192.168.72.0    0.0.0.0         255.255.255.0   U     0      0        0 bridge-01
   202.207.240.0   0.0.0.0         255.255.255.0   U     100    0        0 ens37
   
   # 查看网桥信息
   [root@test ~]# ifconfig bridge-01
   bridge-01: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet 192.168.72.201  netmask 255.255.255.0  broadcast 192.168.72.255
           inet6 fe80::20c:29ff:fefe:75c  prefixlen 64  scopeid 0x20<link>
           ether 00:0c:29:fe:07:5c  txqueuelen 1000  (Ethernet)
           RX packets 679  bytes 40720 (39.7 KiB)
           RX errors 0  dropped 0  overruns 0  frame 0
           TX packets 36  bytes 2870 (2.8 KiB)
           TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   ```

**桥接后**

![image-20220717203925144](https://s2.loli.net/2022/12/08/az95gksCnbwym72.png)

**桥接相关命令**

```bash
[root@test ~]# brctl --help
Usage: brctl [commands]
commands:
	addbr     	<bridge>		add bridge			# 创建网桥
	delbr     	<bridge>		delete bridge		# 删除网桥
	addif     	<bridge> <device>	add interface to bridge	# 将桥接网卡和网桥绑定
	delif     	<bridge> <device>	delete interface from bridge # 将桥接网卡从网桥中删除
	hairpin   	<bridge> <port> {on|off}	turn hairpin on/off
	setageing 	<bridge> <time>		set ageing time		# 设置老化时间
	setbridgeprio	<bridge> <prio>		set bridge priority		# 设置网桥优先级
	setfd     	<bridge> <time>		set bridge forward delay
	sethello  	<bridge> <time>		set hello time			# 设置hello包时间间隔
	setmaxage 	<bridge> <time>		set max message age
	setpathcost	<bridge> <port> <cost>	set path cost
	setportprio	<bridge> <port> <prio>	set port priority
	show      	[ <bridge> ]		show a list of bridges	 # 列出所有网桥
	showmacs  	<bridge>		show a list of mac addrs	 # 显示MAC表
	showstp   	<bridge>		show bridge stp info		# 显示网桥生成树信息
	stp       	<bridge> {on|off}	turn stp on/off			# 开启或关闭生成树协议



# 显示生成树协议信息
[root@test ~]# brctl showstp bridge-01
bridge-01
 bridge id		8000.000c29fe075c
 designated root	8000.000c29db1ba8
 root port		   1			path cost		   4
 max age		  20.00			bridge max age		  20.00
 hello time		   2.00			bridge hello time	   2.00
 forward delay		   2.00			bridge forward delay	   2.00
 ageing time		 300.00
 hello timer		   0.00			tcn timer		   0.00
 topology change timer	   0.00			gc timer		 102.07
 flags			


ens33 (1)
 port id		8001			state		     forwarding
 designated root	8000.000c29db1ba8	path cost		   4
 designated bridge	8000.000c29db1ba8	message age timer	  18.49
 designated port	8001			forward delay timer	   0.00
 designated cost	   0			hold timer		   0.00
 flags			
```

添加路由

```
ip route add 
route add
```