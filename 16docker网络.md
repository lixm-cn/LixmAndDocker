## Docker网络

理解docker网络

理解docker0

清空所有环境

三个基本网络：

```shell
[root@node5 ~]# ip addr show
# 本机回环地址
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
# 虚拟机网络端口
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:70:d2:f8 brd ff:ff:ff:ff:ff:ff
    inet 192.168.89.5/24 brd 192.168.89.255 scope global noprefixroute ens192
       valid_lft forever preferred_lft forever
    inet6 240e:e0:ce1a:8d00:f9a3:99b4:2393:ecae/64 scope global noprefixroute 
       valid_lft forever preferred_lft forever
    inet6 fe80::65ca:4455:31e2:e588/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
# docker的网络接口
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:e8:42:d9:68 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:e8ff:fe42:d968/64 scope link 
       valid_lft forever preferred_lft forever
```

问题：docker是如何处理容器访问网络的？

```shell
[root@node5 ~]# docker run -d -P --name tomcat01 tomcat:latest
Unable to find image 'tomcat:latest' locally
Trying to pull repository docker.io/library/tomcat ... 
latest: Pulling from docker.io/library/tomcat
e4c3d3e4f7b0: Pull complete 
101c41d0463b: Pull complete 
8275efcd805f: Pull complete 
751620502a7a: Pull complete 
a59da3a7d0e7: Pull complete 
9c0f1dffe039: Pull complete 
474314d81831: Pull complete 
90ee5d998c5c: Pull complete 
6b11f2f89cd1: Pull complete 
9eac66e32ef5: Pull complete 
Digest: sha256:30dd6da4bc6b290da345cd8a90212f358b6a094f6197a59fe7f2ba9b8a261b4f
Status: Downloaded newer image for docker.io/tomcat:latest
9345396fe991b72ae122fac7a8b7566da5f0f9bc7ddec81fbf09bee01bc2156b

# 查看容器网络内部的ip地址 ip addr, 发现容器启动时会得到eth0@if262的ip地址，docker自动分配的！
[root@node5 ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
97: eth0@if98: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:2/64 scope link 
       valid_lft forever preferred_lft forever

# linux能不能ping通docker容器的ip地址? 当然可以
[root@node5 ~]# ping -c 4 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.071 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.087 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.083 ms
64 bytes from 172.17.0.2: icmp_seq=4 ttl=64 time=0.086 ms

--- 172.17.0.2 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 2999ms
rtt min/avg/max/mdev = 0.071/0.081/0.087/0.012 ms

# linux可以Ping通docker容器内部
```

>   原理:
>
>   1、我们每启动一个容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0
>
>   2、桥接模式，使用的技术是evth-pair技术！

```shell
# 再次在linux上执行ip addr,又多出一张虚拟网卡vethb527d71@if97
[root@node5 ~]# ip addr 
4: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:e8:42:d9:68 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:e8ff:fe42:d968/64 scope link 
       valid_lft forever preferred_lft forever
98: vethb527d71@if97: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 3e:0a:a8:d8:13:a7 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::3c0a:a8ff:fed8:13a7/64 scope link 
       valid_lft forever preferred_lft forever
```

2、再启动一个tomcat测试,发现又多了一对网卡

```shell
[root@node5 ~]# docker run -d -P --name tomcat02 tomcat:latest
3f7d98280606725fc4b4422aa1ef0078597322e6f8f0e2575c26726251303b59
[root@node5 ~]# ip addr 
4: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:e8:42:d9:68 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:e8ff:fe42:d968/64 scope link 
       valid_lft forever preferred_lft forever
98: vethb527d71@if97: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 3e:0a:a8:d8:13:a7 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::3c0a:a8ff:fed8:13a7/64 scope link 
       valid_lft forever preferred_lft forever
100: veth633b803@if99: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 16:2a:db:2f:5e:c1 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::142a:dbff:fe2f:5ec1/64 scope link 
       valid_lft forever preferred_lft forever

[root@node5 ~]# docker exec -it tomcat02 ip addr 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
99: eth0@if100: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.3/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:3/64 scope link 
       valid_lft forever preferred_lft forever
```

```shell
# 我们发现这个容器的网卡，都是一对一对的
# evth-pair就是一对的虚拟设备接口，他们都是成对出现的，一端连着协议，一端彼此相连； 
# 正因为有这个特性，通常使用evth-pair充当一个桥梁，连接各种虚拟网络设备的； 
# OpenStack, Docker容器之间的连接，OVS的连接，都是使用evth-pair技术。
```

3、我们测试下tomcat01是否能ping通tomcat02；

```shell
[root@node5 ~]# docker exec -it tomcat02 /bin/bash
root@3f7d98280606:/usr/local/tomcat# ping -c 4 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.123 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.076 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.077 ms
64 bytes from 172.17.0.2: icmp_seq=4 ttl=64 time=0.075 ms

--- 172.17.0.2 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.075/0.087/0.123/0.023 ms
```

结论：容器和容器之间是可以互相ping通的。

所有容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用ip

Docker中的所有网络接口都是虚拟的。虚拟的转发效率高。

只要删除容器，对应的网桥也随即删除。

思考一个场景，我们编写一个微服务，database url=ip, 项目不重启，数据库IP换掉了，我们希望可以通过名字来访问容器。

## --link

```shell
[root@node5 ~]# docker exec -it tomcat01 ping tomcat02
ping: tomcat02: Name or service not known

# 如何解决? 使用连接参数--link
[root@node5 ~]# docker run -d -P --name tomcat03 --link tomcat02 tomcat:latest
eaaafa67cf27fc451c829848b883e238d1fa9b825cad2d5201d299e5dbed2c3b
[root@node5 ~]# docker exec -it tomcat03 ping -c 2 tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.111 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.055 ms

--- tomcat02 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.055/0.083/0.111/0.028 ms

# 测试下反向是否能够ping通？不行，需要在tomcat02里也配置--link
[root@node5 ~]# docker exec -it tomcat02 ping -c 2 tomcat03
ping: tomcat03: Name or service not known

[root@node5 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
3745a4d01578        bridge              bridge              local
dad36b8fdd92        host                host                local
b155c0216c45        none                null                local

[root@node5 ~]# docker network inspect 3745a4d01578
[
    {
        "Name": "bridge",
        "Id": "3745a4d01578e002b03e4a44fe1078182c49ac713934c88a6aedcbc0f40d8c13",
        "Created": "2020-10-01T15:09:13.89954204+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {
            "3f7d98280606725fc4b4422aa1ef0078597322e6f8f0e2575c26726251303b59": {
                "Name": "tomcat02",
                "EndpointID": "02fadc65d75b1c15896e7f8b990709072bbfc676a672071408f3f5485ca7e869",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            },
            "9345396fe991b72ae122fac7a8b7566da5f0f9bc7ddec81fbf09bee01bc2156b": {
                "Name": "tomcat01",
                "EndpointID": "e2918cf022cba2e363960fbcda304192da2ca7c85256b4b12bf2a0b8ccec116e",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            },
            "eaaafa67cf27fc451c829848b883e238d1fa9b825cad2d5201d299e5dbed2c3b": {
                "Name": "tomcat03",
                "EndpointID": "8770a32f6c31ec64431137e22d5f70a115746ce4ef157b683105a124c91ff908",
                "MacAddress": "02:42:ac:11:00:04",
                "IPv4Address": "172.17.0.4/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```

```shell
# --link是在hosts添加配置项，对tomcat02主机做一个静态绑定
# --link已不推荐使用
[root@node5 ~]# docker exec -it tomcat03 cat /etc/hosts         
172.17.0.3      tomcat02 3f7d98280606
172.17.0.4      eaaafa67cf27
[root@node5 ~]# docker exec -it tomcat02 cat /etc/hosts 
172.17.0.3      3f7d98280606
```



工作中需要用到自定义网络，高级的功能docker0网桥网络已不能满足需求。

docker0存在的问题：不支持容器名连接访问！









