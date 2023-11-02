## 安装

### 下载

- [nginx的所有版本](http://nginx.org/download/)
- [nginx最新下载页](https://nginx.org/en/download.html)
- [nginx安装文档](https://nginx.org/en/docs/install.html)

### 源码编译安装

```bash
wget https://nginx.org/download/nginx-1.24.0.tar.gz

tar zxf nginx-1.24.0.tar.gz

cd nginx-1.24.0
./configure

make

make install
```

## 开机自启动

**开机自启动方法一：**

```bash
vim /usr/lib/systemd/system/nginx.service
```

```bash

[Unit]
Description=nginx
After=network.target remote-fs.target nss-lookup.target

[Service]

Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

- `[Unit]`:服务的说明  
- `Description`:描述服务  
- `After`:描述服务类别  
- `[Service]`服务运行参数的设置  
- `Type=forking`是后台运行的形式  
- `ExecStart`为服务的具体运行命令  
- `ExecReload`为重启命令  
- `ExecStop`为停止命令  
- `PrivateTmp=True`表示给服务分配独立的临时空间  

> 注意：`[Service]`的启动、重启、停止命令全部要求使用绝对路径。`[Install]`运行级别下服务安装的相关设置，可设置为多用户，即系统运行级别为`3`。

```bash
# 设置开机自启动
systemctl enable nginx
# 启动nginx服务
systemctl start nginx
# 停止开机自启动
systemctl disable nginx
# 查看服务当前状态
systemctl status nginx
# 查看所有已启动的服务
systemctl list-units --type=service
# 重新启动服务
systemctl restart nginx
systemctl --failed # 显示启动失败的服务
# 查看日志
journalctl -u nginx -u php-fpm --since today
```

**开机自启动方法二：**

```bash
vi /etc/rc.local

# 在 rc.local 文件中，添加下面这条命令
/usr/local/nginx/sbin/nginx start

# 如果开机后发现自启动脚本没有执行，你要去确认一下rc.local这个文件的访问权限是否是可执行的，因为rc.local默认是不可执行的。修改rc.local访问权限，增加可执行权限：
# /etc/rc.local是/etc/rc.d/rc.local的软连接，
chmod +x /etc/rc.d/rc.local
```

官方脚本 [ed Hat NGINX Init Script](https://www.nginx.com/resources/wiki/start/topics/examples/redhatnginxinit/)。

## 运维

### 常用命令

```bash
# 启动
/usr/local/nginx/sbin/nginx
# 重新加载
/usr/local/nginx/sbin/nginx -s reload
# 关闭进程
/usr/local/nginx/sbin/nginx -s stop
# 平滑关闭nginx
/usr/local/nginx/sbin/nginx -s quit
# 查看nginx的安装状态，
/usr/local/nginx/sbin/nginx -V 
```

## nginx卸载

如果通过yum安装，使用下面命令安装。

```bash
yum remove nginx
```

编译安装，删除/usr/local/nginx目录即可，如果配置了自启动脚本，也需要删除。


## 编译参数说明

| 参数 | 说明 |
| ---- | ---- |
| --prefix=`<path>` | Nginx安装路径。如果没有指定，默认为 /usr/local/nginx。 |
| --sbin-path=`<path>` | Nginx可执行文件安装路径。只能安装时指定，如果没有指定，默认为`<prefix>`/sbin/nginx。 |
| --conf-path=`<path>` | 在没有给定-c选项下默认的nginx.conf的路径。如果没有指定，默认为`<prefix>`/conf/nginx.conf。 |
| --pid-path=`<path>` | 在nginx.conf中没有指定pid指令的情况下，默认的nginx.pid的路径。如果没有指定，默认为 `<prefix>`/logs/nginx.pid。 |
| --lock-path=`<path>` | nginx.lock文件的路径。 |
| --error-log-path=`<path>` | 在nginx.conf中没有指定error_log指令的情况下，默认的错误日志的路径。如果没有指定，默认为 `<prefix>`/- logs/error.log。 |
| --http-log-path=`<path>` | 在nginx.conf中没有指定access_log指令的情况下，默认的访问日志的路径。如果没有指定，默认为 `<prefix>`/- logs/access.log。 |
| --user=`<user>` | 在nginx.conf中没有指定user指令的情况下，默认的nginx使用的用户。如果没有指定，默认为 nobody。 |
| --group=`<group>` | 在nginx.conf中没有指定user指令的情况下，默认的nginx使用的组。如果没有指定，默认为 nobody。 |
| --builddir=DIR | 指定编译的目录 |
| --with-rtsig_module | 启用 rtsig 模块 |
| --with-select_module --without-select_module | 允许或不允许开启SELECT模式，如果 configure 没有找到更合适的模式，比如：kqueue(sun os),epoll (linux kenel 2.6+), rtsig(- 实时信号)或者/dev/poll(一种类似select的模式，底层实现与SELECT基本相 同，都是采用轮训方法) SELECT模式将是默认安装模式|
| --with-poll_module --without-poll_module | Whether or not to enable the poll module. This module is enabled by, default if a more suitable method such as kqueue, epoll, rtsig or /dev/poll is not discovered by configure. |
| --with-http_ssl_module | Enable ngx_http_ssl_module. Enables SSL support and the ability to handle HTTPS requests. Requires OpenSSL. On Debian, this is libssl-dev. 开启HTTP SSL模块，使NGINX可以支持HTTPS请求。这个模块需要已经安装了OPENSSL，在DEBIAN上是libssl  |
| --with-http_realip_module | 启用 ngx_http_realip_module |
| --with-http_addition_module | 启用 ngx_http_addition_module |
| --with-http_sub_module | 启用 ngx_http_sub_module |
| --with-http_dav_module | 启用 ngx_http_dav_module |
| --with-http_flv_module | 启用 ngx_http_flv_module |
| --with-http_stub_status_module | 启用 "server status" 页 |
| --without-http_charset_module | 禁用 ngx_http_charset_module |
| --without-http_gzip_module | 禁用 ngx_http_gzip_module. 如果启用，需要 zlib 。 |
| --without-http_ssi_module | 禁用 ngx_http_ssi_module |
| --without-http_userid_module | 禁用 ngx_http_userid_module |
| --without-http_access_module | 禁用 ngx_http_access_module |
| --without-http_auth_basic_module | 禁用 ngx_http_auth_basic_module |
| --without-http_autoindex_module | 禁用 ngx_http_autoindex_module |
| --without-http_geo_module | 禁用 ngx_http_geo_module |
| --without-http_map_module | 禁用 ngx_http_map_module |
| --without-http_referer_module | 禁用 ngx_http_referer_module |
| --without-http_rewrite_module | 禁用 ngx_http_rewrite_module. 如果启用需要 PCRE 。 |
| --without-http_proxy_module | 禁用 ngx_http_proxy_module |
| --without-http_fastcgi_module | 禁用 ngx_http_fastcgi_module |
| --without-http_memcached_module | 禁用 ngx_http_memcached_module |
| --without-http_limit_zone_module | 禁用 ngx_http_limit_zone_module |
| --without-http_empty_gif_module | 禁用 ngx_http_empty_gif_module |
| --without-http_browser_module | 禁用 ngx_http_browser_module |
| --without-http_upstream_ip_hash_module | 禁用 ngx_http_upstream_ip_hash_module |
| --with-http_perl_module | 启用 ngx_http_perl_module |
| --with-perl_modules_path=PATH | 指定 perl 模块的路径 |
| --with-perl=PATH | 指定 perl 执行文件的路径 |
| --http-log-path=PATH | Set path to the http access log |
| --http-client-body-temp-path=PATH | Set path to the http client request body temporary files |
| --http-proxy-temp-path=PATH | Set path to the http proxy temporary files |
| --http-fastcgi-temp-path=PATH | Set path to the http fastcgi temporary files |
| --without-http | 禁用 HTTP server |
| --with-mail | 启用 IMAP4/POP3/SMTP 代理模块 |
| --with-mail_ssl_module | 启用 ngx_mail_ssl_module |
| --with-cc=PATH | 指定 C 编译器的路径 |
| --with-cpp=PATH | 指定 C 预处理器的路径 |
| --with-cc-opt=OPTIONS | Additional parameters which will be added to the variable CFLAGS. With the use of the system library PCRE in FreeBSD, it is necessary to indicate --with-cc-opt="-I /usr/local/include". If we are using select() and it is necessary to increase the number of file descriptors, then this also can be assigned here: --with-cc-opt="-D FD_SETSIZE=2048". |
| --with-ld-opt=OPTIONS | Additional parameters passed to the linker. With the use of the system library PCRE in - FreeBSD, it is necessary to indicate --with-ld-opt="-L /usr/local/lib". |
| --with-cpu-opt=CPU | 为特定的 CPU 编译，有效的值包括：pentium, pentiumpro, pentium3, pentium4, athlon, opteron, amd64, sparc32, sparc64, ppc64 |
| --without-pcre | 禁止 PCRE 库的使用。同时也会禁止 HTTP rewrite 模块。在 "location" 配置指令中的正则表达式也需要 PCRE 。 |
| --with-pcre=DIR | 指定 PCRE 库的源代码的路径。 |
| --with-pcre-opt=OPTIONS | Set additional options for PCRE building. |
| --with-md5=DIR | Set path to md5 library sources. |
| --with-md5-opt=OPTIONS | Set additional options for md5 building. |
| --with-md5-asm | Use md5 assembler sources. |
| --with-sha1=DIR | Set path to sha1 library sources. |
| --with-sha1-opt=OPTIONS | Set additional options for sha1 building. |
| --with-sha1-asm | Use sha1 assembler sources. |
| --with-zlib=DIR | Set path to zlib library sources. |
| --with-zlib-opt=OPTIONS | Set additional options for zlib building. |
| --with-zlib-asm=CPU | Use zlib assembler sources optimized for specified CPU, valid values are: pentium, pentiumpro |
| --with-openssl=DIR | Set path to OpenSSL library sources |
| --with-openssl-opt=OPTIONS | Set additional options for OpenSSL building |
| --with-debug | 启用调试日志 |
| --add-module=PATH | Add in a third-party module found in directory PATH |


## 配置详解 nginx.conf

```nginx
#定义Nginx运行的用户和用户组
user www www;

#nginx进程数，建议设置为等于CPU总核心数。
worker_processes 8;

#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log /var/log/nginx/error.log info;

#进程文件
pid /var/run/nginx.pid;

#一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（系统的值ulimit -n）与nginx进程数相除，但是nginx分配请求并不均匀，所以建议与ulimit -n的值保持一致。
worker_rlimit_nofile 65535;

#工作模式与连接数上限
events
{
    #参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型，如果跑在FreeBSD上面，就用kqueue模型。
    use epoll;
    #单个进程最大连接数（最大连接数=连接数*进程数）
    worker_connections 65535;
}

#设定http服务器
http
{
    include mime.types; # 引入文件
    default_type application/octet-stream; #默认文件类型
    #charset utf-8; #默认编码
    server_names_hash_bucket_size 128; #服务器名字的hash表大小
    client_header_buffer_size 32k; #上传文件大小限制
    large_client_header_buffers 4 64k; #设定请求缓
    client_max_body_size 8m; #设定请求缓
    sendfile on; #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成off。
    autoindex on; #开启目录列表访问，合适下载服务器，默认关闭。
    tcp_nopush on; #防止网络阻塞
    tcp_nodelay on; #防止网络阻塞
    keepalive_timeout 120; #长连接超时时间，单位是秒

    #FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;

    #gzip模块设置
    gzip on;        #开启gzip压缩输出
    gzip_min_length 1k; #最小压缩文件大小
    gzip_buffers 4 16k; #压缩缓冲区
    gzip_http_version 1.0; #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）开始压缩的http协议版本(可以不设置,目前几乎全是1.1协议)
    gzip_comp_level 2;   #推荐6压缩级别(级别越高,压的越小,越浪费CPU计算资源)
    gzip_types text/plain application/x-javascript text/css application/xml;
    #压缩类型，默认就已经包含text/html，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。
    gzip_vary on;   # 是否传输gzip压缩标志
    #limit_zone crawler $binary_remote_addr 10m; #开启限制IP连接数的时候需要使用

    upstream backend {
        #upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
        server 192.168.80.121:80 weight=3;
        server 192.168.80.122:80 weight=2;
        server 192.168.80.123:80 weight=3;
    }

    #虚拟主机的配置
    server
    {
        #监听端口
        listen 80;
        #域名可以有多个，用空格隔开
        server_name www.ha97.com ha97.com;
        index index.html index.htm index.php;
        root /www/wwwroot/backend;
        location ~ \.php(.*)$ {
            fastcgi_pass   127.0.0.1:9001;
            fastcgi_index  index.php;
            fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            fastcgi_param  PATH_INFO  $fastcgi_path_info;
            fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;
            include        fastcgi_params;
        }
        #图片缓存时间设置
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires 10d;
        }
        #JS和CSS缓存时间设置
        location ~ .*\.(js|css)?$
        {
            expires 1h;
        }
        #日志格式设定
        log_format access '$remote_addr - $remote_user [$time_local] "$request" '
        '$status $body_bytes_sent "$http_referer" '
        '"$http_user_agent" $http_x_forwarded_for';
        #定义本虚拟主机的访问日志
        access_log /www/wwwlogs/access.backend.log access;

        #对 "/" 启用反向代理
        location / {
            proxy_pass http://127.0.0.1:88;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            #以下是一些反向代理的配置，可选。
            proxy_set_header Host $host;
            client_max_body_size 10m; #允许客户端请求的最大单文件字节数
            client_body_buffer_size 128k; #缓冲区代理缓冲用户端请求的最大字节数，
            proxy_connect_timeout 90; #nginx跟后端服务器连接超时时间(代理连接超时)
            proxy_send_timeout 90; #后端服务器数据回传时间(代理发送超时)
            proxy_read_timeout 90; #连接成功后，后端服务器响应时间(代理接收超时)
            proxy_buffer_size 4k; #设置代理服务器（nginx）保存用户头信息的缓冲区大小
            proxy_buffers 4 32k; #proxy_buffers缓冲区，网页平均在32k以下的设置
            proxy_busy_buffers_size 64k; #高负荷下缓冲大小（proxy_buffers*2）
            proxy_temp_file_write_size 64k; #设定缓存文件夹大小，大于这个值，将从upstream服务器传
        }

        #设定查看Nginx状态的地址
        location /NginxStatus {
            stub_status on;
            access_log on;
            auth_basic "NginxStatus";
            auth_basic_user_file conf/htpasswd;
            #htpasswd文件的内容可以用apache提供的htpasswd工具来产生。
        }

        #所有静态文件由nginx直接读取
        location ~ .*.(htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|pdf|xls|mp3|wma)$
        { expires 15d; }
        location ~ .*.(js|css)?$
        { expires 1h; }
    }
}

```  

### 常见全局变量

| 变量 | 说明 | 变量 | 说明 |
| ---- | ---- | ---- | ---- | 
| $args | 这个变量等于请求行中的参数，同$query_string | $remote_port | 客户端的端口。 |
| $content_length | 请求头中的Content-length字段。 | $remote_user | 已经经过Auth Basic Module验证的用户名。 |
| $content_type | 请求头中的Content-Type字段。 | $request_filename | 当前请求的文件路径，由root或alias指令与URI请求生成。 |
| $document_root | 当前请求在root指令中指定的值。 | $scheme | HTTP方法（如http，https）。 |
| $host | 请求主机头字段，否则为服务器名称。 | $server_protocol | 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。 |
| $http_user_agent | 客户端agent信息 | $server_addr | 服务器地址，在完成一次系统调用后可以确定这个值。 |
| $http_cookie | 客户端cookie信息 | $server_name | 服务器名称。 |
| $limit_rate | 这个变量可以限制连接速率。 | $server_port | 请求到达服务器的端口号。 |
| $request_method | 客户端请求的动作，通常为GET或POST。 | $request_uri | 包含请求参数的原始URI，不包含主机名，如：/foo/bar.php?arg=baz。 |
| $remote_addr | 客户端的IP地址。 | $uri | 不带请求参数的当前URI，$uri不包含主机名，如/foo/bar.html。 |
| $document_uri | 与$uri相同。 | - | - |

[更多详见](http://nginx.org/en/docs/varindex.html)

### 符号参考

| 符号 | 说明 | 符号 | 说明 | 符号 | 说明 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| k,K | 千字节 | m,M | 兆字节 | ms | 毫秒 |
| s | 秒 | m | 分钟 | h |  小时 |
| d | 日 | w | 周 | M |  一个月, 30天 |

### 常用正则

| 正则 | 说明 | 正则 | 说明 |
| ---- | ---- | ---- | ---- | 
| `. ` | 匹配除换行符以外的任意字符 | `$ ` | 匹配字符串的结束 |
| `? ` | 重复0次或1次 | `{n} ` | 重复n次 |
| `+ ` | 重复1次或更多次 | `{n,} ` | 重复n次或更多次 |
| `*` | 重复0次或更多次 | `[c] ` | 匹配单个字符c |
| `\d ` |匹配数字 | `[a-z]` | 匹配a-z小写字母的任意一个 |
| `^ ` | 匹配字符串的开始 | - | - |

### location 基本语法

location [=|~|~*|^~] /uri/ { … }
- `= `：精确匹配，location = /test {}
- `~ `：区分大小写的正则匹配，location ~ ^/test$ {}
- `~*`：不区分大小写的正则匹配，location ~* ^/test$ {}
- `^~`：表示 uri 以某个字符串开头，location ^~ /images/ {}

### location, proxy_pass 路径规则

#### location 是否以 / 结尾

- 没有`/`结尾时，`/abc/def`可以匹配`/abc/defghi`请求，也可以匹配/abc/def/ghi
- 有`/`结尾时，`/abc/def/`不能匹配`/abc/defghi`请求，只能匹配/abc/def/ghi

#### proxy_pass 是否以 / 结尾

- 有`/`结尾时，相当于是绝对路径，则不会把location中匹配的路径加入代理`uri`
- 没有`/`结尾时，则会把匹配的路径部分加入代理`uri`。

> 当location里是正则表达式或者在proxy_pass前面用了`rewrite`，此时 `proxy_pass`的 `uri` 可能会无效

#### Proxy Pass and Paths

| Case # | Nginx location | proxy_pass URL | Test URL | Path received |
| ------ | -------------- | -------------- | -------- | ------------- |
| 01 | /test01 | http://127.0.0.1:8080 | /test01/abc/test | /test01/abc/test|
| 02 | /test02 | http://127.0.0.1:8080/ | /test02/abc/test | //abc/test|
| 03 | /test03/ | http://127.0.0.1:8080 | /test03/abc/test | /test03/abc/test|
| 04 | /test04/ | http://127.0.0.1:8080/ | /test04/abc/test | /abc/test|
| 05 | /test05 | http://127.0.0.1:8080/app1 | /test05/abc/test | /app1/abc/test|
| 06 | /test06 | http://127.0.0.1:8080/app1/ | /test06/abc/test | /app1//abc/test|
| 07 | /test07/ | http://127.0.0.1:8080/app1 | /test07/abc/test | /app1abc/test|
| 08 | /test08/ | http://127.0.0.1:8080/app1/ | /test08/abc/test | /app1/abc/test|
| 09 | / | http://127.0.0.1:8080 | /test09/abc/test | /test09/abc/test|
| 10 | / | http://127.0.0.1:8080/ | /test10/abc/test | /test10/abc/test|
| 11 | / | http://127.0.0.1:8080/app1 | /test11/abc/test | /app1test11/abc/test|
| 12 | / | http://127.0.0.1:8080/app2/ | /test12/abc/test | /app2/test12/abc/test|

### 反向代理

反向代理是一个Web服务器，它接受客户端的连接请求，然后将请求转发给上游服务器，并将从服务器得到的结果返回给连接的客户端。下面简单的反向代理的例子：

```nginx
server {  
  listen       80;                                                        
  server_name  localhost;                                              
  client_max_body_size 1024M;  # 允许客户端请求的最大单文件字节数

  location / {
    proxy_pass                         http://localhost:8080;
    proxy_set_header Host              $host:$server_port;
    proxy_set_header X-Forwarded-For   $remote_addr; # HTTP的请求端真实的IP
    proxy_set_header X-Forwarded-Proto $scheme;      # 为了正确地识别实际用户发出的协议是 http 还是 https
  }
}
```

代理到上游服务器的配置中，最重要的是`proxy_pass`指令。其常用指令：

| 指令 | 说明 |
| ---- | ---- |
| proxy_connect_timeout  | Nginx从接受请求至连接到上游服务器的最长等待时间 |
| proxy_send_timeout  | 后端服务器数据回传时间(代理发送超时) |
| proxy_read_timeout  | 连接成功后，后端服务器响应时间(代理接收超时) |
| proxy_cookie_domain | 替代从上游服务器来的Set-Cookie头的domain属性 |
| proxy_cookie_path   | 替代从上游服务器来的Set-Cookie头的path属性 |
| proxy_buffer_size   | 设置代理服务器（nginx）保存用户头信息的缓冲区大小 |
| proxy_buffers       | proxy_buffers缓冲区，网页平均在多少k以下 |
| proxy_set_header    | 重写发送到上游服务器头的内容，也可以通过将某个头部的值设置为空字符串，而不发送某个头部的方法实现 |
| proxy_ignore_headers | 这个指令禁止处理来自代理服务器的应答。 | 
| proxy_intercept_errors | 使nginx阻止HTTP应答代码为400或者更高的应答。 | 

### 正向代理

```nginx
    proxy_pass $scheme://$host$request_uri;
    resolver 8.8.8.8;
```

### 负载均衡

upstream指令启用一个新的配置区段，在该区段定义一组上游服务器。这些服务器可能被设置不同的权重，也可能出于对服务器进行维护，标记为down。

```nginx
upstream backend {
    ip_hash;
    # upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
    server 127.0.0.1 weight=6;
    server 127.0.0.1:82 weight=3;
    server 127.0.0.1:83 weight=3 down;
    server 127.0.0.1:84 weight=3; max_fails=3  fail_timeout=20s;
    server 127.0.0.1:85 weight=4;
    keepalive 32;
}
server {
    listen       80;
    server_name  example.cn;
    location / {
        proxy_pass   http://backend;    #在这里设置一个代理，和upstream的名字一样
        #以下是一些反向代理的配置可删除
        proxy_redirect             off;
        #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
        proxy_set_header           Host $host;
        proxy_set_header           X-Real-IP $remote_addr;
        proxy_set_header           X-Forwarded-For $proxy_add_x_forwarded_for;
        client_max_body_size       10m;  #允许客户端请求的最大单文件字节数
        client_body_buffer_size    128k; #缓冲区代理缓冲用户端请求的最大字节数
        proxy_connect_timeout      300;  #nginx跟后端服务器连接超时时间(代理连接超时)
        proxy_send_timeout         300;  #后端服务器数据回传时间(代理发送超时)
        proxy_read_timeout         300;  #连接成功后，后端服务器响应时间(代理接收超时)
        proxy_buffer_size          4k; #设置代理服务器（nginx）保存用户头信息的缓冲区大小
        proxy_buffers              4 32k;# 缓冲区，网页平均在32k以下的话，这样设置
        proxy_busy_buffers_size    64k; #高负荷下缓冲大小（proxy_buffers*2）
        proxy_temp_file_write_size 64k; #设定缓存文件夹大小，大于这个值，将从upstream服务器传
    }
}
```

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

**负载均衡：**

upstream模块能够使用3种负载均衡算法：轮询、IP哈希、最少连接数。

**轮询：** 默认情况下使用轮询算法，不需要配置指令来激活它，它是基于在队列中谁是下一个的原理确保访问均匀地分布到每个上游服务器；  
**IP哈希：** 通过ip_hash指令来激活，Nginx通过IPv4地址的前3个字节或者整个IPv6地址作为哈希键来实现，同一个IP地址总是能被映射到同一个上游服务器；  
**最少连接数：** 通过least_conn指令来激活，该算法通过选择一个活跃数最少的上游服务器进行连接。如果上游服务器处理能力不同，可以通过给server配置weight权重来说明，该算法将考虑到不同服务器的加权最少连接数。

#### 权重

指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。 例如

```nginx
upstream test {
    server localhost:8080 weight=9;
    server localhost:8081 weight=1;
}
```

那么10次一般只会有1次会访问到8081，而有9次会访问到8080

#### ip_hash

上面的2种方式都有一个问题，那就是下一个请求来的时候请求可能分发到另外一个服务器，当我们的程序不是无状态的时候（采用了session保存数据），这时候就有一个很大的很问题了，比如把登录信息保存到了session中，那么跳转到另外一台服务器的时候就需要重新登录了，所以很多时候我们需要一个客户只访问一个服务器，那么就需要用iphash了，iphash的每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。

```nginx
upstream test {
    ip_hash;
    server localhost:8080;
    server localhost:8081;
}
```

#### fair

这是个第三方模块，按后端服务器的响应时间来分配请求，响应时间短的优先分配。

```nginx
upstream backend {
    fair;
    server localhost:8080;
    server localhost:8081;
}
```

#### url_hash

这是个第三方模块，按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。 在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法

```nginx
upstream backend {
    hash $request_uri;
    hash_method crc32;
    server localhost:8080;
    server localhost:8081;
}
```

以上5种负载均衡各自适用不同情况下使用，所以可以根据实际情况选择使用哪种策略模式，不过fair和url_hash需要安装第三方模块才能使用

**server指令可选参数：**

1. weight：设置一个服务器的访问权重，数值越高，收到的请求也越多；
2. fail_timeout：在这个指定的时间内服务器必须提供响应，如果在这个时间内没有收到响应，那么服务器将会被标记为down状态；
3. max_fails：设置在fail_timeout时间之内尝试对一个服务器连接的最大次数，如果超过这个次数，那么服务器将会被标记为down;
4. down：标记一个服务器不再接受任何请求；
5. backup：一旦其他服务器宕机，那么有该标记的机器将会接收请求。

### 屏蔽ip

在nginx的配置文件`nginx.conf`中加入如下配置，可以放到http, server, location, limit_except语句块

```nginx
deny 165.91.122.67;

deny IP;   # 屏蔽单个ip访问
allow IP;  # 允许单个ip访问
deny all;  # 屏蔽所有ip访问
allow all; # 允许所有ip访问
deny 123.0.0.0/8   # 屏蔽整个段即从123.0.0.1到123.255.255.254访问的命令
deny 124.45.0.0/16 # 屏蔽IP段即从123.45.0.1到123.45.255.254访问的命令
deny 123.45.6.0/24 # 屏蔽IP段即从123.45.6.1到123.45.6.254访问的命令

# 如果你想实现这样的应用，除了几个IP外，其他全部拒绝
allow 1.1.1.1; 
allow 1.1.1.2;
deny all; 
```

## 第三方模块安装方法

```
./configure --prefix=/你的安装目录  --add-module=/第三方模块目录
```

## 重定向

- `permanent` 永久性重定向。请求日志中的状态码为301
- `redirect` 临时重定向。请求日志中的状态码为302

### 重定向整个网站

```nginx
server {
    server_name old-site.com
    return 301 $scheme://new-site.com$request_uri;
}
```

### 重定向单页

```nginx
server {
    location = /oldpage.html {
        return 301 http://example.org/newpage.html;
    }
}
```

### 重定向整个子路径

```nginx
location /old-site {
    rewrite ^/old-site/(.*) http://example.org/new-site/$1 permanent;
}
```

## 性能

### 内容缓存

允许浏览器基本上永久地缓存静态内容。 Nginx将为您设置`Expires`和`Cache-Control`头信息。

#### **cache-control**

http1.1的规范，使用max-age表示文件可以在浏览器中缓存的时间以秒为单位

| 标记                   | 类型       | 功能                                                         |
| ---------------------- | ---------- | ------------------------------------------------------------ |
| public                 | 响应头     | 响应的数据可以被缓存，客户端和代理层都可以缓存               |
| private                | 响应头     | 可私有缓存，客户端可以缓存，代理层不能缓存（CDN，proxy_pass） |
| no-cache               | 请求头     | 可以使用本地缓存，但是必须发送请求到服务器回源验证           |
| no-store               | 请求和响应 | 应禁用缓存                                                   |
| max-age                | 请求和响应 | 文件可以在浏览器中缓存的时间以秒为单位                       |
| s-maxage               | 请求和响应 | 用户代理层缓存，CDN下发，当客户端数据过期时会重新校验        |
| max-stale              | 请求和响应 | 缓存最大使用时间，如果缓存过期，但还在这个时间范围内则可以使用缓存数据 |
| min-fresh              | 请求和响应 | 缓存最小使用时间，                                           |
| must-revalidate        | 请求和响应 | 当缓存过期后，必须回源重新请求资源。比no-cache更严格。因为HTTP 规范是允许客户端在某些特殊情况下直接使用过期缓存的，比如校验请求发送失败的时候。那么带有must-revalidate的缓存必须校验，其他条件全部失效。 |
| proxy-revalidate       | 请求和响应 | 和must-revalidate类似，只对CDN这种代理服务器有效，客户端遇到此头，需要回源验证 |
| stale-while-revalidate | 响应       | 表示在指定时间内可以先使用本地缓存，后台进行异步校验         |
| stale-if-error         | 响应       | 在指定时间内，重新验证时返回状态码为5XX的时候，可以用本地缓存 |
| only-if-cached         | 响应       | 那么只使用缓存内容，如果没有缓存 则504 getway timeout        |

#### **Expires**

设置以分钟为单位的绝对过期时间, 优先级比Cache-Control低, 同时设置Expires和Cache-Control则后者生效. 也就是说要注意一点:  Cache-Control的优先级高于Expires

```nginx
location /static {
    root /data;
    expires max;
}
```
```nginx
expires 30s;   #缓存30秒
expires 30m;  #缓存30分钟   
expires 2h;     #缓存2小时
expires 30d;    #缓存30天
```
如果要求浏览器永远不会缓存响应（例如用于跟踪请求），请使用-1。

```nginx
location = /empty.gif {
    empty_gif;
    expires -1;
}
```

#### **Last-Modified**

该资源的最后修改时间, 在浏览器下一次请求资源时, 浏览器将先发送一个请求到服务器上, 并附上If-Unmodified-Since头来说明浏览器所缓存资源的最后修改时间, 如果服务器发现没有修改, 则直接返回304(Not Modified)回应信息给浏览器(内容很少), 如果服务器对比时间发现修改了, 则照常返回所请求的资源. 

需要注意:
1) Last-Modified属性通常和Expires或Cache-Control属性配合使用, 因为即使浏览器设置缓存, 当用户点击”刷新”按钮时, 浏览器会忽略缓存继续向服务器发送请求, 这时Last-Modified将能够很好的减小回应开销.

2) ETag将返回给浏览器一个资源ID, 如果有了新版本则正常发送并附上新ID, 否则返回304， 但是在服务器集群情况下, 每个服务器将返回不同的ID, 因此不建议使用ETag.

### Gzip压缩

> 作用域 `http, server, location`

```nginx
gzip  on; 
gzip_buffers 16 8k;
gzip_comp_level 6; 
gzip_http_version 1.1; 
gzip_min_length 256; 
gzip_proxied any;
gzip_vary on;
gzip_types
    text/xml application/xml application/atom+xml application/rss+xml application/xhtml+xml image/svg+xml
    text/javascript application/javascript application/x-javascript
    text/x-json application/json application/x-web-app-manifest+json
    text/css text/plain text/x-component
    font/opentype application/x-font-ttf application/vnd.ms-fontobject
    image/x-icon;
gzip_disable "MSIE [1-6]\.(?!.*SV1)";
```

**gzip on**;

开关，默认关闭

**gzip_buffers 32 4k|16 8k**

缓冲区大小

**gzip_comp_level 1**；

压缩等级 1-9，数字越大压缩比越高，也越消耗cpu

**gzip_http_version 1.1**;

使用gzip的最小版本

**gzip_min_length**

设置将被gzip压缩的响应的最小长度。 长度仅由“Content-Length”响应报头字段确定。

**gzip_proxied**

off 为不做限制，作为反向代理时，针对上游服务器返回的头信息进行压缩

- expired - 启用压缩，如果header头中包含 "Expires" 头信息
- no-cache - 启用压缩，如果header头中包含 "Cache-Control:no-cache" 头信息
- no-store - 启用压缩，如果header头中包含 "Cache-Control:no-store" 头信息
- private - 启用压缩，如果header头中包含 "Cache-Control:private" 头信息
- no_last_modified - 启用压缩,如果header头中不包含 "Last-Modified" 头信息
- no_etag - 启用压缩 ,如果header头中不包含 "ETag" 头信息
- auth - 启用压缩 , 如果header头中包含 "Authorization" 头信息
- any - 无条件启用压缩

**gzip_vary on**;

增加一个header，适配老的浏览器 `Vary: Accept-Encoding`

**gzip_types**

哪些mime类型的文件进行压缩

**gzip_disable**

禁止某些浏览器使用gzip

### 打开文件缓存

```nginx
open_file_cache max=1000 inactive=20s;
open_file_cache_valid 30s;
open_file_cache_min_uses 2;
open_file_cache_errors on;
```

**max** 缓存最大数量，超过数量后会使用LRU淘汰

**inactive** 指定时间内未被访问过的缓存将被删除

**pen_file_cache_min_uses**

被访问到多少次后会开始缓存

**open_file_cache_valid**

间隔多长时间去检查文件是否有变化

**open_file_cache_errors**

对错误信息是否缓存

### SSL缓存

```nginx
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

### 监控

使用 [`ngxtop`](https://github.com/lebinh/ngxtop)实时解析nginx访问日志，并且将处理结果输出到终端，功能类似于系统命令top。所有示例都读取nginx配置文件的访问日志位置和格式。如果要指定访问日志文件和/或日志格式，请使用-f和-a选项。

注意：在nginx配置中`/usr/local/nginx/conf/nginx.conf`日志文件必须是绝对路径。

```bash
# 安装 ngxtop
pip install ngxtop

# 实时状态
ngxtop
# 状态为404的前10个请求的路径：
ngxtop top request_path --filter 'status == 404'

# 发送总字节数最多的前10个请求
ngxtop --order-by 'avg(bytes_sent) * count'

# 排名前十位的IP，例如，谁攻击你最多
ngxtop --group-by remote_addr

# 打印具有4xx或5xx状态的请求，以及status和http referer
ngxtop -i 'status >= 400' print request status http_referer

# 由200个请求路径响应发送的平均正文字节以'foo'开始：
ngxtop avg bytes_sent --filter 'status == 200 and request_path.startswith("foo")'

# 使用“common”日志格式从远程机器分析apache访问日志
ssh remote tail -f /var/log/apache2/access.log | ngxtop -f common
```

## 日志

Nginx日志主要分为两种：访问日志和错误日志,默认都是打开的

### log_format

| 字段 | 作用  | 案例 |
| :------------ |:---------------:| -----:|
| $server_name     | 虚拟主机名称        |   虚拟主机名称 |
| $remote_addr      | 记录客户端IP地址 | 218.108.35.150 |
| $remote_user | 远程客户端用户名称 |    如登录百度的用户名scq2099yt，如果没有登录就是空白 |
| $time_local | 访问的时间与时区 |    16/Mar/2017:17:50:52 +0800 时间信息最后的"+0800"表示服务器所处时区位于UTC之后的8小时 |
| $request | 请求的URI和HTTP协议|    这是整个PV日志记录中最有用的信息，记录服务器收到一个什么样的请求 |
| $status | 记录请求返回的http状态码 |    比如成功是200 |
| $uptream_status| upstream状态 |    比如成功是200 |
| $body_bytes_sent | 发送给客户端的文件主体内容的大小 |  比如899，可以将日志每条记录中的这个值累加起来以粗略估计服务器吞吐量 |
| $http_referer | 记录从哪个页面链接访问过来的 |  ... |
| $http_user_agent | 记录客户端浏览器信息 |  ... |
| $http_x_forwarded_for | 客户端的真实ip|  通常web服务器放在反向代理的后面，这样就不能获取到客户的IP地址了，通过$remote_add拿到的IP地址是反向代理服务器的iP地址。反向代理服务器在转发请求的http头信息中，可以增加x_forwarded_for信息，用以记录原有客户端的IP地址和原来客户端的请求的服务器地址。 |
| $ssl_protocol | SSL协议版本 |  比如TLSv1|
| $ssl_cipher | 交换数据中的算法 |  比如RC4-SHA|
| $upstream_addr | upstream的地址 |  即真正提供服务的主机地址|
| $request_time | 整个请求的总时间 |  ...|
| $upstream_response_time | 请求过程中，upstream的响应时间 |  0.1s|

### access_log    
> 格式：`access_log <path> [format [buffer=size | off]]`   
> 案例：`access_log logs/access.log main;`     
> 上下文：`http、server、location`     
> 注意要点：`Nginx进程设置的用户和组必须对日志路径有创建文件的权限，否则，会报错`    

### error_log

错误日志主要记录客户端访问Nginx出错时的日志，格式不支持自定义。通过错误日志，你可以得到系统某个服务或server的性能瓶颈等。错误日志由指令error_log来指定

> 格式：`error_log <path> <level>`   
> 日志等级：`[ debug | info | notice | warn | error | crit ]`，从左至右，日志详细程度逐级递减，即debug最详细，crit最少        
> 案例：`error_log logs/error.log info;` 
> 上下文：`http、server、location`     
> 注意要点：`error_log off并不能关闭错误日志，而是会将错误日志记录到一个文件名为off的文件中`        
> 正确的关闭错误日志记录功能：`error_log /dev/null;`，上面表示将存储日志的路径设置为“垃圾桶” 

## 常见使用场景

### 跨域问题

在工作中，有时候会遇到一些接口不支持跨域，这时候可以简单的添加add_headers来支持cors跨域。配置如下：

```nginx
server {
  listen 80;
  server_name api.xxx.com;
    
  # Access-Control-Allow-Credentials用于告知浏览器当withCredentials属性设置为true时，是否可以显示跨域请求返回的内容。
  # 简单请求时，浏览器会根据此响应头决定是否显示响应的内容。预先验证请求时，浏览器会根据此响应头决定在发送实际跨域请求时，是否携带认证信息。
  add_header Access-Control-Allow-Origin *;
  add_header Access-Control-Allow-Credentials true;
  add_header Access-Control-Allow-Methods 'GET,POST,PUT,OPTIONS';
  add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization,Token';

  if ($request_method = 'OPTIONS') {
        return 204;
  }

  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host  $http_host;    
  } 
}
```

上面更改头信息，还有一种，使用 [rewrite](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html) 指令重定向URI来解决跨域问题。

```nginx
upstream test {
  server 127.0.0.1:8080;
  server localhost:8081;
}
server {
  listen 80;
  server_name api.xxx.com;
  location / { 
    root  html;
    index  index.html index.htm;
  }
  # 用于拦截请求，匹配任何以 /api/开头的地址 匹配符合以后，停止往下搜索正则。
  location ^~/api/{ 
    # 代表重写拦截进来的请求，并且只能对域名后边的除去传递的参数外的字符串起作用，
    # 例如www.a.com/proxy/api/msg?meth=1&par=2重写，只对/proxy/api/msg重写。
    # rewrite后面的参数是一个简单的正则 ^/api/(.*)$，
    # $1代表正则中的第一个()，$2代表第二个()的值，以此类推。
    rewrite ^/api/(.*)$ /$1 break;
    
    # 把请求代理到其他主机 
    # 其中 http://www.b.com/ 写法和 http://www.b.com写法的区别如下
    # 如果你的请求地址是他 http://server/html/test.jsp
    # 配置一： http://www.b.com/ 后面有“/” 
    #         将反向代理成 http://www.b.com/html/test.jsp 访问
    # 配置一： http://www.b.com 后面没有有“/” 
    #         将反向代理成 http://www.b.com/test.jsp 访问
    proxy_pass http://test;

    # 如果 proxy_pass  URL 是 http://a.xx.com/platform/ 这种情况
    # proxy_cookie_path应该设置成 /platform/ / (注意两个斜杠之间有空格)。
    proxy_cookie_path /platfrom/ /;

    # http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass_header
    # 设置 Cookie 头通过
    proxy_pass_header Set-Cookie;
  } 
}
```

### 跳转到带www的域上面

```nginx
server {
    listen 80;
    server_name www.example.com;
    root /www/wwwroot/html;
    location / {
        try_files $uri $uri/ /index.html =404;
    }
}
server {
    server_name example.com;
    rewrite ^(.*) https://www.example.com$1 permanent;
}
```

### 代理转发

```nginx
upstream api{
    server 127.0.0.1:3110;    
}
upstream resource{
    server 127.0.0.1:3120;
}
server {
    listen       80;
    server_name  example.com;
    root /www/wwwroot/html;
    location ^~/api/ {
        rewrite ^/(.*)$ /$1 break;
        proxy_pass http://api;
    }
    location ^~/img/ {
        rewrite ^/(.*)$ /$1 break;
        proxy_pass http://resource;
    }
    # 路由在前端，后端没有真实路由，在路由不存在的 404状态的页面返回 /index.html
    # 这个方式使用场景，你在写React或者Vue项目的时候，没有真实路由
    location / {
        try_files $uri $uri/ /index.html =404;
        #                               ^ 空格很重要
    }
}
```

### 监控状态信息

通过 `nginx -V` 来查看是否有 `with-http_stub_status_module` 该模块。

> `nginx -V` 这里 `V` 是大写的，如果是小写的 `v` 即 `nginx -v`，则不会出现有哪些模块，只会出现 `nginx` 的版本

```nginx
location /nginx_status {
    stub_status on;
    access_log off;
}
```

通过 http://127.0.0.1/nginx_status 访问出现下面结果。

```bash
Active connections: 3
server accepts handled requests
 7 7 5 
Reading: 0 Writing: 1 Waiting: 2 
```

1. 主动连接(第 1 行)

当前与http建立的连接数，包括等待的客户端连接：3

2. 服务器接受处理的请求(第 2~3 行)

接受的客户端连接总数目：7  
处理的客户端连接总数目：7  
客户端总的请求数目：5  

3. 读取其它信(第 4 行)

当前，nginx读请求连接  
当前，nginx写响应返回给客户端  
目前有多少空闲客户端请求连接  

### 代理转发连接替换

```nginx
location ^~/api/upload {
    rewrite ^/(.*)$ /wfs/v1/upload break;
    proxy_pass http://wfs-api;
}
```

### ssl配置

超文本传输安全协议（缩写：HTTPS，英语：Hypertext Transfer Protocol Secure）是超文本传输协议和SSL/TLS的组合，用以提供加密通讯及对网络服务器身份的鉴定。HTTPS连接经常被用于万维网上的交易支付和企业信息系统中敏感信息的传输。HTTPS不应与在RFC 2660中定义的安全超文本传输协议（S-HTTP）相混。HTTPS 目前已经是所有注重隐私和安全的网站的首选，随着技术的不断发展，HTTPS 网站已不再是大型网站的专利，所有普通的个人站长和博客均可以自己动手搭建一个安全的加密的网站。


创建SSL证书，如果你购买的证书，就可以直接下载

```bash
sudo mkdir /etc/nginx/ssl
# 创建了有效期100年，加密强度为RSA2048的SSL密钥key和X509证书文件。
sudo openssl req -x509 -nodes -days 36500 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
# 上面命令，会有下面需要填写内容
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:New York
Locality Name (eg, city) []:New York City
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Bouncy Castles, Inc.
Organizational Unit Name (eg, section) []:Ministry of Water Slides
Common Name (e.g. server FQDN or YOUR name) []:your_domain.com
Email Address []:admin@your_domain.com
```

创建自签证书

```bash
# 首先，创建证书和私钥的目录
mkdir -p /etc/nginx/cert
cd /etc/nginx/cert
# 创建服务器私钥，命令会让你输入一个口令：
openssl genrsa -des3 -out nginx.key 2048
# 创建签名请求的证书（CSR）：
openssl req -new -key nginx.key -out nginx.csr
# 在加载SSL支持的Nginx并使用上述私钥时除去必须的口令：
cp nginx.key nginx.key.org
openssl rsa -in nginx.key.org -out nginx.key
# 最后标记证书使用上述私钥和CSR：
openssl x509 -req -days 365 -in nginx.csr -signkey nginx.key -out nginx.crt
```

查看目前nginx编译选项

```
sbin/nginx -V
```

如果依赖的模块不存在，可以进入安装目录，输入下面命令重新编译安装。

```bash
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
```

运行完成之后还需要`make` (不用make install)

```bash
# 备份nginx的二进制文件
cp -rf /usr/local/nginx/sbin/nginx　 /usr/local/nginx/sbin/nginx.bak
# 覆盖nginx的二进制文件
cp -rf objs/nginx   /usr/local/nginx/sbin/
```

HTTPS server

```nginx
server {
    listen       443 ssl;
    server_name  localhost;

    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;
    # 禁止在header中出现服务器版本，防止黑客利用版本漏洞攻击
    server_tokens off;
    # 设置ssl/tls会话缓存的类型和大小。如果设置了这个参数一般是shared，buildin可能会参数内存碎片，默认是none，和off差不多，停用缓存。如shared:SSL:10m表示我所有的nginx工作进程共享ssl会话缓存，官网介绍说1M可以存放约4000个sessions。 
    ssl_session_cache    shared:SSL:1m; 

    # 客户端可以重用会话缓存中ssl参数的过期时间，内网系统默认5分钟太短了，可以设成30m即30分钟甚至4h。
    ssl_session_timeout  5m; 

    # 选择加密套件，不同的浏览器所支持的套件（和顺序）可能会不同。
    # 这里指定的是OpenSSL库能够识别的写法，你可以通过 openssl -v cipher 'RC4:HIGH:!aNULL:!MD5'（后面是你所指定的套件加密算法） 来看所支持算法。
    ssl_ciphers  HIGH:!aNULL:!MD5;

    # 设置协商加密算法时，优先使用我们服务端的加密套件，而不是客户端浏览器的加密套件。
    ssl_prefer_server_ciphers  on;

    location / {
        root   html;
        index  index.html index.htm;
    }
}
```

### 强制将http重定向到https

```nginx
server {
    listen       80;
    server_name  example.com;
    rewrite ^ https://$http_host$request_uri? permanent;    # 强制将http重定向到https
    # 在错误页面和“服务器”响应头字段中启用或禁用发射nginx版本。 防止黑客利用版本漏洞攻击
    server_tokens off;
}
```

### 虚拟主机标准配置

```nginx
http {
  server {
    listen          80 default;
    server_name     _ *;
    access_log      logs/default.access.log main;
    location / {
       index index.html;
       root  /var/www/default/htdocs;
    }
  }
}
```

### 爬虫过滤

根据 `User-Agent` 过滤请求

> `~*` 表示不区分大小写的正则匹配

[prerender-nginx](https://gist.github.com/Stanback/6998085)

```nginx
location / {
    if ($http_user_agent ~* "googlebot|bingbot|yandexbot|yandex|baiduspider|twitterbot|facebookexternalhit|rogerbot|linkedinbot|embedly|quora link preview|showyoubot|outbrain|pinterest\/0\.|pinterestbot|slackbot|vkShare|W3C_Validator|whatsapp") {
        return 503;
    }
    # 正常处理
    # ...
}
```

### 防盗链

```nginx
location ~* .*\.(gif|jpg|ico|png|css|svg|js)$ {
    root /usr/local/nginx/static;
    # none “Referer” 来源头部为空的情况
    # blocked “Referer” 来源头部不为空
    valid_referers none blocked  *.example.com ;
    if ($invalid_referer) {
        #rewrite ^/ http://www.example.com/404.jpg;
        return 403;
        break;
    }
    access_log off;
}
```

### 虚拟目录配置

alias指定的目录是准确的，root是指定目录的上级目录，并且该上级目录要含有location指定名称的同名目录。

```nginx
location /img/ {
    alias /var/www/image/;
}
# 访问/img/目录里面的文件时，ningx会自动去/var/www/image/目录找文件
location /img/ {
    root /var/www/image;
}
# 访问/img/目录下的文件时，nginx会去/var/www/image/img/目录下找文件
```

### 屏蔽.git等文件

```nginx
location ~ (.git|.gitattributes|.gitignore|.svn) {
    deny all;
}
```

### 伪静态

```nginx
# laravel
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

## 其他

### Nginx处理HTTP请求的11个阶段

    | Phases | modules / directives  | description |
    | :------------ |:---------------:| -----:|
    | NGX_HTTP_POST_READ_PHASE     | HttpRealIpModule | 读取请求内容阶段 |
    | NGX_HTTP_SERVER_REWRITE_PHASE <br/> Location ( server rewrite )     | HttpRewriteModule <br/> rewrite    |   请求地址重写阶段|
    | NGX_HTTP_FIND_CONFIG_PHASE<br/> Location ( location selection )      |    HttpCoreModule <br/> location |配置查找阶段|
    | NGX_HTTP_REWRITE_PHASE <br/> Location ( location rewrite )      |    HttpLuaModule <br/> set_by_lua、rewrite_by_lua  |请求地址重写阶段|
    | NGX_HTTP_POST_REWRITE_PHASE      |    不注册其他模块 |请求地址重写提交阶段|
    | NGX_HTTP_PREACCESS_PHASE <br/>( location selection )      |    degradation <br/> NginxHttpLimitZoneModule / limit_zone<br/> HttpLimitReqModule / limit req |访问权限检查准备阶段|
    | NGX_HTTP_ACCESS_PHASE      |    HttpAccessModule <br/> allow, deny<br/>NginxHttpAuthBasicModule <br/> HttpLuaModule <br/> access_by_lua |访问权限检查阶段|
    | NGX_HTTP_POST_ACCESS_PHASE      |    该指令可以用于控制access阶段的指令彼此之间的协作方式 |访问权限检查提交阶段|
    | NGX_HTTP_TRY_FILES_PHASE     |    HttpCoreModule <br/> try_files |配置项try_files处理阶段|
    | NGX_HTTP_CONTENT_PHASE     |    HttpProxyModule / proxy <br/> HttpLuaModule / content_by_lua<br/>HttpCoreModule / proxy_pass<br/>HttpFcgiModule / FastCGI |内容产生阶段|
    | NGX_HTTP_TRY_FILES_PHASE     |    HttpLogModuel / access_log |日志模块处理阶段|

#### **post read phase**

> `post-read ` 属于 `rewrite`阶段

> `post-read` 支持Nginx模块的钩子

> 内置模块 `ngx_realip` 把它的处理程序`post-read`分阶段挂起，强制重写请求的原始地址作为特定请求头的值

```nginx
server {
    listen 8080;

    set_real_ip_from 127.0.0.1;
    real_ip_header   X-My-IP;

    location /test {
        set $addr $remote_addr;
        echo "from: $addr";
    }
}
```

> 该配置告诉Nginx强制将每个请求的原始地址重写127.0.0.1为请求头的值X-My-IP。同时它使用内置变量 `$remote_addr`来输出请求的原始地址

```bash
$ curl -H 'X-My-IP: 1.2.3.4' localhost:8080/test
from: 1.2.3.4

$ curl localhost:8080/test
from: 127.0.0.1

$ curl -H 'X-My-IP: abc' localhost:8080/test
from: 127.0.0.1
```

#### **server_rewrite phase**

> 这个阶段主要进行初始化全局变量，或者server级别的重写。如果把重写指令放到 server 中，那么就进入了server rewrite 阶段。（重写指令见rewrite phase）  

> `server-rewrite ` 阶段运行时间早于 `rewrite` 阶段

```nginx
location /tinywan {
    set $bbb "$aaa, world";
    echo $bbb;
}
set $aaa "HELLO";
``` 

> `set $a hello` 声明被放在`server`指令中，所以它运 `server-rewrite`阶段

> 因此 `set $b "$a, world'" `，在location指令中执行 `set ` 指令后，它将获得正确的`$a`值

```bash
# curl http://127.0.0.2:8008/tinywan
HELLO, world
```
> `post-read` 阶段阶段运行时间早于 `server-rewrite` 阶段执行

```nginx
server {
    listen 8080;

    set $addr $remote_addr;

    set_real_ip_from 127.0.0.1;
    real_ip_header   X-Real-IP;

    location /test {
        echo "from: $addr";
    }
}
```

>   `ngx_realip` 阶段阶段运行时间早于 `server 的 set 指令` 阶段执行

```bash
$ curl -H 'X-Real-IP: 1.2.3.4' localhost:8080/test
from: 1.2.3.4
```

> 服务器指令中的命令集始终比模块ngx_realip晚，

> `server-rewrite` 阶段阶段运行时间早于 `find-config` 阶段执行

#### **find config phase**
        
> `find-config` 阶段阶段运行时间早于 `rewrite` 阶段执行
        
> 这个阶段使用重写之后的uri来查找对应的location，值得注意的是该阶段可能会被执行多次，因为也可能有location级别的重写指令。这个阶段并不支持 Nginx 模块注册处理程序，而是由 Nginx 核心来完成当前请求与 location 配置块之间的配对工作
    
#### **rewrite phase**

> 如果把重写指令放到 location中，那么就进入了rewrite phase，这个阶段是location级别的uri重写阶段，重写指令也可能会被执行多次  

> 有`HttpRewriteModule` 的set指令、rewrite指令      

> HttpLuaModule的 set_by_lua指令,   

> ngx_set_misc模块的set_unescape_uri指令   

> 另外HttpRewriteModule的几乎所有指令都属于rewrite阶段。

+   结论：作用域为同一个phase的不同modules的指令，如果modules之间做了特殊的兼容，则它们按照指令在配置文件中出现的顺序依次执行下来
+   HttpLuaModule 模块指令
    +   init_by_lua
        > 在nginx重新加载配置文件时，运行里面lua脚本，常用于全局变量的申请。例如lua_shared_dict共享内存的申请，只有当nginx重起后，共享内存数据才清空，这常用于统计。  

    +   set_by_lua
        > 设置一个变量，常用与计算一个逻辑，然后返回结果,该阶段不能运行Output API、Control API、Subrequest API、Cosocket API

    +   rewrite_by_lua
        > 在access阶段前运行，主要用于rewrite

    +   access_by_lua
        > 主要用于访问控制，能收集到大部分变量，类似status需要在log阶段才有。这条指令运行于nginx access阶段的末尾，因此总是在 allow 和 deny 这样的指令之后运行，虽然它们同属 access 阶段。

    +   content_by_lua 
        > 阶段是所有请求处理阶段中最为重要的一个，运行在这个阶段的配置指令一般都肩负着生成内容（content）并输出HTTP响应。    

    +   header_filter_by_lua
        > 一般只用于设置Cookie和Headers等,该阶段不能运行Output API、Control API、Subrequest API、Cosocket API

    +   body_filter_by_lua
        > 一般会在一次请求中被调用多次, 因为这是实现基于 HTTP 1.1 chunked 编码的所谓“流式输出”的,该阶段不能运行Output API、Control API、Subrequest API、Cosocket API

    +   log_by_lua 
        > 该阶段总是运行在请求结束的时候，用于请求的后续操作，如在共享内存中进行统计数据,如果要高精确的数据统计，应该使用body_filter_by_lua 

+   --with-http_realip_module 模块
    +   set_real_ip_from   192.168.1.0/24;     指定接收来自哪个前端发送的 IP head 可以是单个IP或者IP段
    +   set_real_ip_from   192.168.2.1;  
    +   real_ip_header     X-Real-IP;         IP head  的对应参数，默认即可。        