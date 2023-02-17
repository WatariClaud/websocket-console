# # WebSocket Web Developer Console - installation instructions

To download the latest version of this software, run the following commands:

```console
apt update
apt -y upgrade
apt -y install git
git clone https://github.com/libersoft-org/websocket-console.git
cd websocket-console
```

Now you need to move the content of the "src" folder to your web root subdirectory.

Here are the examples of how to do it based on different web servers:

### NEMP Server

This is the example for NEMP server stored in /root/nemp-server/:

- Move the content of the "src" folder to your web root:

```console
mkdir /root/nemp-server/www/console
mv ./src /root/nemp-server/www/console
```

- Then open the web browser and navigate to: https://nemp.domain.tld/console/

### NGINX

This is the example for web console stored in **/var/www/nemp.domain.tld/console/** directory:

- Create the virtual web configuration file for your NGINX server (by default in: /etc/nginx/sites-available/) named by your domain name (for example: **nemp.domain.tld.conf**) and insert this content:

```ini
server {
 listen 80;
 listen [::]:80;
 listen 443 ssl http2;
 listen [::]:443 ssl http2;
 server_name nemp.domain.tld;
 index index.html index.htm;
 root /var/www/nemp.domain.tld/console/;
 ssl_certificate /etc/letsencrypt/live/nemp.domain.tld/fullchain.pem;
 ssl_certificate_key /etc/letsencrypt/live/nemp.domain.tld/privkey.pem;

 if ($scheme != "https") {
  return 301 https://$host$request_uri;
 }

 location / {
  try_files $uri /index.html;
 }

 location ~ /\.ht {
  deny all;
 }
}
```

Replace **nemp.domain.tld** with your actual domain name and save the file.

... then move the content of the "src" directory to your web server root, for example:

```console
mkdir /var/www/nemp.domain.tld
mkdir /var/www/nemp.domain.tld/console
mv ./src /var/www/nemp.domain.tld/console/
```

Then restart your NGINX server using:

```console
/etc/init.d/nginx restart
```

**IMPORTANT NOTE:** If you are using this software for NEMP Server, you cannot run the NEMP Server on the same network port as your NGINX Server. Please edit your **settings.json** file in your NEMP Server "src" directory and change the HTTPS port to something else than 443 and and turn off the HTTP (by default on port 80) by setting the "http_run" property from "true" to "false" as it is used by NGINX too. Don't forget to specify the NEMP Server HTTPS port in the TXT record in your DNS as well.