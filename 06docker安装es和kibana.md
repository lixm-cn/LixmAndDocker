```shell
# es暴露的端口很多
# es十分的耗内存
# es的数据一般需要放置到安全目录，需要挂载目录
# --net somenetwork 网络配置
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag

#启动elasticsearch
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.9.2

#查看docker的cpu内存使用率
docker stats
CONTAINER           CPU %               MEM USAGE / LIMIT       MEM %               NET I/O             BLOCK I/O           PIDS
ba4d123c0d2b        0.48%               1.233 GiB / 1.952 GiB   63.16%              656 B / 656 B       581 MB / 3.08 MB    51

[root@node5 ~]# curl localhost:9200
{
  "name" : "ba4d123c0d2b",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "RisHP4NTQhubwrjsJGIQMA",
  "version" : {
    "number" : "7.9.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "d34da0ea4a966c4e49417f2da2f244e3e97b4e6e",
    "build_date" : "2020-09-23T00:45:33.626720Z",
    "build_snapshot" : false,
    "lucene_version" : "8.6.2",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
[root@node5 ~]# curl localhost:9300
This is not an HTTP port

# 增加内存限制
# -e 环境配置参数
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xms64m -Xmx512m" -e "discovery.type=single-node" elasticsearch:7.9.2

[root@node5 ~]# docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xms64m -Xmx512m" -e "discovery.type=single-node" elasticsearch:7.9.2
bc08e39768f6df5af950eba8ad26c6077ab76b16c4a45345007bc921f57a2f7b
[root@node5 ~]# docker stats
CONTAINER           CPU %               MEM USAGE / LIMIT       MEM %               NET I/O             BLOCK I/O           PIDS
bc08e39768f6        339.28%             440.5 MiB / 1.952 GiB   22.04%              656 B / 656 B       213 MB / 758 kB     52
```

如何配置kibana连接elasticsearch?