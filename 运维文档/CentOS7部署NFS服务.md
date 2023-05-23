# CentOS7部署NFS服务

## 脚本部署NFS服务端

1. 选择以下两个脚本命令执行

   - 命令1

     ```bash
     wget https://gitee.com/wangjiazhu/packages/raw/main/nfs-deployment.sh && sh nfs-deployment.sh
     ```

     

   - 命令2

     ```bash
     wget https://raw.githubusercontent.com/wangjiazhu/packages/main/nfs-deployment.sh && sh nfs-deployment.sh
     ```

     

2. 按照部署方案输入序号 0

   ![image-20230523154258418](https://s2.loli.net/2023/05/23/ex5COdmqXiYzRl3.png)

3. 按照提示输入NFS服务端的IP地址

   ![image-20230523154512353](https://s2.loli.net/2023/05/23/8M4TtshUI7eKvSr.png)

4. 设置NFS服务共享目录、允许客户端挂载的IP地址范围、是否限定特定用户挂载以及限定特定用户挂载后的用户组名称和用户组ID

   ![image-20230523155445712](https://s2.loli.net/2023/05/23/VLkI5gePUmRy4Jr.png)

经过以上设置，NFS服务端就部署完毕！

## 脚本部署NFS客户端

1. 选择以下两个脚本命令执行

   - 命令1

     ```bash
     wget https://gitee.com/wangjiazhu/packages/raw/main/nfs-deployment.sh && sh nfs-deployment.sh
     ```

     

   - 命令2

     ```bash
     wget https://raw.githubusercontent.com/wangjiazhu/packages/main/nfs-deployment.sh && sh nfs-deployment.sh
     ```

     

2. 按照部署方案输入序号 1

   ![image-20230523155730308](https://s2.loli.net/2023/05/23/ERQiU63GwYoA8Ws.png)

3. 按照提示输入部署完毕的NFS服务端IP地址

   ![image-20230523155913025](https://s2.loli.net/2023/05/23/9h1l3kEMAxKPDOz.png)

4. 判断服务端是否限制了客户端使用特定用户登录以及限定后需要创建同服务端名一致的用户组名和组ID

   ![image-20230523160524555](https://s2.loli.net/2023/05/23/OkA7zncsCYa1Ro2.png)

5. 是否需要挂载服务端共享目录、若挂载则输入挂载目录以及是否需要进行开机挂载设置

   ![image-20230523161012079](https://s2.loli.net/2023/05/23/JG4CpdHr3ycvOzU.png)

6. 检查客户端是否挂载成功

   ![image-20230523161112518](https://s2.loli.net/2023/05/23/HEZILghQVoj3AnX.png)
