# autoindex-material-dark-theme

## Installation

Run the following code in the document root.

```
git clone https://github.com/yuusakuri/autoindex-material-dark-theme.git .autoindex
```

Modify the nginx conf file. Below is an example I'm using in my PHP development environment. The two blocks enclosed in the commented out autoindex are important.

```
server {

    listen 80;
    listen [::]:80;

    # For https
    # listen 443 ssl;
    # listen [::]:443 ssl ipv6only=on;
    # ssl_certificate /etc/nginx/ssl/default.crt;
    # ssl_certificate_key /etc/nginx/ssl/default.key;

    server_name php.test;
    root /var/www/php;
    index index.php index.html index.htm;

    # autoindex
    autoindex on;
    autoindex_exact_size off;
    autoindex_localtime on;
    add_before_body /.autoindex/header.html;
    add_after_body /.autoindex/footer.html;
    # /autoindex

    location / {
         try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_pass php-upstream;
        fastcgi_index index.php;
        fastcgi_buffers 16 16k;
        fastcgi_buffer_size 32k;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        fastcgi_read_timeout 600;
        include fastcgi_params;
        # autoindex
        add_after_body '';
        add_before_body '';
        # /autoindex
    }

    location ~ /\.ht {
        deny all;
    }

    location /.well-known/acme-challenge/ {
        root /var/www/letsencrypt/;
        log_not_found off;
    }

    error_log /var/log/nginx/app_error.log;
    access_log /var/log/nginx/app_access.log;
}
```
