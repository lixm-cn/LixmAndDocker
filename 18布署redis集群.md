# 布署redis集群

redis分片 + 高可用 + 负载均衡

6台redis，3主3从；二二互备；

[r-m1,r-s1], [r-m2,r-s2], [r-m3,r-s3]

```cmd
# 清理环境
[root@node5 ~]# docker ps 
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
6bd2f6675347        tomcat:latest       "catalina.sh run"   26 minutes ago      Up 26 minutes       0.0.0.0:32775->8080/tcp   tomcat-net-02
62320f97e109        tomcat:latest       "catalina.sh run"   26 minutes ago      Up 26 minutes       0.0.0.0:32774->8080/tcp   tomcat-net-01
5baafbf73897        tomcat:latest       "catalina.sh run"   41 minutes ago      Up 41 minutes       0.0.0.0:32773->8080/tcp   tomcat01
[root@node5 ~]# docker rm -f $(docker ps -aq)
6bd2f6675347
62320f97e109
5baafbf73897
[root@node5 ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

[root@node5 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
3745a4d01578        bridge              bridge              local
dad36b8fdd92        host                host                local
324eada22439        mynet               bridge              local
b155c0216c45        none                null                local
[root@node5 ~]# docker network rm 324eada22439
324eada22439
[root@node5 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
3745a4d01578        bridge              bridge              local
dad36b8fdd92        host                host                local
b155c0216c45        none                null                local

# 创建redis网络
[root@node5 ~]# docker network create redisnet --subnet 172.30.0.0/16
499cac86ce74c5bd1f3535e8255df8cf874976b1d4730d049867bb321d962898
[root@node5 ~]# 
[root@node5 ~]# 
[root@node5 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
3745a4d01578        bridge              bridge              local
dad36b8fdd92        host                host                local
b155c0216c45        none                null                local
499cac86ce74        redisnet            bridge              local
[root@node5 ~]# docker network inspect redisnet
[
    {
        "Name": "redisnet",
        "Id": "499cac86ce74c5bd1f3535e8255df8cf874976b1d4730d049867bb321d962898",
        "Created": "2020-10-23T22:19:43.119981368+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.30.0.0/16"
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

# 通过脚本创建六个redis配置
for port in $(seq 1 6); do
mkdir -p /mydata/redis/node-${port}/conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.30.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redisnet --ip 172.30.0.1${port} redis:5.0.9-alphine3.11 redis-server /etc/redis/redis.conf;

# 启动redis-1
docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
-v /mydata/redis/node-1/data:/data \
-v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
-d --net redisnet --ip 172.30.0.11 redis:latest redis-server /etc/redis/redis.conf

# 启动redis-2
docker run -p 6372:6379 -p 16372:16379 --name redis-2 \
-v /mydata/redis/node-2/data:/data \
-v /mydata/redis/node-2/conf/redis.conf:/etc/redis/redis.conf \
-d --net redisnet --ip 172.30.0.12 redis:latest redis-server /etc/redis/redis.conf

# 启动redis-3
docker run -p 6373:6379 -p 16373:16379 --name redis-3 \
-v /mydata/redis/node-3/data:/data \
-v /mydata/redis/node-3/conf/redis.conf:/etc/redis/redis.conf \
-d --net redisnet --ip 172.30.0.13 redis:latest redis-server /etc/redis/redis.conf

# 启动redis-4
docker run -p 6374:6379 -p 16374:16379 --name redis-4 \
-v /mydata/redis/node-4/data:/data \
-v /mydata/redis/node-4/conf/redis.conf:/etc/redis/redis.conf \
-d --net redisnet --ip 172.30.0.14 redis:latest redis-server /etc/redis/redis.conf

# 启动redis-5
docker run -p 6375:6379 -p 16375:16379 --name redis-5 \
-v /mydata/redis/node-5/data:/data \
-v /mydata/redis/node-5/conf/redis.conf:/etc/redis/redis.conf \
-d --net redisnet --ip 172.30.0.15 redis:latest redis-server /etc/redis/redis.conf

# 启动redis-6
docker run -p 6376:6379 -p 16376:16379 --name redis-6 \
-v /mydata/redis/node-6/data:/data \
-v /mydata/redis/node-6/conf/redis.conf:/etc/redis/redis.conf \
-d --net redisnet --ip 172.30.0.16 redis:latest redis-server /etc/redis/redis.conf

# 创建集群
# redis-cli --cluster create 172.30.0.11:6379 172.30.0.12:6379 172.30.0.13:6379 172.30.0.14:6379 172.30.0.15:6379 172.30.0.16:6379 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.30.0.15:6379 to 172.30.0.11:6379
Adding replica 172.30.0.16:6379 to 172.30.0.12:6379
Adding replica 172.30.0.14:6379 to 172.30.0.13:6379
M: 38244bdb4401a62b299fb6d49ae852e5baa60dca 172.30.0.11:6379
   slots:[0-5460] (5461 slots) master
M: 2e6856ec08111ee4f1d8d571fe5fd1204a9d4806 172.30.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: 0601bcd89255532774a7b060464992f0e3d9300c 172.30.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: cc19f4fb50cef2d504bf65ff89cb6c15eb48ac79 172.30.0.14:6379
   replicates 0601bcd89255532774a7b060464992f0e3d9300c
S: 259334a56d17316399b36db738bc96b0d5b44fbf 172.30.0.15:6379
   replicates 38244bdb4401a62b299fb6d49ae852e5baa60dca
S: a966fe542582011162789f5d6ad5f5896b04a79f 172.30.0.16:6379
   replicates 2e6856ec08111ee4f1d8d571fe5fd1204a9d4806
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
.
>>> Performing Cluster Check (using node 172.30.0.11:6379)
M: 38244bdb4401a62b299fb6d49ae852e5baa60dca 172.30.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: cc19f4fb50cef2d504bf65ff89cb6c15eb48ac79 172.30.0.14:6379
   slots: (0 slots) slave
   replicates 0601bcd89255532774a7b060464992f0e3d9300c
M: 2e6856ec08111ee4f1d8d571fe5fd1204a9d4806 172.30.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
M: 0601bcd89255532774a7b060464992f0e3d9300c 172.30.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 259334a56d17316399b36db738bc96b0d5b44fbf 172.30.0.15:6379
   slots: (0 slots) slave
   replicates 38244bdb4401a62b299fb6d49ae852e5baa60dca
S: a966fe542582011162789f5d6ad5f5896b04a79f 172.30.0.16:6379
   slots: (0 slots) slave
   replicates 2e6856ec08111ee4f1d8d571fe5fd1204a9d4806
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
# redis-cli -c
127.0.0.1:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:214
cluster_stats_messages_pong_sent:222
cluster_stats_messages_sent:436
cluster_stats_messages_ping_received:217
cluster_stats_messages_pong_received:214
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:436
127.0.0.1:6379> cluster nodes
38244bdb4401a62b299fb6d49ae852e5baa60dca 172.30.0.11:6379@16379 myself,master - 0 1603465704000 1 connected 0-5460
cc19f4fb50cef2d504bf65ff89cb6c15eb48ac79 172.30.0.14:6379@16379 slave 0601bcd89255532774a7b060464992f0e3d9300c 0 1603465704504 3 connected
2e6856ec08111ee4f1d8d571fe5fd1204a9d4806 172.30.0.12:6379@16379 master - 0 1603465704505 2 connected 5461-10922
0601bcd89255532774a7b060464992f0e3d9300c 172.30.0.13:6379@16379 master - 0 1603465704805 3 connected 10923-16383
259334a56d17316399b36db738bc96b0d5b44fbf 172.30.0.15:6379@16379 slave 38244bdb4401a62b299fb6d49ae852e5baa60dca 0 1603465705305 1 connected
a966fe542582011162789f5d6ad5f5896b04a79f 172.30.0.16:6379@16379 slave 2e6856ec08111ee4f1d8d571fe5fd1204a9d4806 0 1603465705807 2 connected
127.0.0.1:6379> set a b
-> Redirected to slot [15495] located at 172.30.0.13:6379
OK
172.30.0.13:6379> 

127.0.0.1:6379> set a b
-> Redirected to slot [15495] located at 172.30.0.13:6379
OK
172.30.0.13:6379> get a
"b"

# 此时操作停止redis-3
[root@node5 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                              NAMES
a1bbc438b022        redis:latest        "docker-entrypoint..."   24 minutes ago      Up 24 minutes       0.0.0.0:6373->6379/tcp, 0.0.0.0:16373->16379/tcp   redis-3
[root@node5 ~]# docker stop a1bbc438b022
a1bbc438b022

172.30.0.13:6379> get a
Could not connect to Redis at 172.30.0.13:6379: No route to host
(31.23s)
not connected> 
not connected> exit
# redis-cli -c
127.0.0.1:6379> get a
-> Redirected to slot [15495] located at 172.30.0.14:6379
"b"
172.30.0.14:6379> cluster nodes
cc19f4fb50cef2d504bf65ff89cb6c15eb48ac79 172.30.0.14:6379@16379 myself,master - 0 1603465924000 7 connected 10923-16383
0601bcd89255532774a7b060464992f0e3d9300c 172.30.0.13:6379@16379 master,fail - 1603465845850 1603465843543 3 connected
2e6856ec08111ee4f1d8d571fe5fd1204a9d4806 172.30.0.12:6379@16379 master - 0 1603465924025 2 connected 5461-10922
259334a56d17316399b36db738bc96b0d5b44fbf 172.30.0.15:6379@16379 slave 38244bdb4401a62b299fb6d49ae852e5baa60dca 0 1603465925027 1 connected
a966fe542582011162789f5d6ad5f5896b04a79f 172.30.0.16:6379@16379 slave 2e6856ec08111ee4f1d8d571fe5fd1204a9d4806 0 1603465924000 2 connected
38244bdb4401a62b299fb6d49ae852e5baa60dca 172.30.0.11:6379@16379 master - 0 1603465924526 1 connected 0-5460
172.30.0.14:6379> 

# docker搭建redis集群完成！
```







 