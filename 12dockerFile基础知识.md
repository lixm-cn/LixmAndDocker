## 初始DockerFile

DockerFile就是用来构建docker镜像的构建文件！命令脚本!

先编写一个脚本，通过这个脚本可以生成一个镜像。

```shell
# 创建一个dockerfile文件
# 指令(大写) 参数
mkdir /home/docker
vim dockerfile1
FROM centos
VOLUME ["volume01","volume02"]
CMD echo "---end---"
CMD /bin/bash

[root@node5 docker]# docker build -f /home/docker/dockerfile1 -t mycentos:1.0 .
Sending build context to Docker daemon 2.048 kB
Step 1/4 : FROM centos
 ---> 0d120b6ccaa8
Step 2/4 : VOLUME volume01 volume02
 ---> Running in cce8054ae612
 ---> fef8c52528a4
Removing intermediate container cce8054ae612
Step 3/4 : CMD echo "----build end----"
 ---> Running in cfe4c40e4f04
 ---> fdeb32f81f91
Removing intermediate container cfe4c40e4f04
Step 4/4 : CMD /bin/bash
 ---> Running in 3dc5189ecaf5
 ---> 1d6adcfd1622
Removing intermediate container 3dc5189ecaf5
Successfully built 1d6adcfd1622
[root@node5 docker]# ls
dockerfile1
[root@node5 docker]# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
mycentos                        1.0                 1d6adcfd1622        10 seconds ago      215 MB

# 启动自己生成的镜像
[root@node5 docker]# docker run -it mycentos:1.0 /bin/bash
[root@d28df2401d93 /]# ls
# 在dockerfile中声明的卷已自动挂载
[root@d28df2401d93 /]# ls -al
drwxr-xr-x   2 root root   6 Oct 22 09:23 volume01
drwxr-xr-x   2 root root   6 Oct 22 09:23 volume02

[root@node5 docker]# docker volume ls
DRIVER              VOLUME NAME
local               2f80d413a21fb63e94755c385ba9940bcb9f519d10cc4b55edcd6eea11642364
local               f85e86a707e9e977e55927ec0458ca634134fb3b4049256e5c715b0aba766b45
[root@node5 docker]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
d28df2401d93        mycentos:1.0        "/bin/bash"         3 minutes ago       Up 54 seconds                           clever_mahavira
[root@node5 docker]# docker inspect d28df2401d93
[
    {
        "Id": "d28df2401d930525bcbc3ad92cb0a90fb30f27bddf745a5e5d2c028648c6ce3f",
        "Created": "2020-10-22T09:23:48.780508864Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 22337,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-10-22T09:26:09.552776116Z",
            "FinishedAt": "2020-10-22T09:25:50.806483565Z"
        },
        "Image": "sha256:1d6adcfd1622080776f4aba1c6fb40483d8273b21a6dd3093100d922f2d71e92",
        "ResolvConfPath": "/var/lib/docker/containers/d28df2401d930525bcbc3ad92cb0a90fb30f27bddf745a5e5d2c028648c6ce3f/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/d28df2401d930525bcbc3ad92cb0a90fb30f27bddf745a5e5d2c028648c6ce3f/hostname",
        "HostsPath": "/var/lib/docker/containers/d28df2401d930525bcbc3ad92cb0a90fb30f27bddf745a5e5d2c028648c6ce3f/hosts",
        "LogPath": "",
        "Name": "/clever_mahavira",
        "RestartCount": 0,
        "Driver": "overlay2",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "journald",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "docker-runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DiskQuota": 0,
            "KernelMemory": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": -1,
            "OomKillDisable": false,
            "PidsLimit": 0,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0
        },
        "GraphDriver": {
            "Name": "overlay2",
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/8bcbf0e65917c6822409b29408afc991bf46d3804b583781ddbe71708f4e584f-init/diff:/var/lib/docker/overlay2/50972138e001aff563291cca678a39e3848757679e8bc6c27b6b098fe578d9e4/diff",
                "MergedDir": "/var/lib/docker/overlay2/8bcbf0e65917c6822409b29408afc991bf46d3804b583781ddbe71708f4e584f/merged",
                "UpperDir": "/var/lib/docker/overlay2/8bcbf0e65917c6822409b29408afc991bf46d3804b583781ddbe71708f4e584f/diff",
                "WorkDir": "/var/lib/docker/overlay2/8bcbf0e65917c6822409b29408afc991bf46d3804b583781ddbe71708f4e584f/work"
            }
        },
        "Mounts": [
            {
                "Type": "volume",
                "Name": "2f80d413a21fb63e94755c385ba9940bcb9f519d10cc4b55edcd6eea11642364",
                "Source": "/var/lib/docker/volumes/2f80d413a21fb63e94755c385ba9940bcb9f519d10cc4b55edcd6eea11642364/_data",
                "Destination": "volume01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "f85e86a707e9e977e55927ec0458ca634134fb3b4049256e5c715b0aba766b45",
                "Source": "/var/lib/docker/volumes/f85e86a707e9e977e55927ec0458ca634134fb3b4049256e5c715b0aba766b45/_data",
                "Destination": "volume02",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
        "Config": {
            "Hostname": "d28df2401d93",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "mycentos:1.0",
            "Volumes": {
                "volume01": {},
                "volume02": {}
            },
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200809",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "e0a10799c9cb1e87f60fba8ae25ae13256388a5d673d906d97b3113f1ce5ebf8",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/e0a10799c9cb",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "044a63b92dd2c15f4c91cc63e1bc1a79fbb78f2e872b1b73c52f6e613889a7ec",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "3745a4d01578e002b03e4a44fe1078182c49ac713934c88a6aedcbc0f40d8c13",
                    "EndpointID": "044a63b92dd2c15f4c91cc63e1bc1a79fbb78f2e872b1b73c52f6e613889a7ec",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02"
                }
            }
        }
    }
]

```

测试下刚才的卷是否同步出去了？这种方法未来使用的非常多，因为我们通常会构建自己的镜像！

如果没有指定挂载目录，需要在容器启动时手动指定挂载； 这种方式也可以使用多个mysql实现同步！

## 数据卷同步

多人容器之间实现共享

--volumes-from

Ctrl+p+q退出当前容器

```shell
# 启动3个容器
docker images
docker run -it --name docker01 mycentos:1.0
docker run -it --name docker02 --volumes-from docker01 mycentos:1.0
docker run -it --name docker03 --volumes-from docker01 mycentos:1.0

# 测试，删除docker01,再看下docker02/03的目录里是否还存在？


# 多个mysql数据库实现数据共享
docker run -d -p 3310:3306 -v /etc/mysql/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
docker run -d -p 3311:3306 -v volumes-from mysql01 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 mysql:5.7

#些方法只能实现文件数据的共享，同步后mysql02服务会因为冲突而停止； 
```

结论：

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。但是一旦你持久化到了本地，这个时候，本地的数据是不会删除的。



## DockerFile

dockerfile 用来构建docker镜像的文件，命令参数脚本。

构建步骤 ：

1、编写一个dockerfile文件

2、docker build 构建成一个镜像

3、docker run 运行镜像

4、docker push 发布镜像(DockerHub、阿里云镜像仓库)

查看一下官方是怎么做的？

```
FROM scratch
ADD centos-7-x86_64-docker.tar.xz /

LABEL \
    org.label-schema.schema-version="1.0" \
    org.label-schema.name="CentOS Base Image" \
    org.label-schema.vendor="CentOS" \
    org.label-schema.license="GPLv2" \
    org.label-schema.build-date="20200809" \
    org.opencontainers.image.title="CentOS Base Image" \
    org.opencontainers.image.vendor="CentOS" \
    org.opencontainers.image.licenses="GPL-2.0-only" \
    org.opencontainers.image.created="2020-08-09 00:00:00+01:00"

CMD ["/bin/bash"]
```

很多官方镜像都是基础包，很多功能都没有，我们通常需要自己搭建自己的镜像。

DockerFile的构建过程。

基础知识：

1、每个关键字都必须是大写字母； 

2、执行从上到下顺序执行

3、#表示注释

4、每个指令都会创建一个新的镜像层，并提交！

dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单。

docker镜像逐渐成为企业交付的标准。

步骤：开发、布署、运维

DockerFile：构建文件，定义了一切的步骤，源代码； 

DockerImages: 通过DockerFile构建生成的镜像，最终发布的产品和运行的产品。

Docker容器：容器就是镜像运行起来提供服务的。



