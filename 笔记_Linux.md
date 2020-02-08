# Linux_安装Gradle

1. 下载
```
wget https://downloads.gradle.org/distributions/gradle-6.1.1-bin.zip
```

2. 解压
```
unzip gradle-6.1.1
```

3. 配置环境变量 export PATH

4. 配置全局init.gradle
~/.gradle/init.gradle

```
allprojects{
    repositories {
        def ALIYUN_REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public'
        def ALIYUN_JCENTER_URL = 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_REPOSITORY_URL."
                    remove repo
                }
                if (url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_JCENTER_URL."
                    remove repo
                }
            }
        }
        maven {
                url ALIYUN_REPOSITORY_URL
            url ALIYUN_JCENTER_URL
        }
    }
}

```

5. chmod 777 ~/.gradle/init.gradle
# Linux_Tips

1. linux 虚拟终端有256 最好使用命令 logout
   不影响，毕竟很多
- - -

2. /mnt/cdrom/Packages 这里有很多RPM

- - -
3. 在linux中，0表示标准输入，即stdin；1表示标准输出，即stdout；2表示标准错误，即stderr。

>表示重定向，&表示取地址，那么&1就是取到了标准输出的地址，也就是 2>&1表示的是将标准错误重定向到了标准输出，也就是将运行脚本中出错的信息重定向到了标准输出。
```
nohup java -jar halo-1.2.0.jar --spring.config.location=halo-application.yaml > halo.log 2>&1 &
```
- - -
4. Linux 设置代理 没试验过, 直接在/etc/profile下

/etc/profile
~/.bash_profile

增加
http_proxy=xxx:8080
https_proxy=xxx:8080
export http_proxy https_proxy


看能否上网 route -n 可查看网关
进/etc/resolv.confg
有一个字段 nameserver 设成网关 DNS

需要重启网络服务
service network restart
/etc/init.d/network restart

- - -
5. 如果想开放端口（如：8889）

（1）通过vi /etc/sysconfig/iptables 进入编辑增添一条-A INPUT -p tcp -m tcp --dport 8889 -j ACCEPT 即可

（2）执行 /etc/init.d/iptables restart 命令将iptables服务重启

（3）保存 /etc/rc.d/init.d/iptables save

- - -
6. 软链接 原文件一定要有绝对路径

# Linux_安装node

1. 下载
```
 wget http://nodejs.org/dist/v12.14.1/node-v12.14.1-linux-x64.tar.gz
```

2. 解压
```
tar -zxvf node-v12.14.1-linux-x64.tar.gz
```

3.  创建软链接 ln -s 
```
ln -s /home/bruce/software/node-v12.14.1-linux-x64/bin/node /usr/local/bin
```

```
ln -s /home/bruce/software/node-v12.14.1-linux-x64/bin/npm /usr/local/bin
```

4. npm 配置镜像
```
npm config set registry https://registry.npm.taobao.org
```

5. /etc/profile

```
NODE_PATH="/home/bruce/software/node-v12.14.1-linux-x64"
export PATH="$PATH:$NODE_PATH/bin"
```

6. 别忘了 npm global modules的环境变量
# Linux_安装JDK Maven
## 1. JDK
1. 下载
```
wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.rpm
```

## 2. Maven
1. 下载
```
wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.zip
```

2. 配置环境变量
```
/home/bruce/software/apache-maven-3.5.4
```

3. 配置阿里镜像
```
<mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>                  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf> 
</mirror>
```


#linux#
# Linux_安装nginx

## 1. install gcc
```
yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
```

## 2. 下载
```
wget https://nginx.org/download/nginx-1.16.1.tar.gz
```

## 3. 安装 
```
./configure
make && make install
```


## 4. 链接
```
ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx

netstat -ntlp | grep nginx
```

## 5. 添加nginx服务

vim /lib/systemd/system/nginx.service
将以下内容插入：
```
[Unit]
Description=nginx
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

* 以服务的方式启动nginx
```
pkill nginx
systemctl start nginx
```

* 查看服务是否启动
```
 systemctl status nginx
 netstat -ntlp | grep nginx
```

* 配置nginx服务开机自动启动
```
systemctl enable nginx
```

这下子就安装完毕了 ，配置文件在：
```
vim /usr/local/nginx/conf/nginx.conf
```

* 隐藏nginx版本号
nginx的版本号默认是打开的，可以在默认的错误页面和http响应头中查看到。
不同版本，特别是低版本的nginx可能存在漏洞，所以如果不希望被别人获取到版本号的话，可以选择进行版本号隐藏。
```
cd /usr/local/nginx/conf
vim nginx.conf
nginx.conf文件的“server_tokens”修改成”off“：

http {
...
server_tokens off;
...
} 

再修改fastcgi.conf
vim fastcgi.conf
修改如下行
fastcgi_param SERVER_SOFTWARE nginx/$nginx_version;
改为：
fastcgi_param SERVER_SOFTWARE nginx;
```

* 重启nginx
```
systemctl restart nginx
```

6. Others
一键安装上面四个依赖
```
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
```
下载tar包
```
wget http://nginx.org/download/nginx-1.16.1.tar.gz
```
安装 
```
./configure --prefix=
```
路径
```
  nginx path prefix: "/opt/software/nginx"
  nginx binary file: "/opt/software/nginx/sbin/nginx"
  nginx modules path: "/opt/software/nginx/modules"
  nginx configuration prefix: "/opt/software/nginx/conf"
  nginx configuration file: "/opt/software/nginx/conf/nginx.conf"
  nginx pid file: "/opt/software/nginx/logs/nginx.pid"
  nginx error log file: "/opt/software/nginx/logs/error.log"
  nginx http access log file: "/opt/software/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
```


# Linux命令_ls
* 每个条目之后显示标识文件类型的指示符  -F
```
使用 -F（或 GNU 上的长选项 --classify）
斜杠（/）表示目录（或“文件夹”）
星号（*）表示可执行文件。这包括二进制文件（编译代码）以及脚本（具有可执行权限的文本文件）
符号（@）表示符号链接（或“别名”）
等号（=）表示套接字
```

* 长列表 **ls**** ****-****l**** **

* **如果你不想以字节为单位，请添加 -h 标志（或 GNU 中的 --human）以将文件大小转换为更加人性化的表示方法****
****
* ****时间和日期格式****:**** **** --time-style**
```
full-iso：ISO 完整格式（1970-01-01 21:12:00）
long-iso：ISO 长格式（1970-01-01 21:12）
iso：iso 格式（01-01 21:12）
locale：本地化格式（使用你的区域设置）
posix-STYLE：POSIX 风格（用区域设置定义替换 STYLE）

```

* 列出方式: -m 以逗号分隔文件列表 ; 按文件扩展名而不是文件名对条目进行排序，请使用 -X（这是大写 X）

* 隐藏杂项: 显示几乎所有隐藏文件（. 和 .. 除外），请使用 -A 选项

* 递归地列出目录 -R

* 或许需要在列出条目时反转顺序。要这么做，你可以使用-r选项。

* 增加 / (斜线) 标记目录  -p

* 文件大小排序 -S

* 单位大小 使用--block-size=SIZE改单位大小
```
这里的SIZE是
K = Kilobyte
M = Megabyte
G = Gigabyte
T = Terabyte
P = Petabyte
E = Exabyte
Z = Zettabyte
Y = Yottabyte
```


* 只列出目录条目 -d

* 不打印所有者信息 -g
```
  -g隐藏了拥有者信息，—G会隐藏组信息
```

* 统计数量  ls ./|wc -l

* 创建alias
```
要在 Bash shell 中为命令创建别名，请在主目录中创建名为 .bash_aliases 的文件（必须在开头包含 .）。在此文件中，列出要创建的别名，然后是要为其创建别名的命令。例如：

alias ls='ls -A -F -B --human --color'

要使别名起作用，shell 必须知道 .bash_aliases 配置文件存在。在编辑器中打开 .bashrc 文件（如果它不存在则创建它），并包含以下代码块：

if [ -e $HOME/.bash_aliases ]; then 
   source $HOME/.bash_aliases
fi
```


# Linux命令_netstat

* 查看某个端口是否被占用: netstat -a|grep 6379
       当然你也可以使用lsof命令中的方法来查看

* 查找占用端口的进程
       前面虽然知道已经有进程使用了6379端口，但是不知道是哪个进程，因此为了知道进程信息，需要使用-p(program)参数：

       netstat -ap|grep 6379

* 查看处于监听状态的连接
       对于还没有建立完整连接的服务器来说，它启动后正常的状态是LISTEN状态，如果只想查看处于该状态的连接，则可以使用-l（LISTEN）参数

* 不解析主机，端口等信息
        所以慢是因为它需要做解析，使用-n（numeric）参数就可以快速显示原始数字端口或地址了：
         netstat -anp

# Linux命令

* pwdx 进程号
      能查看当前进程所在的文件夹地址

* ps -ax | grep mongod
       查看进程

* 查看是否是虚拟机
       1) lspci | grep -i vmware 
       2) dmidecode
