## Portainer可视化管理docker

```shell
docker run -d -p 9000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

Rancher(CI/CD)

什么是Portainer？

Docker图形化管理工具，提供一个后台管理界面供我们操作。

