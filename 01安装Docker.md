## 安装Docker

>   环境准备

1.  需要会一点点的Linux基础
2.  CentOS7
3.  使用xshell连接远程服务器

### 环境查看

```
# 系统内核是3.10以上
[root@node5 ~]# uname -r
3.10.0-1127.19.1.el7.centos.plus.x86_64
```

```shell
# 系统版本
[root@node5 ~]# hostnamectl
   Static hostname: node5.lixm.cc
         Icon name: computer-vm
           Chassis: vm
        Machine ID: b9df7dfd86964dd5947ee00460b0c00e
           Boot ID: 817869c5e03740e6acaa7cd592993e1a
    Virtualization: vmware
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-1127.19.1.el7.centos.plus.x86_64
      Architecture: x86-64
[root@node5 ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

### 安装

```shell
# 使用阿里云镜像
rm -f /etc/yum.repos.d/*
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

# 1、卸载旧的docker
yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine

# 2、安装必要的系统工具
yum -y install yum-utils device-mapper-persistent-data lvm2

# 3、设置软件源信息
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 4、更新并安装Docker-CE, Docker-CE社区版、Docker-EE企业版
yum makecache fast
yum -y install docker-ce docker-ce-cli containerd.io

# 5、开启Docker服务
systemctl start docker
systemctl enable docker

# 6、查询docker状态
[root@node5 ~]# docker version
Client:
 Version:         1.13.1
 API version:     1.26
 Package version: docker-1.13.1-162.git64e9980.el7.centos.x86_64
 Go version:      go1.10.3
 Git commit:      64e9980/1.13.1
 Built:           Wed Jul  1 14:56:42 2020
 OS/Arch:         linux/amd64

Server:
 Version:         1.13.1
 API version:     1.26 (minimum version 1.12)
 Package version: docker-1.13.1-162.git64e9980.el7.centos.x86_64
 Go version:      go1.10.3
 Git commit:      64e9980/1.13.1
 Built:           Wed Jul  1 14:56:42 2020
 OS/Arch:         linux/amd64
 Experimental:    false

# 7、运行测试程序
[root@node5 ~]# docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

# 8、查看hello-world这个镜像文件
[root@node5 ~]# docker images
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
docker.io/hello-world        latest              bf756fb1ae65        9 months ago        13.3 kB

# 9、卸载docker
yum remove docker-ce docker-ce-cli containerd.io
# 清理images、containers、volumns及自定义的配置文件
rm -rf /var/lib/docker

```

### 设置阿里云镜像加速

```shell
# 登录阿里云后台，找到容器镜像服务后开通，在说明里找到自己的加速地址
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://oxv7blf7.mirror.aliyuncs.com"]
}
EOF
systemctl daemon-reload
systemctl restart docker
```

