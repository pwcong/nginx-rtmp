# docker-nginx-rtmp

A Dockerfile installing NGINX, nginx-rtmp-module and FFmpeg from source with
default settings for HLS live streaming. Built on Alpine Linux.

- Nginx 1.21.0 (Mainline version compiled from source)
- nginx-rtmp-module 1.2.2 (compiled from source)
- ffmpeg 4.4 (compiled from source)
- Default HLS settings (See: [nginx.conf](nginx.conf))

[![Docker Stars](https://img.shields.io/docker/stars/pwcong/nginx-rtmp.svg)](https://hub.docker.com/r/pwcong/nginx-rtmp/)
[![Docker Pulls](https://img.shields.io/docker/pulls/pwcong/nginx-rtmp.svg)](https://hub.docker.com/r/pwcong/nginx-rtmp/)
[![Docker Automated build](https://img.shields.io/docker/automated/pwcong/nginx-rtmp.svg)](https://hub.docker.com/r/pwcong/nginx-rtmp/builds/)
[![Build Status](https://travis-ci.org/alfg/docker-nginx-rtmp.svg?branch=master)](https://travis-ci.org/alfg/docker-nginx-rtmp)

## Usage

### Server

- Pull docker image and run:

```
docker pull pwcong/nginx-rtmp
docker run -it -p 1935:1935 -p 8080:80 --rm pwcong/nginx-rtmp
```

or

- Build and run container from source:

```
docker build -t nginx-rtmp .
docker run -it -p 1935:1935 -p 8080:80 --rm nginx-rtmp
```

- Stream live content to:

```
rtmp://localhost:1935/live/$STREAM_NAME
```

### SSL

To enable SSL, see [nginx.conf](nginx.conf) and uncomment the lines:

```
listen 443 ssl;
ssl_certificate     /www/certs/example.com.crt;
ssl_certificate_key /www/certs/example.com.key;
```

This will enable HTTPS using a self-signed certificate supplied in [/certs](/certs). If you wish to use HTTPS, it is **highly recommended** to obtain your own certificates and update the `ssl_certificate` and `ssl_certificate_key` paths.

I recommend using [Certbot](https://certbot.eff.org/docs/install.html) from [Let's Encrypt](https://letsencrypt.org).

### Environment Variables

This Docker image uses `envsubst` for environment variable substitution. You can define additional environment variables in `nginx.conf` as `${var}` and pass them in your `docker-compose` file or `docker` command.

### Custom `nginx.conf`

If you wish to use your own `nginx.conf`, mount it as a volume in your `docker-compose` or `docker` command as `nginx.conf.template`:

```yaml
volumes:
  - ./nginx.conf:/etc/nginx/nginx.conf.template
```

### OBS Configuration

- Stream Type: `Custom Streaming Server`
- URL: `rtmp://localhost:1935/live`
- Stream Key: `hello`

### Watch Stream

- Load up the example HLS player in your browser:

```
http://localhost:8080/player_hls.html?url=http://localhost:8080/live/hello.m3u8
```

- Load up the example DASH player in your browser:

```
http://localhost:8080/player_dash.html?url=http://localhost:8080/dash/hello.mpd
```

- Or in Safari, VLC or any HLS player, open:

```
http://localhost:8080/live/$STREAM_NAME.m3u8
```

- Example Playlist: `http://localhost:8080/live/hello.m3u8`
- [HLS.js Player](https://hls-js.netlify.app/demo/?src=http%3A%2F%2Flocalhost%3A8080%2Flive%2Fhello.m3u8)
- FFplay: `ffplay -fflags nobuffer rtmp://localhost:1935/live/hello`

## Resources

- https://alpinelinux.org/
- http://nginx.org
- https://github.com/pwcong/nginx-rtmp
- https://www.ffmpeg.org
- https://obsproject.com
