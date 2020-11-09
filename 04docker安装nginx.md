```shell
docker pull nginx
docker images

# -d 后台运行
# --name 容器名称
# -p 端口映射
docker run -d --name nginx01 -p 3344:80 nginx
curl localhost:3344

# 进入容器
docker exec -it nginx01 /bin/bash
whereis docker

# 停止容器
docker stop docker_id
```

