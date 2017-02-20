## php-fpm 参考资料:
https://www.tooto.cc/archives/193


host:port  -> 
PHP-> PHP-FPM -> host:port  127.0.0.1:9000 

apache -> PHP 流程
-> http.php  -> apache -> php5_module -> php-cgi -> parser ->  动态网页内容

nginx -> php 流程
-> http.php -> nginx -> PHP-FPM -> php-cgi -> parser  -> 动态网页内容


## Nginx介绍:
Nginx ("engine x") 是一个高性能的 HTTP 和 反向代理 服务器，也是一个 IMAP/POP3/SMTP 代理服务器。 Nginx 是由 Igor Sysoev 为俄罗斯访问量第二的 Rambler.ru 站点开发的




## Nginx安装
(1)安装nginx的rpm包
rpm -Uvh  http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm 
(2)使用 yum 安装nginx
yum install nginx
启动
systemctl start nginx 
默认的工作目录
/usr/share/nginx/html


nginx -s reload
nginx -s stop
nginx

nginx解析php：
yum install php-fpm

启动php-fpm:
systemctl start php-fpm.service



## 停止 nginx
停止操作是通过向nginx进程发送信号来进行的
步骤1：查询nginx主进程号
ps -ef | grep nginx
在进程列表里 面找master进程，它的编号就是主进程号了。
步骤2：发送信号
从容停止Nginx：
kill -QUIT 主进程号
快速停止Nginx：
kill -TERM 主进程号
强制停止Nginx：
pkill -9 nginx

(3)使用 yum 安装php-fpm  nginx解析php 需要使用 php-fpm
yum install php-fpm

启动php-fpm 
systemctl start php-fpm.service
如果访问 php 程序发现 502 检查php-fpm是否开启

// 检查9000端口使用情况
netstat -tulpn | grep :9000

## Nginx整合PHP
添加一个server 到http{} 里边
然后重启nginx 
server {
        listen 80;
        root  /var/www/html;  

        location ~ \.php$ {
            root           /var/www/html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
    }


## Nginx pathinfo支持

location ~ .php$ {
    fastcgi_index index.php;
    fastcgi_pass 127.0.0.1:9000;
    include fastcgi_params;
}

## Nginx bbs支持


## memcache 连接

location / {
    set $memcached_key "$uri?$args"; #memcache的键
    memcached_pass 127.0.0.1:11211;
    error_page 404 = /writemem.php; 回调
    index  index.php index.html index.htm;
}

多台memcached的话,先用upstream模块,
把多台memcached服务器写成一组,并起组名
然后memcached_pass为组名


## 参数优化

work_processers  CPU * [1,2]; #工作进程数
worker_rlimit_nofile 102400; #最大打开连接符
worker_connections 65535; #每进程最


## 反向代理,图片防盗
location ~ .*\.(jpg|png|gif|bmp)$ {
    proxy_set_header X-Forwarded-For $remote_addr;
    proxy_pass http://192.168.1.204:8080;
}


## 虚拟主机配置

    server {
        listen 8080; #端口
        server_name 192.168.1.204; #域名

        location / {
                root /var/www/html; #根目录
                index index.html;
        access_log  logs/proxy.access.log  main;
        }
    }


## rewrite规则
 
常用的命令
if  (条件) {}  设定条件,再进行重写 
set #设置变量
return #返回状态码 
break #跳出rewrite
rewrite #重写


## 环境配置
WNMP,LNMP
