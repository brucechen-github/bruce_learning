# nginx# _# 配置
```
server {
  listen 80;
  server_name ng-camp.ninghao.co;

  location / {
    root /usr/share/nginx/html;
    index index.html;
    try_files $uri $uri/ /index.html;
  }
}
```

Dockerfile
```
FROM node:10-alpine as builder
WORKDIR /usr/src/app

COPY . .
RUN npm install
RUN npm run build:prod

FROM nginx:1.17
COPY --from=builder /usr/src/app/dist/ninghao-ng-camp /usr/share/nginx/html
COPY ./nginx-angular.conf /etc/nginx/conf.d/default.conf
```

# Nginx

默认情况下，Nginx 会被安装在 /usr/local/nginx。通过设定编译选项，你可以改变这个设定。

nginx 命令
1. nginx -s stop ：快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。

2. nginx -s quit ：平稳关闭Nginx，保存相关信息，有安排的结束web服务。

3. nginx -s reload ：因改变了Nginx相关配置，需要重新加载配置而重载。

4. nginx -s reopen ：重新打开日志文件。

5. nginx -c filename ：为 Nginx 指定一个配置文件，来代替缺省的。

6. nginx -t ：不运行，而仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。

7. nginx -v：显示 nginx 的版本。

8. nginx -V：显示 nginx 的版本，编译器版本和配置参数。

- - -
批处理bat
```
@echo off
rem 如果启动前已经启动nginx并记录下pid文件，会kill指定进程
nginx.exe -s stop

rem 测试配置文件语法正确性
nginx.exe -t -c conf/nginx.conf

rem 显示版本信息
nginx.exe -v

rem 按照指定配置去启动nginx
nginx.exe -c conf/nginx.conf
```

- - -

配置conf

```
  #设定实际的服务器列表 
   upstream zp_server1{
       server 127.0.0.1:8089;
   }

  #反向代理的路径（和upstream绑定），location 后面设置映射的路径
       location / {
           proxy_pass http://zp_server1;
       }
```

- - -
测试
```
启动 webapp，注意启动绑定的端口要和nginx中的 upstream 设置的端口保持一致。

更改 host：

在 C:WindowsSystem32driversetc 目录下的host文件中添加一条DNS 记录127.0.0.1 www.helloworld.com

启动前文中 startup.bat 的命令

在浏览器中访问 www.helloworld.com，不出意外，已经可以访问了。
```

- - -
负载均衡
```
http {
    #设定mime类型,类型由mime.type文件定义
   include       /etc/nginx/mime.types;
   default_type  application/octet-stream;
   #设定日志格式
   access_log    /var/log/nginx/access.log;

   #设定负载均衡的服务器列表
   ***__upstream load_balance_server {
       #weigth参数表示权值，权值越高被分配到的几率越大
       server 192.168.1.11:80   weight=5;
       server 192.168.1.12:80   weight=1;
       server 192.168.1.13:80   weight=6;
   }__***

  #HTTP服务器
  server {
       #侦听80端口
       listen       80;
       
       #定义使用www.xx.com访问
       server_name  www.helloworld.com;

       #对所有请求进行负载均衡请求
       location / {
           root        /root;                 #定义服务器的默认网站根目录位置
           index       index.html index.htm;  #定义首页索引文件的名称
           ***__proxy_pass  http://load_balance_server ;#请求转向load_balance_server 定义的服务器列表

    __***       #以下是一些反向代理的配置(可选择性配置)
           #proxy_redirect off;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
           proxy_set_header X-Forwarded-For $remote_addr;
           proxy_connect_timeout 90;          #nginx跟后端服务器连接超时时间(代理连接超时)
           proxy_send_timeout 90;             #后端服务器数据回传时间(代理发送超时)
           proxy_read_timeout 90;             #连接成功后，后端服务器响应时间(代理接收超时)
           proxy_buffer_size 4k;              #设置代理服务器（nginx）保存用户头信息的缓冲区大小
           proxy_buffers 4 32k;               #proxy_buffers缓冲区，网页平均在32k以下的话，这样设置
           proxy_busy_buffers_size 64k;       #高负荷下缓冲大小（proxy_buffers*2）
           proxy_temp_file_write_size 64k;    #设定缓存文件夹大小，大于这个值，将从upstream服务器传
           
           client_max_body_size 10m;          #允许客户端请求的最大单文件字节数
           client_body_buffer_size 128k;      #缓冲区代理缓冲用户端请求的最大字节数
  **     }__*
***__ }
}
```

负载均衡示例2

```
#服务器的集群（这个就是我们要配置的地方） #test.com:服务器集群名字,自定义 
upstream test.com { 
     #服务器配置 weight是权重的意思，权重越大，分配的概率越大。 
    #127.0.0.1:8080、127.0.0.1:8081对应tomcat服务器地址 
    server 127.0.0.1:8080 weight=1; 
    server 127.0.0.1:8081 weight=2; 
}
server {
    listen 81; 
    server_name localhost;
    location / { 
       #配置反向代理地址 
       proxy_pass http://test.com; 
       proxy_redirect default; 
}
```
- - -
```
http {
   #此处省略一些基本配置
   
   upstream product_server{
       server www.helloworld.com:8081;
   }
   
   upstream admin_server{
       server www.helloworld.com:8082;
   }
   
   upstream finance_server{
       server www.helloworld.com:8083;
   }

   server {
       #此处省略一些基本配置
       #默认指向product的server
       location / {
           proxy_pass http://product_server;
       }

       location /product/{
           proxy_pass http://product_server;
       }

       location /admin/ {
           proxy_pass http://admin_server;
       }
       
       location /finance/ {
           proxy_pass http://finance_server;
       }
   }
}
```

- - -

nginx反向代理主要通过proxy_pass来配置，将你项目的开发机地址填写到proxy_pass后面，正常的格式为proxy_pass URL即可

```
server {
    listen 80;
    location / {
        proxy_pass http://10.10.10.10:20186;
    }
}
```

upstream模块

```
// 修改nginx.conf
worker_processes 1;
events {
    worker_connections 1024;
}
http {
    upstream firstdemo {
        server 39.106.145.33;
        server 47.93.6.93;
    }
    server {
        listen 8080;
        location / {
            proxy_pass http://firstdemo;
        }
    }
}
```

不过，更应该做到的是当用户第一次访问到其中一台服务器后，下次再访问的时候就直接访问该台服务器就好了，不用总变化了。那么就发挥了ip_hash的威力了
```
// 省略...
    upstream firstdemo {
        ip_hash;
        server 39.106.145.33;
        server 47.93.6.93;
    }
```
ip_hash它的作用是如果第一次访问该服务器后就记录，之后再访问都是该服务器了，这样比如第一次访问是33服务器，那之后再访问也会分配为33服务器访问了

```
server {
    listen       80;
    server_name  chd.news.so.m.qss.test.so.com ;
    auth_basic off;
    location / {
        proxy_pass    http://10.10.10.10:20186;
        proxy_set_header Host $host;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_connect_timeout 60;
        proxy_read_timeout 600;
        proxy_send_timeout 600;
    }
}
```

# Nginx_让网站支持Https
1. nginx 开启ssl模块

```
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
```

运行命令: make 不要进行make install，否则就是覆盖安装

然后将刚刚编译好的nginx覆盖掉原有的nginx（这个时候nginx要停止状态）
```
cp ./objs/nginx /usr/local/nginx/sbin/
```
然后启动nginx，仍可以通过命令查看是否已经加入成功
查看nginx模块
```
/usr/local/nginx/sbin/nginx -V
```
在configure arguments:后面显示的原有的configure参数

Nginx 配置Http和Https共存
```
server {
            listen 80 default backlog=2048;
            listen 443 ssl;
            server_name wosign.com;
            root /var/www/html;
  
            ssl_certificate /usr/local/Tengine/sslcrt/ wosign.com.crt;
            ssl_certificate_key /usr/local/Tengine/sslcrt/ wosign.com .Key;
       }
```
把ssl on；这行去掉，ssl写在443端口后面。这样http和https的链接都可以用

- - -
安装certbot

```
cd /root/

wget https://dl.eff.org/certbot-auto --no-check-certificate

chmod +x ./certbot-auto

./certbot-auto -n
```

生成域名证书
```
./certbot-auto certonly --email 1071495011@qq.com --agree-tos --no-eff-email --webroot -w /home/bruce/html -d www.tuobo.ltd
```

nginx conf
```
 listen 443 ssl; 
 ssl_certificate /etc/letsencrypt/live/www.tuobo.ltd/fullchain.pem; 
 ssl_certificate_key /etc/letsencrypt/live/www.tuobo.ltd/privkey.pem; 
```

Nginx SSL性能调优
```
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_ciphers ECDHE-RSA-AES256-SHA384:AES256-SHA256:RC4:HIGH:!MD5:!aNULL:!eNULL:!NULL:!DH:!EDH:!AESGCM;
ssl_prefer_server_ciphers on;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

- - -

未能成功的方法
1. 下载
```
yum install certbot python2-certbot-nginx
```

Issue:   Fail python-urllib3.noarch 0:1.10.2-7.el7
```
pip uninstall urllib3
pip install --upgrade urllib3
```

