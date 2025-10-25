HTTPS - hypertext transfer protocol secure
это тоже что и [[HTTP]] только защищенный и используется на сайтах

чтобы перевести HTTP в HTTPS нужно добавить SSL сертификат
как его добавить на примере NGINX

##### для начало надо скать сертификат 
после прописать в конфиг nginx
```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate     /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    root /var/www/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}

```

