## Description
This Docker image can be used to create a video streaming server ( With support both of Live and VOD ) that supports [**RTMP**](https://en.wikipedia.org/wiki/Real-Time_Messaging_Protocol) input and [**HLS**](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) out of the box. 
It also allows adaptive streaming and custom transcoding of video streams.
All modules are built from source on Debian and Alpine Linux base images.

## Features
 * The backend is [**Nginx**](http://nginx.org/en/) with [**nginx-rtmp-module**](https://github.com/arut/nginx-rtmp-module) and [**nginx-vod-module**](https://github.com/kaltura/nginx-vod-module).
 * [**FFmpeg**](https://www.ffmpeg.org/) for transcoding and adaptive streaming.
 * Default settings: 
	* RTMP is ON
	* HLS Live is ON (adaptive, multi variants by call ffmpeg on config)
	* HLS VOD is ON

Current Image is built using:
 * Nginx 1.18.0 (compiled from source)
 * Nginx-rtmp-module 1.2.1 (compiled from source)
 * Nginx-vod-module 1.27 (compiled from source)
 * FFmpeg 4.3.1 (compiled from source)
  
This image was inspired by similar docker images from [TareqAlqutami](https://github.com/TareqAlqutami/rtmp-hls-server) and [dojocasts](https://gitlab.com/dojocasts/nginx-vod-module-docker) and [nytimes](https://github.com/nytimes/nginx-vod-module-docker).

## Install
Install docker on server, after that just run : `docker pull voltan/nginx-stream`

## Domain
After install this image, Set domain or sub domain to server IP on your domain or dns control panel, and call all urls by domain instead of server IP, Push to this domain and watch output on this domain too 

## SSL
 * You can by ssl certificate for your domain, upload certificate on server and put certificate path in docker run command.
 * If you want use LetsEncrypt ( certbot ), setup certbot on your server and create certificate for your domain and put certificate path in docker run command.
 
## LetsEncrypt SSL
1. Install LetsEncrypt ( certbot ) on server 
      ```
      yum -y install yum-utils
      yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
      yum install certbot
      ```
2. Make well-known path on `/var/www/.well-known`, by this command `mkdir /var/www/.well-known`
3. Run docker image by `nginx_simple.conf` on port 80 and 443 like this example just for setup SSL by default nginx config
     ``` 
     docker run -p 1935:1935 -p 80:80 -p 443:443 -v $PWD/conf/nginx_simple.conf:/etc/nginx/nginx.conf -v /var/www:/var/www  nginx-stream
     ```
4. Run cerboot like this ( replace `www.example.com` to your domain name )    
     ```
     certbot certonly --webroot -w /var/www/ -d www.example.com
     ```
5. After setup SSL certificate, stop container and go to next steps

## Run Service
1. You should run this image just by customize `[nginx_domain.conf]()` or `nginx_domain_ffmpeg.conf` (for multi bitrate support by ffmpeg)
   * Get [nginx_domain.conf](https://github.com/voltan/nginx-stream/blob/master/conf/nginx_domain.conf) for run service normally
   * Get [nginx_domain_ffmpeg.conf](https://github.com/voltan/nginx-stream/blob/master/conf/nginx_domain_ffmpeg.conf) for run multi bitrate support by ffmpeg
2. Create `videos` folder on your server VOD server and put your videos on it
3. Edit `nginx_domain.conf` or `nginx_domain_ffmpeg.conf` and put replace `www.example.com` to your domain name and put it on your server
4. Edit `nginx_domain.conf` or `nginx_domain_ffmpeg.conf` and put SSL certificate information on it ( by default, LetsEncrypt information added )
5. Run image by command like this :
    ```
    docker run -p 1935:1935 -p 80:80 -p 443:443 -v $PWD/conf/nginx_domain.conf:/etc/nginx/nginx.conf -v $PWD/videos:/opt/static/videos -v /var/www:/var/www -v /etc/letsencrypt:/etc/letsencrypt voltan/nginx-stream
    ```
6. For run image on background, just add `-d` after docker run on previous command

## URLs
**Stream RTMP to server**
 - `rtmp://www.example.com:1935/live/<stream_key>`

**View Live stream**
 - `https://www.example.com/hls/<stream-key>.m3u8`

**View VOD stream**
 - `https://www.example.com/vod/<file-name>.mp4/master.m3u8`

**Statistic**
 - `https://www.example.com/stats`

## Building Locally
1. Install docker on server
2. Clone this git repo by : `git clone https://github.com/voltan/nginx-stream.git` 
3. Build image by : `docker build -t nginx-stream -f Dockerfile .`
4. Continue by setup domain, ssl and run service

## Copyright
Released under MIT license.
