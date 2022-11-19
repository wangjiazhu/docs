# win10安装Anaconda并配置python虚拟环境
- [win10安装Anaconda并配置python虚拟环境](#win10安装anaconda并配置python虚拟环境)
  - [下载](#下载)
  - [安装](#安装)
  - [配置](#配置)
  - [pycharm使用anaconda虚拟环境](#pycharm使用anaconda虚拟环境)

## 下载

https://repo.anaconda.com/archive/Anaconda3-2022.10-Windows-x86_64.exe

## 安装

- 安装步骤

![](https://s2.loli.net/2022/11/19/RCcGI4nVFyAKZTY.png)

![](https://s2.loli.net/2022/11/19/OekxINCXZwdsgaJ.png)

![image-20221119195030455](https://s2.loli.net/2022/11/19/BG7zKar5NRdtblx.png)

![image-20221119195321188](https://s2.loli.net/2022/11/19/n9kmE5J2DASHZjc.png)

![image-20221119195408725](https://s2.loli.net/2022/11/19/MinfHhtx4O6KNjc.png)

![image-20221119195535589](https://s2.loli.net/2022/11/19/baeYRfd9uCFcxPi.png)

<img src="https://s2.loli.net/2022/11/19/lqdKUmfuE1gHrY3.png" style="zoom:80%;" />

![image-20221119200519074](https://s2.loli.net/2022/11/19/NeOB5xmU7dTJwuI.png)

- 配置环境变量

此电脑->属性->高级系统设置->高级->环境变量->系统变量(Path)->编辑->新建(Anaconda的安装路径和脚本路径)

![image-20221119201553768](https://s2.loli.net/2022/11/19/piDzj5E1A2TNCbB.png)

- 验证anaconda安装成功与否

win+r====>cmd====>`conda -V`

![image-20221119203254847](https://s2.loli.net/2022/11/19/WL1pmFrscOenRoE.png)

## 配置

- 查看默认虚拟环境安装路径

  输入命令 `conda config --show`，查看`envs_dirs`选项
  **注意：如果需要修改虚拟环境安装路径，参考博客 ：https://blog.csdn.net/weixin_44768070/article/details/126682896**

  ![image-20221119204005979](https://s2.loli.net/2022/11/19/4r1TOHUlxuQF9n7.png)

- 配置conda安装源

  1. 执行命令`conda config --set show_channel_urls yes` 生成``.condarc`文件
  2. win+r====>`%HOMEPATH%`====>编辑`.condarc`文件

  ```txt
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
  ```

- 设置默认pip安装源

  1. win+r====>`%APPDATA%`

  2. 新建`pip`文件夹

  3. 在`pip`文件夹下创建`pip.ini`文件,内容如下

     ```txt
     [global]
     index-url = http://mirrors.aliyun.com/pypi/simple/
     [install]
     trusted-host = mirrors.aliyun.com
     ```

  4. `pip config list`

     ![image-20221119212140669](https://s2.loli.net/2022/11/19/a1vyClr5OSeW6Au.png)

- 创建python3虚拟环境

  **注意：anaconda有关虚拟环境其他操作见：https://blog.csdn.net/weixin_51720652/article/details/127354488**

  > conda create env 虚拟环境名称 python=版本
  >
  > eg: 创建一个python3.7的虚拟环境

## pycharm使用anaconda虚拟环境

![image-20221119213031898](https://s2.loli.net/2022/11/19/kUfPYs7rli12eAj.png)

![image-20221119213345411](https://s2.loli.net/2022/11/19/m1uL9ygWVGBv3ix.png)