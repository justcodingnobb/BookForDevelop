### 安装Nginx

#### 源码

下载地址: [地址](http://nginx.org/en/download.html)

1.下载解压你下载的包 x代表版本

下载

`wget http://url.gz 自己替换路由`

解压

`tar -xf nginx-xxx.tar.gz`

2.配置

`./configure `

4.编译

`make`

5.测试编译是否有问题(可跳过)

`make test`

6.编译通过 开始安装

`make install`

#### yum或者apt

`yum install nginx`

小姿势：

源码安装管理起来麻烦，每次都是
`/usr/local/nginx/sbin/nginx`，极度无聊，也可以通过写init.d文件的方式添加管理

`vim /etc/init.d/nginx`

下面这些内容可以从其他服务器copy，修改部分参数就好

```
#!/bin/bash
# nginx Startup script for the Nginx HTTP Server
# it is v.0.0.2 version.
# chkconfig: - 85 15
# description: Nginx is a high-performance web and proxy server.
#              It has a lot of features, but it's not for everyone.
# processname: nginx
# pidfile: /var/run/nginx.pid
# config: /usr/local/nginx/conf/nginx.conf
nginxd=/usr/local/nginx/sbin/nginx
nginx_config=/usr/local/nginx/conf/nginx.conf
nginx_pid=/var/run/nginx.pid
RETVAL=0
prog="nginx"
# Source function library.
. /etc/rc.d/init.d/functions
# Source networking configuration.
. /etc/sysconfig/network
# Check that networking is up.
[ ${NETWORKING} = "no" ] && exit 0
[ -x $nginxd ] || exit 0
# Start nginx daemons functions.
start() {
if [ -e $nginx_pid ];then
   echo "nginx already running...."
   exit 1
fi
   echo -n $"Starting $prog: "
   daemon $nginxd -c ${nginx_config}
   RETVAL=$?
   echo
   [ $RETVAL = 0 ] && touch /var/lock/subsys/nginx
   return $RETVAL
}
# Stop nginx daemons functions.
stop() {
        echo -n $"Stopping $prog: "
        killproc $nginxd
        RETVAL=$?
        echo
        [ $RETVAL = 0 ] && rm -f /var/lock/subsys/nginx /var/run/nginx.pid
}
# reload nginx service functions.
reload() {
    echo -n $"Reloading $prog: "
    #kill -HUP `cat ${nginx_pid}`
    killproc $nginxd -HUP
    RETVAL=$?
    echo
}
# See how we were called.
case "$1" in
start)
        start
        ;;
stop)
        stop
        ;;
reload)
        reload
        ;;
restart)
        stop
        start
        ;;
status)
        status $prog
        RETVAL=$?
        ;;
*)
        echo $"Usage: $prog {start|stop|restart|reload|status|help}"
        exit 1
esac
exit $RETVAL

```

修改
```
nginx=”/usr/sbin/nginx” 修改成 nginx 执行程序的路径。
NGINX_CONF_FILE=”/etc/nginx/nginx.conf” 修改成 配置文件 的路径。
```

添加执行权限

`chmod a+x /etc/init.d/nginx`

添加到chkconfig

`chkconfig --add /etc/init.d/nginx`

可以使用了

`service nginx restart`

`systemctl restart nginx`

源码安装 nginx -v 是没办法查看版本的，可以通过软连接

`ln -s /usr/local/nginx/bin/nginx /usr/bin/nginx`

（必须是绝对路径）软链之后可以通过nginx -v 查看了

