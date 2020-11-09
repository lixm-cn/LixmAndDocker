## 制作Tomcat镜像

1、准备镜像文件tomcat压缩包，jdk的压缩包！

2、编写dockerfile文件，官方命令Dockerfile，build时会自动寻找这个文件

```dockerfile
FROM centos
MAINTAINER lixueming<lixm@lixm.cc>

COPY readme.txt /usr/local/readme.txt
ADD jdk-8u11-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.22.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_11
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.22
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.22
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.22/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.22/bin/logs/catalina.out
```

3、构建docker镜像

```shell
docker build -t diytomcat:1.0 .
docker run -d -p 9090:8080 --name lixmtomcat -v /home/docker/build/tomcat/test:/usr/local/apache-tomcat-9.0.22/webapps/test -v /home/docker/build/tomcat/tomcatlogs:/usr/local/apache-tomcat-9.0.22/bin/logs diytomcat:1.0
```

4、启动镜像

5、访问测试

6、发布项目(由于做了卷挂载，我们直接在本地编写项目就可以发布了！)

我们以后的开发的步骤：需要掌握DockerFile的编写！我们之后的一切都是使用dockerfile编写生成镜像文件。



后期验证：

```dockerfile
FROM centos:latest
MAINTAINER lixueming<lixm@lixm.cc>

COPY readme.txt /usr/local/readme.txt
ADD jdk-8u11-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.39.tar.gz /usr/local/

RUN yum makecache
RUN yum -y install vim net-tools
RUN yum clean all

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_11
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.39
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.39
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.39/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.39/bin/logs/catalina.out
```

```shell
# 构建docker镜像
[root@node5 docker]# ls
apache-tomcat-9.0.39.tar.gz  Dockerfile  jdk-8u11-linux-x64.tar.gz  readme.txt
[root@node5 docker]# docker build -t diytomcat:1.0 .
Sending build context to Docker daemon 170.3 MB
Step 1/17 : FROM centos:latest
 ---> 0d120b6ccaa8
Step 2/17 : MAINTAINER lixueming<lixm@lixm.cc>
 ---> Running in 5638fee700fe
 ---> ae8cbeec4919
Removing intermediate container 5638fee700fe
Step 3/17 : COPY readme.txt /usr/local/readme.txt
 ---> 8758ccc24d2b
Removing intermediate container 04ab707ed620
Step 4/17 : ADD jdk-8u11-linux-x64.tar.gz /usr/local/
 ---> 703a8276c1f8
Removing intermediate container b33938c83cdd
Step 5/17 : ADD apache-tomcat-9.0.39.tar.gz /usr/local/
 ---> c12834e91acc
Removing intermediate container ea36fb7e22c0
Step 6/17 : RUN yum makecache
 ---> Running in 269f4cc1faa2

CentOS-8 - AppStream                            813 kB/s | 5.8 MB     00:07    
CentOS-8 - Base                                 1.4 MB/s | 2.2 MB     00:01    
CentOS-8 - Extras                                16 kB/s | 8.1 kB     00:00    
Metadata cache created.
 ---> 4492142b888e
Removing intermediate container 269f4cc1faa2
Step 7/17 : RUN yum -y install vim net-tools
 ---> Running in fe9aed7b89fa

Last metadata expiration check: 0:00:01 ago on Fri Oct 23 09:08:09 2020.
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
(1/6): gpm-libs-1.20.7-15.el8.x86_64.rpm        227 kB/s |  39 kB     00:00    
(2/6): vim-filesystem-8.0.1763-13.el8.noarch.rp 412 kB/s |  48 kB     00:00    
(3/6): net-tools-2.0-0.51.20160912git.el8.x86_6 1.0 MB/s | 323 kB     00:00    
(4/6): which-2.21-12.el8.x86_64.rpm             928 kB/s |  49 kB     00:00    
(5/6): vim-enhanced-8.0.1763-13.el8.x86_64.rpm  991 kB/s | 1.4 MB     00:01    
(6/6): vim-common-8.0.1763-13.el8.x86_64.rpm    2.6 MB/s | 6.3 MB     00:02    
--------------------------------------------------------------------------------
Total                                           2.0 MB/s | 8.1 MB     00:04     
warning: /var/cache/dnf/AppStream-02e86d1c976ab532/packages/gpm-libs-1.20.7-15.el8.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 8483c65d: NOKEY
CentOS-8 - AppStream                             44 kB/s | 1.6 kB     00:00    
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
 ---> a6091d8e8390
Removing intermediate container fe9aed7b89fa
Step 8/17 : RUN yum clean all
 ---> Running in 50c1d1e74654

20 files removed
 ---> 4975ef27c2d2
Removing intermediate container 50c1d1e74654
Step 9/17 : ENV MYPATH /usr/local
 ---> Running in ac44126309f7
 ---> 2c09bd8a0dd2
Removing intermediate container ac44126309f7
Step 10/17 : WORKDIR $MYPATH
 ---> c4ef58cbcfcc
Removing intermediate container 6b9fc3292a93
Step 11/17 : ENV JAVA_HOME /usr/local/jdk1.8.0_11
 ---> Running in 791a383bbca5
 ---> 4127af52a8b0
Removing intermediate container 791a383bbca5
Step 12/17 : ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
 ---> Running in 4d7d4ed592c5
 ---> 7250359b802e
Removing intermediate container 4d7d4ed592c5
Step 13/17 : ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.39
 ---> Running in 3ccec3c89f15
 ---> 483ce0a31b01
Removing intermediate container 3ccec3c89f15
Step 14/17 : ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.39
 ---> Running in 1899497f9fd6
 ---> 20621329ed20
Removing intermediate container 1899497f9fd6
Step 15/17 : ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
 ---> Running in be6fa2e85e8b
 ---> 90c4c4f916aa
Removing intermediate container be6fa2e85e8b
Step 16/17 : EXPOSE 8080
 ---> Running in 538179f1b181
 ---> 36da6454b942
Removing intermediate container 538179f1b181
Step 17/17 : CMD /usr/local/apache-tomcat-9.0.39/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.39/bin/logs/catalina.out
 ---> Running in 1b719187dd38
 ---> 42c5bc15d857
Removing intermediate container 1b719187dd38
Successfully built 42c5bc15d857
[root@node5 docker]# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
diytomcat                       1.0                 42c5bc15d857        10 seconds ago      623 MB

# 运行docker镜像
[root@node5 docker]# docker run -d -p 8080:8080 --name lixmtomcat01 -v /home/docker/lixmtomcat01/test:/usr/local/apache-tomcat-9.0.39/webapps/test -v /home/docker/lixmtomcat01/tomcatlogs:/usr/local/apache-tomcat-9.0.39/bin/logs diytomcat:1.0
552043a36a292a1662ed422d946c2d0fe3d3e8e12d89d3207e2e0de8fbd9112b

[root@node5 docker]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
552043a36a29        diytomcat:1.0       "/bin/sh -c '/usr/..."   24 seconds ago      Up 23 seconds       0.0.0.0:8080->8080/tcp   lixmtomcat01

# 些时使用浏览器打开docker服务器指向端口已可以正常访问
```







