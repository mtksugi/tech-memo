---
title: Nginx メモ
---

# nginx etc

- install
```bash
brew install nginx
```

- start / stop
```bash
nginx
nginx -s stop
```

- config file path
  - /usr/local/etc/nginx/nginx.conf
  - /etc/nginx/nginx.conf

など

- django gunicornとの連携
```conf
    server {
        listen       8080;
        server_name  localhost;

		# ↓/mediaの物理パスを指定
        location /media {
            alias /Volumes/SSD-PGU3-A;
        }
		# proxy_passにgunicorn側のurlを指定する
        location / {
#            root   html;
#            index  index.html index.htm;
            proxy_pass http://127.0.0.1:8000;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_redirect off;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
```

- sitemap.xmlへのリダイレクト追加（/opt/bitnami/var/staticに配置した場合）
```conf
    location /sitemap.xml {
        root /opt/bitnami/var/static;
    }
```
- トップページへのリダイレクト設定
```conf
    location /__media__/ {
        return 301 https://$host;
    }
```

- text圧縮設定:  serverディレクティブに追加→pagespeed insightでモバイルが+4の効果
```conf
    gzip on;
    gzip_types      text/plain application/xml;
    gzip_proxied    no-cache no-store private expired auth;
    gzip_min_length 1000;
```
