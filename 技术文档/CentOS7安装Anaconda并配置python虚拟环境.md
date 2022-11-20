# CentOS7安装Anaconda并配置python虚拟环境
- [CentOS7安装Anaconda并配置python虚拟环境](#centos7安装anaconda并配置python虚拟环境)
  - [下载](#下载)
  - [安装、配置](#安装配置)
    - [安装](#安装)
    - [配置](#配置)
  - [anaconda管理虚拟环境](#anaconda管理虚拟环境)

## 下载

下载地址：https://mirrors.aliyun.com/anaconda/

![image-20221016135813495](https://s2.loli.net/2022/11/20/65YAsnWkBtxdKhS.png)

## 安装、配置

### 安装

- 准备

  - 创建目录

    ```bash
    mkdir -p /application
    mkdir -p /server/scripts
    ```

    

  - 将下载的`Anaconda3-2020.07-Linux-x86_64.sh`上传到`/server/scripts`目录

- 安装

  进入`Anaconda3-2020.07-Linux-x86_64.sh`所在目录，执行安装

  ```bash
  # 1.执行脚本
  bash Anaconda3-2020.11-Linux-x86_64.sh
  
  # 2.键入ENTER关键字
  Welcome to Anaconda3 2020.11
  
  In order to continue the installation process, please review the license
  agreement.
  Please, press ENTER to continue
  >>> ENTER
  
  # 3.键入yes接受许可
  Do you accept the license terms? [yes|no]
  [no] >>> yes
  
  # 4.指定anaconda3的安装目录。【默认安装在/root/anaconda3】
  Anaconda3 will now be installed into this location:
  /root/anaconda3
  
    - Press ENTER to confirm the location
    - Press CTRL-C to abort the installation
    - Or specify a different location below
  
  [/root/anaconda3] >>> /application/anaconda3
  
  # 5.键入yes初始化anaconda
  Preparing transaction: done
  Executing transaction: done
  installation finished.
  Do you wish the installer to initialize Anaconda3
  by running conda init? [yes|no]
  [no] >>> yes
  ```

  经过以上步骤，最终显示如下图所示表示安装成功

  ![image-20221016164945780](https://s2.loli.net/2022/11/20/sb1fTvKocQklRJN.png)

### 配置

​	执行`source ~/.bashrc`命令，可以使用`conda`命令

- 设置搜索时显示通道地址

  ```bash
  # 设置搜索时显示i通道地址，如果配置文件~/.condarc文件不存在，此命令会生成该文件
  conda config --set show_channel_urls yes
  
  # 清除索引缓存
  conda clean -i
  ```
  
- 添加 Anaconda Python 免费仓库

  ```bash
  cat >~/.condarc <<EOF
  channels:
    - defaults
  show_channel_urls: true
  default_channels:
    - http://mirrors.aliyun.com/anaconda/pkgs/main
    - http://mirrors.aliyun.com/anaconda/pkgs/r
    - http://mirrors.aliyun.com/anaconda/pkgs/msys2
  custom_channels:
    conda-forge: http://mirrors.aliyun.com/anaconda/cloud
    msys2: http://mirrors.aliyun.com/anaconda/cloud
    bioconda: http://mirrors.aliyun.com/anaconda/cloud
    menpo: http://mirrors.aliyun.com/anaconda/cloud
    pytorch: http://mirrors.aliyun.com/anaconda/cloud
    simpleitk: http://mirrors.aliyun.com/anaconda/cloud
  EOF
  ```

## anaconda管理虚拟环境

- 创建python虚拟环境

  ```bash
  source ~/.bashrc
  
  # 创建解释器为python3.7的虚拟环境python37_env
  conda create --name python37_env python=3.7
  ```

  ![image-20221016171727421](https://s2.loli.net/2022/11/20/iWN71Z5zjsyJ2OC.png)

- 查看所有虚拟环境

  ```bash
  conda env list
  ```

  ![image-20221016172346601](https://s2.loli.net/2022/11/20/uh9KDEjQCkeJmZa.png)

- 激活虚拟环境

  ```bash
  conda activate python37_env
  ```

- 退出虚拟环境

  ```bash
  conda deactivate
  ```

- 删除虚拟环境

  ```bash
  conda remove --name python37_env --all
  ```

- 导出虚拟环境配置

  ```bash
  conda env export >/root/python37_env.yml
  ```

- 从导出文件创建虚拟环境

  ```bash
  conda env create -f /root/python37_env.yml
  ```

- 克隆虚拟环境

  ```bash
  conda create --name clone_python37_env --clone python37_env
  ```

**<font color='red'>注意：</font>**

> `conda env export >/root/python37_env.yml` 和 `conda env create -f /root/python37_env.yml`  命令可以实现在其他主机通过配置文件还原出原来的虚拟环境，但是仍然需要以下注意点：
>
> - 其他主机必须以参考本主机anaconda的安装，最好安装方式一样（如果安装位置不一致，需要改一下导出文件中 prefix 的路径）
> - anaconda安装好以后，相关源的配置与原主机的保持一样
> - 配置默认的**pip源**应该与原主机保持一致

- 构建相同的conda环境

  - 激活需要导出配置的虚拟环境

    > conda activate python37_env

  - 导出该虚拟环境的conda环境

    > conda list --explicit >/root/files.txt

  - 在其他机器上执行来创建虚拟环境

    >  conda create --name python37_env --file /root/files.txt