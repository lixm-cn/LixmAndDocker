## 实战：mysql文件同步

```shell
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
f15d42f48bd9: Pull complete 
098ceecc0c8d: Pull complete 
0918bc006cab: Pull complete 
2e0006443ed3: Pull complete 
Digest: sha256:8f084c4e1cbf80b1975344271c9ee4b4c0019f8d057c1d179644dd89eef46c0f
Status: Downloaded newer image for docker.io/mysql:5.7
[root@node5 ~]# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
docker.io/mysql                 5.7                 2836a03e922f        37 hours ago        448 MB
docker.io/mysql                 latest              b5c10a3624ce        37 hours ago        545 MB

# 运行mysql容器需要配置Mysql的密码
# -d 后台运行
# -p 端口映射
# -v 卷挂载
# -e 环境配置
# 
[root@node5 ~]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

# 启动成功之后，我们在本地使用sqlyog连接测试一下
# sqlyog连接到服务器的3310, 3310 --> 3306 --> 后台进程； 

# 在本地测试创建一个数据库，查看对应的数据库文件是否发生变动；此时如果删除容器，挂载到本地的数据依旧存在，实现数据的持久化
```

