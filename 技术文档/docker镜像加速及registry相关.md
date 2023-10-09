# docker配置文件相关

​	安装完 docker 后， 创建 `/etc/docker/daemon.json` 配置文件

## 配置示例

```json
{
    "insecure-registries": ["172.21.32.15:5000"],
    "registry-mirrors": ["https://docker.mirrors.tuna.tsinghua.edu.cn"],
    "exec-opts": ["native.cgroupdriver=systemd"]
}
```

- `"insecure-registries"` 用来跳过私有仓库的HTTPS安全认证
- `"registry-mirrors"` 用来配置镜像加速



## 镜像加速

```json
{
    "registry-mirrors": ["https://docker.mirrors.tuna.tsinghua.edu.cn"]
}
```



## 仓库认证

### 1. 跳过认证

```json
{
    "insecure-registries": ["172.21.32.15:5000"]
}
```



## cgroup driver管理

默认管理方式是 cgroupfs，修改为 sytemd 管理

```json
{
    "exec-opts": ["native.cgroupdriver=systemd"]
}
```



