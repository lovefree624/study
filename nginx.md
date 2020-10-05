# nginx 的原理

## 1.master和worker

![img](E:\study\图片\nginx\原理.png)

## 2.nginx是如何运行的

![img](E:\study\图片\nginx\原理2.png)

## 3.一个master和多个woker有好处

 ### 1.可以使用nginx –s reload热部署，利用nginx进行热部署操作

### 2.每个woker是独立的进程，如果有其中的一个woker出现问题，其他woker独立的，继续进行争抢，实现请求过程，不会造成服务中断

## 4.设置多少个woker合适

**worker数和服务器的cpu数相等是最为适宜的**

## 5.连接数worker_connection

**第一个：发送请求，占用了 woker的几个连接数？**

**答案：2或者4个**

**第二个：nginx有一个master，有四个woker，每个woker支持最大的连接数1024，支持的最大并发数是多少？**

**l** 普通的静态访问最大并发数是：**worker_connections worker_processes /2**

**l** 而如果是HTTP作 为反向代理来说，最大并发数量应该是**worker_connections * worker_processes/4**

#  内容介绍

## nginx 简介

1. 什么是 nginx 和可以做什么事情

* 正向代理

* 反向代理

* 动静分离

2.  Nginx 的安装

*  在 linux 系统中安装 nginx

3. Nginx 的常用命令和配置文件

4. Nginx 配置实例 1 反向代理

5. Nginx 配置实例 2 负载均衡

6. Nginx 配置实例 3 动静分离

7. Nginx 的高可用集群

* 配置主从模式

* 配置双主模式

## Nginx的简介

### 1. 什么是**nginx**

Nginx 是高性能的 HTTP 和反向代理的服务器，处理高并发能力是十分强大的，能经受高负载的考验,有报告表明能支持高达 50,000 个并发连接数。

### 2.正向代理

1. 需要在客户端配置代理服务器进行指定网站访问

 ![img](https://raw.githubusercontent.com/lovefree624/study/master/%E5%9B%BE%E7%89%87/nginx/%E6%AD%A3%E5%90%91%E4%BB%A3%E7%90%86.png)



### 3.反向代理

暴露的是代理服务器地址，隐藏了真实服务器IP地址.

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86.png?raw=true)



### 4.负载均衡

增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%9D%87%E8%A1%A1%E8%B4%9F%E8%BD%BD.png?raw=true)

###  5.动静分离

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%8A%A8%E9%9D%99%E5%88%86%E7%A6%BB.png?raw=true)



## NGINX的安装

### 1.准备工作

1.打开虚拟机，使用远程连接工具连接linux操作系统

2.到nginx官网下载软件 

[nginx的官网](http://nginx.org)

### 2.开始安装nginx

1.**安装pcre依赖**

	* 联网下载pcre ，命令：**wget http://downloads.sourceforge.net/project/pcre/pcre/8.37/pcre-8.37.tar.gz**
	* 解压文件。命令：tar -zxvf 压缩包
	* 执行./configure ,然后回到pcre文件目录下，执行make && make install

2.**安装openssl、zlib、gcc依赖**，命令：**yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel**

3.**安装nginx**

* 解压文件：tar -zxvf  文件名
* ./configure
* make && make install

4.**启动nginx**

进入nginx的安装文件目录，找到sbin文件，在文件下执行./nginx

5.**nginx 的命令**

* 启动：./nginx
* 重新加载：./nginx -s reload
* 停止：./nginx -s stop
* 查看版本号：./nginx -v

6.**防火墙**

* 查看所有的端口： **firewall-cmd --list-all**
* 设置开放端口：**firewall-cmd --add-port=80/tcp --permanent**
* 重启防火墙：**firewall-cmd -reload**

## 3.nginx 的配置文件

配置文件在nginx文件下的conf文件里面的nginx.conf文件

包含3块部分：

1.**全局块：配置服务器整体运行的配置指令**

 比如：worker_processes    1;处理并发数的配置

2.**events 块：Nginx服务器与用户的网络连接**

比如：worker_connections     1024;支持的最大连接数为1024

3.**http、https块**  还包含两部分：

* **http全局块**

* **server块**



### 3.1nginx 配置https

**1.配置https需要nginx的sslopen模块**

**2.需要pem和key**

```xml
 server {
        listen       443 ssl;
        server_name  rural.yuechongweilai.com;#服务器的地址或者域名

        ssl_certificate      "/usr/local/nginx/key/4576676_rural.yuechongweilai.com.pem";
        ssl_certificate_key  "/usr/local/nginx/key/4576676_rural.yuechongweilai.com.key";

        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_protocols SSLv2 SSLv3 TLSv1 TLSv1.2 TLSv1.1 ;

        ssl_ciphers  ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_prefer_server_ciphers  on;

        location / {
            root   html;
            index  index.html index.htm;
        #   proxy_pass  https://rural.yuechongweilai.com;
        }
        location ~ /wx/ {
             proxy_pass  http://127.0.0.1:8080;#因为是同一个服务器，所以这里请求的地址是本机的地址
                   }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }

```



## 4.实例

### 1.Nginx配置实例：反向代理实例 1

1.**准备工作** 

* 在liunx系统安装tomcat，使用默认端口8080 

* tomcat安装文件放到liunx系统中，解压

*  tomcat的bin目录中，./startup.sh启动tomcat服务器

2.**对外开放访问的端口** 

* **firewall-cmd --add-port=8080/tcp --permanent**

* **firewall-cmd –reload**

3.**查看已经开放的端口号**

* **firewall-cmd --list-all**

4.**在windows系统中通过浏览器访问tomcat服务器**

5.***nginx  的配置文件***-

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86%E9%85%8D%E7%BD%AE1.png?raw=true)

6.**nginx的配置文件的说明**

* 配置文件中，有server块，每个server就是一个监听服务，
  * **server_name**：就是这个服务器的地址或者域名，
  * **location**:就是配置的地址，
    * **proxy_pass**:跳转的地址

### 2.Nginx 配置实例-反向代理实例 2

1.**实现效果** 

使用nginx反向代理，根据访问的路径跳转到不同端口的服务中 

例如：nginx监听端口为9001

访问http://192.168.17.129:9001/edu/直接跳转到127.0.0.1:8080

访问http:// 192.168.17.129:9001/vod/直接跳转到127.0.0.1:8081

2.**准备工作**

1.准备两个tomcat服务器，一个8080端口，一个8081端口

2.创建文件夹和测试页面

3.具体配置

* 找到nginx配置文件，进行反向代理配置

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86%E9%85%8D%E7%BD%AE2.png?raw=true)

说明：**location ~ /edu/**:表示只要路径中包含有edu的，都被转发到8080端口

### 3.nginx的负载均衡 

1.**浏览器地址栏输入地址http://192.168.17.129/edu/a.html，负载均衡效果，平均8080和8081端口中**

2.**准备工作**

* 准备两台tomcat服务器，一台8080，一台8081

* 在两台tomcat里面webapps目录中，创建名称是edu文件夹，在edu文件夹中创建 

* 页面a.html，用于测试

3.在nginx的配置文件中进行负载均衡的配置

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%9D%87%E8%A1%A1%E8%B4%9F%E8%BD%BD.png?raw=true)

说明：**upstream**：是定义的模块，在这个模块中，定义多个服务

4.**nginx分配服务器策略**

1.**第一种 轮询（默认）**

***每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。***

2.**第二种 weight**

**weight代表权重默认为1,权重越高被分配的客户端越多**

 例如：在upstream模块中的每个server的后面添加weight       **server 192.168.17.129:8080 weight=5；**

3.**第三种ip_hash**

**每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器**

 例如：在upstream模块的第一行添加ip_hash；

4.**fair（第三方）**

**按后端服务器的响应时间来分配请求，响应时间短的优先分配。**

例如：就是在upstream模块的最后一行添加fair；

### 4.Nginx配置实例动静分离

#### 1.什么是动静分离

通过location指定不同的后缀名实现不同的请求转发。通过**expires**参数设置，可以使浏览器缓存过期时间，减少与服务器之前的请求和流量。具体**Expires**定义：是给一个资源设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件不建议使用Expires来缓存），我这里设置**3d**，表示在这3天之内访问这个URL，发送一个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码304，如果有修改，则直接从服务器重新下载，返回状态码200。

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%8A%A8%E9%9D%99%E7%BB%93%E5%90%88.png?raw=true)

#### 2.准备工作

在liunx系统中准备静态资源，用于进行访问

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%8A%A8%E9%9D%99%E7%BB%93%E5%90%882.png?raw=true)

#### 3.nginx的配置文件

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%8A%A8%E9%9D%99%E7%BB%93%E5%90%883.png?raw=true)

在location /image/中，配置了autoindex  on，所以在访问静态文件的时候，会出现一个链接，，你单击链接才会显示出你的静态文件

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E5%8A%A8%E9%9D%99%E7%BB%93%E5%90%88-autoindex.png?raw=true)

### 5.Nginx配置高可用的集群

#### 1.什么是高可用

![img](https://github.com/lovefree624/study/blob/master/%E5%9B%BE%E7%89%87/nginx/%E9%AB%98%E5%8F%AF%E7%94%A8.png?raw=true)

#### 2.准备工作

1.需要两台nginx服务器

2.需要keepalived

3.需要虚拟ip

#### 3.配置高可用的准备工作

 

**1.需要两台服务器,例如192.168.17.129和192.168.17.131**

**2.在两台服务器安装nginx**

**3.在两台服务器安装keepalived**

**4.在两台服务器安装keepalived**

* 使用yum命令进行安装：yum install keepalived –y

* 安装之后，在etc里面生成目录keepalived，有文件keepalived.conf

**5.完成高可用配置（主从配置）**

1.修改/etc/keepalived/keepalivec.conf配置文件

```xml
global_defs {
	notification_email {
        acassen@firewall.loc
        failover@firewall.loc
        sysadmin@firewall.loc
	}
	notification_email_from Alexandre.Cassen@firewall.loc
	smtp_server 192.168.17.129
	smtp_connect_timeout 30
	router_id LVS_DEVEL
}


vrrp_script chk_http_port {
	script "/usr/local/src/nginx_check.sh"
	interval 2	#（检测脚本执行的间隔）	
	weight 2		
}		
vrrp_instance VI_1 {		
	state BACKUP	# 备份服务器上将 MASTER 改为 BACKUP	
	interface ens33	//网卡	
	virtual_router_id 51   # 主、备机的 virtual_router_id 必须相同	
	priority 90	# 主、备机取不同的优先级，主机值较大，备份机值较小	
	advert_int 1		
	authentication {
        auth_type PASS #
        auth_pass 1111 #密码
	}
	virtual_ipaddress {
	192.168.17.50 // VRRP H 虚拟地址
	}
}
```

2.在/usr/local/src添加检测脚本

```xml
#!/bin/bash

A=`ps -C nginx –no-header |wc -l`

if [ $A -eq 0 ];then

/usr/local/nginx/sbin/nginx

sleep 2

if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then

killall keepalived

fi

fi
```

3.把两台服务器上nginx和keepalived启动

***启动*** ***nginx******：******./nginx***

***启动*** ***keepalived******：******systemctl start keepalived.service***