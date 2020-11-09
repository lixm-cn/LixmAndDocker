## Docker的常用命令

### 帮助命令

```shell
docker version		#显示docker的版本信息
docker info			#显示更加详细的信息，系统信息，包括镜像和容器的数量
docker 命令 --help
docker --help		#帮助命令
```

### 官方文档

https://docs.docker.com/

https://docs.docker.com/compose/reference/overview/

```shell
[root@node5 ~]# docker images --help

Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]

List images

Options:
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --help            Print usage
      --no-trunc        Don't truncate output
  -q, --quiet           #只显示镜像的ID，Only show numeric IDs
```

**docker search搜索镜像**

```
[root@node5 ~]# docker search mariadb
INDEX       NAME                                             DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/mariadb                                MariaDB is a community-developed fork of M...   3694      [OK]       
docker.io   docker.io/linuxserver/mariadb                    A Mariadb container, brought to you by Lin...   164                  
docker.io   docker.io/bitnami/mariadb                        Bitnami MariaDB Docker Image                    124                  [OK]           
```

在hub.docker.com上搜索镜像

下载镜像docker pull

```shell
[root@node5 ~]# docker pull --help

Usage:  docker pull [OPTIONS] NAME[:TAG|@DIGEST]

Pull an image or a repository from a registry

Options:
  -a, --all-tags                Download all tagged images in the repository
      --disable-content-trust   Skip image verification (default true)
      --help                    Print usage

[root@node5 ~]# docker pull mysql
Using default tag: latest
Trying to pull repository docker.io/library/mysql ... 
latest: Pulling from docker.io/library/mysql
bb79b6b2107f: Pull complete 
49e22f6fb9f7: Pull complete 
842b1255668c: Pull complete 
9f48d1f43000: Pull complete 
c693f0615bce: Pull complete 
8a621b9dbed2: Pull complete 
0807d32aef13: Pull complete 
9eb4355ba450: Pull complete 
6879faad3b6c: Pull complete 
164ef92f3887: Pull complete 
6e4a6e666228: Pull complete 
d45dea7731ad: Pull complete 
Digest: sha256:86b7c83e24c824163927db1016d5ab153a9a04358951be8b236171286e3289a4
Status: Downloaded newer image for docker.io/mysql:latest

# 指定版本下载，首先要确认所指定的版本在仓库中存在
[root@node5 ~]# docker pull mysql:5.7
Trying to pull repository docker.io/library/mysql ... 
5.7: Pulling from docker.io/library/mysql
bb79b6b2107f: Already exists 
49e22f6fb9f7: Already exists 
842b1255668c: Already exists 
9f48d1f43000: Already exists 
c693f0615bce: Already exists 
8a621b9dbed2: Already exists 
0807d32aef13: Already exists 
6d2fc69dfa35: Pull complete 
56153548dd2c: Pull complete 
3bb6ba940303: Pull complete 
3e1888da91a7: Pull complete 
Digest: sha256:b3dc8d10307ab7b9ca1a7981b1601a67e176408be618fc4216d137be37dae10b
Status: Downloaded newer image for docker.io/mysql:5.7

# 删除镜像
[root@node5 ~]# docker rmi --help

Usage:  docker rmi [OPTIONS] IMAGE [IMAGE...]

Remove one or more images

Options:
  -f, --force      Force removal of the image
      --help       Print usage
      --no-prune   Do not delete untagged parents

# 删除单独镜像
[root@node5 ~]# docker rmi 42cdba9f1b08
Untagged: docker.io/mysql:5.7
Untagged: docker.io/mysql@sha256:b3dc8d10307ab7b9ca1a7981b1601a67e176408be618fc4216d137be37dae10b
Deleted: sha256:42cdba9f1b0840cd63254898edeaf6def81a503a6a53d57301c3b38e69cd8f15
Deleted: sha256:d163436e5e1e8180feaf30af2ecbfd3b6a2e86a66b805dcd1e6e0e9ba26be92f
Deleted: sha256:eae434fad508aeec80daf1a0fc16ba89f65707f76a31db7f63883bebf208b743
Deleted: sha256:78b3fc624268ff3cf2ff6558e8c9d47babda61033966f4443e2430dd2607163b
Deleted: sha256:10a3c92754ac4860a385e2095dbe35a1486b4aa2109421a58f3c91f3ce6dbfce

# 删除所有镜像文件
docker rmi -f $(docker images -aq)
```

# 容器的基本命令

说明：我们有了镜像才可以创建容器,linux,下载一个CentOS学习

```shell
[root@node5 ~]# docker pull centos
Using default tag: latest
Trying to pull repository docker.io/library/centos ... 
latest: Pulling from docker.io/library/centos
3c72a8ed6814: Pull complete 
Digest: sha256:76d24f3ba3317fa945743bb3746fbaf3a0b752f10b10376960de01da70685fbd
Status: Downloaded newer image for docker.io/centos:latest


[root@node5 ~]# docker run --help

Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

Options:
      --add-host list                         Add a custom host-to-IP mapping (host:ip) (default [])
  -a, --attach list                           Attach to STDIN, STDOUT or STDERR (default [])
      --blkio-weight uint16                   Block IO (relative weight), between 10 and 1000, or 0 to disable (default 0)
      --blkio-weight-device weighted-device   Block IO weight (relative device weight) (default [])
      --cap-add list                          Add Linux capabilities (default [])
      --cap-drop list                         Drop Linux capabilities (default [])
      --cgroup-parent string                  Optional parent cgroup for the container
      --cidfile string                        Write the container ID to the file
      --cpu-count int                         CPU count (Windows only)
      --cpu-percent int                       CPU percent (Windows only)
      --cpu-period int                        Limit CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int                         Limit CPU CFS (Completely Fair Scheduler) quota
      --cpu-rt-period int                     Limit CPU real-time period in microseconds
      --cpu-rt-runtime int                    Limit CPU real-time runtime in microseconds
  -c, --cpu-shares int                        CPU shares (relative weight)
      --cpus decimal                          Number of CPUs (default 0.000)
      --cpuset-cpus string                    CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string                    MEMs in which to allow execution (0-3, 0,1)
      --credentialspec string                 Credential spec for managed service account (Windows only)
  -d, --detach                                Run container in background and print container ID
      --detach-keys string                    Override the key sequence for detaching a container
      --device list                           Add a host device to the container (default [])
      --device-read-bps throttled-device      Limit read rate (bytes per second) from a device (default [])
      --device-read-iops throttled-device     Limit read rate (IO per second) from a device (default [])
      --device-write-bps throttled-device     Limit write rate (bytes per second) to a device (default [])
      --device-write-iops throttled-device    Limit write rate (IO per second) to a device (default [])
      --disable-content-trust                 Skip image verification (default true)
      --dns list                              Set custom DNS servers (default [])
      --dns-option list                       Set DNS options (default [])
      --dns-search list                       Set custom DNS search domains (default [])
      --entrypoint string                     Overwrite the default ENTRYPOINT of the image
  -e, --env list                              Set environment variables (default [])
      --env-file list                         Read in a file of environment variables (default [])
      --expose list                           Expose a port or a range of ports (default [])
      --group-add list                        Add additional groups to join (default [])
      --health-cmd string                     Command to run to check health
      --health-interval duration              Time between running the check (ns|us|ms|s|m|h) (default 0s)
      --health-retries int                    Consecutive failures needed to report unhealthy
      --health-timeout duration               Maximum time to allow one check to run (ns|us|ms|s|m|h) (default 0s)
      --help                                  Print usage
  -h, --hostname string                       Container host name
      --init                                  Run an init inside the container that forwards signals and reaps processes
      --init-path string                      Path to the docker-init binary
  -i, --interactive                           Keep STDIN open even if not attached
      --io-maxbandwidth string                Maximum IO bandwidth limit for the system drive (Windows only)
      --io-maxiops uint                       Maximum IOps limit for the system drive (Windows only)
      --ip string                             Container IPv4 address (e.g. 172.30.100.104)
      --ip6 string                            Container IPv6 address (e.g. 2001:db8::33)
      --ipc string                            IPC namespace to use
      --isolation string                      Container isolation technology
      --kernel-memory string                  Kernel memory limit
  -l, --label list                            Set meta data on a container (default [])
      --label-file list                       Read in a line delimited file of labels (default [])
      --link list                             Add link to another container (default [])
      --link-local-ip list                    Container IPv4/IPv6 link-local addresses (default [])
      --log-driver string                     Logging driver for the container
      --log-opt list                          Log driver options (default [])
      --mac-address string                    Container MAC address (e.g. 92:d0:c6:0a:29:33)
  -m, --memory string                         Memory limit
      --memory-reservation string             Memory soft limit
      --memory-swap string                    Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --memory-swappiness int                 Tune container memory swappiness (0 to 100) (default -1)
      --name string                           Assign a name to the container
      --network string                        Connect a container to a network (default "default")
      --network-alias list                    Add network-scoped alias for the container (default [])
      --no-healthcheck                        Disable any container-specified HEALTHCHECK
      --oom-kill-disable                      Disable OOM Killer
      --oom-score-adj int                     Tune host's OOM preferences (-1000 to 1000)
      --pid string                            PID namespace to use
      --pids-limit int                        Tune container pids limit (set -1 for unlimited)
      --privileged                            Give extended privileges to this container
  -p, --publish list                          Publish a container's port(s) to the host (default [])
  -P, --publish-all                           Publish all exposed ports to random ports
      --read-only                             Mount the container's root filesystem as read only
      --restart string                        Restart policy to apply when a container exits (default "no")
      --rm                                    Automatically remove the container when it exits
      --runtime string                        Runtime to use for this container
      --security-opt list                     Security Options (default [])
      --shm-size string                       Size of /dev/shm, default value is 64MB
      --sig-proxy                             Proxy received signals to the process (default true)
      --stop-signal string                    Signal to stop a container, SIGTERM by default (default "SIGTERM")
      --stop-timeout int                      Timeout (in seconds) to stop a container
      --storage-opt list                      Storage driver options for the container (default [])
      --sysctl map                            Sysctl options (default map[])
      --tmpfs list                            Mount a tmpfs directory (default [])
  -t, --tty                                   Allocate a pseudo-TTY
      --ulimit ulimit                         Ulimit options (default [])
  -u, --user string                           Username or UID (format: <name|uid>[:<group|gid>])
      --userns string                         User namespace to use
      --uts string                            UTS namespace to use
  -v, --volume list                           Bind mount a volume (default [])
      --volume-driver string                  Optional volume driver for the container
      --volumes-from list                     Mount volumes from the specified container(s) (default [])
  -w, --workdir string                        Working directory inside the container
[root@node5 ~]# 
```



### 新建容器并启动

```shell
docker run [可选参数] image

#参数说明
--name="Name"		#容器名称如tomcat01,tomcat02用于区分容器
-d					#后台方式运行
-it					#使用交互方式运行，进入容器查看内容
-p					#小p,指定容器的端口如-p 8080:8080
	-p 主机端口:容器端口
	-p ip:主机端口:容器端口(常用)
	-p 容器端口
-P					#大P，随机指定端口


# 启动并进入容器
[root@node5 ~]# docker run -it centos /bin/bash
# 查看容器内的CentOS目录，基础版本，很多命令都是不完善的
[root@b77b6591b680 /]# ls 
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@b77b6591b680 /]# exit		#退出容器


# 列出所有运行中的容器
[root@node5 ~]# docker ps --help

Usage:  docker ps [OPTIONS]

List containers

Options:
  -a, --all             #显示所有运行过的容器，Show all containers (default shows just running)
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print containers using a Go template
      --help            Print usage
  -n, --last int        #显示最近创建的容器，Show n last created containers (includes all states) (default -1)
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           #只显示容器的编号，Only display numeric IDs
  -s, --size            Display total file sizes

[root@node5 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@node5 ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS               NAMES
b77b6591b680        centos              "/bin/bash"              2 minutes ago       Exited (0) 10 seconds ago                          brave_boyd
```



### 退出容器

```shell
exit #退出并停止容器
Ctrl+P+Q  #容器不停止退出
```



### 删除容器

```shell
docker rm 容器ID		#删除指定的容器
docker rm -f $(docker ps -aq)	#删除所有容器
docker ps -a -q | xargs docker rm -f #删除所有容器
```

```shell
#不能删除正在运行的容器，如果要强制删除需要使用-f参数
[root@node5 ~]# docker rm 6a14248c7dde
Error response from daemon: You cannot remove a running container 6a14248c7dde066fe4d900d50fba8b0760ed63268f64c82231e850f9180fe14a. Stop the container before attempting removal or use -f

[root@node5 ~]# docker ps -qa | xargs docker rm -f
b77b6591b680
c7554a0a0494
72cbe885eb26
7590efc25ff4
8c813f3aab22
b7dd7e13f416
```



### 启动和停止容器的操作

```shell
docker start 容器id
docker stop 容器id
docker restart 容器id
docker kill 容器id		#强制停止当前容器
```



## 常用的其他命令

后台启动容器

使用docker run -d启动后，再运行docker ps显示容器停止了，docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用就会自动停止。

```shell
[root@node5 ~]# docker run -d centos
3e69434f9994f24a601355b3e109ac33faa55f5eeba56a1d03d9d4e5a757b602
[root@node5 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

查看日志

```shell
[root@node5 ~]# docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --help           Print usage
      --since string   Show logs since timestamp
      --tail string    #显示日志条数，Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     #显示时间戳，Show timestamps
```

测试

```shell
[root@node5 ~]# docker run -d centos /bin/bash -c "while true; do echo HelloDocker; sleep 1; done"
f39d92f604ef9305e72182c27387962fc79b0561e19b0d5de2a19ac61d776328

[root@node5 ~]# docker logs -f -t --tail 10 f39d92f604ef
2020-10-19T08:23:33.058232000Z HelloDocker
2020-10-19T08:23:34.060315000Z HelloDocker
2020-10-19T08:23:35.062515000Z HelloDocker
2020-10-19T08:23:36.064987000Z HelloDocker
2020-10-19T08:23:37.067170000Z HelloDocker
2020-10-19T08:23:38.069245000Z HelloDocker
2020-10-19T08:23:39.071756000Z HelloDocker
2020-10-19T08:23:40.074101000Z HelloDocker
2020-10-19T08:23:41.076429000Z HelloDocker
2020-10-19T08:23:42.078871000Z HelloDocker
```



### 查看容器内部进程的信息docker top

```shell
[root@node5 ~]# docker top --help

Usage:  docker top CONTAINER [ps OPTIONS]

Display the running processes of a container

Options:
      --help   Print usage
```

```shell
[root@node5 ~]# docker top f39d92f604ef
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                25361               25343               0                   16:22               ?                   00:00:00            /bin/bash -c while true; do echo HelloDocker; sleep 1; done
root                25653               25361               0                   16:26               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
```

### 查看镜像的元数据

```
#查看docker容器的元数据
[root@node5 ~]# docker inspect --help

Usage:  docker inspect [OPTIONS] NAME|ID [NAME|ID...]

Return low-level information on Docker objects

Options:
  -f, --format string   Format the output using the given Go template
      --help            Print usage
  -s, --size            Display total file sizes if the type is container
      --type string     Return JSON for specified type
```

```shell
[root@node5 ~]# docker inspect f39d92f604ef
[
    {
        "Id": "f39d92f604ef9305e72182c27387962fc79b0561e19b0d5de2a19ac61d776328",
        "Created": "2020-10-19T08:23:00.720186422Z",
        "Path": "/bin/bash",
        "Args": [
            "-c",
            "while true; do echo HelloDocker; sleep 1; done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 25361,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-10-19T08:23:00.98117466Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:0d120b6ccaa8c5e149176798b3501d4dd1885f961922497cd0abef155c869566",
        "ResolvConfPath": "/var/lib/docker/containers/f39d92f604ef9305e72182c27387962fc79b0561e19b0d5de2a19ac61d776328/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/f39d92f604ef9305e72182c27387962fc79b0561e19b0d5de2a19ac61d776328/hostname",
        "HostsPath": "/var/lib/docker/containers/f39d92f604ef9305e72182c27387962fc79b0561e19b0d5de2a19ac61d776328/hosts",
        "LogPath": "",
        "Name": "/zealous_mahavira",
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
                "LowerDir": "/var/lib/docker/overlay2/1e6a8600963fede1bd6ee1ecebdecc3f6b3c540a3729a5530eb4471169e1e409-init/diff:/var/lib/docker/overlay2/50972138e001aff563291cca678a39e3848757679e8bc6c27b6b098fe578d9e4/diff",
                "MergedDir": "/var/lib/docker/overlay2/1e6a8600963fede1bd6ee1ecebdecc3f6b3c540a3729a5530eb4471169e1e409/merged",
                "UpperDir": "/var/lib/docker/overlay2/1e6a8600963fede1bd6ee1ecebdecc3f6b3c540a3729a5530eb4471169e1e409/diff",
                "WorkDir": "/var/lib/docker/overlay2/1e6a8600963fede1bd6ee1ecebdecc3f6b3c540a3729a5530eb4471169e1e409/work"
            }
        },
        "Mounts": [],
        "Config": {
            "Hostname": "f39d92f604ef",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash",
                "-c",
                "while true; do echo HelloDocker; sleep 1; done"
            ],
            "Image": "centos",
            "Volumes": null,
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
            "SandboxID": "6f284badd3288028f6abe1504570a553cab97259f21982fa484c6ab5a1968e41",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/6f284badd328",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "601c4596876295315feccbe26e7a8c917b690ba7f3c04918ca5e6515dfda8df9",
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
                    "EndpointID": "601c4596876295315feccbe26e7a8c917b690ba7f3c04918ca5e6515dfda8df9",
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
[root@node5 ~]# 
```

## 进入当前正在运行的容器

```shell
# 我们通常都是使用后台方式运行的，需要进入容器，修改一些配置
# 方法一，新开启一个虚拟终端，可以在里面操作(常用)
docker exec -it 容器id /bin/bash

# 方法二,进入容器正在执行的终端
docker attach 容器id
```

## 从容器拷贝文件到主机上

```shell
[root@node5 ~]# docker cp --help

Usage:  docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
        docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH

Copy files/folders between a container and the local filesystem

Options:
  -a, --archive       Archive mode (copy all uid/gid information)
  -L, --follow-link   Always follow symbol link in SRC_PATH
      --help          Print usage
```

拷贝是一个手动过程，后期使用数据卷-v可以实现自动的文件拷贝；

