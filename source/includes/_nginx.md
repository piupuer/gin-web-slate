## Nginx

### Install

> Install(Ubuntu example):

```shell
apt-get install nginx

nginx -v
# my version
# nginx version: nginx/1.14.0 (Ubuntu)
```

You can also download and install it by [this](http://nginx.org/en/download.html) link.

### Run

> Start service:

```shell
systemctl start nginx
```

> Find nginx.conf:

```shell
nginx -t
# nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
# nginx: configuration file /etc/nginx/nginx.conf test is successful
```

> Add virtual host in `http`:

```shell
##
# Virtual Host Configs
##
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```

### Add virtual host

> Add gin-web.conf:

```shell
vim /etc/nginx/conf.d/gin-web.conf 
# write this:

# api
upstream gin-web {
  server 127.0.0.1:10000;
  keepalive 64;
}
# ui
upstream gin-web-vue {
  server 127.0.0.1:10001;
  keepalive 64;
}
# pprof
upstream gin-pprof {
  server 127.0.0.1:10005;
  keepalive 64;
}
server {
  listen 80;
  # enable https
  #listen 443 ssl;
  # cert
  #ssl_certificate cert/domain.com.pem;
  #ssl_certificate_key cert/domain.com.key;
  # http redirect https
  #if ( $ssl_protocol = "") {
  #  rewrite ^ https://$host$request_uri? permanent;
  #}        

  # your domain
  # server_name domain.com;
  server_name 127.0.0.1;

  location / {
    proxy_redirect     off;
    proxy_set_header    X-Real-IP  $remote_addr;
    proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header    X-Forwarded-Proto $scheme;
    proxy_set_header    Host $http_host;
    proxy_set_header    X-NginX-Proxy true;
    proxy_set_header    Connection "";
    proxy_http_version 1.1;
    # end with / (useful for children path)
    proxy_pass         http://gin-web-vue/;
  }
  
  location ^~ /api {
    proxy_redirect     off;
    proxy_set_header    X-Real-IP $remote_addr;
    proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header    X-Forwarded-Proto $scheme;
    proxy_set_header    Host $http_host;
    proxy_set_header    X-NginX-Proxy true;
    proxy_set_header    Upgrade $http_upgrade;
    proxy_set_header    Connection 'upgrade';
    proxy_http_version 1.1;
    proxy_pass         http://gin-web/api;
  }

  location ^~ /swagger {
    proxy_redirect     off;
    proxy_set_header   X-Real-IP $remote_addr;
    proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header   X-Forwarded-Proto $scheme;
    proxy_set_header   Host $http_host;
    proxy_set_header   X-NginX-Proxy true;
    proxy_set_header   Upgrade $http_upgrade;
    proxy_set_header   Connection 'upgrade';
    proxy_http_version 1.1;
    proxy_pass         http://gin-web/swagger;
  }

  location ^~ /debug/pprof/ {
    proxy_redirect     off;
    proxy_set_header    X-Real-IP $remote_addr;
    proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header    X-Forwarded-Proto $scheme;
    proxy_set_header    Host $http_host;
    proxy_set_header    X-NginX-Proxy true;
    proxy_set_header    Connection "";
    proxy_http_version 1.1;
    proxy_pass         http://gin-pprof/debug/pprof/;
  }
}
```

### Restart

> Restart service:

```shell
systemctl restart nginx
```

### Visit

Visit it in your browser [127.0.0.1](http://127.0.0.1), port is hidden.
