# IPTABLES防火墙

## 前提准备

- **主机**

| hostname  |     inet1      |      inet2      |      系统      | 其他 |
| :-------: | :------------: | :-------------: | :------------: | :--: |
| firewalld | 192.168.72.201 | 202.207.240.201 | CentOS7.5.1804 |      |

- **关闭CentOS7默认安全组件**

```bash
systemctl stop firewalld
systemctl disable firewalld

setenforce 0
sed -ri "s|(SELINUX=)(.*)|\1disabled|g" /etc/selinux/config
```

- **配置Base源**

```bash
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-6.10.repo
sed -i -e '/mirrors.cloud.aliyuncs.com/d' -e '/mirrors.aliyuncs.com/d' /etc/yum.repos.d/CentOS-Base.repo

yum clean all
yum makecache fast
```

- **安装iptables**

```bash
yum install iptables iptables-services -y

cat >>/etc/rc.d/rc.local <<EOF
modprobe ip_tables
modprobe iptable_filter
modprobe iptable_nat
modprobe ip_conntrack
modprobe ip_conntrack_ftp
modprobe ip_nat_ftp
modprobe ipt_state
EOF

chmod +x /etc/rc.d/rc.local

systemctl start iptables.service 
systemctl enable iptables.service 

reboot
```

![image-20220717110412537](https://s2.loli.net/2022/12/07/5iSr3kDRdcaUeOl.png)

## iptables基础

### <font color="red">iptables的四表</font>

|                表名称                 | 作用                                                         | 包含链                                                       |
| :-----------------------------------: | :----------------------------------------------------------- | ------------------------------------------------------------ |
| <font color="green">**filter**</font> | 根据具体的规则要求决定如何处理一个数据包（实现真正的防火墙功能） | <font color="yellow">**INPUT 、FORWARD、OUTPUT**</font>      |
|  <font color="green">**nat**</font>   | 用来修改数据包的ip地址，端口号等信息(共享上网、内部和外部IP映射、端口映射) | <font color="yellow">**OUTPUT、PREROUTING、POSTROUTING**</font> |
| <font color="green">**mangle**</font> | 用来修改数据包的服务类型、生命周期，或者设置mark标记实现流量整形等高级应用（很少用） |                                                              |
|  <font color="green">**raw**</font>   | 主要用来决定是否对数据包进行状态跟踪（很少用）               |                                                              |

### <font color="red">iptables的五链</font>

|                   链名称                   | 作用                                                         |
| :----------------------------------------: | ------------------------------------------------------------ |
|    <font color="green">**INPUT**</font>    | 当收到访问防火墙本机地址的数据包时，应用此链中的规则         |
|   <font color="green">**OUTPUT**</font>    | 当防火墙本机向外发送数据包时，应用此链中的规则               |
|   <font color="green">**FORWARD**</font>   | 当接收到需要通过防火墙中转发送给其他地址的数据包时，应用此链中的规则（**LVS** NAT模式中 net.ipv4.ip_forward=0）  sysctl -p  使得内核参数生效 |
| <font color="green">**PREROUTING**</font>  | 在对数据包做路由选择之前，应用此链中的规则（实现**端口转发映射**，如可以把80转换为局域网服务器9000端口上） |
| <font color="green">**POSTROUTING**</font> | 在对数据包做路由选择之后，应用此链中的规则（实现**共享上网**） |

### <font color="red">包过滤的匹配流程</font>

> 1. 规则表应用顺序：raw→mangle→nat→filter
>
> 2. 规则链的应用顺序取决于数据的流向：
>
>    入站数据流向：PREROUTING→路由选择→INPUT→应用程序
>    转发数据流向：PREROUTING→路由选择→FORWARD→POSTROUTING
>    出站数据流向：OUTPUT→路由选择→POSTROUTING
>
> 3. 规则链内部的处理规则：
>
>    顺序从表的开始规则依次匹配，直到匹配到符合的规则停止。（匹配即停止）
>
>    一旦找到一条匹配规则将不再检查后续的其他规则，如果一直找不到匹配的规则，就按默认规则处理。

## iptables基本语法

### **基本语法**

| 命令格式                                                     | 作用                                                 |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| `iptables [-t table] -A chain rule-specification`            | -A chain 在指定的规则链尾部追加一条规则;             |
| `iptables [-t table] -I chain [rulenum] rule-specification`  | 在指定的规则链首部插入一条规则                       |
| `iptables [-t table] -R chain rulenum rule-specification`    | 修改指定规则链中的规则                               |
| `iptables [-t table] -D chain rulenum`                       | 删除指定规则链中规则序号为rulenum的规则              |
| `iptables [-t table] -S [chain [rulenum]]`                   | 打印指定 {表\|规则链} 中的所有规则                   |
| `iptables [-t table] {-F|-L|-Z} [chain [rulenum]] [options...]` | 清除/列出/归零 指定 {表\|规则链} 中的规则            |
| `iptables [-t table] -N chain`                               | 创建用户自定义的规则链                               |
| `iptables [-t table] -X [chain]`                             | 删除用户自定义的规则链(默认删除所有非内置规则链)     |
| `iptables [-t table] -P chain target`                        | 将规则链的策略设为给定的目标（ACCEPT\|DENY）针对内置 |
| `iptables [-t table] -E old-chain-name new-chain-name`       | 将用户自定义的规则链进行重新命名                     |

```bash
rule-specification = [matches...] [target]
match = -m matchname [per-match-options]				# -m {multiport|state|limit...}
target = -j targetname [per-target-options]				# -j {ACCEPT|DROP|REJECT|LOG}
```

### 管理选项(commands)

|    管理选项     | 含义                                             | 示例                                |
| :-------------: | ------------------------------------------------ | ----------------------------------- |
|       -A        | 在指定链末尾追加一条                             | `iptables -A INPUT`                 |
|       -I        | 在指定链中插入一条新的，未指定序号默认作为第一条 | `iptables -I INPUT`                 |
|       -R        | 修改、替换某一条规则                             | `iptables -t nat -R INPUT`          |
|       -D        | 删除                                             | `iptables -t nat -D INPUT`          |
|       -L        | 查看                                             | ``iptables -L -t nat`               |
|       -F        | 清除所有规则                                     | `iptables -F`                       |
|       -P        | 指定默认规则                                     | `iptables -P`                       |
|       -n        | 以数字形式显示                                   | `iptables -nL`                      |
| -n -line-number | 规则带编号显示                                   | `iptables -nL --line-number -t nat` |

### 参数选项(parameters)

| 参数选项            | 作用                                                         |
| ------------------- | ------------------------------------------------------------ |
| `-p {tcp|udp|icmp}` | 指定规则应用的协议是tcp、udp或icmp                           |
| `--dport 目标口号`  | 指定规则中的目标端口                                         |
| `--sport 源端口号`  | 指定规则中的源端口                                           |
| `-s 源IP`           | 指定规则中的源IP地址                                         |
| `-d 目的IP`         | 指定规则中的目的IP地址                                       |
| `-m 模块名`         | 指定规则中需要使用的模块名,如包含{multiport \| limit \| state ...} |
| `-i 网卡名`         | 数据进入的时候，通过哪张网卡                                 |
| `-o 网卡名`         | 数据流出的时候，通过哪张网卡                                 |

### 控制选项(targetname)

|               控制选项                |                             含义                             |
| :-----------------------------------: | :----------------------------------------------------------: |
| <font color="green">**ACCEPT**</font> |                        允许数据包通过                        |
| <font color="green">**REJECT**</font> |      拒绝数据包通过，必要时会给数据包发送端一个响应信息      |
|  <font color="green">**DROP**</font>  |                 直接丢弃数据包，不给任何回应                 |
|  <font color="green">**LOG**</font>   | 在/var/log/messages文件中记录日志信息，然后将数据包给下一条规则，它本身不处理数据包 |

### 常用命令

```bash
iptables -L -n --line-number 					# 查看规则
iptables -D INPUT 3								# 删除INPPUT链中的第三条规则
iptables -F										# 清空规则
iptables -X										# 删除用户自定义的规则链
iptables -P INPUT ACCEPT						# 配置默认规则，允许所有端口访问
iptables -P INPUT DROP							# 配置默认规则，不允许所有端口访问
iptables -Z DOCKER								# 清空用户自定义链（INPUT）中的规则


iptables-save >/etc/sysconfig/iptables			# 保存防火墙规则
iptables-restore </etc/sysconfig/iptables		# 加载防火墙规则
```

## <font color="pink">案例配置</font>

### 拒绝IP地址为192.168.72.131的主机进行远程连接

```bash
iptables -I INPUT -s 192.168.72.131 -p tcp --dport 22 -j REJECT
```

1. 检查防火墙的规则
   ![image-20220717134259467](https://s2.loli.net/2022/12/07/yDhEmF95xC7gzdS.png)

2. 在主机192.168.72.131上远程连接，测试规则是否生效
   ![image-20220717134449810](https://s2.loli.net/2022/12/07/9fwegHnOasQbipl.png)



### 只允许192.168.72.0网段的端口进行访问

```bash
iptables -A INPUT ! -s 192.168.72.0/24 -j REJECT
```

> 注意：这里使用了取反的方法，先配置该网段拒绝访问，然后添加！让该规则取反

1. 查看防火墙规则
   ![image-20220717134854486](https://s2.loli.net/2022/12/07/9vYOXq8UF126zkK.png)

2. 验证防火前是否生效
   ![image-20220717135709811](https://s2.loli.net/2022/12/07/yl7HI3mFPXJ4cku.png)



### 禁止用户访问1024-65535范围的端口/禁止用户访问80和443端口

```bash
iptables -I INPUT -p tcp --dport 1024:65535 -j REJECT					# 指定范围
iptables -I INPUT -p tcp -m multiport --dport 81,444 -j REJECT			# 指定若干端口，需要额外指定模块  -m multiport
```

1. 查看规则
   ![image-20220717142206414](https://s2.loli.net/2022/12/07/63pT4dksfcwOVFI.png)

2. 验证规则

> 验证方法：使用nc命令在firewalld主机开启8888端口服务，使用另外一台主机访问8888端口

```bash
# firewalld端
nc -l 8888

# 客户端
nc 192.168.72.201 8888
nc 202.207.240.201 8888
```

![image-20220717142433498](https://s2.loli.net/2022/12/07/odScvRKkAN5ED6T.png)



### 禁止用户ping 防火墙

1. 方法一：修改内核参数

```bash
echo 'net.ipv4.icmp_echo_ignore_all = 1' >>/etc/sysctl.conf

sysctl -p
```

2. 方法二：防火墙规则

```bash
iptables -I INPUT -p icmp --icmp-type 8 -j REJECT
```

**查看规则**

![image-20220717143520931](https://s2.loli.net/2022/12/07/fjOHBQnNzuEGmsA.png)

**验证规则**

![image-20220717143647264](https://s2.loli.net/2022/12/07/Otw6znaLDmcCf4U.png)



### 匹配网络状态

> -m state --state
> NEW：已经或将启动新的连接
> ESTABLISHED：已建立的连接(默认允许)
> RELATED：正在启动的新连接(默认允许)
> INVALID：非法或无法识别的

```bash
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```



### 限制并发及速率（显示发送数据包的速率）

> -m limit --limit n/{second/minute/hour}:		（指定发送速率）
> 解释：指定时间内的请求速率”n”为速率，后面为时间分别为：秒 分 时
> --limit-burst [n]			（指定并发数）
> 解释：在同一时间内允许通过的请求”n”为数字，不指定默认为5

```bash
iptables -I INPUT -p icmp --icmp-type 8 -j ACCEPT
iptables -I INPUT -p icmp --icmp-type 8 -m limit --limit 6/min --limit-burst 5 -j ACCEPT
```

## 生产环境配置

1. 清空规则

   ```bash
   iptables -F
   iptables -X
   iptables -Z
   ```

   

2. 允许22端口访问

   ```
   iptables -A INPUT -p tcp --dport 22 -j ACCEPT
   ```

   

3. 允许本机lo接口数据流量流入和流出

   ```bash
   iptables -A INPUT -i lo -j ACCEPT 
   iptables -A OUTPUT -o lo -j ACCEPT
   ```

   

4. 匹配网络状态

   ```bash
   iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
   iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
   ```

   

5. 放行80/443端口

   ```bash
   iptables -I INPUT -p tcp -m multiport --dport 80,443 -j ACCEPT
   ```

   

6. 配置默认规则

   ```bash
   iptables -P INPUT DROP
   iptables -P FORWARD DROP			# 如果配置了共享上网，需要设置转发为  ACCEPT
   iptables -P OUTPUT ACCEPT
   ```

   

7. 添加白名单

   ```
   iptables -I INPUT -s 192.168.72.0/24 -j ACCEPT
   iptables -I INPUT -s 202.207.240.0/24 -j ACCEPT
   ```

   

## 共享上网、端口映射、IP映射

### 原理图

![image-20220717161219777](https://s2.loli.net/2022/12/07/EvK6NwrC4mXtunp.png)

### 共享上网

> **说明：**
>
> 主机仅有内网IP（202.207.240.0/24），只有防火墙配置双网卡 公网IP（192.168.72.201） 内网IP（202.207.240.201），使用nat转发模式，让内网主机网关指向防火墙，实现共享上网

#### firewalld端配置

1. 开启网卡转发模式

```bash
echo 'net.ipv4.ip_forward = 1' >>/etc/sysctl.conf
sysctl -p
```

2. 配置nat规则

```bash
iptables -t nat -A POSTROUTING -s 202.207.240.0/24 -j SNAT --to-source 192.168.72.201		# 适用于公网IP固定
iptables -t nat -A POSTROUTING -s 202.207.240.0/24 -j SNAT --to-source MASQUERADE	# 适用于公网IP不固定
```

<font color="red">**注意：FORWARD 链的默认规则必须是 ACCEPT**</font>

#### 内网主机配置

修改网卡配置文件，将网关指向防火墙的内网IP地址

<img src="https://s2.loli.net/2022/12/07/IgQjfa9kSX5l7A2.png" alt="image-20220717160706797" style="zoom:150%;" />

<img src="https://s2.loli.net/2022/12/07/HQpIOzeY63L9ofV.png" alt="image-20220717160816188" style="zoom:150%;" />

---

### 端口映射

案例：访问防火墙的9000端口，实际上访问的是202.207.240.133:22端口（即ssh登录202.207.240.136）

#### firewalld端配置

```bash
iptables -t nat -A PREROUTING -d 192.168.72.201 -p tcp --dport 9000 -j DNAT --to-destination 202.207.240.133:22

echo 'net.ipv4.ip_forward = 1' >>/etc/sysctl.conf
sysctl -p
```

![image-20220717161851847](https://s2.loli.net/2022/12/07/OEgAbRtTcFWqSHM.png)

<font color="red">**注意：FORWARD 链的默认规则必须是 ACCEPT**</font>

#### 客户端测试

```bash
ssh -p 9000 root@192.168.72.201
```

![image-20220717162746969](https://s2.loli.net/2022/12/07/hTtvAyqcdVmkYO1.png)

---

### IP映射

说明：与端口映射类似

#### firewalld端配置

1. 添加公网IP

```bash
ip addr add 192.168.72.150/24 dev ens33 label ens33:0
```

![image-20220717163256736](https://s2.loli.net/2022/12/07/Q8ltgMEhOnHeGkx.png)

2. 配置防火墙规则

```bash
iptables -t nat -A PREROUTING -d 192.168.72.150 -j DNAT --to-destination 202.207.240.133
```

![image-20220717163430507](https://s2.loli.net/2022/12/07/cjWMyg3xRnYvsTN.png)

#### 客户端测试

测试说明：通过ssh远程登录192.168.72.150,实际上登录的是内网主机202.207.240.133主机

![image-20220717163708697](https://s2.loli.net/2022/12/07/VLR7O6EDNYxiX8c.png)

