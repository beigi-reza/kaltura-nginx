# kaltura-nginx video streaming

kaltura-nginx service has been Configet for the online video streaming, using an open source software kaltura nginx-vod-module.

## Terms of use and features

This service must have direct read access to files for stream it. This access can be local or through file sharing services such as Samba or nfs.

- This service can be used in all implemented software and video players that support streaming.
- It is possible to switch to a higher or lower quality video (if the player supports it and the file exists)
- Possibility of creating a playlist for sequential playback
- Possibility of introducing subtitles (if the player supports it)
- This service currently only supports stream mp4 files.

## Use

- Clone This project
- Config & Update Files ...
- Run the Compse Files 



### Update Files

update Paths volumes section and Ports in `kaltura-compose.yml`

```yml
.
..
    ports:
      - 80:80
      - 443:443
    volumes:
      - /videoFiles/videos:/opt/static/videos
      - ./config/nginx.conf:/usr/local/nginx/conf/nginx.conf
      - ./ssl:/etc/nginx/ssl:ro
    networks:
      - my-net
.
..

```

You should create a network with name `my-net` or change the network name to whatever you want.
```bash
docker network create --driver bridge --subnet=150.50.50.0/24 my-net
```


Update `nginx.conf` File

```ini
.
..
 
  server_name example.com;
	# SSL File For SSL
  ssl_certificate /etc/nginx/ssl/example.com.cer;
  ssl_certificate_key /etc/nginx/ssl/example.com.key;
..
.    
```

Add Location For Path

```ini
		location /site/ {
			vod hls;
			alias /opt/static/videos/;
			add_header Access-Control-Allow-Headers '*';
			add_header Access-Control-Expose-Headers 'Server,range,Content-Length,Content-Range';
			add_header Access-Control-Allow-Origin '*';
			add_header Access-Control-Allow-Methods 'GET, HEAD, OPTIONS';
			add_header content-type "ronix-stream-header";
		}
```

## Run & Use

```bash
docker compose -f kaltura-compose.yml up -d
```
To use this service, URL must be created for each digital source. To create this URL.


```
http://<site>/<LocationAlias>/<VideoFileName.mp4>/master.m3u8
```

- LocationAlias : The Location name Defined in the previous step
- VideoFileName.mp4 : The video file must exist with exactly the same name in the declared path.


Our url will look like this:

```
http://site.com/site/test.mp4/master.m3u8
```

If everything is set up correctly and the file exists in the declared path, by entering the created URL in the browser, a file with named `master.m3u8` will be created and downloaded by the browser.
