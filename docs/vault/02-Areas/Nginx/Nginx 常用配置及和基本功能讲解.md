**作者：京东物流 殷世杰**

Nginx 已经广泛应用于 J-one 和 Jdos 的环境部署上，本文对 Nginx 的常用的配置和基本功能进行讲解，适合 Ngnix 入门学习。

# 1 核心配置

找到 Nginx 安装目录下的 conf 目录下 nginx.conf 文件，Nginx 的基本功能配置是由它提供的。

## **1.1 配置文件结构**

Nginx 的配置文件 (conf/nginx.conf) 整体上分为如下几个部分： ：

<table><thead><tr><th>区域</th><th>职责</th></tr></thead><tbody><tr><td>全局块</td><td>配置和 Nginx 运行相关的全局配置</td></tr><tr><td>events 块</td><td>配置和网络链接相关的配置</td></tr><tr><td>http 块</td><td>配置代理、缓存、日志记录、虚拟主机等配置</td></tr><tr><td>server 块</td><td>配置虚拟主机的相关参数，一个 http 快中可以有多个 server 块</td></tr><tr><td>location 块</td><td>配置请求的路由，以及各种页面的处理情况</td></tr></tbody></table>

配置层级图如下所示。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/92c2ef9e2cdb41058f25ba2c01e33120~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

## **1.2 配置文件示例**

一个比较全的配置文件示例如下。

```yml
# 以下是全局段配置
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;  #设置进程数，默认为1
#pid /nginx/pid/nginx.pid; #指定nginx进程运行文件存放地址
error_log log/error.log debug;  #制定日志路径，级别：debug|info|notice|warn|error|crit|alert|emerg
# events段配置信息
events {
    accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;    #最大连接数，默认为512
}
# http、配置请求信息
http {
    include       mime.types;   #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。


    upstream mysvr {   
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    error_page 404 https://www.baidu.com; #错误页
    # 第一个Server区块开始，表示一个独立的虚拟主机站点
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
}


```

## **1.3 locat 路径映射讲解**

### **1.3.1 格式：**

location [ = | ~ | ~* | !~ | !~* | @ ] uri {...}

### **1.3.2 解释：**

= 表示精确匹配，如果找到，立即停止搜索并立即处理此请求。

~ 表示执行一个正则匹配，区分大小写匹配

~* 表示执行一个正则匹配，不区分大小写匹配

!~ 区分大小写不匹配

!~* 不区分大小写不匹配

^~ 即表示只匹配普通字符（空格）。使用前缀匹配，^ 表示 “非”，即不查询正则表达式。如果匹配成功，则不再匹配其他 location。

@ 指定一个命名的 location，一般只用于内部重定向请求。例如 error_page, try_files

uri 是待匹配的请求字符串，可以不包含正则表达式，也可以包含正则表达式；

### **1.3.3 优先级和示例：**

*   [不加] < [~/~*] <[^~] < [=]
    
*   示例如下：
    

```yml
location = / {
    # 精确匹配/，主机名后面不能带任何字符串 /
    # 只匹配http://abc.com
    # http://abc.com [匹配成功]
    # http://abc.com/index [匹配失败]
}
location ^~ /img/ {
      #以 /img/ 开头的请求，都会匹配上
    #http://abc.com/img/a.jpg   [成功]
    #http://abc.com/img/b.mp4  [成功]
    }
location ~* /Example/ {
  # 则会忽略 uri 部分的大小写
  #http://abc.com/test/Example/ [匹配成功]
  #http://abc.com/example/ [匹配成功]
}
location /documents {
    # 如果有正则表达式可以匹配，则优先匹配正则表达式。
    #http://abc.com/documentsabc [匹配成功]
}
location / {
    #http://abc.com/abc [匹配成功]
}


```

# 2 反向代理

## **2.1 反向代理概念：**

反向代理 (Reverse Proxy) 是指以代理服务器来接受 internet 上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给 internet 上请求连接的客户端。真实的服务器不能直接被外部网络访问，所以需要一台代理服务器，而代理服务器能被外部网络访问的同时又跟真实服务器在同一个网络环境，当然也可能是同一台服务器，端口不同而已。

反向代理通过 proxy_pass 指令来实现。

## **2.2 反向代理示例：**

```
server {
    listen       80;
    server_name  localhost;


    location / {
         proxy_pass http://localhost:8081;
         proxy_set_header Host $host:$server_port;#为请求头添加Host字段，用于指定请求服务器的域名/IP地址和端口号。  


         # 设置用户ip地址
         proxy_set_header X-Forwarded-For $remote_addr;#为请求头添加XFF字段，值为客户端的IP地址。
         # 当请求服务器出错去寻找其他服务器
         proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;
    


```

当我们访问 localhost 的时候，ngnix 就将我们的请求转到 localhost:8081 了

# 3 负载均衡

## **3.1 负载均衡概念：**

当有 2 台或以上服务器时，代理服务器根据规则将请求分发到指定的服务器上处理。

## **3.2 负载均衡策略及示例：**

Nginx 目前支持多种负载均衡策略，这里讲解常用的 6 种。

### **3.2.1RR(round robin : 轮询 默认)：**

每个请求按时间顺序逐一分配到不同的后端服务器，也就是说第一次请求分配到第一台服务器上，第二次请求分配到第二台服务器上，如果只有两台服务器，第三次请求继续分配到第一台上，这样循环轮询下去，也就是服务器接收请求的比例是 1:1， 如果后端服务器 down 掉，能自动剔除。轮询是默认配置，不需要太多的配置

同一个项目分别使用 8081 和 8082 端口启动项目

```
upstream web_servers {
   server localhost:8081;
   server localhost:8082;
}




server {
    listen       80;
    server_name  localhost;
    #access_log  logs/host.access.log  main;
    location / {
        proxy_pass http://web_servers;
        proxy_set_header Host $host:$server_port;
    }
 


```

### **3.2.2 热备：**

假设有 2 台服务器，当一台服务器发生事故时，才启用第二台服务器给提供服务。服务器处理请求的顺序：AAAAAA 突然 A 挂了，服务器处理请求的顺序：BBBBBBBBBBBBBB.....

```
upstream web_servers {
      server 127.0.0.1:7878; 
      server 192.168.10.121:3333 backup;  #热备     
    }


```

### **3.2.3 权重**

跟据配置的权重的大小而分发给不同服务器不同数量的请求。如果不设置，则默认为 1。下面服务器的请求顺序为：ABBABBABBABBABB....。

```
upstream web_servers {
    server localhost:8081 weight=1;
    server localhost:8082 weight=2;
}


```

### **3.2.4 ip_hash**

这样每个 ip 地址固定访问一个后端服务器，可以解决 session 的问题。

```
upstream test {
    ip_hash;
    server localhost:8080;
    server localhost:8081;
}


```

### **3.2.5 fair(第三方)**

按后端服务器的响应时间来分配请求，响应时间短的优先分配。这个配置是为了更快的给用户响应。

```
upstream backend {
    fair;
    server localhost:8080;
    server localhost:8081;
}


```

### **3.2.6 url_hash(第三方)**

按访问 url 的 hash 结果来分配请求，使每个 url 定向到同一个后端服务器，后端服务器为缓存时比较有效。在 upstream 中加入 hash 语句，hash_method 是使用的 hash 算法

```
upstream backend {
    hash_method crc32;
    hash $request_uri;
    server localhost:8080;
    server localhost:8081;
}


```

以上 6 种负载均衡各自适用不同情况下单独或者混合使用，可以根据实际情况选择使用，fair 和 url_hash 需要安装第三方模块才能使用。

# 4 动静分离：

## **4.1 动静分离概念：**

动静分离是指在 web 服务器架构中，将静态页面与动态页面或者静态内容接口和动态内容接口分开不同系统访问的架构设计方法，进而提升整个服务访问性能和可维护性。

## **4.2 动静分离示例：**

```
upstream web_servers {
       server localhost:8081;
       server localhost:8082;
}
server {
    listen       80;
    server_name  localhost;
    set $doc_root /usr/local/var/www;


    location ~* \.(gif|jpg|jpeg|png|bmp|ico|swf|css|js)$ {
       root $doc_root/img;
    }
    location / {
        proxy_pass http://web_servers;
        proxy_set_header Host $host:$server_port;
    }
    error_page 500 502 503 504  /50x.html;  #出现 500 502 503 504错误时走内部跳转
    location = /50x.html { 
        root $doc_root;
    }
 }


```

结果：访问 [http://localhost/test.jpg 时直接返回 / usr/local/var/www/img 路径下的图片](https://link.juejin.cn?target=http%3A%2F%2Flocalhost%2Ftest.jpg%25E6%2597%25B6%25E7%259B%25B4%25E6%258E%25A5%25E8%25BF%2594%25E5%259B%259E%2Fusr%2Flocal%2Fvar%2Fwww%2Fimg%25E8%25B7%25AF%25E5%25BE%2584%25E4%25B8%258B%25E7%259A%2584%25E5%259B%25BE%25E7%2589%2587 "http://localhost/test.jpg%E6%97%B6%E7%9B%B4%E6%8E%A5%E8%BF%94%E5%9B%9E/usr/local/var/www/img%E8%B7%AF%E5%BE%84%E4%B8%8B%E7%9A%84%E5%9B%BE%E7%89%87").

访问 [http://localhost/index.html 就会访问后端服务器 (tomcat 等)](https://link.juejin.cn?target=http%3A%2F%2Flocalhost%2Findex.html%25E5%25B0%25B1%25E4%25BC%259A%25E8%25AE%25BF%25E9%2597%25AE%25E5%2590%258E%25E7%25AB%25AF%25E6%259C%258D%25E5%258A%25A1%25E5%2599%25A8(tomcat%25E7%25AD%2589) "http://localhost/index.html%E5%B0%B1%E4%BC%9A%E8%AE%BF%E9%97%AE%E5%90%8E%E7%AB%AF%E6%9C%8D%E5%8A%A1%E5%99%A8(tomcat%E7%AD%89)")

# 5 其他常用的指令：

## **5.1.return 指令**

返回 http 状态码和可选的第二个参数可以是重定向的 URL

```
return code [text];
return code URL;
return URL;
例如：
location / {
 return 404; # 直接返回状态码
}
location / {
 return 404 "pages not found"; # 返回状态码 + 一段文本
}
location / {
 return 302 /bbs ; # 返回状态码 + 重定向地址
}
location / {
 return https://www.baidu.com ; # 返回重定向地址
}


```

## **5.2 rewrite 指令**

重写 URI 请求 rewrite，通过使用 rewrite 指令在请求处理期间多次修改请求 URI，该指令具有一个可选参数和两个必需参数。

第一个 (必需) 参数是请求 URI 必须匹配的正则表达式。

第二个参数是用于替换匹配 URI 的 URI。

可选的第三个参数重写策略

*   last 重写后的 URL 发起新请求，再次进入 server 段，重试 location 的中的匹配；
    
*   break 直接使用重写后的 URL ，不再匹配其它 location 中语句；
    
*   redirect 返回 302 临时重定向；
    
*   permanent 返回 301 永久重定向；
    

```
location /users/ {
    rewrite ^/users/(.*)$ /show?user=$1 break;
}


```

## **5.3 error_page 指令**

使用 error_page 指令，您可以配置 NGINX 返回自定义页面以及错误代码，替换响应中的其他错误代码，或将浏览器重定向到其他 URI。在以下示例中，error_page 指令指定要返回 404 页面错误代码的页面 (/404.html)。

```
    server{
    	error_page 500 502 503 504 /50x.html;
    	location =/50x.html{
    		root html;
    	}
    }


```

## **5.4 日志**

访问日志：需要开启压缩 gzip on; 否则不生成日志文件，打开 log_format、access_log 注释

```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';




access_log  /usr/local/etc/nginx/logs/host.access.log  main;




gzip 


```

## **5.5 deny 、allow 指令**

```
#禁止访问某个目录
location / {
    allow 192.168.0.0;
    allow 127.0.0.1;
    deny all;
#这段配置值允许192.168.0./24网段和127.0.0.1的请求，其他来源IP全部拒绝。
}




```

## **5.6 内置变量**

nginx 的配置文件中可以使用的内置变量以美元符 $ 开始。其中，大部分预定义的变量的值由客户端发送携带。

*   args ：# 这个变量等于请求行中的参数，同 query_string
    
*   $content_length ：请求头中的 Content-length 字段。
    
*   $content_type ：请求头中的 Content-Type 字段。
    
*   $document_root ：当前请求在 root 指令中指定的值。
    
*   $host ：请求行的主机名，为空则为请求头字段 Host 中的主机名，再为空则与请求匹配的 server_name
    
*   $http_user_agent ：客户端 agent 信息
    
*   $http_cookie ：客户端 cookie 信息
    
*   $limit_rate ：这个变量可以限制连接速率。
    
*   $request_method ：客户端请求的动作，通常为 GET 或 POST。
    
*   $remote_addr ：客户端的 IP 地址。
    
*   $remote_port ：客户端的端口。
    
*   $remote_user ：已经经过 Auth Basic Module 验证的用户名。
    
*   $request_filename ：当前请求的文件路径，由 root 或 alias 指令与 URI 请求生成。
    
*   $scheme ：HTTP 方法（如 http，https）。
    
*   $server_protocol ：请求使用的协议，通常是 HTTP/1.0 或 HTTP/1.1。
    
*   $server_addr ：服务器地址，在完成一次系统调用后可以确定这个值。
    
*   $server_name ：服务器名称。
    
*   $server_port ：请求到达服务器的端口号。
    
*   $request_uri ：包含请求参数的原始 URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
    
*   uri：不带请求参数的当前 URI，uri 不包含主机名，如”/foo/bar.html”。
    
*    documentu​ri：与 uri 相同
    

# 6 总结

Ngnix 是一款高性能反向代理服务器，学习它非常有必要，本文讲解了 Ngnix 核心配置，介绍了反向代理，负载均衡，动静分离三大功能，最后扩展了一些常用的指令。本文介绍了 Ngnix 的基础用法，后续的 Ngnix 内核以及原理部分有待研究。