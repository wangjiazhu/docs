# CentOS7安装Anaconda及创建Python虚拟环境

## 脚本安装方式

1. 执行以下两个脚本命令之一，完成Anaconda安装

   - 脚本1

     ```bash
     wget https://gitee.com/wangjiazhu/packages/raw/main/python_env_install.sh && sh python_env_install.sh
     ```

     

   - 脚本2

     ```bash
     wget https://raw.githubusercontent.com/wangjiazhu/packages/main/python_env_install.sh && sh python_env_install.sh
     ```

     

2. 创建Python环境

   以上脚本完成Anaconda安装后，会询问是否创建python环境以及python版本，如下如所示：

   ![image-20230522223247091](https://s2.loli.net/2023/05/22/GLFHk4QyXUW8Jvz.png)

   ​		经过以上输入，python环境创建成功，之后系统会重启，重启成功后会自动进入所创建的虚拟环境中。

   