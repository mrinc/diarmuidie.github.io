---
layout: post
title: 'Setup Mass Dynamic Virtual Hosts on Nginx'
redirect_from: /blog/post/setup-mass-dynamic-virtual-hosts-on-nginx
tags:
    - Guide

---
Apache has a handy feature that allows you to dynamically serve a domain without having to create a virtualhost for each time, called [Dynamic Virtual Hosts](https://httpd.apache.org/docs/2.2/vhosts/mass.html). Nginx doesn't have this feature built in, but it does gives you the tools you need to to set it up in no time. Here is a guide to how I got it working on my server after I transitioned my site from Apache to Nginx.

##My Requirements##

There are a few example of how to setup dynamic virtual hosts on Nginx, but none of them did exactly what I wanted. Here were my requirements:

1. Serve a domain i.e. `example.com` from `/var/www/example.com/public_html` without having to update the server config or restarting Nginx.
2. Redirect [www requests to non-www](http://no-www.org/).
3. Include PHP Fastcgi config.

##Config##

To setup this in nginx all you have to do is edit your default nginx server config file (usually `/etc/nginx/sites-enabled/default`) in a text editor (I use VIM) to look like the below (changing where necessary to suit your setup):

    # www Redirect to non-www

    server {
      # don't forget to tell on which port this server listens
      listen 80;

      # listen on the www host
      server_name ~^(www\.)(?<domain>.+)$;

      # and redirect to the non-www host (declared below)
      return 301 $scheme://$domain$request_uri;
    }


    # Nginx Dynamic Hosts

    server {

      listen 80;

      # catch all non-www domains
      server_name ~(?<domain>.+)$;

      root /var/www/$domain/public_html/;
      access_log /var/log/nginx/$domain/logs/access.log;

      location / {
        index index.html index.htm index.php;
      }

      location ~ [^/]\.php(/|$) {
        fastcgi_split_path_info ^(.+?\.php)(/.*)$;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
      }
    }

you can easily override this default server config on a case by case basis by creating a new server block where you explicitly define the server_name. Nginx will load this server config before a server_name matched with regular expressions (As defined in the [docs](http://nginx.org/en/docs/http/server_names.html)):

    server {
      # don't forget to tell on which port this server listens
      listen 80;

      # explicitly list the server_name
      server_name example2.com;

      ...

    }

The above examples were built based on the information from [NGINX dynamically configured mass virtual hosting](http://syshero.org/post/68729802960/nginx-dynamically-configured-mass-virtual-hosting) and [Nginx HTTP server boilerplate configs](https://github.com/h5bp/server-configs-nginx).
