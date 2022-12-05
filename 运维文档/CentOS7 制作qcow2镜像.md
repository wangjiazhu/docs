# CentOS7 制作qcow2镜像
- [CentOS7 制作qcow2镜像](#centos7-制作qcow2镜像)
  - [准备条件](#准备条件)
  - [软件及镜像](#软件及镜像)
  - [安装VNC软件](#安装vnc软件)
  - [安装操作系统](#安装操作系统)
  - [制作镜像](#制作镜像)
## 准备条件

- Centos7.5虚拟机一台
- Centos7.5的DVD系统镜像 CentOS-7-x86-DVD-1804.iso

> **说明：**
>
> ​	本文通过CentOS7系统的KVM功能制作一个带有CentOS7.5系统的一个qcow2格式磁盘镜像，制作其他版的系统镜像可参考此教程。

## 软件及镜像

1. 将CentOS-7-x86-DVD-1804.iso上传至虚拟机/opt目录下

   ![image-20211108145309812](https://s2.loli.net/2022/12/05/4TsB7Z8AKrDC1Yb.png)

2. 安装KVM相关软件并启动相关服务

   ```bash
   yum install libvirt qemu-kvm virt-install -y
   
   systemctl enable libvirtd
   systemctl start libvirtd
   ```

3. 创建qcow2磁盘

   ```bash
   qemu-img create -f qcow2 /opt/centos7_GUI.qcow2 15G
   ```

   ![image-20211108145740233](https://s2.loli.net/2022/12/05/VB5HEXKbCiWJj8x.png)

4. 创建KVM虚拟机

   ```bash
   virt-install --name centos7_GUI --ram 2048 --disk /opt/centos7_GUI.qcow2,format=qcow2 --network network=default --graphics vnc,listen=0.0.0.0 --noautoconsole --os-type=linux --os-variant=centos7.0 --location=/opt/CentOS-7-x86_64-DVD-1804.iso
   ```

   ![image-20211108150218928](https://s2.loli.net/2022/12/05/Cdg45zQmbNISrpU.png)

5. 查看并启动KVM虚拟机

   ```bash
   virsh list --all
   virsh start centos7_GUI
   ```

   ![image-20211108150807164](https://s2.loli.net/2022/12/05/i7KkIhqFXNxbvCu.png)

6. 查看VNC连接端口

   ```bash
   netstat -antp|grep 5900
   ```

   ![image-20211108150941408](https://s2.loli.net/2022/12/05/Eq2ysDaOGHYmznM.png)

## 安装VNC软件

> **说明:**
>
> ​	经过上述步骤，我们创建了一个KVM虚拟机，并指定了该虚拟机的iso镜像。到现在为止，我们需要给该KVM虚拟机安装操作系统，安装后，通过qemu-img磁盘管理工具从该虚拟机磁盘文件进行创建一个新的带有操作系统的qcow2格式的磁盘文件就完成了镜像的制作。**但是完成这些工作需要安装VNC软件来完成.**

1. 在win10本机上安装VNC Viewer软件，用来连接KVM虚拟机

2. 在宿主机上安装VNC相关软件并初始化VNC服务

   ```bash
   yum install tightvnc-server tightvnc -y
   
   # 初始化vnc服务
   vncserver
   ```

   ![image-20211108151813701](https://s2.loli.net/2022/12/05/Vb8gJyr6d5H1GoZ.png)

3. 配置VNC相关systemd服务管理文件

   ```bash
   cp /usr/lib/systemd/system/vncserver\@.service{,.bak}
   echo "">/usr/lib/systemd/system/vncserver\@.service
   
   cat >/usr/lib/systemd/system/vncserver\@.service <<EOF
   [Unit]
   Description=Remote desktop service (VNC)
   After=syslog.target network.target
   
   [Service]
   Type=simple
   User=root
   
   # Clean any existing files in /tmp/.X11-unix environment
   ExecStartPre=-/usr/bin/vncserver -kill %i
   ExecStart=/usr/bin/vncserver %i
   PIDFile=/root/.vnc/%H%i.pid
   ExecStop=-/usr/bin/vncserver -kill %i
   
   [Install]
   WantedBy=multi-user.target
   EOF
   ```

   ![image-20211108152350973](https://s2.loli.net/2022/12/05/9DEWuHhFVtMawzQ.png)

4. 重启VNC服务

   ```bash
   systemctl daemon-reload
   systemctl restart vncserver@:1.service
   ```

   ![image-20211108152532322](https://s2.loli.net/2022/12/05/j4Sz2dxHRYNal6F.png)

## 安装操作系统

> **说明:**
>
> ​	经过以上VNC软件的安装步骤,我们接下来就可以通过VNC软件来安装操作系统了．

1. win10系统通过VNC Viewer软件连接KVM虚拟机,完成操作系统安装

   > 宿主机IP地址后面加上  :0  表示连接的是第一台KVM虚拟机；类似的  :1 表示第二台KVM虚拟机

   ![image-20211108152917468](https://s2.loli.net/2022/12/05/Fh6XtaYnkvEDZg1.png)

   ![image-20211108153009195](https://s2.loli.net/2022/12/05/RWkQNHPix9DC71E.png)

   > **说明:**
   >
   > - 完成操作系统安装后,按照操作reboot操作系统,此时通过命令`virsh list --all`查看KVM虚拟机发现是`shut off`状态.
   > -  通过`virsh start`命令重新启动该KVM虚拟机，并再次通过VNC　Viewer软件连接.

2. 进入KVM虚拟机,进行相关配置

   > - 关闭防火墙、SElinux等安全组件
   > - 设置网络服务为dhcp
   > - 其他相关组件的安装等(vim工具、yum源等)

## 制作镜像

> **说明:**
>
> ​		经过以上步骤,我们通过KVM工具完成的创建了一个带有操作系统的KVM虚拟机,接下来就可以通过qemu-img工具来创建带有操作系统的qcow2格式的镜像了.

1. 查看KVM虚拟机的磁盘文件属性
   ![image-20211108161654273](https://s2.loli.net/2022/12/05/TxLy37evStrYUh9.png)

2. 制作qcow2格式镜像

   > **说明:**
   >
   > ​		其中,`centos7_GUI.qcow2`为原来KVM虚拟机的磁盘文件,`centos7_GUI_convt.qcow2`为新创建的qcow2磁盘镜像文件,可通过`centos7_GUI_convt.qcow2`来创建新的KVM虚拟机或者用于OpenStack的镜像服务,用来创建OpenStack实例.
   >
   > ​		qcow2具有压缩的功能,创建后发现新镜像的实际大小比原虚拟机磁盘文件比较较小.

   ```bash
   qemu-img convert -c -O qcow2 centos7_GUI.qcow2 centos7_GUI_convt.qcow2
   ```

   ![image-20211108171351327](https://s2.loli.net/2022/12/05/ZFvCoDIsUzBW8Ay.png)