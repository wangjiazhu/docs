# CentOS7部署DHCP服务

## 脚本部署DHCP服务

1. 选择以下两个脚本命令之一执行

   - 命令1

     ```bash
     wget https://gitee.com/wangjiazhu/packages/raw/main/nfs-deployment.sh && sh dhcp-deployment.sh
     ```

     

   - 命令2

     ```bash
     wget https://raw.githubusercontent.com/wangjiazhu/packages/main/nfs-deployment.sh && sh dhcp-deployment.sh
     ```

     

2. 按照输入提示输入DHCP子网、子网掩码、DHCP的IP地址范围、默认的DNS、路由以及广播地址

   ![image-20230525220016465](https://s2.loli.net/2023/05/25/WztCUXdG5YFA3Jc.png)

   经过上述配置，成功部署了DHCP服务器，以下是配置文件内容：

   ![image-20230525220315238](https://s2.loli.net/2023/05/25/JeHgzANaKQqRy7L.png)

   观察发现配置文件中所涉及的子网、掩码、IP地址范围等信息与部署过程中输入的信息一致。

3. 启动一台客户机，配置其IP地址获取方式为DHCP方式，查看获取的IP地址在上述DHCP服务器所提供的IP地址范围。

   ![image-20230525220825525](https://s2.loli.net/2023/05/25/21vBEDuWjyRHSc9.png)

