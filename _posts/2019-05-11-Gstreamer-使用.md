---
layout: post
comments: true
title: "TX2上使用Gstreamer"
excerpt: "This is a note for myself, documenting the nifty details about how to use Gstreamer tools."
date: 2019-05-11
category: "AI"
tags: Gstreamer
---
# 概念
Gstreamer是一个基于pipeline的多媒体框架，基于GObject，以C语言写成。应用GStreamer这个这个多媒体框架，你可以写出任意一种流媒体的应用来如：meidaplayer、音视频编辑器、VOIP、流媒体服务器、音视频编码等等。

gstreamer的整体功能核心是pipeline框架以及用于扩展功能的插件：

1. pipeline用于安排数据流图，明确数据流处理过程。通过gstreamer多媒体数据协商机制和同步机制，pipeline能够很好的地处理流式数据。

2. 插件用于扩展功能，开发者可以灵活利用已有插件，而且还可以自定义特定功能插件。

# 使用

先简要介绍一下三个重要的gstreamer插件--v4l2src，ximagesink，xvimagesink

1. v4l2src是使用v4l2接口的视频源插件，v4l2本身不仅仅是支持视频采集功能，它还支持其他的视频功能，但是v4l2src插件，只是用来做视频采集的。关于视频采集，有一点必须要明确，视频采集是有多种格式的，从大方向上区分的话，至少要分为rgb格式和yuv格式。

2. ximagesink是用来显示视频图像的sink插件，它是基于X11库的，(简单点说ximagesink会调用XPutImage函数)，XPutImage不支持yuv格式的数据，常用的就是rgb格式。

3. xvimagesink也是用来显示视频图像的sink插件，但是它是基于X Video Extension库的，(简单点说xvimagesink会调用XvPutImage函数)，而XvPutImage则可能支持yuv格式的数据，这个显卡有关。

## 打开摄像头
```
$ gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw,format=I420,framerate=30/1,width=1280,height=720 ! videoconvert ! xvimagesink
```
