server {
        listen 80;
        server_name hrms.loc;
    
        access_log /var/log/nginx/hrms-access.log;
        error_log  /var/log/nginx/hrms-error.log ;

        root /var/www/html/hrms/;

        autoindex on;

        location /.git {
                ##
                deny all;
                return 404;
                ##
        }

        location / {
                
                #auth_basic "Authenticaton Required";
                #auth_basic_user_file /var/www/html/.htpasswd;

                ##
                index index.php index.htm index.html;
                try_files $uri $uri/ /index.php;
                ##
        }

        location ~ \.php$ {
                include /etc/nginx/fastcgi_params;
                fastcgi_pass  127.0.0.1:9000;
		#fastcgi_pass unix:/run/php/php7.4-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_read_timeout 3600;
                # return 444;
        }
 
        # set client body size to 100M #
        client_max_body_size 100M;

        # Media: images, icons, video, audio, HTC, font woff

        location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|svgz|mp4|ogg|ogv|webm|htc|woff)$ {
                ##
                expires 1y;
                access_log off;
                add_header Cache-Control "public";
                ##
        }
 
        # CSS and Javascript
        location ~* \.(?:css|js)$ {
                expires 1M;
                access_log off;
                add_header Cache-Control "public";
        }

        gzip on;
        gzip_static on;
        gzip_disable "MSIE [1-6]\.(?!.*SV1)";
        gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;  
}

