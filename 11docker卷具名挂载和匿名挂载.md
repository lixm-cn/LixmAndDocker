### 具名挂载和匿名挂载

```shell
# 匿名挂载
# -P, --publish-all    #Publish all exposed ports to random ports
docker run -d -P --name nginx01 -v /etc/nginx nginx

[root@node5 test]# docker volume --help

Usage:  docker volume COMMAND

Manage volumes

Options:
      --help   Print usage

Commands:
  create      创建卷，Create a volume
  inspect     显示卷的详细信息，Display detailed information on one or more volumes
  ls          列出卷，List volumes
  prune       移除所有未使用的卷，Remove all unused volumes
  rm          移除一个或多个卷，Remove one or more volumes

Run 'docker volume COMMAND --help' for more information on a command.

# 查看本地所有的卷volume,这些是匿名卷
[root@node5 test]# docker volume ls
DRIVER              VOLUME NAME
local               54f910a04af494423c083bc522928831a74ce8810dbdb67e307a7f5fdf4e9f12

# 具名挂载
# 通过 -v 卷名:容器内路径
[root@node5 local]# docker run -d -P --name nginx02 -v named-nginx:/etc/nginx nginx
9e9e05d2fad13939fd909c786b861e5040e2410e0ee9c736ed2d34712ababd45
[root@node5 local]# docker volume ls
DRIVER              VOLUME NAME
local               1453f8e886d12b72b88c830465e76c6b109dc6455ee0d704ec5256b5eb37131f
local               54f910a04af494423c083bc522928831a74ce8810dbdb67e307a7f5fdf4e9f12
local               named-nginx

# 查看具名卷的详细信息
[root@node5 local]# docker volume inspect named-nginx
[
    {
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/named-nginx/_data",
        "Name": "named-nginx",
        "Options": {},
        "Scope": "local"
    }
]
```

所有的docker容器内的卷，没有指定目录的情况下都是在**/var/lib/docker/volumes/xxx/_data**目录下, 我们通过具名挂载可以方便的找到我们的一个卷，大多数情况在使用的**具名挂载**。

### 如何确定是具名挂载还是匿名挂载，还是指定路径挂载？

-v 容器内路径 				#匿名挂载

-v 卷名:容器内路径		#具名挂载

-v 宿主机路径:容器内路径		#指定路径挂载

扩展： 

```shell
# 只读挂载，一担以只读方式挂载，则容器只能读取文件，文件的修改只能从宿主机上修改
docker run -d -P --name nginx02 -v named-nginx:/etc/nginx:ro nginx

# 读写挂载
[root@node5 local]# docker run -d -P --name nginx02 -v named-nginx:/etc/nginx:rw nginx
```











