## docker compose安装wordpress

环境搭建步骤：

1、下载程序；

2、安装 数据库；

3、配置...

compose应用 => 一键启动

1. 下载项目(docker-compose.yml)
2. 如果需要文件。Dockerfile
3. 文件准备齐全(直接一键启动项目)。

```yml
# create docker-compose.yml
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```

```shell
# 执行compose
[root@node61 workspace]# docker-compose up
Creating network "workspace_default" with the default driver
Creating volume "workspace_db_data" with default driver
Pulling db (mysql:5.7)...
Trying to pull repository docker.io/library/mysql ... 
5.7: Pulling from docker.io/library/mysql
bb79b6b2107f: Pull complete
49e22f6fb9f7: Pull complete
842b1255668c: Pull complete
9f48d1f43000: Pull complete
c693f0615bce: Pull complete
8a621b9dbed2: Pull complete
0807d32aef13: Pull complete
f15d42f48bd9: Pull complete
098ceecc0c8d: Pull complete
b6fead9737bc: Pull complete
351d223d3d76: Pull complete
Digest: sha256:4d2b34e99c14edb99cdd95ddad4d9aa7ea3f2c4405ff0c3509a29dc40bcb10ef
Status: Downloaded newer image for docker.io/mysql:5.7
Pulling wordpress (wordpress:latest)...
Trying to pull repository docker.io/library/wordpress ... 
latest: Pulling from docker.io/library/wordpress
bb79b6b2107f: Already exists
80f7a64e4b25: Pull complete
da391f3e81f0: Pull complete
8199ae3052e1: Pull complete
284fd0f314b2: Pull complete
f38db365cd8a: Pull complete
1416a501db13: Pull complete
1a45b5b978cd: Pull complete
c662caa8d2ec: Pull complete
2db216a7247d: Pull complete
d23772456121: Pull complete
3c068acf6c51: Pull complete
2980002e0c52: Pull complete
b6ddfa4e307a: Pull complete
2553391243c3: Pull complete
a3c2b330e95d: Pull complete
bc1e61f6eb18: Pull complete
033f43da5ae7: Pull complete
277905382399: Pull complete
a1b0f1f9235d: Pull complete
Digest: sha256:11ac02124f5e1d7dedd5ea1277f2a5ec2f7ec808c9969449635eddff72740a4b
Status: Downloaded newer image for docker.io/wordpress:latest
Creating workspace_db_1 ... done
Creating workspace_wordpress_1 ... done
Attaching to workspace_db_1, workspace_wordpress_1
db_1         | 2020-10-27 03:33:59+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.32-1debian10 started.
wordpress_1  | WordPress not found in /var/www/html - copying now...
db_1         | 2020-10-27 03:33:59+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
db_1         | 2020-10-27 03:33:59+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.32-1debian10 started.
db_1         | 2020-10-27 03:33:59+00:00 [Note] [Entrypoint]: Initializing database files
db_1         | 2020-10-27T03:33:59.802153Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
db_1         | 2020-10-27T03:34:00.084298Z 0 [Warning] InnoDB: New log files created, LSN=45790
db_1         | 2020-10-27T03:34:00.132126Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
db_1         | 2020-10-27T03:34:00.191313Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 408e7468-1805-11eb-9b1a-0242ac120002.
db_1         | 2020-10-27T03:34:00.194065Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
wordpress_1  | Complete! WordPress has been successfully copied to /var/www/html
wordpress_1  | [27-Oct-2020 03:34:01 UTC] PHP Warning:  mysqli::__construct(): (HY000/2002): Connection refused in Standard input code on line 22
wordpress_1  | 
wordpress_1  | MySQL Connection Error: (2002) Connection refused
db_1         | 2020-10-27T03:34:01.455305Z 0 [Warning] CA certificate ca.pem is self signed.
db_1         | 2020-10-27T03:34:02.259180Z 1 [Warning] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
wordpress_1  | 
wordpress_1  | MySQL Connection Error: (2002) Connection refused
db_1         | 2020-10-27 03:34:04+00:00 [Note] [Entrypoint]: Database files initialized
db_1         | 2020-10-27 03:34:04+00:00 [Note] [Entrypoint]: Starting temporary server
db_1         | 2020-10-27 03:34:04+00:00 [Note] [Entrypoint]: Waiting for server startup
db_1         | 2020-10-27T03:34:04.978091Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
db_1         | 2020-10-27T03:34:04.981527Z 0 [Note] mysqld (mysqld 5.7.32) starting as process 80 ...
db_1         | 2020-10-27T03:34:04.987014Z 0 [Note] InnoDB: PUNCH HOLE support available
db_1         | 2020-10-27T03:34:04.987063Z 0 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
db_1         | 2020-10-27T03:34:04.987075Z 0 [Note] InnoDB: Uses event mutexes
db_1         | 2020-10-27T03:34:04.987087Z 0 [Note] InnoDB: GCC builtin __atomic_thread_fence() is used for memory barrier
db_1         | 2020-10-27T03:34:04.987097Z 0 [Note] InnoDB: Compressed tables use zlib 1.2.11
db_1         | 2020-10-27T03:34:04.987115Z 0 [Note] InnoDB: Using Linux native AIO
db_1         | 2020-10-27T03:34:04.987612Z 0 [Note] InnoDB: Number of pools: 1
db_1         | 2020-10-27T03:34:04.987879Z 0 [Note] InnoDB: Using CPU crc32 instructions
db_1         | 2020-10-27T03:34:04.990926Z 0 [Note] InnoDB: Initializing buffer pool, total size = 128M, instances = 1, chunk size = 128M
db_1         | 2020-10-27T03:34:05.006976Z 0 [Note] InnoDB: Completed initialization of buffer pool
db_1         | 2020-10-27T03:34:05.010854Z 0 [Note] InnoDB: If the mysqld execution user is authorized, page cleaner thread priority can be changed. See the man page of setpriority().
db_1         | 2020-10-27T03:34:05.024266Z 0 [Note] InnoDB: Highest supported file format is Barracuda.
db_1         | 2020-10-27T03:34:05.036909Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
db_1         | 2020-10-27T03:34:05.037046Z 0 [Note] InnoDB: Setting file './ibtmp1' size to 12 MB. Physically writing the file full; Please wait ...
db_1         | 2020-10-27T03:34:05.063251Z 0 [Note] InnoDB: File './ibtmp1' size is now 12 MB.
db_1         | 2020-10-27T03:34:05.064230Z 0 [Note] InnoDB: 96 redo rollback segment(s) found. 96 redo rollback segment(s) are active.
db_1         | 2020-10-27T03:34:05.064255Z 0 [Note] InnoDB: 32 non-redo rollback segment(s) are active.
db_1         | 2020-10-27T03:34:05.065597Z 0 [Note] InnoDB: Waiting for purge to start
db_1         | 2020-10-27T03:34:05.115845Z 0 [Note] InnoDB: 5.7.32 started; log sequence number 2746711
db_1         | 2020-10-27T03:34:05.116129Z 0 [Note] InnoDB: Loading buffer pool(s) from /var/lib/mysql/ib_buffer_pool
db_1         | 2020-10-27T03:34:05.117685Z 0 [Note] Plugin 'FEDERATED' is disabled.
db_1         | 2020-10-27T03:34:05.119796Z 0 [Note] InnoDB: Buffer pool(s) load completed at 201027  3:34:05
db_1         | 2020-10-27T03:34:05.127614Z 0 [Note] Found ca.pem, server-cert.pem and server-key.pem in data directory. Trying to enable SSL support using them.
db_1         | 2020-10-27T03:34:05.127651Z 0 [Note] Skipping generation of SSL certificates as certificate files are present in data directory.
db_1         | 2020-10-27T03:34:05.128905Z 0 [Warning] CA certificate ca.pem is self signed.
db_1         | 2020-10-27T03:34:05.128996Z 0 [Note] Skipping generation of RSA key pair as key files are present in data directory.
db_1         | 2020-10-27T03:34:05.130920Z 0 [Warning] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
db_1         | 2020-10-27T03:34:05.152092Z 0 [Note] Event Scheduler: Loaded 0 events
db_1         | 2020-10-27T03:34:05.152504Z 0 [Note] mysqld: ready for connections.
db_1         | Version: '5.7.32'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server (GPL)
db_1         | 2020-10-27 03:34:05+00:00 [Note] [Entrypoint]: Temporary server started.
db_1         | Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
db_1         | Warning: Unable to load '/usr/share/zoneinfo/leap-seconds.list' as time zone. Skipping it.
wordpress_1  | 
wordpress_1  | MySQL Connection Error: (2002) Connection refused
db_1         | Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
db_1         | Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
db_1         | 2020-10-27 03:34:08+00:00 [Note] [Entrypoint]: Creating database wordpress
db_1         | 2020-10-27 03:34:08+00:00 [Note] [Entrypoint]: Creating user wordpress
db_1         | 2020-10-27 03:34:08+00:00 [Note] [Entrypoint]: Giving user wordpress access to schema wordpress
db_1         | 
db_1         | 2020-10-27 03:34:08+00:00 [Note] [Entrypoint]: Stopping temporary server
db_1         | 2020-10-27T03:34:08.327496Z 0 [Note] Giving 0 client threads a chance to die gracefully
db_1         | 2020-10-27T03:34:08.327535Z 0 [Note] Shutting down slave threads
db_1         | 2020-10-27T03:34:08.327555Z 0 [Note] Forcefully disconnecting 0 remaining clients
db_1         | 2020-10-27T03:34:08.327569Z 0 [Note] Event Scheduler: Purging the queue. 0 events
db_1         | 2020-10-27T03:34:08.327649Z 0 [Note] Binlog end
db_1         | 2020-10-27T03:34:08.328414Z 0 [Note] Shutting down plugin 'ngram'
db_1         | 2020-10-27T03:34:08.328446Z 0 [Note] Shutting down plugin 'partition'
db_1         | 2020-10-27T03:34:08.328454Z 0 [Note] Shutting down plugin 'BLACKHOLE'
db_1         | 2020-10-27T03:34:08.328466Z 0 [Note] Shutting down plugin 'ARCHIVE'
db_1         | 2020-10-27T03:34:08.328472Z 0 [Note] Shutting down plugin 'PERFORMANCE_SCHEMA'
db_1         | 2020-10-27T03:34:08.328513Z 0 [Note] Shutting down plugin 'MRG_MYISAM'
db_1         | 2020-10-27T03:34:08.328535Z 0 [Note] Shutting down plugin 'MyISAM'
db_1         | 2020-10-27T03:34:08.328556Z 0 [Note] Shutting down plugin 'INNODB_SYS_VIRTUAL'
db_1         | 2020-10-27T03:34:08.328570Z 0 [Note] Shutting down plugin 'INNODB_SYS_DATAFILES'
db_1         | 2020-10-27T03:34:08.328582Z 0 [Note] Shutting down plugin 'INNODB_SYS_TABLESPACES'
db_1         | 2020-10-27T03:34:08.328594Z 0 [Note] Shutting down plugin 'INNODB_SYS_FOREIGN_COLS'
db_1         | 2020-10-27T03:34:08.328606Z 0 [Note] Shutting down plugin 'INNODB_SYS_FOREIGN'
db_1         | 2020-10-27T03:34:08.328619Z 0 [Note] Shutting down plugin 'INNODB_SYS_FIELDS'
db_1         | 2020-10-27T03:34:08.328625Z 0 [Note] Shutting down plugin 'INNODB_SYS_COLUMNS'
db_1         | 2020-10-27T03:34:08.328635Z 0 [Note] Shutting down plugin 'INNODB_SYS_INDEXES'
db_1         | 2020-10-27T03:34:08.328641Z 0 [Note] Shutting down plugin 'INNODB_SYS_TABLESTATS'
db_1         | 2020-10-27T03:34:08.328650Z 0 [Note] Shutting down plugin 'INNODB_SYS_TABLES'
db_1         | 2020-10-27T03:34:08.328656Z 0 [Note] Shutting down plugin 'INNODB_FT_INDEX_TABLE'
db_1         | 2020-10-27T03:34:08.328666Z 0 [Note] Shutting down plugin 'INNODB_FT_INDEX_CACHE'
db_1         | 2020-10-27T03:34:08.328672Z 0 [Note] Shutting down plugin 'INNODB_FT_CONFIG'
db_1         | 2020-10-27T03:34:08.328680Z 0 [Note] Shutting down plugin 'INNODB_FT_BEING_DELETED'
db_1         | 2020-10-27T03:34:08.328686Z 0 [Note] Shutting down plugin 'INNODB_FT_DELETED'
db_1         | 2020-10-27T03:34:08.328709Z 0 [Note] Shutting down plugin 'INNODB_FT_DEFAULT_STOPWORD'
db_1         | 2020-10-27T03:34:08.328721Z 0 [Note] Shutting down plugin 'INNODB_METRICS'
db_1         | 2020-10-27T03:34:08.328728Z 0 [Note] Shutting down plugin 'INNODB_TEMP_TABLE_INFO'
db_1         | 2020-10-27T03:34:08.328736Z 0 [Note] Shutting down plugin 'INNODB_BUFFER_POOL_STATS'
db_1         | 2020-10-27T03:34:08.328742Z 0 [Note] Shutting down plugin 'INNODB_BUFFER_PAGE_LRU'
db_1         | 2020-10-27T03:34:08.328752Z 0 [Note] Shutting down plugin 'INNODB_BUFFER_PAGE'
db_1         | 2020-10-27T03:34:08.328758Z 0 [Note] Shutting down plugin 'INNODB_CMP_PER_INDEX_RESET'
db_1         | 2020-10-27T03:34:08.328766Z 0 [Note] Shutting down plugin 'INNODB_CMP_PER_INDEX'
db_1         | 2020-10-27T03:34:08.328772Z 0 [Note] Shutting down plugin 'INNODB_CMPMEM_RESET'
db_1         | 2020-10-27T03:34:08.328781Z 0 [Note] Shutting down plugin 'INNODB_CMPMEM'
db_1         | 2020-10-27T03:34:08.328787Z 0 [Note] Shutting down plugin 'INNODB_CMP_RESET'
db_1         | 2020-10-27T03:34:08.328795Z 0 [Note] Shutting down plugin 'INNODB_CMP'
db_1         | 2020-10-27T03:34:08.328801Z 0 [Note] Shutting down plugin 'INNODB_LOCK_WAITS'
db_1         | 2020-10-27T03:34:08.328810Z 0 [Note] Shutting down plugin 'INNODB_LOCKS'
db_1         | 2020-10-27T03:34:08.328816Z 0 [Note] Shutting down plugin 'INNODB_TRX'
db_1         | 2020-10-27T03:34:08.328826Z 0 [Note] Shutting down plugin 'InnoDB'
db_1         | 2020-10-27T03:34:08.328995Z 0 [Note] InnoDB: FTS optimize thread exiting.
db_1         | 2020-10-27T03:34:08.329289Z 0 [Note] InnoDB: Starting shutdown...
db_1         | 2020-10-27T03:34:08.429622Z 0 [Note] InnoDB: Dumping buffer pool(s) to /var/lib/mysql/ib_buffer_pool
db_1         | 2020-10-27T03:34:08.430202Z 0 [Note] InnoDB: Buffer pool(s) dump completed at 201027  3:34:08
db_1         | 2020-10-27T03:34:09.938933Z 0 [Note] InnoDB: Shutdown completed; log sequence number 12604872
db_1         | 2020-10-27T03:34:09.941367Z 0 [Note] InnoDB: Removed temporary tablespace data file: "ibtmp1"
db_1         | 2020-10-27T03:34:09.941430Z 0 [Note] Shutting down plugin 'MEMORY'
db_1         | 2020-10-27T03:34:09.941461Z 0 [Note] Shutting down plugin 'CSV'
db_1         | 2020-10-27T03:34:09.941474Z 0 [Note] Shutting down plugin 'sha256_password'
db_1         | 2020-10-27T03:34:09.941481Z 0 [Note] Shutting down plugin 'mysql_native_password'
db_1         | 2020-10-27T03:34:09.941652Z 0 [Note] Shutting down plugin 'binlog'
db_1         | 2020-10-27T03:34:09.942421Z 0 [Note] mysqld: Shutdown complete
db_1         | 
wordpress_1  | 
wordpress_1  | MySQL Connection Error: (2002) Connection refused
db_1         | 2020-10-27 03:34:10+00:00 [Note] [Entrypoint]: Temporary server stopped
db_1         | 
db_1         | 2020-10-27 03:34:10+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.
db_1         | 
db_1         | 2020-10-27T03:34:10.542885Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
db_1         | 2020-10-27T03:34:10.545830Z 0 [Note] mysqld (mysqld 5.7.32) starting as process 1 ...
db_1         | 2020-10-27T03:34:10.551191Z 0 [Note] InnoDB: PUNCH HOLE support available
db_1         | 2020-10-27T03:34:10.551234Z 0 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
db_1         | 2020-10-27T03:34:10.551249Z 0 [Note] InnoDB: Uses event mutexes
db_1         | 2020-10-27T03:34:10.551259Z 0 [Note] InnoDB: GCC builtin __atomic_thread_fence() is used for memory barrier
db_1         | 2020-10-27T03:34:10.551268Z 0 [Note] InnoDB: Compressed tables use zlib 1.2.11
db_1         | 2020-10-27T03:34:10.551281Z 0 [Note] InnoDB: Using Linux native AIO
db_1         | 2020-10-27T03:34:10.551800Z 0 [Note] InnoDB: Number of pools: 1
db_1         | 2020-10-27T03:34:10.552056Z 0 [Note] InnoDB: Using CPU crc32 instructions
db_1         | 2020-10-27T03:34:10.555208Z 0 [Note] InnoDB: Initializing buffer pool, total size = 128M, instances = 1, chunk size = 128M
db_1         | 2020-10-27T03:34:10.569060Z 0 [Note] InnoDB: Completed initialization of buffer pool
db_1         | 2020-10-27T03:34:10.572794Z 0 [Note] InnoDB: If the mysqld execution user is authorized, page cleaner thread priority can be changed. See the man page of setpriority().
db_1         | 2020-10-27T03:34:10.588059Z 0 [Note] InnoDB: Highest supported file format is Barracuda.
db_1         | 2020-10-27T03:34:10.601004Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
db_1         | 2020-10-27T03:34:10.601145Z 0 [Note] InnoDB: Setting file './ibtmp1' size to 12 MB. Physically writing the file full; Please wait ...
db_1         | 2020-10-27T03:34:10.627452Z 0 [Note] InnoDB: File './ibtmp1' size is now 12 MB.
db_1         | 2020-10-27T03:34:10.628464Z 0 [Note] InnoDB: 96 redo rollback segment(s) found. 96 redo rollback segment(s) are active.
db_1         | 2020-10-27T03:34:10.628495Z 0 [Note] InnoDB: 32 non-redo rollback segment(s) are active.
db_1         | 2020-10-27T03:34:10.630064Z 0 [Note] InnoDB: 5.7.32 started; log sequence number 12604872
db_1         | 2020-10-27T03:34:10.630684Z 0 [Note] InnoDB: Loading buffer pool(s) from /var/lib/mysql/ib_buffer_pool
db_1         | 2020-10-27T03:34:10.631216Z 0 [Note] Plugin 'FEDERATED' is disabled.
db_1         | 2020-10-27T03:34:10.637382Z 0 [Note] InnoDB: Buffer pool(s) load completed at 201027  3:34:10
db_1         | 2020-10-27T03:34:10.640482Z 0 [Note] Found ca.pem, server-cert.pem and server-key.pem in data directory. Trying to enable SSL support using them.
db_1         | 2020-10-27T03:34:10.640527Z 0 [Note] Skipping generation of SSL certificates as certificate files are present in data directory.
db_1         | 2020-10-27T03:34:10.641757Z 0 [Warning] CA certificate ca.pem is self signed.
db_1         | 2020-10-27T03:34:10.641853Z 0 [Note] Skipping generation of RSA key pair as key files are present in data directory.
db_1         | 2020-10-27T03:34:10.642832Z 0 [Note] Server hostname (bind-address): '*'; port: 3306
db_1         | 2020-10-27T03:34:10.642901Z 0 [Note] IPv6 is available.
db_1         | 2020-10-27T03:34:10.642924Z 0 [Note]   - '::' resolves to '::';
db_1         | 2020-10-27T03:34:10.642976Z 0 [Note] Server socket created on IP: '::'.
db_1         | 2020-10-27T03:34:10.644525Z 0 [Warning] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
db_1         | 2020-10-27T03:34:10.667213Z 0 [Note] Event Scheduler: Loaded 0 events
db_1         | 2020-10-27T03:34:10.667576Z 0 [Note] mysqld: ready for connections.
db_1         | Version: '5.7.32'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
workspace_wordpress_1 exited with code 1
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
workspace_wordpress_1 exited with code 1
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
workspace_wordpress_1 exited with code 1
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
workspace_wordpress_1 exited with code 1
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
workspace_wordpress_1 exited with code 1
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
workspace_wordpress_1 exited with code 1
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
workspace_wordpress_1 exited with code 1
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
workspace_wordpress_1 exited with code 1
wordpress_1  | AH00534: apache2: Configuration error: No MPM loaded.
workspace_wordpress_1 exited with code 1
^CGracefully stopping... (press Ctrl+C again to force)
Stopping workspace_wordpress_1 ... done
Stopping workspace_db_1        ... done
```

