---
title: CentOS 安装Wine
date: 2020-05-03 18:49:48
categories: 
- Wine
tags:
- Wine
- CentOS
---

介绍nodejs在linux系统下编译成exe所需的依赖安装及环境搭建
<!-- more -->

## 下载源码包
文件下载镜像
中国科技大学 [http://mirrors.ustc.edu.cn/][1]

下载如下文件：
- wine-5.0.tar.xz
- wine_gecko-2.47-x86.msi
- wine_gecko-2.47-x86_64.msi
- wine-mono-5.0.0-x86.msi

## 安装依赖
- `yum groupinstall 'Development Tools' -y`
- `yum install libX11-devel freetype-devel zlib-devel libxcb-devel -y`
- `yum install alsa-lib-devel.i686 libsndfile-devel.i686 readline-devel.i686 glib2.i686 glibc-devel.i686 libgcc.i686 libstdc++-devel.i686 pulseaudio-libs-devel.i686 cmake audiofile-devel.i686 freeglut-devel.i686 lcms-devel.i686 libieee1284-devel.i686 openldap-devel.i686 unixODBC-devel.i686 sane-backends-devel.i686 fontforge libgphoto2-devel.i686 isdn4k-utils-devel.i686 mesa-libGL-devel.i686 mesa-libGLU-devel.i686 libXxf86dga-devel.i686 libXxf86vm-devel.i686 giflib-devel.i686 cups-devel.i686 gsm-devel.i686 libv4l-devel.i686 fontpackages-devel ImageMagick-devel.i686 libX11-devel.i686 docbook-utils-pdf libtextcat tex-cm-lgc`
- `yum install alsa-lib-devel audiofile-devel.i686 audiofile-devel cups-devel.i686 cups-devel dbus-devel.i686 dbus-devel fontconfig-devel.i686 fontconfig-devel freetype.i686 freetype-devel.i686 freetype-devel giflib-devel.i686 giflib-devel lcms-devel.i686 lcms-devel libICE-devel.i686 libICE-devel libjpeg-turbo-devel.i686 libjpeg-turbo-devel libpng-devel.i686 libpng-devel libSM-devel.i686 libSM-devel libusb-devel.i686 libusb-devel libX11-devel.i686 libX11-devel libXau-devel.i686 libXau-devel libXcomposite-devel.i686 libXcomposite-devel libXcursor-devel.i686 libXcursor-devel libXext-devel.i686 libXext-devel libXi-devel.i686 libXi-devel libXinerama-devel.i686 libXinerama-devel libxml2-devel.i686 libxml2-devel libXrandr-devel.i686 libXrandr-devel libXrender-devel.i686 libXrender-devel libxslt-devel.i686 libxslt-devel libXt-devel.i686 libXt-devel libXv-devel.i686 libXv-devel libXxf86vm-devel.i686 libXxf86vm-devel mesa-libGL-devel.i686 mesa-libGL-devel mesa-libGLU-devel.i686 mesa-libGLU-devel ncurses-devel.i686 ncurses-devel openldap-devel.i686 openldap-devel openssl-devel.i686 openssl-devel zlib-devel.i686 pkgconfig sane-backends-devel.i686 sane-backends-devel xorg-x11-proto-devel glibc-devel.i686 prelink fontforge flex bison libstdc++-devel.i686 pulseaudio-libs-devel.i686 gnutls-devel.i686 libgphoto2-devel.i686 isdn4k-utils-devel.i686 gsm-devel.i686 samba-winbind libv4l-devel.i686 cups-devel.i686 libtiff-devel.i686 gstreamer-devel.i686 gstreamer-plugins-base-devel.i686 gettext-devel.i686`

## 安装wine
- 进入wine-5.0.tar.xz所在目录 
- 解压wine-5.0.tar.xz `tar jxvf wine-5.0.tar.xz`
- 进入解压目录 `cd wine-5.0/`
- 创建安装目录X64 `mkdir /root/wine-5.0/wine64`
- 进入安装目录X64 `cd wine64/`
- 安装
``` sql
../configure --enable-win64
make
```
- 创建安装目录X86 `mkdir /root/wine-5.0/wine32`
- 进入安装目录X86 `cd wine32/`
- 安装
``` sql
../configure --enable-win32
make
make install
```

- 安装X64Wine 
``` vim
cd ../wine64
make install
```

## 安装mono
- mkdir -p /usr/local/bin/share/wine/mono
- mv wine-mono-5.0.0-x86.msi /usr/local/bin/share/wine/mono/

## 安装gecko

- mkdir -p /usr/local/bin/share/wine/gecko
- mv wine_gecko-2.47-x86_64.msi /usr/local/bin/share/wine/gecko/
- mv wine_gecko-2.47-x86.msi /usr/local/bin/share/wine/gecko/
- 配置
``` stylus
WINEARCH=win32 WINEPREFIX=~/.wine32 winecfg
WINEARCH=win64 WINEPREFIX=~/.wine64 winecfg
```


  [1]: http://mirrors.ustc.edu.cn/
