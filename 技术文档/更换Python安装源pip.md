# 更换Python安装源pip
- [更换Python安装源pip](#更换python安装源pip)
  - [CentOS7更换pip源](#centos7更换pip源)
  - [Win 10更换pip源](#win-10更换pip源)
## CentOS7更换pip源

1. 创建`.pip`目录

   ```bash
   mkdir -p ~/.pip
   ```

2. 创建`pip.conf`文件，并写入配置- [更换Python安装源pip](#更换python安装源pip)

   ```bash
   # 创建pip文件
   touch ~/.pip/pip.conf
   
   # 写入pip源
   cat > ~/.pip/pip.conf <<EOF
   [global]
   trusted-host = mirrors.aliyun.com
   index-url = http://mirrors.aliyun.com/pypi/simple/
   EOF
   ```

3. 使用`pip config list`命令查看`pip`源是否修改成功

![image-20221120134724282](https://s2.loli.net/2022/11/20/FkamoKgUxjXODYh.png)

## Win 10更换pip源

1. 按下`Win+R`，键入`%APPDATA%%`,回车

2. 创建`pip`文件夹

3. 创建`pip.ini`文件，并写入配置

   ```ini
   [global]
   index-url = http://mirrors.aliyun.com/pypi/simple/
   [install]
   trusted-host = mirrors.aliyun.com
   ```

4. 进入DOS命令窗口,使用`pip config list`命令查看`pip`源是否修改成功

![查看pip源](https://s2.loli.net/2022/11/19/a1vyClr5OSeW6Au.png)

