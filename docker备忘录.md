# docker备忘录

## 常用指令

* `docker search php` 搜索镜像

* `docker pull php:7.2-fpm` 拉取镜像

* `docker run -p 9000:9000 --name php-fpm72 -d -v `pwd`/<app1路径>:/var/www/html:ro php:7.2-fpm`  运行phpfpm容器

* `docker exec -it php-fpm72 bash` 进入容器

* `docker image prune` 清除不没有关联的镜像

* `docker image ls --format "{{.ID}}: {{.Repository}}"` 只包含镜像ID和仓库名

* `docker image ls --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"` 以表格等距显示，并且有标题行

## 常用配置

> min.conf

```
server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name _;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    location ~ \.php$ {
        fastcgi_pass   php-fpm72:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /var/www/html/$fastcgi_script_name;
        include        fastcgi_params;
    }
}

```
