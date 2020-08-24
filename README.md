## Description

This Docker image can be used to create a video streaming server that supports [**RTMP**](https://en.wikipedia.org/wiki/Real-Time_Messaging_Protocol), [**HLS**](https://en.wikipedia.org/wiki/HTTP_Live_Streaming), [**DASH**](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) out of the box. 
It also allows adaptive streaming and custom transcoding of video streams.
All modules are built from source on Debian and Alpine Linux base images.

## Features
 * The backend is [**Nginx**](http://nginx.org/en/) with [**nginx-rtmp-module**](https://github.com/arut/nginx-rtmp-module).
 * [**FFmpeg**](https://www.ffmpeg.org/) for transcoding and adaptive streaming.
 * Default settings: 
	* RTMP is ON
	* HLS is ON (adaptive, 5 variants)
	* DASH is ON 
	* Other Nginx configuration files are also provided to allow for RTMP-only streams or no-FFmpeg transcoding. 

Current Image is built using:
 * Nginx 1.18.0 (compiled from source)
 * Nginx-rtmp-module 1.2.1 (compiled from source)
 * FFmpeg 4.3.1 (compiled from source)



## Building locally
* 1- Install docker
* 2- Clone this git repo and build image by : `docker build -t nginxlive -f Dockerfile .`
* 2- Add domain or sub domain to server IP on your domain or dns control panel 
* 3- Install LetsEncrypt ( certbot ) on server 

   ```
  yum -y install yum-utils
  yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
  yum install certbot
  ```

* 4- Make well-known path on `/var/www/.well-known`, by this command `mkdir /var/www/.well-known`
* 5- Run fist time docker image on port 80 and 443 like this example just for setup SSL by default nginx config

   ``` 
   docker run -d -p 1935:1935 -p 80:80 -p 443:443 -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf -v /var/www:/var/www  nginxlive
   ```
   
* 6- Run cerboot like this ( replace www.example.com to your domain name )

   ```
   certbot certonly --webroot -w /var/www/ -d www.example.com
   ```

* 7- After setup SSL certificate, stop container
* 8- Edit 'nginx_domain.conf' and put replace `www.example.com` to your domain name and run again by command like this

   ```
   docker run -d -p 1935:1935 -p 80:80 -p 443:443 -v $PWD/conf/nginx_domain.conf:/etc/nginx/nginx.conf -v /var/www:/var/www -v /etc/letsencrypt:/etc/letsencrypt nginxlive
   ```
## Example

* Stat : https://www.example.com/stat

### Stream live RTMP

* rtmp://www.example.com:1935/live/<stream_key>

### View the stream
* https://www.example.com/hls/<stream-key>.m3u8
* https://www.example.com/dash/<stream-key>_src.mpd

### Statistic
* https://www.example.com/stats

