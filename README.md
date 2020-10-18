# linuxLearning

> Base on Ubuntu

## 一、Set up environment

### 1.安装mongodb

- 自动

  - 通过apt安装mongodb

    ```bash
    root@ubuntu:~# sudo apt install mongodb
    ```

  - 查看mongo运行状态

    ```bash
    root@ubuntu:/# sudo systemctl status mongodb
    mongodb.service - An object/document-oriented database
         Loaded: loaded (/lib/systemd/system/mongodb.service; enabled; vendor prese>
         Active: active (running) since Sat 2020-10-17 11:06:45 UTC; 40s ago
           Docs: man:mongod(1)
       Main PID: 24925 (mongod)
          Tasks: 23 (limit: 1074)
         Memory: 42.5M
         CGroup: /system.slice/mongodb.service
                 └─24925 /usr/bin/mongod --unixSocketPrefix=/run/mongodb --config />
    ```

  - 执行mongo进入到mongo shell

    ```bash
    root@ubuntu:~# mongo
    MongoDB shell version v3.6.8
    connecting to: mongodb://127.0.0.1:27017
    Implicit session: session { "id" : UUID("e352ccdf-2188-4a51-ace3-1d8d93279e94") }
    MongoDB server version: 3.6.8
    Server has startup warnings: 
    2020-10-17T11:06:45.973+0000 I STORAGE  [initandlisten] 
    2020-10-17T11:06:45.973+0000 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
    2020-10-17T11:06:45.973+0000 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
    2020-10-17T11:06:48.006+0000 I CONTROL  [initandlisten] 
    2020-10-17T11:06:48.006+0000 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
    2020-10-17T11:06:48.006+0000 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
    2020-10-17T11:06:48.006+0000 I CONTROL  [initandlisten] 
    > 
    ```

- 手动

  - 下载mongodb安装包

    ```bash
    root@ubuntu:/usr/local/mongodb# sudo wget https://www.mongodb.com/dr/fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz
    --2020-10-18 11:30:30--  https://www.mongodb.com/dr/fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz
    Resolving www.mongodb.com (www.mongodb.com)... 13.226.238.19, 13.226.238.31, 13.226.238.92, ...
    Connecting to www.mongodb.com (www.mongodb.com)|13.226.238.19|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 76846 (75K) [text/html]
    Saving to: ‘mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz’
    
    mongodb-linux-x86_6 100%[===================>]  75.04K   344KB/s    in 0.2s    
    
    2020-10-18 11:30:31 (344 KB/s) - ‘mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz’ saved [76846/76846]
    
    root@ubuntu:/usr/local/mongodb# ls
    mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz
    ```

  - 解压

    ```bash
    root@ubuntu:/usr/local/mongodb# tar -zxvf mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz 
    mongodb-linux-x86_64-ubuntu2004-4.4.1/LICENSE-Community.txt
    mongodb-linux-x86_64-ubuntu2004-4.4.1/MPL-2
    mongodb-linux-x86_64-ubuntu2004-4.4.1/README
    mongodb-linux-x86_64-ubuntu2004-4.4.1/THIRD-PARTY-NOTICES
    mongodb-linux-x86_64-ubuntu2004-4.4.1/bin/install_compass
    mongodb-linux-x86_64-ubuntu2004-4.4.1/bin/mongo
    mongodb-linux-x86_64-ubuntu2004-4.4.1/bin/mongod
    mongodb-linux-x86_64-ubuntu2004-4.4.1/bin/mongos
    
    root@ubuntu:/usr/local/mongodb# ls
    mongodb-linux-x86_64-ubuntu2004-4.4.1  mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz
    
    root@ubuntu:/usr/local/mongodb# mkdir /home/download
    root@ubuntu:/usr/local/mongodb# mv mongodb-linux-x86_64-ubuntu2004-4.4.1 /home/download
    root@ubuntu:/usr/local/mongodb# mv mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz /home/download/
    root@ubuntu:/usr/local/mongodb# ls
    root@ubuntu:/usr/local/mongodb# cd /home/download/
    root@ubuntu:/home/download# ls
    mongodb-linux-x86_64-ubuntu2004-4.4.1  mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz
    root@ubuntu:/home/download# cp -rf mongodb-linux-x86_64-ubuntu2004-4.4.1/. /usr/local/mongodb/
    root@ubuntu:/home/download# cd /usr/local/mongodb/
    root@ubuntu:/usr/local/mongodb# ls
    LICENSE-Community.txt  MPL-2  README  THIRD-PARTY-NOTICES  bin
    ```

  - 将mongodb的目录地址添加到PATH路径中

    ```bash
    root@ubuntu:/usr/local/mongodb# export PATH=/usr/local/mongodb/bin:$PATH
    root@ubuntu:/usr/local/mongodb# echo $PATH
    /usr/local/mongodb/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
    ```

  - 创建mongodb的存储和日志目录,设置读写权限

    ```bash
    root@ubuntu:/usr/local/mongodb# sudo mkdir -p /var/lib/mongo
    root@ubuntu:/usr/local/mongodb# sudo mkdir -p /var/log/mongo
    root@ubuntu:/usr/local/mongodb# sudo chown `whoami` /var/lib/mongo
    root@ubuntu:/usr/local/mongodb# sudo chown `whoami` /var/log/mongo
    ```

  - 配置mongodb并启动

    ```bash
    root@ubuntu:/usr/local/mongodb/bin# vim mongodb.conf
    dbpath=/var/lib/mongo
    logpath=/var/log/mongo/mongodb.log
    port=27017
    fork=true
    #auth=true
    bind_ip=0.0.0.0
    
    root@ubuntu:/usr/local/mongodb/bin# mongod -f mongodb.conf
    ```

  - 查看端口映射检查是否启动成功

    ```bash
    root@ubuntu:/usr/local/mongodb/bin# netstat -nltp|grep mongod
    tcp        0      0 0.0.0.0:27017           0.0.0.0:*               LISTEN      2364/mongod
    ```

### 2.安装nginx

- 自动

  - 通过apt安装nginx

    ```bash
    root@ubuntu:~# sudo apt install nginx
    ```

  - 启动nginx服务(安装后默认自启,提示失败信息-Address already in use)

    ```bash
    root@ubuntu:~# sudo nginx
    nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
    nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
    nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
    nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
    nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
    nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
    nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
    nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
    nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
    nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
    nginx: [emerg] still could not bind()
    ```

  - 使用netstat -ltunp查看端口使用状态

    ```bash
    root@ubuntu:~# netstat -ltunp
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
    tcp        0      0 127.0.0.1:27017         0.0.0.0:*               LISTEN      24925/mongod        
    tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      40322/nginx: master 
    tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      466/systemd-resolve 
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      542/sshd: /usr/sbin 
    tcp6       0      0 :::80                   :::*                    LISTEN      40322/nginx: master 
    tcp6       0      0 :::22                   :::*                    LISTEN      542/sshd: /usr/sbin 
    udp        0      0 127.0.0.53:53           0.0.0.0:*                           466/systemd-resolve 
    udp        0      0 45.77.29.212:68         0.0.0.0:*                           345/systemd-network
    ```

  - 通过browser访问nginx服务

    ```
    Welcome to nginx!
    If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
    
    For online documentation and support please refer to nginx.org.
    Commercial support is available at nginx.com.
    
    Thank you for using nginx.
    ```

- 手动

  - 下载并解压nginx安装包

    ```bash
    root@ubuntu:/home/download# wget http://nginx.org/download/nginx-1.18.0.tar.gz
    --2020-10-18 12:54:31--  http://nginx.org/download/nginx-1.18.0.tar.gz
    Resolving nginx.org (nginx.org)... 3.125.197.172, 52.58.199.22, 2a05:d014:edb:5702::6, ...
    Connecting to nginx.org (nginx.org)|3.125.197.172|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 1039530 (1015K) [application/octet-stream]
    Saving to: ‘nginx-1.18.0.tar.gz’
    
    nginx-1.18.0.tar.gz 100%[===================>]   1015K   645KB/s    in 1.6s    
    
    2020-10-18 12:54:33 (645 KB/s) - ‘nginx-1.18.0.tar.gz’ saved [1039530/1039530]
    
    root@ubuntu:/home/download# ls
    mongodb-linux-x86_64-ubuntu2004-4.4.1      nginx-1.18.0.tar.gz
    mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz
    
    root@ubuntu:/home/download# sudo tar -zxvf nginx-1.18.0.tar.gz 
    root@ubuntu:/home/download# ls
    mongodb-linux-x86_64-ubuntu2004-4.4.1      nginx-1.18.0
    mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz  nginx-1.18.0.tar.gz
    ```

  - 编译安装

    ```bash
    root@ubuntu:/home/download# mkdir /usr/local/nginx
    root@ubuntu:/home/download# cp -rf nginx-1.18.0/. /usr/local/nginx/
    root@ubuntu:/home/download# cd /usr/local/nginx
    root@ubuntu:/usr/local/nginx# ls
    CHANGES     LICENSE  auto  configure  html  src
    CHANGES.ru  README   conf  contrib    man  
    root@ubuntu:/usr/local/nginx# ./configure 
    checking for OS
     + Linux 5.4.0-48-generic x86_64
    checking for C compiler ... not found
    #problem
    ./configure: error: C compiler cc is not found
    
    #solve [C compiler not found, Ubuntu]https://stackoverflow.com/questions/22557029/c-compiler-not-found-ubuntu
    root@ubuntu:/usr/local/nginx# sudo apt install build-essential
    
    root@ubuntu:/usr/local/nginx# ./configure 
    #...
    checking for PCRE library ... not found
    checking for PCRE library in /usr/local/ ... not found
    checking for PCRE library in /usr/include/pcre/ ... not found
    checking for PCRE library in /usr/pkg/ ... not found
    checking for PCRE library in /opt/local/ ... not found
    
    #problem
    ./configure: error: the HTTP rewrite module requires the PCRE library.
    You can either disable the module by using --without-http_rewrite_module
    option, or install the PCRE library into the system, or build the PCRE library
    statically from the source with nginx by using --with-pcre=<path> option.
    
    #solve [Ubuntu安装nginx, the HTTP rewrite module requires the PCRE library ]https://www.chengxulvtu.com/ubuntu%E5%AE%89%E8%A3%85nginx-the-http-rewrite-module-requires-the-pcre-library-%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95/
    root@ubuntu:/usr/local/nginx# sudo apt install libpcre3 libpcre3-dev openssl libssl-dev
    
    #problem
    checking for zlib library ... not found
    #solve [./configure: error: the HTTP gzip module requires the zlib library.]https://www.cnblogs.com/tinywan/p/6377066.html
    root@ubuntu:/usr/local/nginx# sudo apt-get install zlib1g-dev
    
    #success
    Configuration summary
      + using system PCRE library
      + OpenSSL library is not used
      + using system zlib library
    
      nginx path prefix: "/usr/local/nginx"
      nginx binary file: "/usr/local/nginx/sbin/nginx"
      nginx modules path: "/usr/local/nginx/modules"
      nginx configuration prefix: "/usr/local/nginx/conf"
      nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
      nginx pid file: "/usr/local/nginx/logs/nginx.pid"
      nginx error log file: "/usr/local/nginx/logs/error.log"
      nginx http access log file: "/usr/local/nginx/logs/access.log"
      nginx http client request body temporary files: "client_body_temp"
      nginx http proxy temporary files: "proxy_temp"
      nginx http fastcgi temporary files: "fastcgi_temp"
      nginx http uwsgi temporary files: "uwsgi_temp"
      nginx http scgi temporary files: "scgi_temp"
     
    root@ubuntu:/usr/local/nginx# make
    root@ubuntu:/usr/local/nginx# make install
    
    root@ubuntu:/usr/local/nginx# ls
    CHANGES     LICENSE   README  conf       contrib  man   sbin
    CHANGES.ru  Makefile  auto    configure  html     objs  src
    root@ubuntu:/usr/local/nginx# cd sbin/
    root@ubuntu:/usr/local/nginx/sbin# ls
    nginx
    root@ubuntu:/usr/local/nginx/sbin# export PATH=/usr/local/nginx/sbin:$PATH
    root@ubuntu:/usr/local/nginx/sbin# echo $PATH
    /usr/local/nginx/sbin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
    root@ubuntu:/usr/local/nginx/sbin# nginx -v
    nginx version: nginx/1.18.0
    ```

  - 启动

    ```bash
    root@ubuntu:/usr/local/nginx/sbin# nginx
    nginx: [alert] could not open error log file: open() "/usr/local/nginx/logs/error.log" failed (2: No such file or directory)
    2020/10/18 13:41:16 [emerg] 14989#0: open() "/usr/local/nginx/logs/access.log" failed (2: No such file or directory)
    root@ubuntu:/usr/local/nginx# mkdir logs
    root@ubuntu:/usr/local/nginx# nginx
    
    #使用ps查看运行进程的状态
    root@ubuntu:/usr/local/nginx# ps -e|grep nginx
      15004 ?        00:00:00 nginx
      15005 ?        00:00:00 nginx
    ```

### 3.安装redis

- 自动

  - 通过apt安装redis-server

    ```bash
    root@ubuntu:~# sudo apt install redis-server
    ```

  - 查看redis服务运行状态

    ```bash
    root@ubuntu:~# sudo systemctl status redis
    ● redis-server.service - Advanced key-value store
         Loaded: loaded (/lib/systemd/system/redis-server.service; enabled; vendor preset: enabled)
         Active: active (running) since Sun 2020-10-18 10:32:18 CST; 5min ago
           Docs: http://redis.io/documentation,
                 man:redis-server(1)
       Main PID: 41916 (redis-server)
          Tasks: 4 (limit: 1074)
         Memory: 1.8M
         CGroup: /system.slice/redis-server.service
                 └─41916 /usr/bin/redis-server 127.0.0.1:6379
    ```

- 手动

  - 下载并解压redis安装包

    ```bash
    root@ubuntu:/home/download# wget https://download.redis.io/releases/redis-6.0.8.tar.gz
    --2020-10-18 13:54:52--  https://download.redis.io/releases/redis-6.0.8.tar.gz
    Resolving download.redis.io (download.redis.io)... 45.60.125.1
    Connecting to download.redis.io (download.redis.io)|45.60.125.1|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 2247528 (2.1M) [application/octet-stream]
    Saving to: ‘redis-6.0.8.tar.gz’
    
    redis-6.0.8.tar.gz     100%[============================>]   2.14M  --.-KB/s    in 0.06s   
    
    2020-10-18 13:54:53 (35.9 MB/s) - ‘redis-6.0.8.tar.gz’ saved [2247528/2247528]
    
    root@ubuntu:/home/download# tar xzf redis-6.0.8.tar.gz
    root@ubuntu:/home/download# ls
    mongodb-linux-x86_64-ubuntu2004-4.4.1      nginx-1.18.0         redis-6.0.8
    mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz  nginx-1.18.0.tar.gz  redis-6.0.8.tar.gz
    ```

  - 安装

    ```bash
    root@ubuntu:/home/download# mkdir /usr/local/redis
    root@ubuntu:/home/download# cp -rf redis-6.0.8/. /usr/local/redis/
    root@ubuntu:/home/download# cd /usr/local/redis/
    root@ubuntu:/usr/local/redis# make
    root@ubuntu:/usr/local/redis# ls
    00-RELEASENOTES  COPYING    Makefile   deps        runtest-cluster    sentinel.conf  utils
    BUGS             INSTALL    README.md  redis.conf  runtest-moduleapi  src
    CONTRIBUTING     MANIFESTO  TLS.md     runtest     runtest-sentinel   tests
    
    root@ubuntu:/usr/local/redis/src# export PATH=/usr/local/redis/src:$PATH
    root@ubuntu:/usr/local/redis/src# echo $PATH
    /usr/local/redis/src:/usr/local/nginx/sbin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
    ```

  - 运行

    ```bash
    root@ubuntu:/usr/local/redis/src# redis-server
    19243:C 18 Oct 2020 14:12:23.706 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
    19243:C 18 Oct 2020 14:12:23.706 # Redis version=6.0.8, bits=64, commit=00000000, modified=0, pid=19243, just started
    19243:C 18 Oct 2020 14:12:23.706 # Configuration loaded
    19243:M 18 Oct 2020 14:12:23.707 * Increased maximum number of open files to 10032 (it was originally set to 1024).
                    _._                                                  
               _.-``__ ''-._                                             
          _.-``    `.  `_.  ''-._           Redis 6.0.8 (00000000/0) 64 bit
      .-`` .-```.  ```\/    _.,_ ''-._                                   
     (    '      ,       .-`  | `,    )     Running in standalone mode
     |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
     |    `-._   `._    /     _.-'    |     PID: 19243
      `-._    `-._  `-./  _.-'    _.-'                                   
     |`-._`-._    `-.__.-'    _.-'_.-'|                                  
     |    `-._`-._        _.-'_.-'    |           http://redis.io        
      `-._    `-._`-.__.-'_.-'    _.-'                                   
     |`-._`-._    `-.__.-'    _.-'_.-'|                                  
     |    `-._`-._        _.-'_.-'    |                                  
      `-._    `-._`-.__.-'_.-'    _.-'                                   
          `-._    `-.__.-'    _.-'                                       
              `-._        _.-'                                           
                  `-.__.-'                                               
    
    19243:M 18 Oct 2020 14:12:23.708 # Server initialized
    19243:M 18 Oct 2020 14:12:23.708 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
    19243:M 18 Oct 2020 14:12:23.709 * Ready to accept connections
    
    ^C19243:signal-handler (1603001634) Received SIGINT scheduling shutdown...
    19243:M 18 Oct 2020 14:13:54.583 # User requested shutdown...
    19243:M 18 Oct 2020 14:13:54.583 * Saving the final RDB snapshot before exiting.
    19243:M 18 Oct 2020 14:13:54.584 * DB saved on disk
    19243:M 18 Oct 2020 14:13:54.584 * Removing the pid file.
    19243:M 18 Oct 2020 14:13:54.584 # Redis is now ready to exit, bye bye...
    
    root@ubuntu:/usr/local/redis# ps aux | grep redis-server 
    root       19238  0.0  0.0  11072   732 pts/2    S+   14:09   0:00 grep --color=auto redis-server
    
    root@ubuntu:/usr/local/redis# vim redis.conf
    #...
    # By default Redis does not run as a daemon. Use 'yes' if you need it.
    # Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
    daemonize yes
    
    root@ubuntu:/usr/local/redis# redis-server redis.conf
    
    root@ubuntu:/usr/local/redis# ps aux | grep redis-server
    root       19261  0.1  0.3  56532  3724 ?        Ssl  14:19   0:00 redis-server 127.0.0.1:6379
    root       19267  0.0  0.0  11072   736 pts/2    S+   14:20   0:00 grep --color=auto redis-server
    
    root@ubuntu:/usr/local/redis# redis-cli
    127.0.0.1:6379> 
    ```

    

### 4.安装rabbitmq

> 由于RabbitMQ需要erlang语言的支持，在安装RabbitMQ之前需要安装erlang
>
> root@ubuntu:~# sudo apt install erlang
>
> root@ubuntu:~# erl
>
> Erlang/OTP 22 [erts-10.6.4] [source] [64-bit] [smp:1:1] [ds:1:1:10] [async-threads:1]
>
> 
>
> Eshell V10.6.4 (abort with ^G)
>
> 1> 

- 自动

  - 通过apt安装rabbitmq-server

    ```bash
    root@ubuntu:~# sudo apt install rabbitmq-server
    ```

  - 查看rabbitmq服务状态

    ```bash
    root@ubuntu:~# sudo service rabbitmq-server status
    ● rabbitmq-server.service - RabbitMQ Messaging Server
         Loaded: loaded (/lib/systemd/system/rabbitmq-server.service; enabled; vendor preset: enabled)
         Active: active (running) since Sun 2020-10-18 10:46:16 CST; 2min 48s ago
       Main PID: 43715 (beam.smp)
         Status: "Initialized"
          Tasks: 85 (limit: 1074)
         Memory: 85.3M
         CGroup: /system.slice/rabbitmq-server.service
                 ├─43711 /bin/sh /usr/sbin/rabbitmq-server
                 ├─43715 /usr/lib/erlang/erts-10.6.4/bin/beam.smp -W w -A 64 -MBas ageffcbf -MHas ageffcbf>
                 ├─43964 erl_child_setup 65536
                 ├─43991 inet_gethost 4
                 └─43992 inet_gethost 4
    ```

- 手动

  - 下载rabbitmq安装包

    ```bash
    root@ubuntu:/home/download# wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.9/rabbitmq-server_3.8.9-1_all.deb
    --2020-10-18 14:44:55--  https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.9/rabbitmq-server_3.8.9-1_all.deb
    Resolving github.com (github.com)... 52.78.231.108
    Connecting to github.com (github.com)|52.78.231.108|:443... connected.
    HTTP request sent, awaiting response... 302 Found
    Location: https://github-production-release-asset-2e65be.s3.amazonaws.com/924551/62e50a00-feb3-11ea-9151-a8e971cdf906?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20201018%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20201018T064455Z&X-Amz-Expires=300&X-Amz-Signature=595467094eb419659f7714328a0ae2cd6dcdc3fde94bae28a5a1be372a7e09bc&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=924551&response-content-disposition=attachment%3B%20filename%3Drabbitmq-server_3.8.9-1_all.deb&response-content-type=application%2Foctet-stream [following]
    --2020-10-18 14:44:55--  https://github-production-release-asset-2e65be.s3.amazonaws.com/924551/62e50a00-feb3-11ea-9151-a8e971cdf906?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20201018%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20201018T064455Z&X-Amz-Expires=300&X-Amz-Signature=595467094eb419659f7714328a0ae2cd6dcdc3fde94bae28a5a1be372a7e09bc&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=924551&response-content-disposition=attachment%3B%20filename%3Drabbitmq-server_3.8.9-1_all.deb&response-content-type=application%2Foctet-stream
    Resolving github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)... 52.216.107.92
    Connecting to github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)|52.216.107.92|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 15369338 (15M) [application/octet-stream]
    Saving to: ‘rabbitmq-server_3.8.9-1_all.deb’
    
    rabbitmq-server_3.8 100%[===================>]  14.66M  5.91MB/s    in 2.5s    
    
    2020-10-18 14:44:58 (5.91 MB/s) - ‘rabbitmq-server_3.8.9-1_all.deb’ saved [15369338/15369338]
    
    root@ubuntu:/home/download# ls
    mongodb-linux-x86_64-ubuntu2004-4.4.1      rabbitmq-server_3.8.9-1_all.deb
    mongodb-linux-x86_64-ubuntu2004-4.4.1.tgz  redis-6.0.8
    nginx-1.18.0                               redis-6.0.8.tar.gz
    nginx-1.18.0.tar.gz
    ```

  - 解压

    ```bash
    root@ubuntu:/home/download# sudo dpkg -i rabbitmq-server_3.8.9-1_all.deb
    Selecting previously unselected package rabbitmq-server.
    (Reading database ... 143918 files and directories currently installed.)
    Preparing to unpack rabbitmq-server_3.8.9-1_all.deb ...
    Unpacking rabbitmq-server (3.8.9-1) ...
    #error
    dpkg: dependency problems prevent configuration of rabbitmq-server:
     rabbitmq-server depends on socat; however:
      Package socat is not installed.
    
    dpkg: error processing package rabbitmq-server (--install):
     dependency problems - leaving unconfigured
    Processing triggers for man-db (2.9.1-1) ...
    Processing triggers for systemd (245.4-4ubuntu3.2) ...
    Errors were encountered while processing:
     rabbitmq-server
    
    #solve: nstall dependencies manually
    root@ubuntu:/home/download# sudo apt -y install socat logrotate init-system-helpers adduser
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    adduser is already the newest version (3.118ubuntu2).
    init-system-helpers is already the newest version (1.57).
    init-system-helpers set to manually installed.
    logrotate is already the newest version (3.14.0-4ubuntu3).
    logrotate set to manually installed.
    The following NEW packages will be installed:
      socat
    0 upgraded, 1 newly installed, 0 to remove and 17 not upgraded.
    1 not fully installed or removed.
    Need to get 323 kB of archives.
    After this operation, 1394 kB of additional disk space will be used.
    Get:1 http://archive.ubuntu.com/ubuntu focal/main amd64 socat amd64 1.7.3.3-2 [323 kB]
    Fetched 323 kB in 2s (182 kB/s) 
    Selecting previously unselected package socat.
    (Reading database ... 144044 files and directories currently installed.)
    Preparing to unpack .../socat_1.7.3.3-2_amd64.deb ...
    Unpacking socat (1.7.3.3-2) ...
    Setting up socat (1.7.3.3-2) ...
    Setting up rabbitmq-server (3.8.9-1) ...
    Adding group `rabbitmq' (GID 131) ...
    Done.
    Adding system user `rabbitmq' (UID 123) ...
    Adding new user `rabbitmq' (UID 123) with group `rabbitmq' ...
    Not creating home directory `/var/lib/rabbitmq'.
    Created symlink /etc/systemd/system/multi-user.target.wants/rabbitmq-server.service → /lib/systemd/system/rabbitmq-server.service.
    Processing triggers for man-db (2.9.1-1) ...
    Processing triggers for systemd (245.4-4ubuntu3.2) ...
    
    root@ubuntu:/home/download# sudo dpkg -i rabbitmq-server_3.8.9-1_all.deb
    (Reading database ... 144084 files and directories currently installed.)
    Preparing to unpack rabbitmq-server_3.8.9-1_all.deb ...
    Unpacking rabbitmq-server (3.8.9-1) over (3.8.9-1) ...
    Setting up rabbitmq-server (3.8.9-1) ...
    Processing triggers for man-db (2.9.1-1) ...
    Processing triggers for systemd (245.4-4ubuntu3.2) ...
    ```

  - 运行

    ```bash
    root@ubuntu:/home/download# service rabbitmq-server start
    root@ubuntu:/home/download# sudo service rabbitmq-server status
    ● rabbitmq-server.service - RabbitMQ broker
         Loaded: loaded (/lib/systemd/system/rabbitmq-server.service; enabled; vendor preset: enabled)
         Active: active (running) since Sun 2020-10-18 14:53:27 CST; 1min 23s ago
       Main PID: 33984 (beam.smp)
         Status: "Initialized"
          Tasks: 84 (limit: 1074)
         Memory: 73.4M
         CGroup: /system.slice/rabbitmq-server.service
                 ├─33984 /usr/lib/erlang/erts-10.6.4/bin/beam.smp -W w -K true -A 64 -MBas ageffcbf -MHas ageffcbf>
                 ├─34082 erl_child_setup 32768
                 ├─34128 inet_gethost 4
                 └─34129 inet_gethost 4
    ```

    

    