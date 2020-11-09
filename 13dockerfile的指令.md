### DockerFile的指令

```
FROM 		#基础镜像，一切从这里开始构建
MAINTAINER	# 镜像是谁写的，姓名+邮箱
RUN			# 镜像构建的时候需要运行的命令
ADD			# 步骤: tomcat容器，添加tomcat的压缩包，用于添加内容
WORKDIR		# 镜像的工作目录
VOLUME		# 挂载的目录
EXPOSE		# 指定暴露给外部的端口
CMD			# 指定容器启动的时候要运行命令,只有最后一个会生效，可被替代
ENTRYPOINT	# 指定容器启动的时候要运行命令，可以追加命令
ONBUILD		# 当构建一个被继承的Dockerfile这个时候就会运行ONBUILD的指令。触发指令。
COPY		# 类似ADD，将文件拷贝到镜像中。
ENV			# 构建的时候设置环境变量。
```

构建自己的CentOS操作系统

```shell
FROM centos
MAINTAINER li xueming<lixm@lixm.cc>
ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum makecache
RUN yum -y install vim net-tools
RUN yum clean all

EXPOSE 80

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash
```

```shell
# docker build构建过程
[root@node5 docker]# docker build -f ./dockerfile-mycentos -t mycentos:0.1 .
Sending build context to Docker daemon 3.072 kB
Step 1/11 : FROM centos
 ---> 0d120b6ccaa8
Step 2/11 : MAINTAINER li xueming<lixm@lixm.cc>
 ---> Running in 208ddaf62503
 ---> 7401f74a1c35
Removing intermediate container 208ddaf62503
Step 3/11 : ENV MYPATH /usr/local
 ---> Running in 9668c03d43d2
 ---> fc5254fad4c6
Removing intermediate container 9668c03d43d2
Step 4/11 : WORKDIR $MYPATH
 ---> 4ceec580777d
Removing intermediate container 2f9550755b1a
Step 5/11 : RUN yum makecache
 ---> Running in 2f3c2a8edbde

CentOS-8 - AppStream                            425 kB/s | 5.8 MB     00:13    
CentOS-8 - Base                                 1.0 MB/s | 2.2 MB     00:02    
CentOS-8 - Extras                                19 kB/s | 8.1 kB     00:00    
Metadata cache created.
 ---> b75eda8ff084
Removing intermediate container 2f3c2a8edbde
Step 6/11 : RUN yum -y install vim net-tools
 ---> Running in db324a36dcc8

Last metadata expiration check: 0:00:02 ago on Fri Oct 23 06:05:24 2020.
Dependencies resolved.
================================================================================
 Package            Arch       Version                      Repository     Size
================================================================================
Installing:
 net-tools          x86_64     2.0-0.51.20160912git.el8     BaseOS        323 k
 vim-enhanced       x86_64     2:8.0.1763-13.el8            AppStream     1.4 M
Installing dependencies:
 gpm-libs           x86_64     1.20.7-15.el8                AppStream      39 k
 vim-common         x86_64     2:8.0.1763-13.el8            AppStream     6.3 M
 vim-filesystem     noarch     2:8.0.1763-13.el8            AppStream      48 k
 which              x86_64     2.21-12.el8                  BaseOS         49 k

Transaction Summary
================================================================================
Install  6 Packages

Total download size: 8.1 M
Installed size: 32 M
Downloading Packages:
(1/6): gpm-libs-1.20.7-15.el8.x86_64.rpm        138 kB/s |  39 kB     00:00    
(2/6): vim-filesystem-8.0.1763-13.el8.noarch.rp 432 kB/s |  48 kB     00:00    
(3/6): net-tools-2.0-0.51.20160912git.el8.x86_6 734 kB/s | 323 kB     00:00    
(4/6): which-2.21-12.el8.x86_64.rpm             827 kB/s |  49 kB     00:00    
(5/6): vim-enhanced-8.0.1763-13.el8.x86_64.rpm  773 kB/s | 1.4 MB     00:01    
(6/6): vim-common-8.0.1763-13.el8.x86_64.rpm    277 kB/s | 6.3 MB     00:23    
--------------------------------------------------------------------------------
Total                                           343 kB/s | 8.1 MB     00:24     
warning: /var/cache/dnf/AppStream-02e86d1c976ab532/packages/gpm-libs-1.20.7-15.el8.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 8483c65d: NOKEY
CentOS-8 - AppStream                            1.6 MB/s | 1.6 kB     00:00    
Importing GPG key 0x8483C65D:
 Userid     : "CentOS (CentOS Official Signing Key) <security@centos.org>"
 Fingerprint: 99DB 70FA E1D7 CE22 7FB6 4882 05B5 55B3 8483 C65D
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : which-2.21-12.el8.x86_64                               1/6 
  Installing       : vim-filesystem-2:8.0.1763-13.el8.noarch                2/6 
  Installing       : vim-common-2:8.0.1763-13.el8.x86_64                    3/6 
  Installing       : gpm-libs-1.20.7-15.el8.x86_64                          4/6 
  Running scriptlet: gpm-libs-1.20.7-15.el8.x86_64                          4/6 
  Installing       : vim-enhanced-2:8.0.1763-13.el8.x86_64                  5/6 
  Installing       : net-tools-2.0-0.51.20160912git.el8.x86_64              6/6 
  Running scriptlet: net-tools-2.0-0.51.20160912git.el8.x86_64              6/6 
  Running scriptlet: vim-common-2:8.0.1763-13.el8.x86_64                    6/6 
  Verifying        : gpm-libs-1.20.7-15.el8.x86_64                          1/6 
  Verifying        : vim-common-2:8.0.1763-13.el8.x86_64                    2/6 
  Verifying        : vim-enhanced-2:8.0.1763-13.el8.x86_64                  3/6 
  Verifying        : vim-filesystem-2:8.0.1763-13.el8.noarch                4/6 
  Verifying        : net-tools-2.0-0.51.20160912git.el8.x86_64              5/6 
  Verifying        : which-2.21-12.el8.x86_64                               6/6 

Installed:
  gpm-libs-1.20.7-15.el8.x86_64                                                 
  net-tools-2.0-0.51.20160912git.el8.x86_64                                     
  vim-common-2:8.0.1763-13.el8.x86_64                                           
  vim-enhanced-2:8.0.1763-13.el8.x86_64                                         
  vim-filesystem-2:8.0.1763-13.el8.noarch                                       
  which-2.21-12.el8.x86_64                                                      

Complete!
 ---> c142ded3643f
Removing intermediate container db324a36dcc8
Step 7/11 : RUN yum clean all
 ---> Running in 62aef93b82b5

20 files removed
 ---> 6f587cec5a45
Removing intermediate container 62aef93b82b5
Step 8/11 : EXPOSE 80
 ---> Running in 71f5342cb705
 ---> ca965c1e1281
Removing intermediate container 71f5342cb705
Step 9/11 : CMD echo $MYPATH
 ---> Running in 5ea2aca7f7e3
 ---> 109c4f2e306f
Removing intermediate container 5ea2aca7f7e3
Step 10/11 : CMD echo "----end----"
 ---> Running in f7f003521130
 ---> 49ba56035e76
Removing intermediate container f7f003521130
Step 11/11 : CMD /bin/bash
 ---> Running in ab51fe8ad0d2
 ---> 233fd0fa8fc9
Removing intermediate container ab51fe8ad0d2
Successfully built 233fd0fa8fc9
```

```shell
# 测试运行
[root@node5 docker]# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED              SIZE
mycentos                        0.1                 233fd0fa8fc9        About a minute ago   284 MB

# 运行镜像文件
[root@node5 docker]# docker run -it mycentos:0.1 /bin/bash
[root@a2050f0a4ae9 local]#

[root@a2050f0a4ae9 local]# ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
77: eth0@if78: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.3/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:3/64 scope link 
       valid_lft forever preferred_lft forever
[root@a2050f0a4ae9 local]# ip route show
default via 172.17.0.1 dev eth0 
172.17.0.0/16 dev eth0 proto kernel scope link src 172.17.0.3 
[root@a2050f0a4ae9 local]# ping www.baidu.com -c 4
PING www.a.shifen.com (180.101.49.11) 56(84) bytes of data.
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=1 ttl=52 time=8.60 ms
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=2 ttl=52 time=8.77 ms
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=3 ttl=52 time=12.5 ms
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=4 ttl=52 time=8.55 ms

--- www.a.shifen.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 7ms
rtt min/avg/max/mdev = 8.550/9.598/12.475/1.664 ms
```

## 列出本地镜像的变更历史docker history

```shell
[root@node5 docker]# docker history 233fd0fa8fc9
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
233fd0fa8fc9        7 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/b...   0 B                 
49ba56035e76        7 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "ec...   0 B                 
109c4f2e306f        7 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "ec...   0 B                 
ca965c1e1281        7 minutes ago       /bin/sh -c #(nop)  EXPOSE 80/tcp                0 B                 
6f587cec5a45        7 minutes ago       /bin/sh -c yum clean all                        1.74 MB             
c142ded3643f        7 minutes ago       /bin/sh -c yum -y install vim net-tools         48.9 MB             
b75eda8ff084        8 minutes ago       /bin/sh -c yum makecache                        18.2 MB             
4ceec580777d        8 minutes ago       /bin/sh -c #(nop) WORKDIR /usr/local            0 B                 
fc5254fad4c6        8 minutes ago       /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0 B                 
7401f74a1c35        8 minutes ago       /bin/sh -c #(nop)  MAINTAINER li xueming<l...   0 B                 
0d120b6ccaa8        2 months ago        /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B                 
<missing>           2 months ago        /bin/sh -c #(nop)  LABEL org.label-schema....   0 B                 
<missing>           2 months ago        /bin/sh -c #(nop) ADD file:538afc0c5c964ce...   215 MB              
[root@node5 docker]# docker history 891fcd9c5b3a
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
891fcd9c5b3a        8 days ago          /bin/sh -c #(nop)  CMD ["catalina.sh" "run"]    0 B                 
<missing>           8 days ago          /bin/sh -c #(nop)  EXPOSE 8080                  0 B                 
<missing>           8 days ago          /bin/sh -c set -e  && nativeLines="$(catal...   0 B                 
<missing>           8 days ago          /bin/sh -c set -eux;   savedAptMark="$(apt...   20 MB               
<missing>           8 days ago          /bin/sh -c #(nop)  ENV TOMCAT_SHA512=307ca...   0 B                 
<missing>           8 days ago          /bin/sh -c #(nop)  ENV TOMCAT_VERSION=9.0.39    0 B                 
<missing>           8 days ago          /bin/sh -c #(nop)  ENV TOMCAT_MAJOR=9           0 B                 
<missing>           8 days ago          /bin/sh -c #(nop)  ENV GPG_KEYS=05AB331109...   0 B                 
<missing>           8 days ago          /bin/sh -c #(nop)  ENV LD_LIBRARY_PATH=/us...   0 B                 
<missing>           8 days ago          /bin/sh -c #(nop)  ENV TOMCAT_NATIVE_LIBDI...   0 B                 
<missing>           8 days ago          /bin/sh -c #(nop) WORKDIR /usr/local/tomcat     0 B                 
<missing>           8 days ago          /bin/sh -c mkdir -p "$CATALINA_HOME"            0 B                 
<missing>           8 days ago          /bin/sh -c #(nop)  ENV PATH=/usr/local/tom...   0 B                 
<missing>           8 days ago          /bin/sh -c #(nop)  ENV CATALINA_HOME=/usr/...   0 B                 
<missing>           9 days ago          /bin/sh -c #(nop)  CMD ["jshell"]               0 B                 
<missing>           9 days ago          /bin/sh -c set -eux;   arch="$(dpkg --prin...   323 MB              
<missing>           9 days ago          /bin/sh -c #(nop)  ENV JAVA_VERSION=11.0.8      0 B                 
<missing>           9 days ago          /bin/sh -c { echo '#/bin/sh'; echo 'echo "...   27 B                
<missing>           9 days ago          /bin/sh -c #(nop)  ENV PATH=/usr/local/ope...   0 B                 
<missing>           9 days ago          /bin/sh -c #(nop)  ENV JAVA_HOME=/usr/loca...   0 B                 
<missing>           9 days ago          /bin/sh -c #(nop)  ENV LANG=C.UTF-8             0 B                 
<missing>           9 days ago          /bin/sh -c set -eux;  apt-get update;  apt...   11.1 MB             
<missing>           10 days ago         /bin/sh -c apt-get update && apt-get insta...   146 MB              
<missing>           10 days ago         /bin/sh -c set -ex;  if ! command -v gpg >...   17.5 MB             
<missing>           10 days ago         /bin/sh -c apt-get update && apt-get insta...   16.5 MB             
<missing>           10 days ago         /bin/sh -c #(nop)  CMD ["bash"]                 0 B                 
<missing>           10 days ago         /bin/sh -c #(nop) ADD file:6627ad39ea0cb9f...   114 MB              
```

### CMD和ENTRYPOINT的区别

```shell
CMD				# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT		# 指定这个容器启动的时候要运行的命令，可以追加命令
```

```
FROM centos
CMD ["ls", "-a"]
```

```shell
[root@node5 docker]# docker build -f dockerfile-cmd-test -t cmdtest:0.1 .
Sending build context to Docker daemon 4.096 kB
Step 1/2 : FROM centos
 ---> 0d120b6ccaa8
Step 2/2 : CMD ls -a
 ---> Running in 16b8cf46ff0a
 ---> 3ac69e32e29a
Removing intermediate container 16b8cf46ff0a
Successfully built 3ac69e32e29a
root@node5 docker]# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
cmdtest                         0.1                 3ac69e32e29a        4 seconds ago       215 MB
# build成功后直接执行，发现我们的ls -a命令生效了
[root@node5 docker]# docker run 3ac69e32e29a
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

#想追加一个命令参数-l，执行ls -al会提示错误
[root@node5 docker]# docker run 3ac69e32e29a -l
container_linux.go:235: starting container process caused "exec: \"-l\": executable file not found in $PATH"
/usr/bin/docker-current: Error response from daemon: oci runtime error: container_linux.go:235: starting container process caused "exec: \"-l\": executable file not found in $PATH".
ERRO[0000] error getting events from daemon: net/http: request canceled 

#只能添加全部命令行
[root@node5 docker]# docker run 3ac69e32e29a ls -al
total 0
drwxr-xr-x   1 root root  17 Oct 23 06:29 .
drwxr-xr-x   1 root root  17 Oct 23 06:29 ..
drwxrwxrwt   7 root root 145 Aug  9 21:40 tmp
drwxr-xr-x  12 root root 144 Aug  9 21:40 usr
drwxr-xr-x  20 root root 262 Aug  9 21:40 var
```

### 测试ENTRYPOINT

```
FROM centos
ENTRYPOINT ["ls","-a"]
```

```shell
[root@node5 docker]# docker build -f dockerfile-cmd-entrypoint -t entrypointtest:0.1 .
Sending build context to Docker daemon  5.12 kB
Step 1/2 : FROM centos
 ---> 0d120b6ccaa8
Step 2/2 : ENTRYPOINT ls -a
 ---> Running in 724f71026d33
 ---> 3af405467b8b
Removing intermediate container 724f71026d33
Successfully built 3af405467b8b
[root@node5 docker]# docker run 3af405467b8b
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
[root@node5 docker]# docker run 3af405467b8b -l
total 0
drwxr-xr-x   1 root root  17 Oct 23 06:34 .
drwxr-xr-x   1 root root  17 Oct 23 06:34 ..
-rwxr-xr-x   1 root root   0 Oct 23 06:34 .dockerenv
lrwxrwxrwx   1 root root   7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root 340 Oct 23 06:34 dev
drwxr-xr-x   1 root root  66 Oct 23 06:34 etc
drwxr-xr-x   2 root root   6 May 11  2019 home
lrwxrwxrwx   1 root root   7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root   9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root   6 Aug  9 21:40 lost+found
drwxr-xr-x   2 root root   6 May 11  2019 media
drwxr-xr-x   2 root root   6 May 11  2019 mnt
drwxr-xr-x   2 root root   6 May 11  2019 opt
dr-xr-xr-x 260 root root   0 Oct 23 06:34 proc
dr-xr-x---   2 root root 162 Aug  9 21:40 root
drwxr-xr-x   1 root root  21 Oct 23 06:34 run
lrwxrwxrwx   1 root root   8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root   6 May 11  2019 srv
dr-xr-xr-x  13 root root   0 Oct 22 09:23 sys
drwxrwxrwt   7 root root 145 Aug  9 21:40 tmp
drwxr-xr-x  12 root root 144 Aug  9 21:40 usr
drwxr-xr-x  20 root root 262 Aug  9 21:40 var
```



DockerFile中很多命令都十分的相似，我们需要了解它们的区别，我们最好的学习就是对比它他们然后测试效果！

















