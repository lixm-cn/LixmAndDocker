## 容器数据卷

什么是容器数据卷？

容器之间可以有一个数据共享技术！Docker容器中产生的数据，同步到本地！

这就是卷技术！目录的挂载，将我们的容器内目录，挂载到Linux上面！

/usr/mysql -> /home/mysql

总结成一句话：容器的持久化和同步操作！容器间也是可以数据共享的！

### 使用数据卷

### 方式一：直接使用命令来挂载-v

docker run -it -v  主机目录:容器内目录

```shell
[root@node5 ~]# docker run -it -v /home/test:/home centos:latest /bin/bash
[root@4fc8dadf9ef7 /]# 

[root@node5 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
4fc8dadf9ef7        centos:latest       "/bin/bash"         14 seconds ago      Up 14 seconds                           objective_hoover
# 启动后可以通过inspect查看容器的详细信息，信息里的Mounts记录相关挂载点信息
[root@node5 ~]# docker inspect 4fc8dadf9ef7
[
    {
        "Id": "4fc8dadf9ef7cc2cbaca384fc047b83e7a1d920ea94c600cfd35a47739f7e51e",
        "Created": "2020-10-22T05:44:22.128015607Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 19616,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-10-22T05:44:22.618264765Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:0d120b6ccaa8c5e149176798b3501d4dd1885f961922497cd0abef155c869566",
        "ResolvConfPath": "/var/lib/docker/containers/4fc8dadf9ef7cc2cbaca384fc047b83e7a1d920ea94c600cfd35a47739f7e51e/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/4fc8dadf9ef7cc2cbaca384fc047b83e7a1d920ea94c600cfd35a47739f7e51e/hostname",
        "HostsPath": "/var/lib/docker/containers/4fc8dadf9ef7cc2cbaca384fc047b83e7a1d920ea94c600cfd35a47739f7e51e/hosts",
        "LogPath": "",
        "Name": "/objective_hoover",
        "RestartCount": 0,
        "Driver": "overlay2",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "/home/test:/home"
            ],
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
                "LowerDir": "/var/lib/docker/overlay2/559f7deb337dbd49e441288f9ff8d8d10048c3b80aa2b512c15bd28a0b0495a4-init/diff:/var/lib/docker/overlay2/50972138e001aff563291cca678a39e3848757679e8bc6c27b6b098fe578d9e4/diff",
                "MergedDir": "/var/lib/docker/overlay2/559f7deb337dbd49e441288f9ff8d8d10048c3b80aa2b512c15bd28a0b0495a4/merged",
                "UpperDir": "/var/lib/docker/overlay2/559f7deb337dbd49e441288f9ff8d8d10048c3b80aa2b512c15bd28a0b0495a4/diff",
                "WorkDir": "/var/lib/docker/overlay2/559f7deb337dbd49e441288f9ff8d8d10048c3b80aa2b512c15bd28a0b0495a4/work"
            }
        },
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/home/test",
                "Destination": "/home",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
        "Config": {
            "Hostname": "4fc8dadf9ef7",
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
            "Image": "centos:latest",
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
            "SandboxID": "f05055ab9d5ce94aec54540e5a4c413e4adcea60eb552ef65c1c933a6d8189b6",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/f05055ab9d5c",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "86acccc00eaf2c384a38cad54700a26c6d0925bd78abdbe189a95c92416fdc83",
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
                    "EndpointID": "86acccc00eaf2c384a38cad54700a26c6d0925bd78abdbe189a95c92416fdc83",
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

```
# 测试一
在容器内创建文件，在宿主机上查看；
# 测试二
停止容器，在宿主机上修改文件，再启动容器；
```

好处：我们以后修改只需在本地进行修改，不需要文件同步操作； 