+++
title = "Tutorial on how to setup a node.js application behind nginx"
date = "2016-10-14"
lang = "en"
+++

## Introduction

Node.js is a JavaScript runtime built on Chrome V8 JavaScript engine. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient.
NGINX is a free, open-source, high-performance HTTP server and reverse proxy, as well as an IMAP/POP3 proxy server.

In this brief tutorial we will setup how to configure a small production application that will use nginx as a reverse proxy for serving the node.js application.
For managing node.js application we will use PM2.


## Requirements

In the next steps we are going to install node.js in the /opt directory.
Check the node version on the url, if this blog post becames old you can change the version on the url.

{{< highlight bash >}}
cd ~
wget https://nodejs.org/dist/v4.2.3/node-v4.2.3-linux-x64.tar.gz
mkdir node
tar xvf node-v*.tar.?z --strip-components=1 -C ./node
cd ~
rm -rf node-v*
mkdir node/etc
echo 'prefix=/usr/local' > node/etc/npmrc
sudo mv node /opt/
sudo chown -R root: /opt/node
sudo ln -s /opt/node/bin/node /usr/local/bin/node
sudo ln -s /opt/node/bin/npm /usr/local/bin/npm
sudo npm install pm2 -g
{{< / highlight >}}

Add the user for execute the node.js application

{{< highlight bash >}}
sudo adduser nodejs_app_username
{{< / highlight >}}

## Deamonize node.js application

We need to deamonize our node.js process to allow it to be restarted automatically if the application crashes or is killed.
For this purpose we are going to use PM2, which is a node.js process manager for node.js applications.

The startup subcommand generates and configures a startup script to launch PM2 and its managed processes on server boots

{{< highlight bash >}}
sudo pm2 startup systemd
{{< / highlight >}}

if you are using ubuntu try to use "ubuntu" instead systemd.

In my case I was trying to configure a keystone.js project, so I started it with this command (start it with the nodejs app unix user):

{{< highlight bash >}}
sudo su nodejs_application_username
pm2 start keystone.js
{{< / highlight >}}

you should see your process in the list of PM2.

Check that the node.js process is working with (change the port if necessary):

{{< highlight bash >}}
wget http://localhost:3000/
{{< / highlight >}}

if for some reason your app is not working check the subcommand logs

{{< highlight bash >}}
pm2 logs
{{< / highlight >}}

## Nginx configuration

We will use nginx as a reverse proxy. This tutorial required nginx already installed, so we are going directly to site configuration.
Also this tutorial assumes that your are using the "sites-available" and "sites-enables" to configure more than one domain.

Add a new file to /etc/nginx/sites-available with the following:

{{< highlight bash >}}

upstream nodejs_app_server {
  # fail_timeout=0 means we always retry an upstream even if it failed
  # to return a good HTTP response (in case the Unicorn master nukes a
  # single worker for timing out).
  server 127.0.0.1:3000 fail_timeout=0;
}

server {
    listen   80;
    server_name www.yourdomain.com;

    location / {
        proxy_pass http://nodejs_app_server;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    access_log /var/log/nginx/nodejs_app-access.log;
    error_log /var/log/nginx/nodejs_app-error.log;
}
{{< / highlight >}}


{{< highlight bash >}}
    service nginx restart
{{< / highlight >}}