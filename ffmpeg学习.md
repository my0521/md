---
title: ffmpeg学习
date: 2019-05-06 20:00:48
categories: 
 - ffmpeg
tags:
 - ffmpeg
---

记录ffmpeg一些学习概况
<!-- more -->

## 相关网站
- 官网 [http://www.ffmpeg.org/][1]
- github [https://github.com/FFmpeg/FFmpeg][2]
- Build网站 [http://ffmpeg.zeranoe.com/builds/][3]
- 互动百科 [http://www.baike.com/wiki/ffmpeg][4]
- 分支 [http://libav.org][5]

## 从ffmpeg开始

ffmpeg的编译版本可以从Build网站[http://ffmpeg.zeranoe.com/builds/][3]下载，可供下载的有shared，dev，static3个版本，各个版本的差异可以参考相关网站。

## ffmpeg项目组成
- 项目结构
1. libavformat
用于各种音视频封装格式的生成和解析，包括获取解码所需信息以生成解码上下文结构和读取音视频帧等功能，包含demuxers和muxer库；
2. libavcodec
用于各种类型声音/图像编解码；
3. libavutil
包含一些公共的工具函数；
4. libswscale
用于视频场景比例缩放、色彩映射转换；
5. libpostproc
用于后期效果处理；
6. ffmpeg
是一个命令行工具，用来对视频文件转换格式，也支持对电视卡实时编码；
7. ffsever
是一个HTTP多媒体实时广播流服务器，支持时光平移；
8. ffplay
是一个简单的播放器，使用ffmpeg 库解析和解码，通过SDL显示；
- 生成库文件
1. libavcodec
包含音视频编码器和解码器；
2. libavutil
包含多媒体应用常用的简化编程的工具，如随机数生成器、数据结构、数学函数等。
3. libavformat 
包含多种多媒体容器格式的封装、解封装工具；
4. libavfilter
包含多媒体处理常用的滤镜功能；
5. libavdevice
用于音视频数据采集和渲染等功能的设备相关；
6. libswscale
用于图像缩放、色彩空间、像素格式转换等功能；
8. libswresample
用于音频重采样和格式转换等功能。

## ffmpeg工具
ffmpeg工具的使用是基于命令行的
1. ffmpeg.exe 音视频转码器
2. ffplay.exe 简单的音视频播放器
3. ffserver.exe 流媒体服务器
4. ffprobe.exe 简单的多媒体码流分析器

## ffmpeg参数
1. 通用参数
``` haml
-f fmt: 指定格式(音频或者视频格式)。
-i filename: 指定输入文件名。
-y: 覆盖已有文件。
-t duration: 指定时长。
-fs limit_size: 设置文件大小的上限。
-ss limit_size: 从指定的时间（单位为秒）开始，也支持[-]hh:mm:ss[.xxx]格式
-re: 代表按照帧率发送。在作为推流工具的时候一定要加入该参数，否则ffmpeg会按照最高速率向流媒体服务器不停地发送数据。
-map: 指定输出文件的流映射关系。如果没有-map选项，则ffmpeg采用默认的映射关系。
```
2. 视频参数
``` haml
-b: 指定比特率(bit/s)，ffmpeg是自动使用VBR的，若指定了该参数则使用平均比特率。
-bitexact: 使用标准比特率。
-vb: 指定视频比特率(bit/s).
-r rate: 帧速率（fps）。
-s size: 指定分辨率（320 * 320）
-aspect aspect: 设置视频长宽比（4:3，16:9或者1.3333，1.7777）。
-croptop size: 设置顶部切除尺寸（in pixels）。
-cropbottom size: 设置顶底切除尺寸（in pixels）。
-cropleft size: 设置左切除尺寸（in pixels）。
-cropright size: 设置右切除尺寸（in pixels）。
-padtop size: 设置顶部补齐尺寸（in pixels）。
-padbottom size: 设置底部补齐尺寸（in pixels）。
-padleft size:设置左补齐尺寸（in pixels）。
-padright size:设置右补齐尺寸（in pixels）。
-padcolor color: 补齐带颜色（000000-FFFFFF）。
-vn: 取消视频的输出。
-vcode codec: 强制使用codec编解码方式('copy'代表不进行重新编码)。
```
3. 音频参数
``` haml
-ab: 设置比特率(单位为bit/s)。
-aq quality: 设置音频质量（指定编码）。
-ar rate: 设置音频采样率（单位为Hz）。
-ac channels: 设置声道数。1就是单声道，2就是立体声。
-an: 取消音频轨。
-acodec codec: 指定音频编码('copy'代表不做音频转码)。
-vol volume: 设置录制音量大小（默认256）<百分比>。
```


  [1]: http://www.ffmpeg.org/
  [2]: https://github.com/FFmpeg/FFmpeg
  [3]: http://ffmpeg.zeranoe.com/builds/
  [4]: http://www.baike.com/wiki/ffmpeg
  [5]: http://libav.org
