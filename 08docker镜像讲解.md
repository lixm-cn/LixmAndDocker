## Docker镜像讲解

镜像是什么？

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来！

如何获取镜像：

-   从远程仓库下载
-   本地拷贝
-   使用DockerFile生成



### UnionFS (联合文件系统)

docker的镜像实际上由一层一层的文件系统组成，这种层次的文件系统称为UnionFS.

bootfs(boot file system) 主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时加载bootfs文件系统，在Docker镜像的最底层是bootfs，这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，些时系统也会卸载bootfs。

rootfs，在bootfs之上。包含的就是典型Linux系统中的/dev,/proc,/bin,/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，CentOS等等。

平时我们接触到的虚拟机都是好几个G，为什么Docker这里才200M?

```shell
[root@node5 ~]# docker images centos
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/centos    latest              0d120b6ccaa8        2 months ago        215 MB
```

对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令、工具和程序库就可以了，因为底层直接用Host的Kernel，自己只是需要 提供rootfs就可以了。由此可见对于不同的Linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs。

虚拟机是分钟级，容器是秒级。

### 分层理解

分层的镜像

我们可以下载一个镜像，注意观察下载的日志输出，可以看到一层一层的在下载！

```shell
[root@node5 ~]# docker pull redis
Using default tag: latest
Trying to pull repository docker.io/library/redis ... 
latest: Pulling from docker.io/library/redis
bb79b6b2107f: Pull complete 
1ed3521a5dcb: Pull complete 
5999b99cee8f: Pull complete 
f99a38f44786: Pull complete 
d6fc863042e2: Pull complete 
9bd1af4eae13: Pull complete 
Digest: sha256:33ca074e6019b451235735772a9c3e7216f014aae8eb0580d7e94834fe23efb3
Status: Downloaded newer image for docker.io/redis:latest


[root@node5 ~]# docker image inspect redis:latest
[
    {
        "Id": "sha256:bd571e6529f32461648680c82e2540f9db4b3bb92709ae5d19dd347531c98f19",
        "RepoTags": [
            "docker.io/redis:latest"
        ],
        "RepoDigests": [
            "docker.io/redis@sha256:33ca074e6019b451235735772a9c3e7216f014aae8eb0580d7e94834fe23efb3"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2020-10-13T22:07:24.375626298Z",
        "Container": "80733509b3e962b415a5a913b000a74cc6f38cc2b240c4a86a83dd1bab898424",
        "ContainerConfig": {
            "Hostname": "80733509b3e9",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.0.8",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.0.8.tar.gz",
                "REDIS_DOWNLOAD_SHA=04fa1fddc39bd1aecb6739dd5dd73858a3515b427acd1e2947a66dadce868d68"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:974dcd648a85b0ef1ae78d66080dd7e7cbcf534c343b805242f1173dab60679a",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "18.09.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.0.8",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.0.8.tar.gz",
                "REDIS_DOWNLOAD_SHA=04fa1fddc39bd1aecb6739dd5dd73858a3515b427acd1e2947a66dadce868d68"
            ],
            "Cmd": [
                "redis-server"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:974dcd648a85b0ef1ae78d66080dd7e7cbcf534c343b805242f1173dab60679a",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 104192177,
        "VirtualSize": 104192177,
        "GraphDriver": {
            "Name": "overlay2",
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/9523148292ac691e9e91f28568a272f7ee37b49a8fdaa20fd7d581859f30d3ab/diff:/var/lib/docker/overlay2/5f9be11282d605aed29d314752e3fc31668b5c07887cf8e8d8f1dbdcec7c25fa/diff:/var/lib/docker/overlay2/868cb28e7b48bf1236971f80727688376cc2bfc46ed481b42bbf9961c5eec4ee/diff:/var/lib/docker/overlay2/6e3875b7fa2526ae09e8399ca88c9688d63805f125198c6f470c8ab29dd9d842/diff:/var/lib/docker/overlay2/6f6f1ac8d8e39900bdb9e8c25f658d15329198a2712f5874a7cdf34398af3c34/diff",
                "MergedDir": "/var/lib/docker/overlay2/5e08159710fd939610c62a9749da1d11f836425cca91a6a92daf35dcf55c37d3/merged",
                "UpperDir": "/var/lib/docker/overlay2/5e08159710fd939610c62a9749da1d11f836425cca91a6a92daf35dcf55c37d3/diff",
                "WorkDir": "/var/lib/docker/overlay2/5e08159710fd939610c62a9749da1d11f836425cca91a6a92daf35dcf55c37d3/work"
            }
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:d0fe97fa8b8cefdffcef1d62b65aba51a6c87b6679628a2b50fc6a7a579f764c",
                "sha256:832f21763c8e6b070314e619ebb9ba62f815580da6d0eaec8a1b080bd01575f7",
                "sha256:223b15010c47044b6bab9611c7a322e8da7660a8268949e18edde9c6e3ea3700",
                "sha256:a1a4c1d0c191cee2ac3912696fe57cf9ad84cbfaf3d13f314061bd89fe73cabb",
                "sha256:e9acf707b7fe3eef2f7c6bd127b3b34b262889cb670013adfb46883a49a4ac8e",
                "sha256:b854b24feeee6cbd8fc5d6401f5f1b4a3d40c483444e6c4b06a2b7b31f347b40"
            ]
        }
    }
]
```

理解：

所有的Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层！

如何提交自己的镜像？

## commit镜像

```shell
# 提交容器成为一个新的副本
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[tag]
```

测试

```shell
# 1、启动一个默认的tomcat
docker run -d -p 8080:8080 tomcat
docker exec -it docker_id /bin/bash
# 2、发现默认的tomcat是没有webapps应用，镜像的原因，官方的镜像默认webapps下面是没有文件的！
# 3、我们自己拷贝进去基本的文件
cp -r webapps.dist/* webapps
# 4、将我们操作过的容器通过commit提交为一个镜像！我们以后就使用我们修改过的镜像即可，这就是我们自己的一个修改过的镜像
docker commit -a="lixm" -m="Add webapps" 容器ID tomcat02:1.0
docker images
```