# nginx-rtmp-module-docker

* 1- Install docker
* 2- Clone this git repo and build image by : `docker build -t nginxlive -f Dockerfile .`
* 2- Add domain or sub domain to server IP on your domain or dns control panel 
* 3- Setup LetsEncrypt ( certbot ) on server 

   ```
  yum -y install yum-utils
  yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
  yum install certbot
  ```

* 4- Make well-known path on `/var/www/.well-known`, by this command `mkdir /var/www/.well-known`
* 5- Run fist time docker image on port 80 and 443 like this example just for setup SSL by default nginx config

   ``` 
   docker run -d -p 1935:1935 -p 80:80 -p 443:443 -v ./conf/nginx.conf:/etc/nginx/nginx.conf -v /var/www:/var/www  nginxlive
   ```
   
* 6- Run cerboot like this ( replace www.example.com to your domain name )

   ```
   certbot certonly --webroot -w /var/www/ -d www.example.com
   ```

* 7- After setup SSL certificate, stop container
* 8- Edit 'nginx_domain.conf' and put replace `www.example.com` to your domain name and run again by command like this

   ```
   docker run -d -p 1935:1935 -p 80:80 -p 443:443 -v ./conf/nginx_domain.conf:/etc/nginx/nginx.conf -v /var/www:/var/www -v /etc/letsencrypt:/etc/letsencrypt nginxlive
   ```





