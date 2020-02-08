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
# Issues_Solution

## 1. 使用SSH 登陆服务器显示Host key verification failed.

```
使用命令 
ssh-keygen -R 服务器IP
```
意思就是从 known_hosts 文件中删除所有属于 hostname 的密钥

- - -
## 2. nginx 403错误
网上找答案，是权限的问题，
```
在/usr/local/nginx/conf/nginx.conf 
开始加一行 user root;
```

应该加user root owner;  ->没试过
# Bruce_工作整理

## JPA
几点注意点
1 表名 小写字母 字母间用_
2 列名 全大写 字母间用_
- - -
## 数据库
    delete from 表名 清空表数据内容，不释放空间
    truncate table 表名  清空表数据，释放空间
    drop table 表名 整张表删除

    select distinct, order by desc 逆序，默认是asc

- - -
## Mysql
设置root账户的host地址（修改了才可以远程连接）

```
mysql>grant all privileges on *.* to 'root'@'%' identified by 'root';

mysql>flush privileges;
```

- - -

安装完mysql后会有个临时密码去日志查看，但是查看登录修改密后还是不行

ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password:yes)

这段可以先生成一个密码
```
update mysql.user set authentication_string=password('123456') where user='root' ;
```

```
update user set host = '%' where user ='root';    
```
密码级别与默认的级别要求不符时候会报
此时需要修改级别与最小的默认密码位数

```
set global validate_password_policy=0;  
set global validate_password_length=4;
```

然后在进行设置密码就好了
需要修改密码
```
set password=password('YdP@wq29BfRR');
flush privileges;
```

======================
mysql初始密码会生成在 /var/log/mysqld.log 下

在/etc/my.cnf下 增加 skip-grant-tables后，
重启mysqld: 
```
service mysqld start
```
- - -
还有在安装前如果你的系统有Mariadb，就要卸载
rpm -qa|grep mariadb
rpm -e --nodeps mariadb-libs-5.5.44-2.el7.centos.x86_64

- - -

网站_中国开源镜像站点
#中国开源镜像站点汇总
网易开源镜像站：http://mirrors.163.com/ <荐>

FreeBSD | FreeBSD-updates | archlinux | centos | cpan | cygwin | debian | debian-backports | debian-cd | debian-security | fedora | gentoo | gentoo-portage | kdemod | mandriva | openSUSE | plf | rpmfusion | slackware | tinycorelinux | ubuntu | ubuntu-releases

搜狐开源镜像站：http://mirrors.sohu.com/
- - -

北京交通大学：http://mirror.bjtu.edu.cn/cn/ <教育网荐>
兰州大学：http://mirror.lzu.edu.cn/ <西北高校FTP搜索引擎>
厦门大学：http://mirrors.xmu.edu.cn/
上海交通大学：http://ftp.sjtu.edu.cn/
清华大学：http://mirrors.tuna.tsinghua.edu.cn/
http://mirrors.6.tuna.tsinghua.edu.cn/ 
http://mirrors.4.tuna.tsinghua.edu.cn/ 
天津大学：http://mirror.tju.edu.cn/

中国科学技术大学：http://mirrors.ustc.edu.cn/ <教育网、电信、IPv6地址>
http://mirrors4.ustc.edu.cn/ <教育网、电信>
http://mirrors6.ustc.edu.cn/ 
西南大学：http://linux.swu.edu.cn/swudownload/
泰安移动：http://mirrors.ta139.com/
东北大学：http://mirror.neu.edu.cn/
浙江大学：http://mirrors.zju.edu.cn/
东软信息学院：http://mirrors.neusoft.edu.cn/
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

# Useful_Windows_Tips

**1****.**** ****把自己的电脑当成服务器
**在****某****个****文件夹****下****运行****，****共享****文件****
```
********python -m http.server 8000****
```
****

**- - -****
****2****.**** **在windows上 查看 chromedriver是否存在
    使用 where命令, 用来看是否在环境变量path下
```
where chromedriver
```


# Useful_段子

1. 少年歌行

我欲乘风向北行，雪落轩辕大如席
我欲借船向东游，绰约仙子迎风立
我欲踏云千万里，庙堂龙吟耐我何
昆仑之巅沐日光，沧海绝境见青山
长风万里燕归来，不见天涯人不回

- - -
【学会自我增值】1、每天读书；2、学习新的语言；3、战胜你的恐惧；4、升级你的技能；5、承认自己的缺点；6、向你佩服的人学习；7、减少在QQ微博上的时间；8、培养一个新的习惯；9、好好休息；10、帮助他人；11、让过去的过去；12、从现在开始！

- - -
关于辞旧，最喜欢莎士比亚剧作中的那句：“凡是过往，皆为序章”。新的一年，愿日子如熹光，温柔又安详。你我赤诚且勇敢，欣喜也在望。爱的人，都喜乐如常。盼的事，都归于心上。（来自右友）
- - -
年少时，我曾以为：人就是要挣很多钱，住很大的房子，能想买什么就买什么，那才算得上幸福人生。后来我才明白，幸福原来如此简单。有事做，让有限的时光变得充实；有人爱，让平淡的人生变得温暖；有期待，让未知的未来充满希望。这，便是人生最大的幸福吧！
- - -
我之前写过一篇文章，讲过一句话：人一闲，就废了。
一个人如果想要不被这个社会所淘汰，就不能过得太安逸，太闲了，人生就很容易废了。

所以，如果你现在的工作很舒服，很轻松，没有什么压力的话，并不是什么好事情。
当你工作了几年发现，自己的能力没有长足的进步，这是很危险的，不进则退，你没有能力应对工作上的任何变故，到时候只能一边泪眼婆娑地抱怨，一边又无奈地吞下苦果。

如果现在工作不忙，别将时间全部花在追剧、玩游戏上，多看看书，多学一项技能，百益而无一害。
你永远不知道杀死你的是谁，你能做的只有让自己强大，时刻保持警惕，在灾难来临前早作打算。
思危才能更好地安居，好好走下去，别被灭了。
- - -
很多的恋爱新手，都会向身边的人请教“怎么样维持一段恋爱关系？”其实，谈一场恋爱哪有那么多套路，只要能做到每时每刻“甜”，就足够了。

甜是什么感觉呢？并不是单指，出现在味蕾上的那种让人舒服的味道。有些时候，“甜”是在眼睛里，对视的时候眼睛会发光；“甜”也是在耳朵里，你说的话他/她都能记住；也有很多的“甜”住在心里，就像酒一样回甘，还有点令人上头。

两个人日渐上升的默契度，也决定了这场恋爱甜度。不过，恋爱时的两个人就像白糖和红糖，无论做什么都是甜的。

恋爱是一件很幸福的事，大脑释放出的多巴胺，和体内产生的各类激素，都能让你变得快乐、自信、勇敢，没有人可以拒绝这样一场人造的快乐！

你要相信，无论如何，始终会有一个人穿越过汹涌的人群，迈向你。

恋爱也是一件幸运的事，甜甜的爱情，不会放弃任何一个可爱的人。
- - -

大家都知道，哆啦A梦有一个任意门，可以去心中所有的远方。
然而，你知道创作哆啦A梦的藤子·F·不二雄是怎样工作的吗？
为了“有趣”两个字，他毕其一生绞尽脑汁，每天画到凌晨4点，为了一个乡村取材，孤身一人跑到偏远山区去拍摄。
所以，现实的世界从没有唾手可得的任意门。
任意门前，都是脚踏实地的行动力，发自肺腑的自我付出，以及冥思苦想的思考分析。
前几天我分享了一篇推送《知乎热答：2018年你最想撤回什么？》，很多人都后悔这一年没有真正地尽力，只能落个被人挑挑拣拣的结局。
真的是应了那一句：你不对自己下狠手，生活就会对你下狠手。
没关系吧，2019年再逼自己一把，一切还来得及。
- - -


# Useful_网站
## 1. 下载视频 www.shipinyu.com


# NPM_node package

## 1. hexo
npm install hexo-cli -g

```
mkdir hexoblog  #手动创建一个目录
cd hexoblog  
hexo init   #初始化hexo环境
hexo g      #生成静态文件
hexo s      #启动hexo
```

- - -
## 2. http-server
npm install http-server

```
hs -o -p xxx端口
```

- - -
1. fastclick
2. stylus   stylus-loader
3. vue-awesome-swiper@2.6.7
4. better-scroll
# Usage_pip

1. 配置阿里镜像
https://mirrors.aliyun.com/pypi/simple/

在linux ~/.pip/pip.conf
```
[global]
index-url = http://pypi.douban.com/simple
[install]
use-mirrors =true
mirrors =http://pypi.douban.com/simple/
trusted-host =pypi.douban.com

```



- - -

加速安装的命令: 
pip install -i https://pypi.douban.com/simple 模块名

pip install -i https://mirrors.aliyun.com/pypi/simple 模块名

- - -

# 数据库Mysql
* ## 忘记密码
skip-grant-tables模式启动

skip-grant-tables表示，在启动mysql时不启动授权表功能，可以接免密码登录

```
#修改/etc/my.cnf文件

vim /etc/my.cnf


#在[mysqld]区域添加配置,并保存my.cnf文件

skip-grant-tables


#重启mysql

systemctl restart mysqld


#登录mysql

mysql -u root -p


#如果出现输入密码，直接回车，就可以进入数据库了
```

* 修改root密码
```
#登录mysql，此时还没有进入数据库，使用如下命令

use mysql;

#修改root密码（mysql5.7版本）

update user set authentication_string = password('密码'), password_expired = 'N', password_last_changed = now () where user = 'root';

#如果你的mysql是5.6版本修改root密码（mysql5.6版本）
update user set password = password('密码') where user ='root';

#使其生效
flush privileges;

#退出
exit
```


* 新增管理用户
```
#登录mysql，此时还没有进入数据库，使用如下命令
use mysql;
#刷新数据库
flush privileges;

#创建一个用户，并赋予管理员权限

grant all privileges on *.*  to '用户'@'%' identified by '密码';

#例如，创建一个admin用户，密码为admin

grant all privileges on *.* to 'admin'@'%' identified by 'admin';
```

* 重启
```
#修改/etc/my.cnf文件

vim /etc/my.cnf

#在[mysqld]区域删除改配置,并保存my.cnf文件

#skip-grant-tables

#重启mysql

systemctl restart mysqld

#此时，修改完毕
```


* mysql8
```
mysql5.7.6版本后，废弃user表中password（）方法，所以旧方法重置密码对mysql8.0版本是行不通的！

mysql8可以采用此方法修改root密码
#在skip-grant-tables模式下，将root密码置空

update user set authentication_string = '' where user ='root';

#退出，将/etc/my.cnf文件下的skip-grant-tables去掉，重启服务器

#登录mysql
mysql -u root -p

#因为密码置空，直接回车，进入数据库之后，修改密码

ALTER USER 'root'@'localhost' IDENTIFIED BY 'Hello@123456';

#因为mysql8，使用强校验，所以，如果密码过于简单，会报错，密码尽量搞复杂些！

```

* 
# Useful Links

网站
- - -

vblog.itboyhub.com

vhr.itboyhub.com

http://www.springboot.wiki/
- - -

国内部分开源镜像站点。

企业站

网易：http://mirrors.163.com/  （大概是 2009 年上线）

搜狐：http://mirrors.sohu.com/ （2011 年）

阿里：https://opsx.alibaba.com/mirror （2018 年）

华为 [https://mirrors.huaweicloud.com/](https://mirrors.huaweicloud.com/) （2018 年）

腾讯 [https://mirrors.cloud.tencent.com/](https://mirrors.cloud.tencent.com/) （2019 年）

- - -

中国科技大学：https://mirrors.ustc.edu.cn/

清华大学：https://mirrors.tuna.tsinghua.edu.cn/

北京交通大学：http://mirror.bjtu.edu.cn/

北京理工大学：http://mirror.bit.edu.cn/web/

兰州大学：http://mirror.lzu.edu.cn/

- - -

上海交通大学：http://ftp.sjtu.edu.cn/

大连东软信息学院：http://mirrors.neusoft.edu.cn/

浙江大学：http://mirrors.zju.edu.cn/

重庆大学：http://mirrors.cqu.edu.cn/

华中科技大学：http://mirror.hust.edu.cn/

- - -

西安电子科技大学：http://linux.xidian.edu.cn/

南阳理工学院：http://mirror.nyist.edu.cn/

江苏广播电视大学：http://mirrors.jstvu.edu.cn/

西北农林科技大学：http://mirrors.nwsuaf.edu.cn/

大连理工大学：http://mirror.dlut.edu.cn/
# netstat命令

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
* 
# Linux命令
## 1. ls
使用 -F（或 GNU 上的长选项 --classify）以在每个条目之后显示标识文件类型的指示符
斜杠（/）表示目录（或“文件夹”）。
星号（*）表示可执行文件。这包括二进制文件（编译代码）以及脚本（具有可执行权限的文本文件）。
符号（@）表示符号链接（或“别名”）。
等号（=）表示套接字。***__~~
~~__*****__~~*
*~~__****长****列表**** ****ls**** ****-****l**** ****
* ****如果你不想以字节为单位，请添加 -h 标志（或 GNU 中的 --human）以将文件大小转换为更加人性化的表示方法****
****
* ****时间和日期格式****:**** **** --time-style****
**full-iso：ISO 完整格式（1970-01-01 21:12:00）
long-iso：ISO 长格式（1970-01-01 21:12）
iso：iso 格式（01-01 21:12）
locale：本地化格式（使用你的区域设置）
posix-STYLE：POSIX 风格（用区域设置定义替换 STYLE）
****
* ******列出方式****:**** ****-****m**** ****以逗号分隔文件列表**** ****;**** ****按文件扩展名而不是文件名对条目进行排序，请使用 -X（这是大写 X）****
**
* ****隐藏****杂项****:**** ****显示几乎所有隐藏文件（. 和 .. 除外），请使用 -A 选项****
**
* ******递归地列出目录****** ****-****R**
* 或许需要在列出条目时反转顺序。要这么做，你可以使用-r选项。

* ****增加 / (斜线) 标记目录****  -p
****
* ****文件****大小****排序**** ****-****S******
**
****单位****大小**** ******使用**-****-**block-size=SIZE改单位大小。这里的SIZE是：
K = Kilobyte
M = Megabyte
G = Gigabyte
T = Terabyte
P = Petabyte
E = Exabyte
Z = Zettabyte
Y = Yottabyte****
**
* ************只列出目录条目****** ****-****d**** **

* ********不打印****所有者****信息**** ****-****g** 
       -g隐藏了拥有者信息，—G会隐藏组信息**
****
* ****统计****数量**** **** ****ls ./|wc -l******
**
****
****要在 Bash shell 中为命令创建别名，请在主目录中创建名为 .bash_aliases 的文件（必须在开头包含 .）。在此文件中，列出要创建的别名，然后是要为其创建别名的命令。例如：

alias ls='ls -A -F -B --human --color'******
****
****要使别名起作用，shell 必须知道 .bash_aliases 配置文件存在。在编辑器中打开 .bashrc 文件（如果它不存在则创建它），并包含以下代码块：

if [ -e $HOME/.bash_aliases ]; then **
**** **** **** **source $HOME/.bash_aliases**
**fi**
# - - -# 
## 2. pwdx 进程号
      能查看当前进程所在的文件夹地址

- - -

## 3. ps -ax | grep mongod
      查看进程
- - -



- - -

## 查看是否是虚拟机
1) lspci | grep -i vmware

2) dmidecode



# Docker
查看Docker引擎日志 journalctl -u docker.service

查看Docker容器日志 docker logs CONTAINER
# Usage_Git

## 1. 配置
git config --global user.name "username"
git config --global user.email  "email-addr"

```
ssh-keygen -t rsa -C "邮箱"
```
- - -
## 2. 操作
echo "# typescript_learning" >> README.md 
git init 
git add README.md git commit -m "first commit" 

git remote add origin git@github.com:brucechen-github/typescript_learning.git 

git push -u origin master

- - -

# TypeScript

生成tsc目录下的配置文件:tsconfig.json
tsc --init

- - -
执行以下命令将 TypeScript 转换为 JavaScript 代码：

tsc test.ts
# SpringBoot_2
****Spring IoC容器****

bean是Spring IOC中非常核心的概念，Spring容器负责bean的生命周期的管理。在最初，Spring使用XML配置文件的方式来描述bean的定义以及相互间的依赖关系，但随着Spring的发展，越来越多的人对这种方式表示不满，因为Spring项目的所有业务类均以bean的形式配置在XML文件中，造成了大量的XML文件，使项目变得复杂且难以管理。

后来，基于纯Java Annotation依赖注入框架 Guice出世，其性能明显优于采用XML方式的Spring，甚至有部分人认为， Guice可以完全取代Spring（ Guice仅是一个轻量级IOC框架，取代Spring还差的挺远）。正是这样的危机感，促使Spring及社区推出并持续完善了 JavaConfig子项目，它基于Java代码和Annotation注解来描述bean之间的依赖绑定关系

- - -

## 大量的注解封装、约定大于配置的原则

Spring Boot 建议的目录结果如下： 
root package 结构：com.example.myproject

com
  +- example
    +- myproject
      +- Application.java
      |
      +- model
      |  +- Customer.java
      |  +- CustomerRepository.java
      |
      +- service
      |  +- CustomerService.java
      |
      +- controller
      |  +- CustomerController.java
      |

- - -

## 引入 web 模块

pom.xml中添加支持web的模块：

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>**__*spring-boot-starter-web*__**</artifactId>
</dependency>***__
__******__
__***
**__*@RunWith(SpringRunner.class)
@SpringBootTest*__****
****- - -****
****
**** <dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>__*spring-boot-devtools*__</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
            </configuration>
        </plugin>
</plugins>
</build>**

# SpringBoot 注解Annotation
## 1## .##  ## SpringBoot
*__@SpringBootApplication__*
    @Configuration
    @EnableAutoConfiguration
    @ComponentScan*__ __*__*表示将该类自动发现扫描组件。个人理解相当于，如果扫描到有@Component、@Controller、@Service等这些注解的类，并注册为Bean，可以自动收集所有的Spring组件，包括@Configuration类。*__

@Component可配合CommandLineRunner使用，在程序启动后执行一些基础任务

@RestController 注解是@Controller和@ResponseBody的合集,表示这是个控制器bean,并且是将函数的返回值直 接填入HTTP响应体中,是REST风格的控制器

*__@PathVariable__*

@Autowired 自动导入

@Service：一般用于修饰service层的组件

@Repository：使用@Repository注解可以确保DAO或者repositories提供异常转译，这个注解修饰的DAO或者repositories类会被ComponetScan发现并配置，同时也不需要为它们提供XML配置项。

@Bean：用@Bean标注方法等价于XML中配置的bean。

@Value：注入Spring boot application.properties配置的属性的值。

@ResponseBody：表示该方法的返回结果直接写入HTTP response body中，一般在异步获取数据时使用，用于构建RESTful的api。在使用@RequestMapping后，返回值通常解析为跳转路径，加上@responsebody后返回结果不会被解析为跳转路径，而是直接写入HTTP response body中。

比如异步获取json数据，加上@responsebody后，会直接返回json数据。该注解一般会配合@RequestMapping一起使用。

示例代码：

@RequestMapping(“/test”)
@ResponseBody
public String test(){
return”ok”;
}

__*@Controller*__：用于定义控制器类，在spring 项目中由控制器负责将用户发来的URL请求转发到对应的服务接口（service层），一般这个注解在类中，通常方法需要配合注解@RequestMapping。

- - -
## 2## .##  ## JPA注解

@Entity：@Table(name=”“)：表明这是一个实体类。一般用于jpa这两个注解一般一块使用，但是如果表名和实体类名相同的话，@Table可以省略

@MappedSuperClass：用在确定是父类的entity上。父类的属性子类可以继承。

@NoRepositoryBean：一般用作父类的repository，有这个注解，spring不会去实例化该repository。

@Column：如果字段名与列名相同，则可以省略。

@Id：表示该属性为主键。

@GeneratedValue(strategy = GenerationType.SEQUENCE,generator = “repair_seq”)：表示主键生成策略是sequence（可以为Auto、IDENTITY、native等，Auto表示可在多个数据库间切换），指定sequence的名字是repair_seq。

@SequenceGeneretor(name = “repair_seq”, sequenceName = “seq_repair”, allocationSize = 1)：name为sequence的名称，以便使用，sequenceName为数据库的sequence名称，两个名称可以一致。

@Transient：表示该属性并非一个到数据库表的字段的映射,ORM框架将忽略该属性。如果一个属性并非数据库表的字段映射,就务必将其标示为@Transient,否则,ORM框架默认其注解为@Basic。@Basic(fetch=FetchType.LAZY)：标记可以指定实体属性的加载方式

@JsonIgnore：作用是json序列化时将Java bean中的一些属性忽略掉,序列化和反序列化都受影响。

@JoinColumn（name=”loginId”）:一对一：本表中指向另一个表的外键。一对多：另一个表指向本表的外键。

@OneToOne、@OneToMany、@ManyToOne：对应hibernate配置文件中的一对一，一对多，多对一

## - - -## 

## 3. MVC
@RequestMapping：@RequestMapping(“/path”)表示该控制器处理所有“/path”的UR L请求。RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。

用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。该注解有六个属性：

params:指定request中必须包含某些参数值是，才让该方法处理。

headers:指定request中必须包含某些指定的header值，才能让该方法处理请求。

value:指定请求的实际地址，指定的地址可以是URI Template 模式

method:指定请求的method类型， GET、POST、PUT、DELETE等

consumes:指定处理请求的提交内容类型（Content-Type），如application/json,text/html;

produces:指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回

@RequestParam：用在方法的参数前面。

*__@RequestParam__*

String a =request.getParameter(“a”)。

@PathVariable：路径变量。如

RequestMapping(“user/get/mac/{macAddress}”)
public String getByMacAddress(@PathVariable String macAddress){
//do something;
}

参数与大括号里的名字一样要相同。



# Usage_Shell_CMD
查看文档树结构 tree 
常用参数：/f 文件夹路径/文件名

tree /f video>C:\Users\

type NUL>d:\demo\newFile.txt 
该命令会在D盘demo目录下新建空白的newFile.txt文件
# 数据库SQL

## 1. group by
group查询就是分组查询，为什么要分组查询？因为我们想按某个维度进行统计
1. 每天Java3y这个公众号的点击量是多少
select name,time,sum(pv) as pv  
from xxx_table 
where name = 'Java3y' group by name,time

要知道的是：使用group by分组统计之后，我们的select 后面只能跟着group by 的字段，或者是聚合函数
![](/storage/emulated/0/Android/data/com.colanotes.android/notes/6d3f0c17-0874-323a-80cf-bae0a3e1eaf4.textbundle/assets/mmexport1581071980067.jpg)


- - -
2. 只希望留下某一条记录作为查询结果就好了，我们可以写下以下的SQL：

select * from user where id in(
   select min(id) from user where name = 'Java3y' and pv = 20 and time='7-25' group by name,pv,time;
)
上面已经说了，使用group by分组统计之后，我们的select 后面只能跟着group by 的字段，或者是聚合函数。

很多时候我们group by了以后，还想要查询结果中包含group by之外的字段(一般情况下，我们都不可能将group by 涵盖所有的字段)，我们就可以上面那样，将查询后的结果作为子查询，放在外部查询的where 子句后，这样外部查询是可以select 出其他字段的。
- - -

## 2. join
join表时一定要写关联条件去除笛卡尔积

上面说了，在join的时候一定要写**关联条件**，如果是inner join的话，只有符合关联条件的数据才会存在最大表中

如果是left join的话，即便关联条件不符合，左边表的数据一定会存在大表中

如果是right join的话，即便关联条件不符合，右边表的数据一定会存在大表
- - -
## 3. case when then else end

## 4. 时间函数
昨天
SELECT * FROM 表名 WHERE TO_DAYS( NOW( ) ) - TO_DAYS( 时间字段名) <= 1

7天
SELECT * FROM 表名 where DATE_SUB(CURDATE(), INTERVAL 7 DAY) <= date(时间字段名)

近30天
SELECT * FROM 表名 where DATE_SUB(CURDATE(), INTERVAL 30 DAY) <= date(时间字段名)

本月
SELECT * FROM 表名 WHERE DATE_FORMAT( 时间字段名, '%Y%m' ) = DATE_FORMAT( CURDATE( ) , '%Y%m' )

上一月
SELECT * FROM 表名 WHERE PERIOD_DIFF( date_format( now( ) , '%Y%m' ) , date_format( 时间字段名, '%Y%m' ) ) =1

5. 其他函数
length  --计算字符串长度
concat  --连接两个字符串
substring -- 截取字符串
count   -- 统计数量
max   -- 最大
min   -- 最小
sum   -- 合计
substring_index





# Java面试题

## 1. 重载，重写区别
重载： 发生在同一个类中，方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同，发生在编译时。　　
重写： 发生在父子类中，方法名、参数列表必须相同，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类；如果父类方法访问修饰符为 private 则子类就不能重写该方法。
在讲继承的时候我们就知道父类的私有属性和构造方法并不能被继承，所以 Constructor 也就不能被 override（重写）,但是可以 overload（重载）,所以你可以看到一个类中有多个构造函数的情况。
## - - -## 
## 2## .## String str="abcd"与 String str1=new String("acbd")
第一种方式先检查字符串常量池中有没有"abcd"，如果字符串常量池中没有，则创建一个，然后str指向字符串常量池中的对象，如果有，则直接将str指向字符串常量池中的"abcd""；

第二种方式是直接在堆内存空间创建一个新的对象。

推荐使用第一种方式创建字符串。

str和str1不相等，因为一个是堆内存中的String对象一个是常量池中的String对象。
- - -
## 3.## StringBuffer## ,##  ## StringBuilder
StringBuffer 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。StringBuilder 并没有对方法进行加同步锁，所以是非线程安全的

- - -
## 4. 接口和抽象类的区别是什么？抽象类必须要有抽象方法吗？抽象类能使用 final 修饰吗？
•接口的方法默认是 public，所有方法在接口中不能有实现(Java 8 开始接口方法可以有默认实现），而抽象类可以有非抽象的方法。
•接口中的实例变量默认是 final 类型的，而抽象类中则不一定。
•一个类可以实现多个接口，但最多只能实现一个抽象类。
•一个类实现接口的话要实现接口的所有方法，而抽象类不一定。
•接口不能用 new 实例化，但可以声明，但是必须引用一个实现该接口的对象。从设计层面来说，抽象是对类的抽象，是一种模板设计，而接口是对行为的抽象，是一种行为的规范。

- - -

## 5.==
== : 它的作用是判断两个对象的地址是不是相等。即，判断两个对象是不是同一个对象(基本数据类型==比较的是值，引用数据类型==比较的是内存地址)

- - -
6. 

# Usage_Vue

1. vue create vue-hello-world (命令行)

2.  vue ui (界面)
# Termux

1. 命令: **jupyter notebook**




- - -

常用安装pkg

```
*__~~~~__*pkg install libxml2   libxslt  libiconv  libllvm clang libzmq libc++
```



#termux#
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
# 面试相关
1 下次问你为什么看机会，你就说：之前的经历主要是传统企业，自己在公司这边技术得不到提升，从个人长久的发展来看，还是想去互联网企业看下，提升自己的技术技能比较好，  互联网机会好

2 这段时间面试了很多同学，发现大多是介绍自己什么时间什么公司做了什么事，把简历上口头叙述了一遍。
但面试官更想听到的是个人能力的成长经历，什么阶段学会了什么，目前的能力特长是什么，解决过什么难点痛点问题，怎么分析怎么落地实现，拿到什么结果，一两个例子能反映你的核心能力；
和大家共勉，一起成长[抱拳]

- - -

## 写简历
短小精悍，开门见山
突出亮点 项目经验、相关工作经验就是重点，其他的诸如兴趣爱好、自我评价啥的可有可无，即使要写也应该放到后面
比如说，你可以写熟悉Java的集合、并发、动态代理、虚拟机，也可以说熟悉常见数据结构，如：队列、链表、堆，但是千万不要写熟悉Java语言和数据结构这么宽泛，除非你对Java跟数据结构都很精通了，那么可以这样写。
什么是项目经验？无非就是这个项目是什么？用到了哪些技术？你在这个项目中的角色又是什么？你在做的过程中遇到了什么困难？又是怎么克服的？这些才是面试官想看到的内容。你泛泛而谈，罗列了一大堆项目，但每个项目都是一笔带过，那写了也等于白写。
# Python_pip module

## 1 . 后台api: pip install fastapi uvicorn

1 针对Python3
error: command gcc
1 yum install gcc libffi-devel __***python3-devel***__ openssl-devel***__
__***2 安装uvicorn 需要httptools
- - -
from fastapi import FastAPI
app = FastAPI()
@app.get('/')
def index():
return {'message': '你已经正确创建 FastApi 服务！'}
写好代码以后，我们需要使用uvicorn来运行 FastApi。首先使用pip或者pipenv安装uvicorn：

pip install uvicorn
pipenv install uvicorn

然后执行命令：
uvicorn main:app --reload

但使用 FastApi 的时候，我们只需要类型标注就能解决所有问题。首先我们导入from pydantic import BaseModel，然后继承BaseModel实现我们允许 POST 方法提交上来的数据字段和格式：

from pydantic import BaseModel
app = FastAPI()
class People(BaseModel):
name: str
age: int
address: str
salary: float

People这个类通过类型标注，指定了它里面的 4 个字段和他们的类型。现在，我们来实现 POST 方法：
- - -
## 2 . log相关的: pip3 install loguru
- - -
## 3. mysql数据库的: pip install mycli
- - -
## 4. pip install lxml

- - -
## 5. pip install flask-cors

下载flask_cors包
使用flask_cors的CORS，代码示例
from flask_cors import *
app = Flask(__name__)
CORS(app, supports_credentials=True)

- - -
## 6. pipenv
pipenv默认虚拟环境路径在用户目录下的.\virtualenvs下，不同虚拟环境目录不同，如果要更改为在当前项目根目录下，可以在项目根目录下手动创建.venv目录。

1、linux环境下设置环境变量export PIPENV_VENV_IN_PROJECT=1

2、window设置WORKON_HOME到其他的地方 （如果当前目录下已经有.venv,此项设置失效，如果没有.venv则会在项目目录下创建WORKON_HOME指定的目录，同时在目录下以项目名称开头创建虚拟环境目录）。

PIPENV_VENV_IN_PROJECT

Windows下pipenv将虚环境文件的位置设置在项目根目录下
在windows下使用pipenv shell时，虚拟环境文件夹会在C：\Users\Administrator\.virtualenvs\目录下默认创建，为了方便管理，将这个虚环境的文件的位置更改一下。

新建一个名为“ WORKON_HOME ”的环境变量（如果已存在就忽略此步骤），然后将环境变量的值设置为“ PIPENV_VENV_IN_PROJECT ” （其他名字也行？还试过）

我觉得重点是在 WORKON_HOME 这个变量名。


# WORKON_HOME   can change virtualenv's default directory
# PIPENV_VENV_IN_PROJECT=1表示 把虚拟环境 放在项目文件夹中
# PIPENV_PYPI_MIRROR 能设置本机 pipenv 的下载源。

#=====把下面的放到sh配置文件中，比如 .bashrc  .zshrc
eval "$(pipenv --completion)"
export PIPENV_PYPI_MIRROR="https://mirrors.aliyun.com/pypi/simple/"
export PIPENV_VENV_IN_PROJECT=1
- - -

***__
__***
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

- - -

添加nginx服务

vim /lib/systemd/system/nginx.service
将以下内容插入：

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
以服务的方式启动nginx

pkill nginx

systemctl start nginx
查看服务是否启动

 systemctl status nginx
 netstat -ntlp | grep nginx
配置nginx服务开机自动启动

systemctl enable nginx
这下子就安装完毕了 ，配置文件在：

vim /usr/local/nginx/conf/nginx.conf
可选：

nginx的版本号默认是打开的，可以在默认的错误页面和http响应头中查看到。

不同版本，特别是低版本的nginx可能存在漏洞，所以如果不希望被别人获取到版本号的话，可以选择进行版本号隐藏。

隐藏nginx版本号

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
# 改为：
fastcgi_param SERVER_SOFTWARE nginx;
重启nginx

systemctl restart nginx

- - -
//一键安装上面四个依赖
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel

//下载tar包
wget http://nginx.org/download/nginx-1.16.1.tar.gz

安装 
./configure --prefix=

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


# export default
node中导入模块：var 名称 = require('模块标识符')

node中向外暴露成员的形式：module.exports = {}

在ES6中，也通过规范的形式，规定了ES6中如何导入和导出模块

ES6中导入模块，使用 import 模块名称 from '模块标识符'    import '表示路径'

import *** from *** 是ES6中导入模块的方式

在ES6中，使用export default 和 export 向外暴露成员
注意：
// test.js
var info = {
    name: 'zs',
    age: 20
}
export default info
 
export var title = '小星星'
 
export var content = '哈哈哈'

-----------------
import person from './test.js'
console.log(person);

import person, {title, content as content1} from './test.js'
console.log(person);
console.log(title + '=======' + content1);

1、export default 向外暴露的成员，可以使用任意变量来接收

2、在一个模块中，export default 只允许向外暴露一次

3、在一个模块中，可以同时使用export default 和export 向外暴露成员

4、使用export向外暴露的成员，只能使用{  }的形式来接收，这种形式，叫做【按需导出】

5、export可以向外暴露多个成员，同时，如果某些成员，在import导入时，不需要，可以不在{ }中定义

6、使用export导出的成员，必须严格按照导出时候的名称，来使用{ }按需接收

7、使用export导出的成员，如果想换个变量名称接收，可以使用as来起别名
-----------------------------
export命令对外输出了指定名字的变量（变量也可以是函数或类）。

与export default命令的区别：import命令接受一对大括号，里面指定要从其他模块导入的变量名。大括号里面的变量名，必须与被导入模块（profile.js）对外接口的名称相同。

如果想为输入的变量重新取一个名字，import命令要使用as关键字，将输入的变量重命名。

使用import命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。但是，用户肯定希望快速上手，未必愿意阅读文档，去了解模块有哪些属性和方法。

为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到export default命令，为模块指定默认输出。

#npm#
# VUE
1. 插值表达式
2. v-text   v-html
3. v-on:click="函数名"  === @click=""
4. 属性绑定 v-bind: 属性='' === :属性=""
    string 拼接bruce  " data +'bruce'"
5. 双向绑定 v-model=""
6. 计算属性 
        computed 是一个函数 通过cli建的
        如果是直接实例new Vue()
        {
            computed: {
                 xxx: function() {
                      return this.x + this.y
                 }
            }

        }


7. 侦听器 watch
         watch: {
              x: function() {
              }
         }

8. v-if    v-show     ='show'  show是true，false

9. v-for='item of xxList' :key=''

10. 全局组件 Vue.component('xxx_name', {
            template: ''
       })

      局部组件 var xxx_name = {}
      需要在外层Vue实例里 添加
          components: {
              'todo-item': xxx_name
          }
11. 父子组件传值
     1)在父组件 bind 某个值
        在子组件 用 props属性里传过来
     2) 子组件要删除某个，需要传到父组件里
          发布订阅模式
          需要在子组件里写一个方法:
          向外触发xxx_事件， 在父组件监听此事件    @xxx_事件
          this.$emit('xxx_事件', 属性)
12. 挂载点下面 都是模板， 模板里可以用组件


13.  通过cli创建的
      组件里 
      <script>
         export default {
           data: function() {
               return { 
                     xxx
               }
           }
         }
      </script>
     在ES6中可以简写成 data () {}

#vue#
# Linux_安装JDK|Maven
## 1. 下载JDK
wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.rpm

## 2. 下载Maven
wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.zip
/home/bruce/software/apache-maven-3.5.4

<mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>                  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf> 
</mirror>


#linux#
# Usage_node 
## 1. 配置国内淘宝registry

```
npm config set registry http://registry.npm.taobao.org/
```
 
 在Linux ~/.npmrc
```
registry=http://registry.npm.taobao.org/
```

## 2## .##  ## npm## 升级## :##  ## npm##  ## install##  ## -## g##  ## npm

--registry=https://registry.npm.taobao.org


## 3## .##  ## Termux安装nodejs## :##  ## pkg##  ## install##  ## nodejs

# API# 管理# 系统
1. **Swagger**
2. YApi
3. eolinker
4. ShowDoc
5. 易文档

# Python
## 1## .##  ## import
sys.path
reload

不支持循环import

## 2.##  ## =## =##  ## is##  
== 值比较
is 内存地址

## 3.##  ## 深拷贝##  ## 浅拷贝
浅拷贝 变量 = 变量
深拷贝 会递归copy
import copy
xx  = copy.deepcopy(xxx)
- - -
copy.copy(xxx) 浅拷贝 会根据是否是可变类型

## 4.


#python#
# JavaScript
## 1## .##  ## 变量类型
数字
字符串
true
null
undefined
## 2. 其他


#javascript#

