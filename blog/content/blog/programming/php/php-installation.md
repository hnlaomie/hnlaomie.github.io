Title: php installation
Date: 2015-03-03 10:30
Category: programming
Tags: 201503, php, nginx
Author: laomie
Summary: php安装

php安装
---------------------------------
```bash
$ sudo apt-get install php5-fpm php5-gd php5-mcrypt php5-mysql php5-readline php-gettext
```

nginx安装
------------------------
```bash
$ sudo add-apt-repository ppa:nginx/stable
$ sudo apt-get update
$ sudo apt-get install nginx
```

php和nginx设置
--------------------------
修改/etc/php5/fpm/php.ini，将"cgi.fix_pathinfo=1"改为"cgi.fix_pathinfo=0"

修改/etc/php5/fpm/pool.d/www.conf，将"listen = 127.0.0.1:9000"改为"listen = /var/run/php5-fpm.sock"

修改/etc/nginx/sites-available/default
```xml
[...]
server {
    listen   80;

    root /usr/local/data/env/www;
    index index.php index.html index.htm;

    server_name localhost;

    location / {
        try_files $uri $uri/ /index.html;
    }

    error_page 404 /404.html;

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/local/data/env/www;
    }

    # pass the PHP scripts to FastCGI server listening on the php-fpm socket
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

}
[...]

重启服务
```bash
$ sudo service nginx restart
$ sudo service php5-fpm restart
```
```

相关链接
-------------------
<https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-12-04>
<http://www.lonelycoder.be/nginx-php-fpm-mysql-phpmyadmin-on-ubuntu-12-04/>


