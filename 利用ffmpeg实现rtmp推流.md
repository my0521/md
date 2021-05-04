---
title: 利用ffmpeg实现rtmp推流
date: 2019-05-06 20:00:48
categories: 
 - ffmpeg
tags:
 - ffmpeg
---

在已有的nginx搭建的rtmp服务器上做视频推流拉流

<!-- more -->

## 准备
1. 查看电脑设备
``` bash
ffmpeg -list_devices true -f dshow -i dummy
```
显示一个video ，一个audio 
``` puppet
...
[dshow @ 063c4ec0] DirectShow video devices (some may be both video and audio devices)
[dshow @ 063c4ec0]  "USB2.0 VGA UVC WebCam"
[dshow @ 063c4ec0]     Alternative name "@device_pnp_\\?\usb#vid_04f2&pid_b48c&mi_00#6&5aaffc2&0&0000#{65e8773d-8f56-11d0-a3b9-00a0c9223196}\global"

[dshow @ 063c4ec0] DirectShow audio devices
[dshow @ 063c4ec0]  "楹﹀厠椋?(Conexant SmartAudio HD)"
[dshow @ 063c4ec0]     Alternative name "@device_cm_{33D9A762-90C8-11D0-BD43-00A0C911CE86}\wave_{B42C58AF-7EB1-4FAA-B3BE-D63B5C1D9F84}"
dummy: Immediate exit requested
```
2. 测试视频设备是否可用
2种方式都可以，弹出播放窗口显示设备正常
- `fffplay -f dshow -i video="USB2.0 VGA UVC WebCam"`
- `ffplay -f vfwcap -i 0`
3. 查看设备信息
- 查看摄像头信息
``` bash
ffmpeg -list_options true -f dshow -i video="USB2.0 VGA UVC WebCam"
```
- 查看麦克风信息
``` bash
ffmpeg -list_options true -f dshow -i audio="麦克风 (Conexant SmartAudio HD)" 
```

## 视频推流
1.  本地视频推流（点播）
将视频文件放`1.2预测失效原因.mp4`在/tmp的目录下，在vlc中：媒体-打开网络串流-网络，输入
`rtmp://121.36.64.197:1993/my/1.2预测失效原因.mp4`,点击播放
``` perl
rtmp {
    server{
        listen 1993;
        application my {
            play /tmp;
        }
    }
}
```
2. 直播视频配置
- rtmp 添加一个application live
``` applescript
rtmp {
    server{
        listen 1993;
        application my {
            play /tmp;
        }
        application live {
            live on;
        }
    }
}
```
- http >server添加2个location
``` nginx
server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location /stat {     #first location field
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }

        location /stat.xsl { #second location field
            root /usr/local/src/nginx-rtmp-module/;
        }
}
```
- 浏览器访问  [http://121.36.64.197/stat][1]
- ffmpeg推流 `ffmpeg.exe -re -i 1.2预测失效原因.mp4 -f flv rtmp://121.36.64.197:1993/live/123`

3. 视频回看服务器配置
- nginx.conf
``` applescript
worker_processes  1;

events {
    worker_connections  1024;
}

rtmp {
    server{
        listen 1993;
        application my {
            play /tmp;
        }
        application live {
            live on;

	    hls on;    #这个参数把直播服务器改造成实时回放服务器。
            wait_key on;  #对视频切片进行保护，这样就不会产生马赛克了。
            hls_path /tmp/hls;  #切片视频文件存放位置。
            hls_fragment 10s;     #每个视频切片的时长。
            hls_playlist_length 60s;  #总共可以回看的事件，这里设置的是1分钟。
            hls_continuous on; #连续模式。
            hls_cleanup on;    #对多余的切片进行删除。
            hls_nested on;     #嵌套模式。
        }
    }
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

       
        location /stat {     #first location field
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }

        location /stat.xsl { #second location field
            root /usr/local/src/nginx-rtmp-module/;
        }
        
        location /live {
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }
            alias /tmp/hls;    # 视频存放路径
            expires -1;
            add_header Cache-Control no-cache;
        }

        location / {
            root   html;
            index  index.html index.htm;
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }        
    }
}
```
- `chmod 777 /tmp/hls/`
- `http://121.36.64.197/live/test/index.m3u8`
index.m3u8 是目录

4. 录制flv视频服务器的配置
- nginx.conf
``` applescript
worker_processes  1;

events {
    worker_connections  1024;
}

rtmp {
    server{
        listen 1993;
        application my {
            play /tmp;
        }
        application live {
            live on;

	    hls on;    #这个参数把直播服务器改造成实时回放服务器。
            wait_key on;  #对视频切片进行保护，这样就不会产生马赛克了。
            hls_path /tmp/hls;  #切片视频文件存放位置。
            hls_fragment 10s;     #每个视频切片的时长。
            hls_playlist_length 60s;  #总共可以回看的事件，这里设置的是1分钟。
            hls_continuous on; #连续模式。
            hls_cleanup on;    #对多余的切片进行删除。
            hls_nested on;     #嵌套模式。

	    record all;    #record flv
            record_path /tmp/hls;
            record_suffix -%d-%b-%y-%T.flv;
        }
    }
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

       
        location /stat {     #first location field
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }

        location /stat.xsl { #second location field
            root /usr/local/src/nginx-rtmp-module/;
        }
        
        location /live {
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }
            alias /tmp/hls;    # 视频存放路径
            expires -1;
            add_header Cache-Control no-cache;
        }

        location / {
            root   html;
            index  index.html index.htm;
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }        
    }
}
```

  [1]: http://121.36.64.197/stat
