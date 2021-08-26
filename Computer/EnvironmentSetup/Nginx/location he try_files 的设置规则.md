# location he try_files 的设置规则

## references

> https://segmentfault.com/a/1190000013267839
>
> https://moonbingbing.gitbooks.io/openresty-best-practices/content/ngx/nginx_local_pcre.html
>
> https://rumenz.com/rumenbiji/nginx-try_files.html
>
> https://blog.csdn.net/u013474436/article/details/79724111



有一个项目是服务端用的 laravel，前端用的 vue。都是用的同一个域名，但是前后端的入口文件不同。所以需要分别设置路由重写

```
 log_format sapi 'http://$http_host$request_uri$request_body\t$msec\t$time_iso8601\t$proxy_add_x_forwarded_for\t$request_time';

server {
    listen 80;
    server_name sapi.example.com;
    root /var/www/public;
    access_log  /var/log/nginx/sapi.example.com.log sapi;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;


    location ^~ /storage/member-center/v1 {
        try_files $uri $uri/ /storage/member-center/v1/index.html;
    }

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        #fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        fastcgi_pass cps_php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

需要注意的是这一段

```
    location ^~ /storage/member-center/v1 {
        try_files $uri $uri/ /storage/member-center/v1/index.html;
    }
```

 ^~  表示匹配到该规则，采用该规则，不再进行后续的查找

/storage/member-center/v1 访问项目的 uri

/storage/member-center/v1/index.html 入口文件

前端项目的访问地址是

```
http://sapi.example.com/storage/member-center/v1
```

