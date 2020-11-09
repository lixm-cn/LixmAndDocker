## docker自定义网络

容器互联

```cmd
[root@node5 ~]# docker network --help

Usage:  docker network COMMAND

Manage networks

Options:
      --help   Print usage

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks

Run 'docker network COMMAND --help' for more information on a command.

# 查看所有的docker网络
[root@node5 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
3745a4d01578        bridge              bridge              local
dad36b8fdd92        host                host                local
b155c0216c45        none                null                local
```

网络模式：

-   bridge: 桥接（默认）
-   none: 不配置网络
-   host: 和宿主机共享网络
-   container: 容器间网络连通！(用的少，局限大)

```cmd
# 启动docker容器,之前我们直接启动的命令，里面有一个默认参数--net bridge,而这个是就是docker0
docker run -d -P --name tomcat01 tomcat:latest
docker run -d -P --name tomcat01 --net bridge tomcat:latest

# docker0特点：默认，域名不能访问，--link可以打通连接

# 我们可以自定义一个网络
[root@node5 ~]# docker network create --help

Usage:  docker network create [OPTIONS] NETWORK

Create a network

Options:
      --attachable             Enable manual container attachment
      --aux-address map        Auxiliary IPv4 or IPv6 addresses used by Network driver (default map[])
  -d, --driver string          Driver to manage the Network (default "bridge")
      --gateway stringSlice    IPv4 or IPv6 Gateway for the master subnet
      --help                   Print usage
      --internal               Restrict external access to the network
      --ip-range stringSlice   Allocate container ip from a sub-range
      --ipam-driver string     IP Address Management Driver (default "default")
      --ipam-opt map           Set IPAM driver specific options (default map[])
      --ipv6                   Enable IPv6 networking
      --label list             Set metadata on a network (default [])
  -o, --opt map                Set driver specific options (default map[])
      --subnet stringSlice     Subnet in CIDR format that represents a network segment
      

# 创建一个自定义子网
# --driver bridge
# --subnet 192.168.0.0/16
# --gateway 192.168.0.1
[root@node5 ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
324eada22439aa075e815dad2130d868340c6adc19393c9b7e0192b737be3835
[root@node5 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
3745a4d01578        bridge              bridge              local
dad36b8fdd92        host                host                local
324eada22439        mynet               bridge              local
b155c0216c45        none                null                local

# 查看自己创建的网络
[root@node5 ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "324eada22439aa075e815dad2130d868340c6adc19393c9b7e0192b737be3835",
        "Created": "2020-10-23T19:20:55.926479073+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]

# 创建境像文件
[root@node5 ~]# docker run -d -P --name tomcat-net-01 --net mynet tomcat:latest
62320f97e109be3b873788eb8cdfe203abcfa64cbf4e10435b5ba9ea62bc122d
[root@node5 ~]# docker run -d -P --name tomcat-net-02 --net mynet tomcat:latest
6bd2f66753470855e16e7d38fe0f27340e5620c6cd7ad33205f16557e359761c

[root@node5 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
6bd2f6675347        tomcat:latest       "catalina.sh run"   27 seconds ago      Up 26 seconds       0.0.0.0:32775->8080/tcp   tomcat-net-02
62320f97e109        tomcat:latest       "catalina.sh run"   35 seconds ago      Up 34 seconds       0.0.0.0:32774->8080/tcp   tomcat-net-01
[root@node5 ~]# docker exec -it 62320f97e109 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
106: eth0@if107: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:a8:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.0.2/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:c0ff:fea8:2/64 scope link 
       valid_lft forever preferred_lft forever
[root@node5 ~]# docker exec -it 6bd2f6675347 ip addr            
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
108: eth0@if109: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:a8:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.0.3/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:c0ff:fea8:3/64 scope link 
       valid_lft forever preferred_lft forever

# 再次测试ping连接
[root@node5 ~]# docker exec -it tomcat-net-01 ping -c 2 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.132 ms
64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.065 ms

# 现在不使用--link也可以ping容器名，我们自定义的网络都已经帮我们维护好了对应的关系，推荐我们平时这样使用网络
[root@node5 ~]# docker exec -it tomcat-net-01 ping -c 2 tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.059 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.052 ms

# 好处：不同的集群使用不同的网络，保证集群安全和相互隔离
```

## 网络连通

跨网络的互联方法: docker0 <--> mynet

```cmd
[root@node5 ~]# docker exec -it tomcat01 ip addr
103: eth0@if104: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:2/64 scope link 
       valid_lft forever preferred_lft forever

[root@node5 ~]# docker exec -it tomcat01 ping -c 2 192.168.0.2
PING 192.168.0.2 (192.168.0.2) 56(84) bytes of data.

--- 192.168.0.2 ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1000ms

[root@node5 ~]# docker exec -it tomcat01 ping -c 2 192.168.0.1
PING 192.168.0.1 (192.168.0.1) 56(84) bytes of data.
64 bytes from 192.168.0.1: icmp_seq=1 ttl=64 time=0.070 ms
64 bytes from 192.168.0.1: icmp_seq=2 ttl=64 time=0.065 ms

--- 192.168.0.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.065/0.067/0.070/0.008 ms

# 网络连通
[root@node5 ~]# docker network connect --help

Usage:  docker network connect [OPTIONS] NETWORK CONTAINER

Connect a container to a network

Options:
      --alias stringSlice           Add network-scoped alias for the container
      --help                        Print usage
      --ip string                   IP Address
      --ip6 string                  IPv6 Address
      --link list                   Add link to another container (default [])
      --link-local-ip stringSlice   Add a link-local address for the container

# 打通tomcat01和mynet
[root@node5 ~]# docker network connect mynet tomcat01

# 连通后即将tomcat01直接连接到mynet网络下
# 一个容器两个ip地址
[root@node5 ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "324eada22439aa075e815dad2130d868340c6adc19393c9b7e0192b737be3835",
        "Created": "2020-10-23T19:20:55.926479073+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {
            "5baafbf73897b07765c72a2bb2f6561a27f39515e7f43a63ab486b64764eba1f": {
                "Name": "tomcat01",
                "EndpointID": "768a52d87e31f85b750ec5e4e38a947bc65e4983510bccbd8ed391724e7db8e3",
                "MacAddress": "02:42:c0:a8:00:04",
                "IPv4Address": "192.168.0.4/16",
                "IPv6Address": ""
            },
            "62320f97e109be3b873788eb8cdfe203abcfa64cbf4e10435b5ba9ea62bc122d": {
                "Name": "tomcat-net-01",
                "EndpointID": "a768e15d1e3332fd5f080aaab2f97766f6843bb79a1a9c447487a8485ea1b59a",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            },
            "6bd2f66753470855e16e7d38fe0f27340e5620c6cd7ad33205f16557e359761c": {
                "Name": "tomcat-net-02",
                "EndpointID": "a970708f18f67471b5044c2322f5b66857ae8129e5fc37e1160b920c04cda638",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

# 再次测试,夸网络连接ping成功
[root@node5 ~]# docker exec -it tomcat01 ping -c 2 tomcat-net-01
PING tomcat-net-01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.100 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.073 ms

--- tomcat-net-01 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.073/0.086/0.100/0.016 ms
```

结论：假设要夸网络操作别的容器，就需要使用docker network connect连通操作。











