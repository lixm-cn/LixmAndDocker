## 底层原理

Docker是怎么工作的？

Docker是一个Client-Server结构的系统，Docker的守护进程运行在主机上。通过socks从客户端访问！

DockerServer接收Docker-Client的指令，再在后台执行对应命令！

Docker为什么比VM快？

1、Docker比虚拟机有更少的抽象层。

2、docker利用的是宿主机的内核，vm使用的是Guest OS的内核。所以说，新建一个容器的时候，docker不需要像虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机是加载GuestOS，分钟级别的，而docker是利用宿主机的操作系统吗，省略了这个复杂的过程，秒级！