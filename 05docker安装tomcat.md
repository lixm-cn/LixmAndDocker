```shell
docker run -d -p 8080:8080 --name tomcat01 tomcat

# --rm 退出容器后立即删除当前容器
docker run -it --rm -p 8888:8080 tomcat:9.0

[root@node5 ~]# docker run -d -p 8080:8080 --name tomcat01 tomcat
Unable to find image 'tomcat:latest' locally
Trying to pull repository docker.io/library/tomcat ... 
latest: Pulling from docker.io/library/tomcat
Digest: sha256:1c78db4bfbf4c75ab763dd463ff74731abbeadcfded2e03024b39cb0af3f1fb3
Status: Downloaded newer image for docker.io/tomcat:latest
e28763fdba2f98325249a7374394696fff59196ce114a4596fa51a74e22a2e39
[root@node5 ~]# 
[root@node5 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
e28763fdba2f        tomcat              "catalina.sh run"   49 seconds ago      Up 48 seconds       0.0.0.0:8080->8080/tcp   tomcat01

[root@node5 ~]# docker exec -it tomcat01 /bin/bash
root@e28763fdba2f:/usr/local/tomcat# ls
BUILDING.txt     LICENSE  README.md      RUNNING.txt  conf  logs            temp     webapps.dist
CONTRIBUTING.md  NOTICE   RELEASE-NOTES  bin          lib   native-jni-lib  webapps  work

# 1、linux命令少了很多,只保留最少可运行的程序
# 2、/usr/local/tomcat/webapps目录下没有网页文件，需要将webapps.dist目录下的文件拷贝到webapps里web前端才会显示tomcat相关内容。
```

