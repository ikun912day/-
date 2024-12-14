## NGINX

### 一、安装方式

##### 编译安装

##### 1.下载依赖包

```
#Ubuntu22.04和Ubuntu20.04
apt update && apt -y install gcc make libpcre3 libpcre3-dev openssl libssl-dev zlib1g-dev

#centos8
yum -y install gcc pcre-devel openssl-devel zlib-devel

#rocky8
yum -y install gcc make gcc-c++ libtool pcre pcre-devel zlib zlib-devel openssl openssl-devel perl-ExtUtils-Embed 
```

##### 2、创建nginx用户

```
#-s用来指定登录的shell，/sbin/nologin表示不能登录来交互的访问系统，-M表示不创建家目录
useradd -s /sbin/nologin nginx -M
```

##### 3、在官网下载nginx源码包并解压

```
#/usr/local/src/这个目录通常用于存放一些源码包，所以我放这里。可以放到其它目录下不影响。
cd /usr/local/src/
#可以到nginx官网去选其它版本，注意要偶数版的，因为是稳定版，基数版是测试版
wget http://nginx.org/download/nginx-1.18.0.tar.gz 
tar -xf nginx-1.18.0.tar.gz
```

##### 4、进入到nginx-1.18.0下执行configure命令

```
cd nginx-1.18.0/

#--prefix=/apps/nginx   指定安装nginx的目录，包含nginx的二进制文件，配置文件，日志文件，模块文件。并且这个文件会自动生成，不需要手动创建。
#--user=nginx			指定以nginx这个用户来运行nginx
#--group=nginx			指定以nginx这个组来运行nginx
#--with-http_ssl_module 启用HTTP SSL模块，让nginx支持HTTPS协议，也就是加密的HTTP通信
#--with-http_v2_module  启用HTTP/2模块，让nginx支持HTTP/2协议，也就是更高效的HTTP通信
#--with-http_realip_module 启用HTTP Real IP模块，这个模块可以让nginx获取客户端的真实IP地址，而不是代理服务器的IP地址。你需要在配置文件中指定哪些代理服务器是可信的，并设置相应的头部字段
#--with-http_stub_status_module 启用HTTP Stub Status模块，这个模块可以让nginx提供一个简单的状态页面，显示一些基本的统计信息，比如连接数，请求数等。你需要在配置文件中指定一个location来访问这个状态页面，并设置相应的权限
#--with-http_gzip_static_module 启用HTTP Gzip Static模块，这个模块可以让nginx直接发送预压缩的静态文件，而不是动态压缩它们。这样可以节省CPU资源和带宽，提高响应速度。你需要事先压缩好静态文件，并在配置文件中开启gzip_static指令
#--with-pcre  PCRE库来支持正则表达式的匹配和重写功能,先安装好PCRE库，在配置文件中使用相应的指令。
#--with-stream 启用Stream Core模块，这个模块可以让nginx支持TCP/UDP代理功能。你需要在配置文件中使用stream{}块来定义代理服务器和上游服务器
#--with-stream_ssl_module 启用Stream SSL模块，这个模块可以让nginx支持加密的TCP/UDP代理功能
#-with-stream_realip_module 启用Stream Real IP模块，这个模块可以让nginx获取TCP/UDP客户端的真实IP地址，而不是代理服务器的IP地址。你需要在配置文件中指定哪些代理服务器是可信的，并设置相应的头部字段
#--with-cc-opt=-Wno-error=deprecated-declarations 表示你想给编译器传递一个参数-Wno-error=deprecated-declarations，这个参数可以忽略一些废弃函数的错误提示，比如OpenSSL 3.0中废弃了一些引擎相关的函数
./configure --prefix=/apps/nginx \--user=nginx \--group=nginx \--with-http_ssl_module \--with-http_v2_module \--with-http_realip_module \--with-http_stub_status_module \--with-http_gzip_static_module \--with-pcre \--with-stream \--with-stream_ssl_module \--with-stream_realip_module \--with-cc-opt=-Wno-error=deprecated-declarations

```

注意:

a、Makefile中保存了编译和链接的规则和命令。在执行configure脚本时，它会根据你指定的编译选项和参数，用nginx-1.18.0/objsauto/make生成一个Makefile文件，这个文件是最终用来执行make命令的。

b、objs文件夹是用来存放nginx编译过程中生成的一些文件的，比如.o文件、.so文件、.h文件、.c文件等。这些文件是用来链接成最终的nginx可执行文件的，也就是nginx.exe。

```
#执行configure命令后会生成Makefile和objs文件
root@Ubuntu22:/usr/local/src/nginx-1.18.0/objs# ls
autoconf.err  Makefile  ngx_auto_config.h  ngx_auto_headers.h  ngx_modules.c  src
```

##### 5、编译以及安装

注意：要在nginx-1.18.0下执行命令，而不要在nginx-1.18.0/objs下执行。nginx-1.18.0下的Makefile会调用objs下的Makefile。

```
#先执行make命令，如果成功了再执行make install命令。这样可以避免在编译失败时还尝试安装。&&是一个逻辑运算符，表示如果左边的命令返回0（表示成功），才会执行右边的命令。
#make命令是用configure命令生成的Makefile来编译源代码，生成可执行文件或者库文件。
#make install命令是用来安装编译好的文件到指定的位置
make && make install
```

##### 6、修改安装目录权限并查看相关文件

```
chown -R nginx.nginx /apps/nginx

#conf：保存nginx所有的配置文件，其中nginx.conf是nginx服务器的最核心最主要的配置文件，其他的.conf则是用来配置nginx相关的功能的，例如fastcgi功能使用的是fastcgi.conf和fastcgi_params两个文件，配置文件一般都有一个样板配置文件，是以.default为后缀，使用时可将其复制并将default后缀去掉即可。
#html目录中保存了nginx服务器的web文件，但是可以更改为其他目录保存web文件,另外还有一个50x的web文件是默认的错误页面提示页面。
#logs：用来保存nginx服务器的访问日志错误日志等日志，logs目录可以放在其他路径，比如/var/logs/nginx里面。
#sbin：保存nginx二进制启动脚本，可以接受不同的参数以实现不同的功能。
root@Ubuntu22:/usr/local/src/nginx-1.18.0# ll /apps/nginx/
total 24
drwxr-xr-x 6 nginx nginx 4096 Jul 30 21:01 ./
drwxr-xr-x 3 root  root  4096 Jul 30 21:01 ../
drwxr-xr-x 2 nginx nginx 4096 Jul 30 21:01 conf/
drwxr-xr-x 2 nginx nginx 4096 Jul 30 21:01 html/
drwxr-xr-x 2 nginx nginx 4096 Jul 30 21:01 logs/
drwxr-xr-x 2 nginx nginx 4096 Jul 30 21:01 sbin/
```

##### 7、创建软连接，将/apps/nginx/sbin/下的启动脚本创建到环境变量PATH下

```
ln -s /apps/nginx/sbin/nginx /usr/sbin/
```

##### 8、验证版本以及编译参数

```
#查看nginx版本
nginx -v
#查看nginx版本以及编译参数
root@Ubuntu22:/usr/local/src/nginx-1.18.0# nginx -V
nginx version: nginx/1.18.0
built by gcc 11.3.0 (Ubuntu 11.3.0-1ubuntu1~22.04.1) 
built with OpenSSL 3.0.2 15 Mar 2022
TLS SNI support enabled
configure arguments: --prefix=/apps/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre --with-stream --with-stream_ssl_module --with-stream_realip_module --with-cc-opt=-Wno-error=deprecated-declarations
```

##### 9、创建nginx自启动文件

```
vim /lib/systemd/system/nginx.service

[Unit]
Description=nginx - high performance web server
Documentation=http://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target
[Service]
Type=forking
#指定pid文件的目录,默认在logs目录下,可选配置
PIDFile=/apps/nginx/run/nginx.pid
ExecStart=/apps/nginx/sbin/nginx -c /apps/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
LimitNOFILE=100000
[Install]
WantedBy=multi-user.target

#[Unit]部分是用来描述nginx服务的基本信息和依赖关系的
#Description：这个参数是用来给nginx服务一个简短的描述的
#After：这个参数用来指定nginx服务在哪些其他服务之后启动的，以保证依赖的服务都已经启动。让网络、远程文件系统和域名解析等服务都启动完成后才启动nginx，以避免出现问题。
#Wants：这个参数用来指定nginx服务需要哪些其他服务启动的，如果这些服务没有启动，nginx服务也会尝试启动它们。
#[Service]部分是用来描述nginx服务的具体行为和设置的
#Type：这个参数是用来指定nginx服务的启动类型的，forking表示nginx会创建一个子进程并退出父进程，systemd会认为父进程退出后就表示服务启动成功。
#PIDFile：这个参数是用来指定nginx服务的进程ID文件的位置的，systemd会根据这个文件来监控和管理nginx服务的进程。
#ExecStart：这个参数是用来指定在启动nginx服务之前执行的命令的，-c表示启动nginx时用/apps/nginx/conf/nginx.conf作为配置文件
#ExecReload：这个参数是用来指定重新加载nginx配置文件的命令的。/bin/kill -s HUP $MAINPID 等价于nginx -s reload。$MAINPID是一个变量，表示nginx服务的主进程ID，它可以从/run/nginx.pid文件中读取。
#ExecStop：这个参数是用来指定停止nginx服务的命令的。/bin/kill -s TERM $MAINPID等价于nginx -s stop。
#[Install]部分是用来描述nginx服务如何被安装和启用的。
#WantedBy：这个参数是用来指定哪些其他单元需要或者想要启动nginx服务的，multi-user.target表示多用户模式下需要启动nginx服务。
```

##### 10、创建pid文件的存放目录并修改配置文件

```
mkdir /apps/nginx/run/

vim /apps/nginx/conf/nginx.conf
pid   /apps/nginx/run/nginx.pid;
#nginx.conf里面的pid文件路径是用来告诉nginx进程自己要把pid文件写在哪里的，而nginx.service里面的PIDFile参数是用来告诉systemd服务管理器要从哪里读取pid文件的。这两个路径必须保持一致，否则systemd无法正确地监控和控制nginx服务。
```

##### 11、重载配置文件，让systemd识别新创建或修改的服务文件，让systemctl来管理nginx服务

```
systemctl daemon-reload
```

##### 12、设置开机启动

```
systemctl enable --now nginx
```

##### 13、重启nginx并查看pid文件是否生成

```
systemctl restart nginx.service

root@Ubuntu22:~# ll /apps/nginx/run/
total 12
drwxr-xr-x  2 root  root  4096 Jul 31 21:04 ./
drwxr-xr-x 12 nginx nginx 4096 Jul 31 20:50 ../
-rw-r--r--  1 root  root     6 Jul 31 21:04 nginx.pid
```

##### 14、查看pid文件的进程号是否和master进程编号一致，安装完成

注意的点:

```
源码包
auto  CHANGES  CHANGES.ru  conf  configure  contrib  html  LICENSE  man  README  src

执行configure后
auto  CHANGES  CHANGES.ru  conf  configure  contrib  html  LICENSE  Makefile  man  objs  README  src

make以前的objs
root@Ubuntu22:/usr/local/src/nginx-1.18.0/objs# ls
autoconf.err  Makefile  ngx_auto_config.h  ngx_auto_headers.h  ngx_modules.c  src

make以后的objs
root@Ubuntu22:/usr/local/src/nginx-1.18.0/objs# ls
autoconf.err  Makefile  nginx  nginx.8  ngx_auto_config.h  ngx_auto_headers.h  ngx_modules.c  ngx_modules.o  src
```

### 二、nginx新增模块

前言：编译nginx的时候，会用configure命令来指定需要的模块。如果在编译完成之后，需要添加模块，比如第三方模块nginx-module-vts、echo-nginx-module，则需要重新编译生成二进制可执行文件。

1、下载需要的模块(手动下载需解压)

```
cd /usr/local/src
#github很多时候连不上，那就手动下载传上来
#这里需要注意的是，使用git下载的文件和手动下载的文件略有不同，手动下载的文件是压缩的形式并且后最是master结尾的，在执行configure的时候模块路径要写对
git clone git://github.com/vozlt/nginx-module-vts.git
git clone git://github.com/openresty/echo-nginx-module
```

2、找到编译nginx时执行的参数

```
root@Ubuntu22:/usr/local/src# nginx -V
nginx version: nginx/1.18.0
built by gcc 11.3.0 (Ubuntu 11.3.0-1ubuntu1~22.04.1) 
built with OpenSSL 3.0.2 15 Mar 2022
TLS SNI support enabled
configure arguments: --prefix=/apps/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre --with-stream --with-stream_ssl_module --with-stream_realip_module --with-cc-opt=-Wno-error=deprecated-declarations
```

3、在原参数的基础上增加新的模块，并执行configre命令

```
cd /usr/local/src/nginx-1.18.0

./configure --prefix=/apps/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre --with-stream --with-stream_ssl_module --with-stream_realip_module --with-cc-opt=-Wno-error=deprecated-declarations --add-module=/usr/local/src/echo-nginx-module-master --add-module=/usr/local/src/nginx-module-vts-master
```

4、查看当前nginx程序的信息

这个时候nginx还没有被覆盖，在编译重新安装后，程序会被覆盖。

```
root@Ubuntu22:/usr/local/src/nginx-1.18.0# ll /apps/nginx/sbin/nginx 
-rwxr-xr-x 1 nginx nginx 5746968 Jul 31 19:57 /apps/nginx/sbin/nginx*
```

5、编译安装

```
make && make install
```

6、查看当前nginx程序的信息

```
#已被覆盖
root@Ubuntu22:/usr/local/src/nginx-1.18.0# ll /apps/nginx/sbin/nginx 
-rwxr-xr-x 1 root root 6789944 Jul 31 22:52 /apps/nginx/sbin/nginx*
```

7、重启nginx服务

```
#重新编译后必须重启nginx服务，并且不支持reload
systemctl restart nginx
```

### 三、nginx平滑升级

```
#从nginx-1.18.0升级到nginx-1.20.1
```

原理：

```
a、在不停掉老进程的情况下，启动新进程
b、老进程负责处理仍然没有处理完的请求，但不再接受处理请求
c、新进程接受新请求
d、老进程处理完所有请求，关闭所有连接后，停止
```

1、下载新版本的nginx源码包并解压

```
#这里为了方便我直接下载到/root下了
wget http://nginx.org/download/nginx-1.20.1.tar.gz
tar -xf nginx-1.20.1.tar.gz
```



2、查看旧版本的编译参数

```
root@Ubuntu22:~/nginx-1.20.1# nginx -V
nginx version: nginx/1.18.0
built by gcc 11.3.0 (Ubuntu 11.3.0-1ubuntu1~22.04.1) 
built with OpenSSL 3.0.2 15 Mar 2022
TLS SNI support enabled
configure arguments: --prefix=/apps/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre --with-stream --with-stream_ssl_module --with-stream_realip_module --with-cc-opt=-Wno-error=deprecated-declarations
```

3、在新版本nginx包目录下执行configure命令，使用就版本的编译参数

```
cd /root/nginx-1.20.1

./configure --prefix=/apps/nginx \--user=nginx \--group=nginx \--with-http_ssl_module \--with-http_v2_module \--with-http_realip_module \--with-http_stub_status_module \--with-http_gzip_static_module \--with-pcre \--with-stream \--with-stream_ssl_module \--with-stream_realip_module \--with-cc-opt=-Wno-error=deprecated-declarations
```

4、编译并查看俩个版本的nginx程序

```
#只执行make，不要执行make install，make install会直接替换旧的nginx二进制文件。make会在objs下生成nginx程序。
make

root@Ubuntu22:~/nginx-1.20.1# ll objs/nginx /apps/nginx/sbin/nginx
-rwxr-xr-x 1 nginx nginx 5746968 Jul 31 19:58 /apps/nginx/sbin/nginx*
-rwxr-xr-x 1 root  root  5857768 Aug  1 09:22 objs/nginx*
```

5、将新的nginx程序替换旧的nginx程序，并备份旧的nginx程序

```
#备份
root@Ubuntu22:~/nginx-1.20.1# mv /apps/nginx/sbin/nginx /root/nginx.old

root@Ubuntu22:~/nginx-1.20.1# mv ./objs/nginx /apps/nginx/sbin/
```

6、检测新版本nginx程序和旧版本配置文件的兼容性

```
root@Ubuntu22:~/nginx-1.20.1# /apps/nginx/sbin/nginx -t 
nginx: the configuration file /apps/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /apps/nginx/conf/nginx.conf test is successful
```

7、向旧的master进程发送USR2信号

```
#SIGUSR2信号默认处理是终止进程，但SIGUSR2信号是一个用户自定义信号，nginx对其做了特殊处理，在nginx中不再用来表示终止进程，而用来实现平滑升级的功能。
kill -USR2 `cat /apps/nginx/run/nginx.pid`
#另外俩种写法也行，一个意思
#kill -SIGUSR2 `cat /apps/nginx/run/nginx.pid`
#kill -12 `cat /apps/nginx/run/nginx.pid`
```

给旧的master进程发送USR2信号会发生下面几个过程：

a、旧的master进程的pid文件会由nginx.pid更名为nginx.pid.oldbin

b、新的nginx程序会启动一个新的master进程，继承旧的master进程监听的所有 socket 描述符，开始接收新的连接请求。

c、生成一个新的nginx.pid保存新的master进程的pid

8、查看此时是谁在处理新的请求

```
#再另一台主机上curl下nginx服务器，发现此时仍然是旧的master下的worker进程在接收新的请求
root@Ubuntu22:~# curl -I 10.0.0.162
HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Tue, 01 Aug 2023 02:58:38 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Mon, 31 Jul 2023 11:58:40 GMT
Connection: keep-alive
ETag: "64c7a1f0-264"
Accept-Ranges: bytes
```



9、向旧的master进程发送WINCH信号

```
kill -WINCH `cat /apps/nginx/run/nginx.pid.oldbin`
```



给旧的master进程发WINCH信号会发生下面几个过程：

a、关闭旧的master进程下的worker进程(不关闭master进程的原因是方便进行回滚)

b、新的请求都交给新的master下的worker进程来处理

10、再次查看此时是谁在处理新的请求

```
#发现nginx的版本已经更新到1.20.1了
root@Ubuntu22:~# curl -I 10.0.0.162
HTTP/1.1 200 OK
Server: nginx/1.20.1
Date: Tue, 01 Aug 2023 03:03:00 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Mon, 31 Jul 2023 11:58:40 GMT
Connection: keep-alive
ETag: "64c7a1f0-264"
Accept-Ranges: bytes
```



**思考**：发送WINCH信号后，要不要等待worker进程先处理完请求再发送quit信号？

不用，quit是平滑的关闭进程，如果进程正在处理请求，它会等待进程处理完请求之后再关闭进程。

11、这里可以先停下来进行测试，确认新版本服务有没有问题。没有问题则继续升级，有问题则进行回滚。

(1)确认新版本服务没有问题，继续升级

a、向旧的master进程发送QUIT信号(先等待前面旧的worker进程处理完请求)

```
#QUIT信号会关闭旧的master进程，平滑的关闭
kill -QUIT信号会关闭旧的master进程 `cat /apps/nginx/run/nginx.pid.oldbin` 

root@Ubuntu22:~/nginx-1.20.1# ps aux |grep nginx
root       28034  0.0  0.6  10016  6248 ?        S    10:27   0:00 nginx: master process /apps/nginx/sbin/nginx -c /apps/nginx/conf/nginx.conf
nginx      28035  0.0  0.3  10772  3596 ?        S    10:27   0:00 nginx: worker process
root       28073  0.0  0.2   6476  2208 pts/0    S+   11:14   0:00 grep --color=auto nginx
```

b、验证

```
nginx -v
curl -I 127.0.0.1
```

(2)确认新版本服务有问题，进行回滚

a、发送HUP信号,重新拉起旧版本的worker进程

```
kill -HUP `cat /apps/nginx/run/nginx.pid.oldbin`
```

b、平滑关闭新版本的master进程以及worker进程

```
#不发送上面的HUP信号，只发送QUIT也可以拉起旧版本的worker进程
#执行QUIT信号后新的master进程不会接收请求了，由旧的master下的worker进程进程接收
kill -QUIT `cat /apps/nginx/run/nginx.pid`
```

c、将旧的nginx二进制可执行程序还原到原来的路径下

```
mv /root/nginx.old /apps/nginx/sbin/nginx
```

d、验证

```
nginx -v
curl -I 127.0.0.1
```

问题:

1、生产环境中，发送WINCH信号后，要不要等待worker进程先处理完请求再发送quit信号。

2、回滚的时候，新版本的worker线程正在处理请求，那后面再来的请求由谁来处理？如果还是由新的worker线程处理不是关不了了吗？

#### 二、基础命令和配置文件

1. 启动 Nginx 服务：`systemctl start nginx` （在使用 `systemd` 的系统中） 或者 `/usr/sbin/nginx` （直接运行 Nginx 二进制文件）
2. 停止 Nginx 服务：`systemctl stop nginx` 或者 `nginx -s stop`
3. 重新加载配置（使配置更改生效）：`systemctl reload nginx` 或者 `nginx -s reload`
4. 测试 Nginx 配置文件是否正确：`nginx -t`
5. 查看 Nginx 进程状态：`ps aux | grep nginx`
6. 查看 Nginx 版本：`nginx -v`



#### nginx的负载均衡策略（upstream）

1.轮询，按时间顺序把请求分配到每个服务器，如果某个服务器挂了宕机了也会被直接踢掉

2.用weigh=n关键字加权轮询，根据服务器端性能分配不同的权重值，值越高分配的次数越多

3.ip_hash根据客户端的ip地址计算哈希值，然后将客户端的请求固定发送到同一台服务器，适用于需要保持会话/缓存一致性的场景

4.最小连接数least_conn;动态统计每个服务器的连接数，把请求发送给请求连接数最少的服务器，适用长/短/混合连接

5.随机random

#### upstream stream proxy_pass

upstream定义一组后端服务器为负载均衡做准备。

stream处理四层代理的流量。

proxy_pass将请求转发到后面接上的服务器或url。

#### 作为服务器

server{

listen 80;

server_name localhost;



location / {

​	root /路径/html目录;

​	index html目录下的文件;#例如index index.html index.htm;

}}

#### 四层代理

stream{

​	upstream 服务器组名{

​	server 10.0.0.1:8080;

​	server 10.0.0.2:8080;

​	server 10.0.0.3:8080;

​	}

​	server{

​	server_name 18.18.18.18;

​	listen 80;#监听本机80端口

​	proxy_pass 服务器组名;

​	}

}
四层不用 location / {
  }这个结构

流程：客户端访问本机的80端口，本机80端口接收到请求后转发给proxy_pass定义的服务器组，服务器组通过负载均衡策略分配转发给组内的服务器，组内后端服务器处理请求返回给nginx，nginx返回个客户端。

#### 七层代理（ 能够对URL、请求头、方法等应用层信息进行更精细的请求匹配和处理。）

http{

​	upstream 服务器组名{

​	server 10.0.0.3:8080;

​	server 10.0.0.4:8080;

​	}

​	server{

​		listen 80;

​		server_name 18.18.18.18;

​		location / {#匹配路径规则

​			proxy_pass http://服务器组名;#http://10.0.0.03:8080...组成这种	

​		}	

​	}

}

流程：客户端向nginx服务器发送http请求，nginx服务器在80端口接收到这个请求后，根据location路径匹配规则转发到proxy_pass指定的服务器组，再根据负载均衡进行转发到组内服务器，组内的后端服务器处理请求返回给nginx，nginx返回给客户端。

### 三、nginx如何优化

| 配置项             | 作用                     | 方案                                    |
| ------------------ | ------------------------ | --------------------------------------- |
| worker_processes   | 调整工作进程最大连接数   | worker_processes auto; #与cpu核心数同步 |
| worker_connections | 调整最大连接数，默认1024 | worker_connections 10240;               |
|                    | 高效传输模式             |                                         |
|                    |                          |                                         |
|                    |                          |                                         |

### 四、状态码

1. `200 OK`：表示请求成功，服务器成功返回了请求的数据。
2. `204 No Content`：表示服务器成功处理了请求，但没有返回任何内容。
3. `206 Partial Content`：表示服务器成功处理了部分 GET 请求，常用于断点续传。
4. `301 Moved Permanently`：表示请求的资源已被永久移动到新的 URL，浏览器会自动重定向到新的 URL。
5. `302 Found`：表示请求的资源临时被移动到了其他位置，浏览器会重定向到新的临时 URL。
6. `304 Not Modified`：表示客户端发送了一个条件 GET 请求，服务器返回此状态码表示客户端请求的资源未被修改，客户端可以使用本地缓存。
7. `400 Bad Request`：表示客户端发送的请求语法错误，服务器无法理解。
8. `401 Unauthorized`：表示请求需要用户认证。
9. `403 Forbidden`：表示服务器理解请求，但拒绝执行，通常是因为权限不足。
10. `404 Not Found`：表示服务器未找到请求的资源。
11. `500 Internal Server Error`：表示服务器内部错误，无法完成请求。
12. `502 Bad Gateway`：表示作为网关或代理的服务器，从上游服务器接收到了无效的响应。
13. `503 Service Unavailable`：表示服务器暂时不可用，通常是由于服务器过载或维护。
14. `504 Gateway Timeout`：表示作为网关或代理的服务器，未在规定时间内从上游服务器获取到响应。
