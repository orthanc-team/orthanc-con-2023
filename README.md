## What is this repository for?

Orthanc deployment demo for Orthanc CON 2023

## native Nginx config
```
server {

    server_name orthanc-con-2023.orthanc.team; # managed by Certbot

       # To avoid 504 error
       proxy_read_timeout 120s;

       # To avoid "too big header..." error
       proxy_busy_buffers_size   512k;
       proxy_buffers   4 512k;
       proxy_buffer_size   256k;

       # To avoid "414 Request-URI Too Large" whant opening 15(!) studies in OHIF
       large_client_header_buffers 8 16k;

       location / {
               proxy_pass http://127.0.0.1:8080;
               proxy_set_header Host $http_host;
               proxy_set_header X-Forwarded-For $remote_addr;
               proxy_set_header X-Real-IP $remote_addr;
               real_ip_recursive on;
               proxy_request_buffering off;
               proxy_max_temp_file_size 0;
               client_max_body_size 0;
       }

    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/orthanc-con-2023.orthanc.team/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/orthanc-con-2023.orthanc.team/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
    if ($host = orthanc-con-2023.orthanc.team) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


        listen 80 ;
        listen [::]:80 ;
    server_name orthanc-con-2023.orthanc.team;
    return 404; # managed by Certbot


}
```