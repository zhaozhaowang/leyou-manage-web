# leyou-manage-web是Vue项目

## 1.Build Setup

``` bash
# install dependencies 通过命令安装依赖的jar
npm install

# serve with hot reload at localhost:8080 运行项目
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

## 2.项目结构
```properties
build: 目录下面放的都是webpack的配置文件

config: 目录下面放的都是webpack运行所需要的环境参数

dist: 目录下面放的都是Vue项目打包后的目录

node_modules: Vue的相关jar都在这里
```

## 3.项目说明

```properties
Vue项目一般要关注package.json这个文件,这里面定义了该项目所用到的jar,类似于pom.xml文件.
1.这是Vue项目,是后台系统的前端页面.
2.拿到项目后,需要下载项目的依赖jar,执行命令:npm install.
3.然后通过:npm run dev 命令运行项目.
```

## 4.修改host文件里面的域名和端口映射关系,以后通过域名来进行访问项目.

## 5.什么是nginx?nginx属于反向代理

```properties
正向代理:代理用户
反向代理:代理服务器
```

## 6.zuul nginx区别?

	zuul和nginx有什么区别?
	zuul是在程序内部进行负载均衡
	nginx是在程序外部进行的负载均衡
## 7.MAC下安装nginx教程

```properties
https://www.cnblogs.com/meng1314-shuai/p/8335140.html
```

## 8.修改jenkins端口

```properties
nginx的默认端口是80,可以省略不写,但是会和已经安装好的jenkins冲突,所以需要修改Jenkins的port.
修改jenkins端口的方法：
先关闭jenkins;
命令行下修改端口：sudo defaults write /Library/Preferences/org.jenkins-ci httpPort 7071
启动jenkins： sudo launchctl load /Library/LaunchDaemons/org.jenkins-ci.plist
停止jenkins：sudo launchctl unload /Library/LaunchDaemons/org.jenkins-ci.plist
```

## 9.MAC上配置nginx(version:1.17.3_1)并且启动

```properties
1进入安装路径   cd /usr/local/Cellar/nginx/1.17.3_1/bin

2启动 sudo ./nginx

3重启 sudo ./nginx -s reload 

4判断配置文件是否正确 sudo ./nginx -t 

5nginx停止  首先查询nginx主进程号  ps -ef|grep nginx 

正常停止   sudo kill -QUIT 主进程号

快速停止   sudo kill -TERM 主进程号
```

## 10.MAC上nginx是否启动成功

```properties
sudo lsof -i :80
COMMAND   PID                USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
nginx   97824 mobiletestingdevice    6u  IPv4 0xc1c4e861af9f9063      0t0  TCP *:http-alt (LISTEN)
nginx   97825 mobiletestingdevice    6u  IPv4 0xc1c4e861af9f9063      0t0  TCP *:http-alt (LISTEN)
```

## 11.MAC上编辑nginx的配置文件

```properties
 cat /usr/local/etc/nginx/nginx.conf
```

```properties

#user  nobody;

#Nginx运行时使用的CPU核数
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    #一个woeker进程的最大连接数
    worker_connections  1024;
}


#Nginx用作虚拟主机时使用。每一个server模块生成一个虚拟主机。
http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;超时时间
    keepalive_timeout  65;

    #gzip  on;




    server {
        listen       80;
        server_name  api.leyou.com;
        charset utf-8;
        location / {
			proxy_pass http://127.0.0.1:10010;
			proxy_connect_timeout 600;
			proxy_read_timeout 600;
        }
    }

    #自定义端口和映射本地网络路径
    server {
        #监听端口
        listen       80;
        #服务地址
        server_name  manage.leyou.com;

        #编码方式
        charset utf-8;

        #access_log  logs/host.access.log  main;

        #这是nginx.cnf的默认配置
        #location / {
            #root   html;
            #设置默认网页
            #index  index.html index.htm;
        #}

        #这是我们自己的配置
        #/表示所有的请求都会走的路径
        location / {
            #代理地址
            proxy_pass http://127.0.0.1:9001;
            proxy_connect_timeout 6000;
            proxy_read_timeout 600;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*;
}
```





