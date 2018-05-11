新建一个文件夹,文件夹里存好你要访问的文件,再 cd 到这目录里

python2 `python -m SimpleHTTPServer 8888`
python3 `python -m http.server 8888`




####服务器上快速起服务访问 HTML文件

写个 r.sh 文件
```
python -m SimpleHTTPServer 8888 > 88.log 2>&1 &
```
再` sh ./r.sh`

访问 django 项目
`python3  manage.py runserver 0.0.0.0:80 > /dev/null 2>&1 &`


####使用 nginx,并发访问
先安装 nginx
再` vi /etc/nginx/conf.d/default.conf`
```
#
# The default server
#
server {
listen 80;
server_name localhost;
root  /var/www;  #修改新的目录为var下的www目录 

#charset koi8-r;

#access_log logs/host.access.log main;

location / {
root /var/www;
index index.html index.htm index.php index.phtml; #添加index.php和index.phtml
# example
#ModSecurityEnabled on;
#ModSecurityConfig /etc/nginx/modsecurity.conf;
}

error_page 404 /404.html;
location = /404.html {
root /var/www; #修改新的目录文件
}

# redirect server error pages to the static page /50x.html
#
error_page 500 502 503 504 /50x.html;
location = /50x.html {
root /var/www;  #修改新的目录文件
}

# proxy the PHP scripts to Apache listening on 127.0.0.1:80
#
#location ~ \.php$ {
# proxy_pass http://127.0.0.1;
#}

# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
#
location ~ \.php$ {
root /var/www;  #修改新的目录文件
fastcgi_pass 127.0.0.1:9000;
fastcgi_index index.php;
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
include fastcgi_params;
}

# deny access to .htaccess files, if Apache's document root
# concurs with nginx's one
#
#location ~ /\.ht {
# deny all;
#}
}
```
最后`service nginx restart`
