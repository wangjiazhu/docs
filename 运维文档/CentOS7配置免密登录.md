# CentOS7配置免密登录

## 使用免密脚本配置免密登录

1. 执行以下两条之一：

- **脚本1**

```bash
wget https://gitee.com/wangjiazhu/packages/raw/main/secret-free-login.sh && sh secret-free-login.sh
```

- **脚本2**

```bash
wget https://raw.githubusercontent.com/wangjiazhu/packages/main/secret-free-login.sh && sh secret-free-login.sh
```

2. 输入需要被免密登录的远程主机IP及其root密码

   ​		假设被免密登录的远程主机IP为`192.168.72.10`,root密码为`111111`则执行脚本后按提示输入如下：

![image-20230521211747072](https://s2.loli.net/2023/05/21/ey6TD8otm4PVswh.png)

3. 测试免密登录

