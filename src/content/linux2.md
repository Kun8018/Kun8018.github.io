---
title: Linux2
date: 2020-03-02 21:40:33
categories: 技术博客
tags:
    - IT，
toc: true
thumbnail: https://s1.ax1x.com/2020/03/27/G9OI3V.th.jpg
---

​     服务器配置

<!--more-->

## 配置多个sshkey

检查.ssh文件夹是否有文件，id_rsa.pub是公钥，id_rsa是密钥

```shell
cd ~/.ssh
ls
ssh-keygen
cat ~/.ssh/id_rsa.pub
```

生成后将公钥发送给github服务器或者linux服务器

OpenSSH 规定，用户公钥保存在服务器的`~/.ssh/authorized_keys`

你要以哪个用户的身份登录到服务器，密钥就必须保存在该用户主目录的`~/.ssh/authorized_keys`文件。只要把公钥添加到这个文件之中，就相当于公钥上传到服务器了。每个公钥占据一行。如果该文件不存在，可以手动创建。

ssh-copy-id 命令：自动上传公钥

OpenSSH 自带一个`ssh-copy-id`命令，可以自动将公钥拷贝到远程服务器的`~/.ssh/authorized_keys`文件。如果`~/.ssh/authorized_keys`文件不存在，`ssh-copy-id`命令会自动创建该文件。

`ssh-copy-id`是直接将公钥添加到`authorized_keys`文件的末尾。如果`authorized_keys`文件的末尾不是一个换行符，会导致新的公钥添加到前一个公钥的末尾，两个公钥连在一起，使得它们都无法生效。所以，如果`authorized_keys`文件已经存在，使用`ssh-copy-id`命令之前，务必保证`authorized_keys`文件的末尾是换行符

```shell
$ ssh-copy-id -i key_file user@host
```



## Nginx

Nginx是常用的网络服务器，ubuntu和debian类似，这里以centos7为例

```nginx
yum install nginx
```

nginx的默认安装目录为`/usr/local/nginx`,配置文件的目录为`/usr/local/nginx/conf/nginx.conf`

启动nginx服务

```nginx
systemctl enable nginx.service 
systemctl start nginx.service
systemctl stop nginx.service
systemctl restart nginx.service
```

nginx命令

```nginx
nginx -s reload  # 热重启
nginx -s reopen  # 重启Nginx
nginx -s stop    # 快速关闭
nginx -s quit    # 等待工作进程处理完成后关闭
nginx -T         # 查看配置文件的实际内容
```

### 默认主页、目录访问

```nginx
server {
  root /网站根目录;
  # 优先使用默认主页
  index index.html index.htm index.php;
  # 当默认主页不存在时直接列出目录内文件树
  autoindex on;
}
```

#### root与alias

nginx指定文件路径有两种方式root和alias，指令的使用方法和作用域

[root]

语法：root path

默认值：root html

配置段：http、server、location、if

[alias]

语法：alias path

配置段：location

root的处理结果是：root路径 ＋ location路径

alias的处理结果是：使用alias路径替换location路径

alias是一个目录别名的定义，root则是最上层目录的定义。

还有一个重要的区别是alias后面必须要用“/”结束，否则会找不到文件的，而root则可有可无。

比如访问的URI都是/t/a.html时，

root会返回/www/root/html/t/a.html的文件

alias会返回/www/root/html/new_t/a.html的文件

```nginx
location ^~ /t/ {
	root /www/root/html/;
}

location ^~ /t/ {
	alias /www/root/html/new_t/;
}
```



### 反向代理

先了解正向代理

正向代理：局域网中的电脑用户想要直接访问网络是不可行的，只能通过代理服务器来访问，这种代理服务就被称为正向代理。

反向代理是一个`Web`服务器，它接受客户端的连接请求，然后将请求转发给上游服务器，并将从服务器得到的结果返回给连接的客户端。

客户端访问网络不需要配置，只要把请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据，然后再返回到客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器IP地址

location匹配规则：

～：正则匹配，区分大小写

～*：正则匹配，不区分大小写

@：定义一个命名的location，用于内部定向，例如error_page、try_files

=：普通字符匹配，精确匹配

^～：普通字符匹配，如果该选项匹配，则只匹配该选项，不再向下匹配其他选项

匹配优先级（与location书写的先后顺序关系不大）：

1.精确匹配：=符号严格匹配这个查询，如果找到，停止搜索，

2.普通字符匹配：所有剩下的常规字符串，最长的匹配；如果找到^~这个符号停止搜索；

3.正则匹配；

4.默认匹配：如果第三条条件生效使用第三条，否则使用第二条

nginx做http反向代理

```nginx
location ^~ /api {
  proxy_pass http://192.168.40.174:32020;
}

server{
  listen:90;
  server_name:192.168.0.1
    
  location /edu/{
    root html;
    proxy_pass http://192.168.40.174:32020;
  }
  location /ovd/{
    root html;
    proxy_pass http://192.168.40.174:32020;
  }
}
```

更多指令说明

| 指令                     | 说明                                                         |
| :----------------------- | :----------------------------------------------------------- |
| `proxy_connect_timeout`  | `Nginx`从接受请求至连接到上游服务器的最长等待时间            |
| `proxy_send_timeout`     | 后端服务器数据回传时间(代理发送超时)                         |
| `proxy_read_timeout`     | 连接成功后，后端服务器响应时间(代理接收超时)                 |
| `proxy_cookie_domain`    | 替代从上游服务器来的`Set-Cookie`头的`domain`属性             |
| `proxy_cookie_path`      | 替代从上游服务器来的`Set-Cookie`头的`path`属性               |
| `proxy_buffer_size`      | 设置代理服务器（`nginx`）保存用户头信息的缓冲区大小          |
| `proxy_buffers`          | `proxy_buffers`缓冲区，网页平均在多少`k`以下                 |
| `proxy_set_header`       | 重写发送到上游服务器头的内容，也可以通过将某个头部的值设置为空字符串，而不发送某个头部的方法实现 |
| `proxy_ignore_headers`   | 这个指令禁止处理来自代理服务器的应答。                       |
| `proxy_intercept_errors` | 使`nginx`阻止`HTTP`应答代码为400或者更高的应答。             |



#### 泛域名转发

```nginx
server {
  listen 80;
  server_name ~^([\w-]+)\.user\.demo\.com$;
  
  #配合上面语句可以把不同的域名转发到不同目录，如xuexb.user.demo.com-> /home/user/wwwroot/user/xuexb a01.user.demo.com-> /home/user/wwwroot/user/a01
  root /home/user/wwwroot/user/$1;
  
  ## xuexb.user.demo.com/path -> 127.0.0.1:8080/xuexb/path a01.user.demo.com/path -> 127.0.0.1:8080/a01/path
  location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    proxy_pass http://127.0.0.1:8080/$1$request_uri;
  }
}
```

#### Nodejs



```nginx
server {
  server_name www.xxoo.com;
  listen 80;
  
  root /wwwroot/www.xxoo.com/;
  
  if (-f $request_filename/index.html) {
    rewrite (.*) $1/index.html break;
  }
  
  if (!-f $request_filename) {
    rewrite (.*) /index.js;
  }
  
  location = /index.js {
    
    proxy_set_header Connection "";
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    
    proxy_pass http://127.0.0.1:8001$request_uri;
    
    proxy_redirect off;
  }
}
```



### 配置浏览器缓存

不缓存

```nginx
server {
  expires -1;
  if_modified_since off;##
}
## Cache-Control: no-cache;
```

设置缓存

```nginx
server {
  expires 1d;
  ## expires max;
}
## Cache-Control: max-age=315360000
```

根据路径设置不同的缓存策略

```nginx
server {
  set $expires_time 1M;
  
  if($request_uri ~* ^/admin(\/.*)?$) {
    set $expires_time -1;
  }
  
  if($request_uri ~* ^/admin(\/.*)?$) {
    set $expires_time max;
  }
  
  expires $expires_time;
}
```



### 负载均衡

`upstream`模块能够使用3种负载均衡算法：轮询、`IP`哈希、最少连接数。

**轮询：** 默认情况下使用轮询算法，不需要配置指令来激活它，它是基于在队列中谁是下一个的原理确保访问均匀地分布到每个上游服务器；

轮询时考研指定轮询几率，`weight`和访问比率成正比，用于后端服务器性能不均的情况。 

```nginx
#10次一般只会有1次会访问到8081，而有9次会访问到8080
upstream test {
    server localhost:8080 weight=9;
    server localhost:8081 weight=1;
}
```

**IP哈希：** 通过`ip_hash`指令来激活，`Nginx`通过`IPv4`地址的前`3`个字节或者整个`IPv6`地址作为哈希键来实现，同一个IP地址总是能被映射到同一个上游服务器；

```nginx
upstream test {
    ip_hash;
    server localhost:8080;
    server localhost:8081;
}
```

**最少连接数：** 通过`least_conn`指令来激活，该算法通过选择一个活跃数最少的上游服务器进行连接。如果上游服务器处理能力不同，可以通过给`server`配置`weight`权重来说明，该算法将考虑到不同服务器的加权最少连接数。

upstream使用第三方模块

Fair：按后端服务器的响应时间来分配请求，响应时间短的优先分配。

```nginx
upstream backend {
    fair;
    server localhost:8080;
    server localhost:8081;
}
```

url_hash:这是个第三方模块，按访问`url`的`hash`结果来分配请求，使每个`url`定向到同一个后端服务器，后端服务器为缓存时比较有效。 在`upstream`中加入`hash`语句，`server`语句中不能写入`weight`等其他的参数，`hash_method`是使用的`hash`算法

```nginx
upstream backend {
    hash $request_uri;
    hash_method crc32;
    server localhost:8080;
    server localhost:8081;
}
```

upstream一般放在conf文件的开头

upstream还能够为每一个设备设置状态值，这些状态值的含义分别例如以下

1. down 表示单前的server临时不參与负载.
2. weight 默觉得1.weight越大，负载的权重就越大。
3. max_fails ：同意请求失败的次数默觉得1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误.
4. fail_timeout : max_fails次失败后。暂停的时间。

**backup：** 其他全部的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。

```nginx
upstream bakend{ #定义负载均衡设备的Ip及设备状态 
      ip_hash; 
      server 10.0.0.11:9090 down; 
      server 10.0.0.11:8080 weight=2; 
      server 10.0.0.11:6060; 
      server 10.0.0.11:7070 backup; 
}
```

#### 四层负载均衡简介

https://www.kawabangga.com/posts/5301

#### sticky

Sticky就是基于cookie的一种负载均衡解决方案，它是通过基于cookie实现客户端与后端服务器的会话保持, 在一定条件下可以保证同一个客户端访问的都是同一个后端服务器。请求来了，服务器发个cookie，并说：下次来带上，直接来找我。

Sticky是nginx的一个模块，它是基于cooki e的一种nginx的负载均衡解决方案，通过分发和识别cookie，来使同一个客户端的请求落在同一台服务器上，默认标识名为route

- 1.客户端首次发起访问请求，nginx接收后，发现请求头没有cookie，则以轮询方式将请求分发给后端服务器。
- 2.后端服务器处理完请求，将响应数据返回给nginx。
- 3.此时nginx生成带route的cookie，返回给客户端。route的值与后端服务器对应，可能是明文，也可能是md5、sha1等Hash值
- 4.客户端接收请求，并保存带route的cookie。
- 5.当客户端下一次发送请求时，会带上route，nginx根据接收到的cookie中的route值，转发给对应的后端服务器。

下载sticky

```shell
wget https://bitbucket.org/nginx-goodies/nginx-sticky-module-ng/get/master.tar.gz
tar xf master.tar.gz

#把此模块放进nginx/module目录下,名称太长，重命名一下
mkdir /usr/local/nginx/module
mv nginx-goodies-nginx-sticky-module-ng-08a395c66e42 /usr/local/nginx/module/nginx-sticky-module
```

配置

```nginx
upstream backend {
    sticky name=ngx_cookie expires=6h;
    server 192.168.31.240:8080 weight=3 max_fails=3 fail_timeout=10s;
    server 192.168.31.241:8080 weight=3 max_fails=3 fail_timeout=10s;
    server 192.168.31.242:8080 weight=6 max_fails=3 fail_timeout=10s;
    server 192.168.31.243:8080;
    server 192.168.31.244:8080 down;
}
```





### 支持CORS跨域

nginx配置做跨域处理--添加请求头

```nginx
location ^~ /p/asm {
  proxy_pass http://192.168.40.174:32020;
  add_header 'Access-Control-Allow-Origin' '*' always;
  add_header 'Access-Control-Allow-Credentials' 'true' always;
  add_header 'Access-Control-Allow-Methods' 'GET,POST,PUT,DELETE,PATCH,OPTIONS';
  add_header 'Access-Control-Allow-Headers' 'Content-Type,ssid';
  if ($request_method = 'OPTIONS') {return 204;}
  proxy_redirect     off;
  proxy_set_header   Host $host;
}
```

### 高可用keep-alived

正常情况下nginx是可以访问的，但是如果nginx出现宕机或者内存不够等程序错误，就会堵塞请求。为了防止这种情况的发生，配置高可用keep-alived进行预防

安装keep-alived

```shell
yum install keepalived -y
rpm -q -a keepalived
//keepalived-1.3.5-16.el7.x86_64
```

修改配置文件

```shell
systemctl start keepalived.service
vi keepalived.conf 
```

把原主机ip地址换为虚拟ip

```shell
global_defs {
   notification_email {
     acassen@firewall.loc
     failover@firewall.loc
     sysadmin@firewall.loc
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 192.168.25.147
   smtp_connect_timeout 30
   router_id LVS_DEVEL # 访问的主机地址
}
vrrp_script chk_nginx {
  script "/usr/local/src/nginx_check.sh"  # 检测文件的地址
  interval 2   # 检测脚本执行的间隔
  weight 2   # 权重
}
vrrp_instance VI_1 {
    state BACKUP    # 主机MASTER、备机BACKUP    
    interface ens33   # 网卡
    virtual_router_id 51 # 同一组需一致
    priority 90  # 访问优先级，主机值较大，备机较小
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.25.50  # 虚拟ip
    }
}
```

启动keep-alived

```shell
systemctl start keepalived.service
```



### 屏蔽ip/国外ip

在`nginx`的配置文件`nginx.conf`中加入如下配置，可以放到`http`, `server`, `location`, `limit_except`语句块，需要注意相对路径，本例当中`nginx.conf`，`blocksip.conf`在同一个目录中

```nginx
include blockip.conf;
```

blockip.conf

```nginx
deny IP;   # 屏蔽单个ip访问
allow IP;  # 允许单个ip访问
deny all;  # 屏蔽所有ip访问
allow all; # 允许所有ip访问
deny 123.0.0.0/8   # 屏蔽整个段即从123.0.0.1到123.255.255.254访问的命令
deny 124.45.0.0/16 # 屏蔽IP段即从123.45.0.1到123.45.255.254访问的命令
deny 123.45.6.0/24 # 屏蔽IP段即从123.45.6.1到123.45.6.254访问的命令
```

国外ip

基于 Nginx 的 ngx_http_geoip2 模块来禁止国外 IP 访问网站。

安装模块依赖

```shell
yum install libmaxminddb-devel -y
```

下载模块

```shell
git clone https://github.com/leev/ngx_http_geoip2_module.git
```

解压到/usr/local目录

```shell
mv ngx_http_geoip2_module/ /usr/local/
```

模块安装成功后，还要在 Nginx 里指定数据库，在安装运行库时默认安装了两个，位于 /usr/share/GeoIP/ 目录下，一个只有 IPv4，一个包含 IPv4 和 IPv6。

登录 www.maxmind.com 网址，创建账户，下载最新的库文件。（账户创建就不演示了）点击左侧，Download Files：

选择 GeoLite2 Country，点击 Download GZIP 下载即可：

上传到 /usr/share/GeoIP/ 下并解压：

```shell
cd /usr/share/GeoIP/
```

在nginx.conf中的http中引入数据库文件

```shell
geoip2 /usr/share/GeoIP/GeoLite2-City.mmdb {
        auto_reload 5m;
        $geoip2_data_country_code country iso_code;
        }
        map $geoip2_data_country_code $allowed_country {
default yes;
        CN no;
        }
```

在 server 中的 location 下添加条件，如果满足 IP 是国外 IP，就执行下面的 return 动作，

可以直接返回 404或者别的页面：

```nginx
if ($allowed_country = yes) {
    # return https://www.baidu.com;
    # return /home/japan;
    return 404;
}
```



### 重定向

```nginx
//重定向网站
server {
    server_name old-site.com
    return 301 $scheme://new-site.com$request_uri;
}
//重定向单页面
server {
    location = /oldpage.html {
        return 301 http://example.org/newpage.html;
    }
}
//重定向子路径
location /old-site {
    rewrite ^/old-site/(.*) http://example.org/new-site/$1 permanent;
}
```

### 配置图片防盗链

防盗链是指当图片不是自己网站打开时返回403或者指定图片，通过判断请求的来路判断是否是自己的站点来设置

```nginx
server {
  location ~* \.(gif|jpg|png|bmp)$ {
    valid_referers none blocked *.xuexb.com server_names
      
      if ($invalid_referer) {
      	return 403;
    	}
  }
}
```



### Https配置

#### let's Encrypt

let's Encrypt作为一个公共且免费SSL的项目逐渐被广大用户传播和使用，由Mozilla、Cisco、Akamai、IdenTrust等组织发起，主要的目的也是为了推进网站由http向https过度。

let's Encrypt免费SSL证书的出现，也会对传统提供付费SSL证书服务的商家有不少的打击。目前Let‘s Encrypt获得IndenTrust交叉签名，也就是可以应用且支持包括Firefox、Chrome在内的主流浏览器的兼容和支持。

使用git安装

```shell
git clone https://github.com/letsencrypt/letsencrypt
```

生成证书

```shell
cd letsencrypt
./lensencrypt-auto certonly --standalone --email quiniton@163.com -d www.zhaoheqiang.me -d zhaoheqiang.me
```

执行命令之后，会在/etc/letsencrypt/live/下找到各个域名的文件夹，每个文件夹里面会有四个密钥证书文件：

cert.pem：Apache服务器端证书

chain.pem：Apache根证书和中继证书

fullchain.pem：Nginx所需要的ssl_certificate文件

privkey.pem：安全证书KEY文件

如果是Nginx，使用fullchain.pem和privacy.pem文件，在配置文件中加入语句



```nginx
server {
  listen 443 https;
	ssl_certificate /etc/letsencrypt/live/www.zhaoheqiang.me/fullchain.pem
	ssl_certificate_key /etc/letsencrypt/live/www.zhaoheqiang.me/privkey.pem
}
```

延长有效期

let's Encrypt的证书一般有有效期，需要手动更新续期

```shell
./lensencrypt-auto certonly --renew-by-default --email quiniton@163.com -d www.zhaoheqiang.me -d zhaoheqiang.me
```

#### certbot

certbot是let's Encrypt官方推荐的获取证书的客户端。可以帮我们获取免费的let's Encrypt证书。certbot支持所有unix内核的操作系统。

安装certbot

```shell
yum install certbot
```

获取证书

```shell
certbot certonly --standalone -d example.com -d www.example.com
```

也可以用指定根目录的方式生成证书

```shell
certbot certonly --webroot -w /var/www/example -d example.com -d www.example.com
```

证书生成后就可以在/etc/letsencrypt/live目录下看到对应域名的证书

let's Encrypt提供的证书一般都有90天有效期，在证书到期之前需要更新证书，certbot提供自动更新

```shell
certbot renew --dry-run
```

安装时如果报错

```shell
Problem binding to port 80:Could not bind to IPv4 or IPv6
```

因为nginx占用80端口，需要先停掉nginx进行操作，执行自动更新时也需要停掉nginx

#### mkcert



#### openssl

先生成一个key

```shell
openssl genrsa -des3 -out ssl.key 1024
```

根据key生成证书请求文件

```shell
openssl req -new -key ssl.key -out ssl.csr
```

最后根据这两个文件生成crt证书文件，如果需要pfx可以用第二个命令生成

```shell
openssl x509 -req -days 3650 -in ssl.csr -signkey ssl.key -out ssl.cer
openssl pkcs12 -export -inkey ssl.key -in ssl.crt -out ssl.pfx
```

在需要使用证书的server中配置

```nginx
server {
  ssl on;
  ssl_certificate /home/ssl.crt;
  ssl_certificate_key /home/ssl.key;
  ssl_session_timeout 5m;
  ssl_protocols SSLv2 SSLv3 TLSv1;
  ssl_ciphers ALL:!EXPORT56:RC4+RSA+HIGH:+MEDIUM+LOW:+SSLv2:+EXP;
  ssl_prefer_server_cipher on;
  
  listen 443;
  ssl on;
  ssl_certificate /usr/local/webserver/nginx/conf/vhost/ssl/server.crt;
  ssl_certificate_key /usr/local/webserver/nginx/conf/vhost/ssl/server.key;
}
```



重启nginx就可以，使用https进行访问

#### nginx配置/http重定向到https

不可以把301和proxy_pass写在同一个server中，会产生重定向次数过多的问题

```nginx
server {
    server_name www.kunzhang.me kunzhnag.me;
    if ($host = www.kunzhang.me){
       return 301 https://$host/$request_url;
    }
}
server {
    listen 443 ssl http 1.1;
    ssl_certificate /etc/letsencrypt/live/diamondfsd.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/diamondfsd.com/privkey.pem;

    location / {
        proxy_pass http://127.0.0.1:3999;
        proxy_http_version 1.1;
        proxy_set_header X_FORWARDED_PROTO https;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
    }
}
```

### SSE/Websocket配置

要配置 [Nginx](https://zhida.zhihu.com/search?content_id=237303570&content_type=Article&match_order=1&q=Nginx&zhida_source=entity) 以支持大模型的流式输出，特别是使用 [SSE](https://zhida.zhihu.com/search?content_id=237303570&content_type=Article&match_order=1&q=SSE&zhida_source=entity) (Server-Sent Events)，你需要确保 Nginx 能够处理 [HTTP 流式连接](https://zhida.zhihu.com/search?content_id=237303570&content_type=Article&match_order=1&q=HTTP+流式连接&zhida_source=entity)

```nginx
# 定义变量用于处理 WebSocket 的 Upgrade 和 Connection 请求头：
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''  close;
}

server {
    listen 80;
    server_name 192.168.1.9;

    # 处理静态文件的路径
    location /static/ {
        alias /home/staticfiles/;
    }
    
    # 正常的接口请求转发到后端
    location / {
        proxy_pass http://127.0.0.1:8000;  # 转发到后端
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    # 转发 SSE 请求
    location ~ ^/sse/ {  # 匹配以 /sse/ 开头的路径
        proxy_pass http://127.0.0.1:8000;  # 配置代理的后端服务器地址
        proxy_http_version 1.1; # 设置 HTTP 版本，SSE 需要 HTTP/1.1
        proxy_set_header Connection ''; # 保持连接活性，不发送连接关闭的信号
        proxy_buffering off;  #不对 SSE 响应进行缓冲，直接透传给客户端
        proxy_cache off; # 关闭代理缓存
        proxy_read_timeout 3600s;  # 设置代理读取服务器响应的超时时间
        proxy_send_timeout 3600s;
        proxy_connect_timeout 1h;  # 设置客户端连接的超时时间
        proxy_set_header Host $host; # 配置代理传递的头部，确保 Host 头部正确传递
        proxy_set_header X-Accel-Buffering no; # 设置代理的响应头部，保持传输编码为 chunked
        add_header Cache-Control no-cache;
        # add_header X-Accel-Buffering "no";
        chunked_transfer_encoding on; # 启用分块传输编码
        gzip off;
                
        
        # 设置跨域资源共享 (CORS)，如果你的客户端和服务器不在同一个域上
        add_header 'Access-Control-Allow-Origin' '*' always;
        add_header 'Access-Control-Allow-Credentials' 'true' always;
        add_header 'Access-Control-Allow-Methods' 'GET, OPTIONS' always;
        add_header 'Access-Control-Allow-Headers' 'Origin,Authorization,Accept,X-Requested-With' always;
        if ($request_method = 'OPTIONS') {
            # 如果请求方法为 OPTIONS，则返回 204 (无内容)
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Methods' 'GET, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'Origin,Authorization,Accept,X-Requested-With';
            add_header 'Access-Control-Max-Age' 1728000;
            add_header 'Content-Type' 'text/plain charset=UTF-8';
            add_header 'Content-Length' 0;
            return 204;
        }
    }
    
    # 转发 WebSocket 请求
    location ^~ /ws/ {
        proxy_pass http://127.0.0.1:8000;  # 转发到 ASGI 服务器
        # 保证连接不会超时
        proxy_read_timeout 300s;
        proxy_send_timeout 300s;
        proxy_connect_timeout 3600s;
    
        # 转发 WebSocket 请求头
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    
        # WebSocket 特有配置
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    
        # 可选: 传递 Authorization 头部，如果需要身份验证
        # proxy_set_header Authorization $http_authorization;
    }
}
```



### 水平扩容

动态水平扩容是Nginx的一个重要特性，它允许在运行时根据需求动态地增加或减少服务器的处理能力。

Nginx的动态水平扩容基于其模块化架构和事件驱动的处理方式。Nginx由多个模块组成，每个模块负责特定的功能，如请求处理、[负载均衡](https://cloud.baidu.com/product/blb.html)等。这种模块化设计使得Nginx能够灵活地扩展其功能，通过添加或删除模块来适应不同的需求。

事件驱动的架构使得Nginx能够高效地处理大量并发连接。它采用非阻塞IO模型，能够同时处理数万个并发连接，而不会因为单个连接的延迟而影响整体性能。这种架构使得Nginx能够在不增加服务器硬件的情况下，通过增加工作进程数量来提高处理能力。

要实现Nginx的动态水平扩容，通常需要以下几个步骤：

1. 配置负载均衡器：首先，需要在Nginx之前配置一个负载均衡器，如HAProxy或Consul。负载均衡器负责将客户端请求分发到不同的服务器上。通过配置负载均衡器，可以轻松地添加或减少服务器实例，实现动态扩容。
2. 配置Nginx服务器：在每个Nginx服务器上，需要配置反向代理和负载均衡。通过配置代理模块，如ngx_http_proxy_module，可以将客户端请求转发到后端的应用服务器上。同时，通过配置负载均衡算法，如轮询或IP哈希，可以确保请求被均匀地分发到各个应用服务器上。
3. 监控和自动扩容：要实现动态水平扩容，需要监控服务器的性能指标，如CPU使用率、内存消耗和请求延迟等。当这些指标超过预设阈值时，可以自动触发扩容操作。通过编写脚本或使用第三方工具，可以自动增加或减少Nginx服务器实例的数量，以满足需求。

在实现Nginx的动态水平扩容时，需要注意以下几点：

1. 选择合适的负载均衡器：根据实际需求选择适合的负载均衡器，如HAProxy适用于高可用性场景，Consul适用于服务发现和负载均衡。
2. 配置健康检查：为了确保请求能够正确地分发到健康的应用服务器上，需要配置负载均衡器的健康检查功能。这样可以避免将请求转发到故障服务器上。
3. 优化Nginx配置：根据实际场景优化Nginx的配置，如调整工作进程数量、开启keepalive连接等，以提高服务器的处理能力和性能。
4. 监控和分析：建立完善的监控体系和分析机制，以便及时发现和解决性能瓶颈和问题。通过收集和分析[日志](https://cloud.baidu.com/product/bls.html)数据，可以深入了解服务器的运行状况和用户行为。



### 缓冲优化

Nginx代理之后会有相应的代理缓存区，缓存区默认只有几十K，某些版本的nginx默认设置中没有相关处理，导致部分文件代理是会出现加载不全的现象，其实不仅仅是JS文件。只是因为框架的JS文件略大，所以经常出现类似问题。

在nginx.conf中添加

```conf
http {
	proxy_buffer_size 128k;
	proxy_buffers   32 128k;
	proxy_busy_buffers_size 128k;
}
```

### gzip压缩

```nginx
http {
  # 开启gzip
  gzip on;

  # 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
  gzip_min_length 1k;

  # gzip 压缩级别，1-10，数字越大压缩的越好，也越占用CPU时间
  gzip_comp_level 1;

  # 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
  gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;

  # 是否在http header中添加Vary: Accept-Encoding，建议开启
  gzip_vary on;

  # 禁用IE 6 gzip
  gzip_disable "MSIE [1-6]\.";
}
```



### 错误日志

打开nginx.conf文件

```shell
vim /etc/nginx/nginx.conf
```

#### logstash/Doris收集日志

https://hezhiqiang8909.gitbook.io/java/docs/javatool/elastic/elastic-logstash

Logstash 可以传输和处理你的日志、事务或其他数据，然后交给es检索。

Logstash 有两个必要元素：`input` 和 `output` ，一个可选元素：`filter`

这三个元素，分别代表 Logstash 事件处理的三个阶段：输入 > 过滤器 > 输出

- input 负责从数据源采集数据。
- filter 将数据修改为你指定的格式或内容。
- output 将数据传输到目的地。

在实际应用场景中，通常输入、输出、过滤器不止一个。Logstash 的这三个元素都使用插件式管理方式，用户可以根据应用需要，灵活的选用各阶段需要的插件，并组合使用。

- `**logstash.yml**`：logstash 的默认启动配置文件
- `**jvm.options**`：logstash 的 JVM 配置文件。
- `**startup.options**` (Linux)：包含系统安装脚本在 `/usr/share/logstash/bin` 中使用的选项为您的系统构建适当的启动脚本。安装 Logstash 软件包时，系统安装脚本将在安装过程结束时执行，并使用 `startup.options` 中指定的设置来设置用户，组，服务名称和服务描述等选项。

Doris: https://199604.com/3220

### 性能

内容缓存：允许浏览器基本上永久地缓存静态内容。 `Nginx`将为您设置`Expires`和`Cache-Control`头信息。

设置nginx的静态文件地址

```nginx
location / {
  add_header Cache-Control max-age=360000;
  root /usr/share/nginx/html/webrtc-sdk/dist/;
}
```

设置nginx做websocket代理

```nginx
location ^~ /websocket {
  proxy_pass         http://192.168.40.174:31089;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "Upgrade";
}
```

设置nginx最大打开文件限制

```nginx
user root root;
worker_processes 4;
worker_rlimit_nofile 65535;
```

设置nginx拦截某个请求，并直接返回状态码



```nginx
location ^~ /p/asm {
  return 204 "OK";
}
```

设置nginx给某个路径单独的日志文件

```nginx
location ^~ /p/asm {
  access_log /var/log/nginx/a.log;
  error_log /var/log/nginx/a.err.log;
}
```

#### 警告

```shell
Starting nginx: nginx: [warn] could not build optimal proxy_headers_hash, you should increase either proxy_headers_hash_max_size: 512 or proxy_headers_hash_bucket_size: 64; ignoring proxy_headers_hash_bucket_size
nginx: [warn] could not build optimal proxy_headers_hash, you should increase either proxy_headers_hash_max_size: 512 or proxy_headers_hash_bucket_size: 64; ignoring proxy_headers_hash_bucket_size
nginx: [warn] could not build optimal proxy_headers_hash, you should increase either proxy_headers_hash_max_size: 512 or proxy_headers_hash_bucket_size: 64;
```

在代理中设置

```conf
proxy_headers_hash_max_size 51200;
proxy_headers_hash_bucket_size 6400;
```

### 伪静态

伪静态即是网站本身动态网页，如。php、。asp、。aspx等格式动态网页，加？参数来读取数据库内不同资料，伪静态就是做url重写操作，伪静态最主要的作用是利于seo，

```nginx
location / {
  rewrite c(\d+1)_(.*).html /index.php?c=user&id=$1&title=$2 last;
  root /usr/share/nginx/html/sta;
  index index.html index.htm index.php
}
```

### 405报错

Apache、IIS、[Nginx](https://www.izhangheng.com/tag/nginx)等绝大多数web服务器，都不允许静态文件响应POST请求，否则会返回“HTTP/1.1 405 Method not allowed”错误。

nignx的问题，一般可以通过下面的方法解决，只需要更改nginx配置，增加一句

```conf
server {
    listen       80;
    server_name  域名;
 location /{
       root /www/文件目录;
       index index.html index.htm index.php;
       error_page 405 =200 http://$host$request_uri;
    }
 }  
```



### Nginx WebUI（直连）

nginx网页配置工具

github：https://github.com/cym1102/nginxWebUI

https://www.nginxwebui.cn/product.html



#### nginx-proxy-manager

https://github.com/NginxProxyManager/nginx-proxy-manager



#### nginx-ui

在实际使用nginx-proxy-manage过程中也存在一些缺点和局限性：

- **不支持负载均衡：**Nginx Proxy Manager虽然基于Nginx，但它不支持Nginx的负载均衡功能。
- **不支持中文界面：**Nginx Proxy Manager的用户界面默认是英文，目前没有中文版本。

一个Nginx Web用户界面，提供了一个简单、强大且快速的方式来管理Nginx配置。支持实时监控服务器指标、在线编辑配置、自动部署和续期Let's Encrypt证书、查看Nginx日志等功能，并且具备响应式设计，支持国际化，使用Go和Vue.js构建，旨在提供一种直观且高效的Nginx管理体验。

特点：

**服务器指标在线统计：**实时监控CPU使用率、内存使用率、平均负载和磁盘使用情况。

**在线ChatGPT助手：**在平台内直接获得AI驱动的ChatGPT帮助。

**一键部署和自动续期：**轻松一键部署和自动续期Let's Encrypt证书。

**在线编辑网站配置：**使用Nginx UI提供的Nginx配置编辑器（Nginx的区块编辑器）或Ace代码编辑器（支持Nginx配置语法高亮）在线编辑配置。

**在线查看Nginx日志：**直接在线访问和查看Nginx日志。

**自动测试和重新加载配置：**自动测试配置文件并在保存更改后自动重新加载Nginx。

**多语言支持：**支持英语、简体中文和繁体中文等，满足了不同用户的语言需求

**Web终端：**提供基于Web的终端以便管理。

**暗黑模式：**支持暗黑模式，提供更舒适的用户体验。

响应式网页设计：通过响应式设计在任何设备上享受无缝体验。

docker安装

```shell
docker run -dit \  --name=nginx-ui \  --restart=always \  -e TZ=Asia/Shanghai \  -v /mnt/user/appdata/nginx:/etc/nginx \  -v /mnt/user/appdata/nginx-ui:/etc/nginx-ui \  -v /var/www:/var/www \  -p 8080:80 -p 8443:443 \  uozi/nginx-ui:latest
```

脚本安装

```shell
bash <(curl -L -s https://mirror.ghproxy.com/https://raw.githubusercontent.com/0xJacky/nginx-ui/master/install.sh) install -r https://mirror.ghproxy.com/
```



https://github.com/0xJacky/nginx-ui



### nginx配置生成工具

https://github.com/digitalocean/nginxconfig.io



### 资源

Https://xuexb.github.io/learn-nginx

