### 安装PHP

#### 源码 (依赖乱七八糟的太多了....)

下载地址: [地址](http://www.php.net/releases/) 

1.安装依赖

        yum install libxml2 libxml2-devel openssl openssl-devel bzip2 bzip2-devel libcurl libcurl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel gmp gmp-devel libmcrypt libmcrypt-devel readline readline-devel libxslt libxslt-devel

2.下载解压你下载的PHP包 x代表版本

        wget http://url.gz  //替换路由

        tar -xf php-xxx.tar.gz //解压

3.配置 (注意prefix的路径，代表你安装的路径)

        ./configure --prefix=/usr/local/phpxxx/ --with-config-file-path=/usr/local/phpxxx/etc/ --enable-mbstring --with-curl --with-gd --enable-fpm --enable-mysqlnd  --with-pdo-mysql=mysqlnd  --with-mysqli=mysqlnd

4.编译

        make

5.测试编译是否有问题 (可跳过)

        make test

6.编译通过 开始安装

        make install

7.复制 

`php.ini-development` 到 `/usr/local/phpxxx/etc/php.ini`


#### yum或apt源安装或更新到指定版本

强迫症患者请先 `yum list installed` 查看安装了哪些php，然后用通配符卸载

1.卸载旧版本PHP

        yum remove php* php-common

2.rpm安装PHP源

        rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

3.安装php7.x和相关拓展

        yum install php71w.x86_64 php71w-cli.x86_64 php71w-common.x86_64 php71w-gd.x86_64 php71w-ldap.x86_64 php71w-mbstring.x86_64 php71w-mcrypt.x86_64 php71w-mysql.x86_64 php71w-pdo.x86_64 php71w-opcache.x86_64

4.安装php-fpm

        yum install php71w-fpm  //71代表7.1


开机自启php-fpm

        systemctl enable php-fpm


(下面的可做可不做，fpm一般操作的不多，不需要把服务加到service)

1. `ln -s` 软链 php-fpm 到 `/usr/sbin/php-fpm`

2. 添加配置

   `vim /etc/init.d/php-fpm`
   
```
    #! /bin/sh
    #
    # chkconfig: - 84 16
    # description:	PHP FastCGI Process Manager
    # processname: php-fpm
    # config: /etc/php-fpm.conf
    # config: /etc/sysconfig/php-fpm
    # pidfile: /var/run/php-fpm/php-fpm.pid
    #
    ### BEGIN INIT INFO
    # Provides: php-fpm
    # Required-Start: $local_fs $remote_fs $network $named
    # Required-Stop: $local_fs $remote_fs $network
    # Short-Description: start and stop PHP FPM
    # Description: PHP FastCGI Process Manager
    ### END INIT INFO
    
    # Standard LSB functions
    #. /lib/lsb/init-functions
    
    # Source function library.
    . /etc/init.d/functions
    
    # Check that networking is up.
    . /etc/sysconfig/network
    
    # Additional environment file
    if [ -f /etc/sysconfig/php-fpm ]; then
          . /etc/sysconfig/php-fpm
    fi
    
    if [ "$NETWORKING" = "no" ]
    then
    	exit 0
    fi
    
    RETVAL=0
    prog="php-fpm"
    pidfile=${PIDFILE-/var/run/php-fpm/php-fpm.pid}
    lockfile=${LOCKFILE-/var/lock/subsys/php-fpm}
    
    start () {
    	echo -n $"Starting $prog: "
    	dir=$(dirname ${pidfile})
    	[ -d $dir ] || mkdir $dir
    	daemon --pidfile ${pidfile} /usr/sbin/php-fpm --daemonize
    	RETVAL=$?
    	echo
    	[ $RETVAL -eq 0 ] && touch ${lockfile}
    }
    stop () {
    	echo -n $"Stopping $prog: "
    	killproc -p ${pidfile} php-fpm
    	RETVAL=$?
    	echo
    	if [ $RETVAL -eq 0 ] ; then
    		rm -f ${lockfile} ${pidfile}
    	fi
    }
    
    restart () {
            stop
            start
    }
    
    reload () {
    	echo -n $"Reloading $prog: "
    	if ! /usr/sbin/php-fpm --test ; then
    	        RETVAL=6
    	        echo $"not reloading due to configuration syntax error"
    	        failure $"not reloading $prog due to configuration syntax error"
    	else
    		killproc -p ${pidfile} php-fpm -USR2
    		RETVAL=$?
    	fi
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
      status)
    	status -p ${pidfile} php-fpm
    	RETVAL=$?
    	;;
      restart)
    	restart
    	;;
      reload|force-reload)
    	reload
    	;;
      configtest)
     	/usr/sbin/php-fpm --test
    	RETVAL=$?
    	;;
      condrestart|try-restart)
    	[ -f ${lockfile} ] && restart || :
    	;;
      *)
    	echo $"Usage: $0 {start|stop|status|restart|reload|force-reload|condrestart|try-restart|configtest}"
    	RETVAL=2
            ;;
    esac
    
    exit $RETVAL
```
   
   找到里面的 /usr/sbin/php-fpm 换成你源码编译的php-fpm 路径就好

3. 添加执行权限

        chmod a+x /etc/init.d/nginx
   
   添加到chkconfig
   
        chkconfig --add /etc/init.d/nginx
   
   可以使用了

        service php-fpm start
    
        systemctl restart php-fpm`


Tips：
   1.美化输出数组
   
    echo "<pre>";print_r($exception);echo "<pre>";
    
   


