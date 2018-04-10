---
title: 安卓端手机设置字体大小导致h5页面在webview中rem计算值不正确
date: 2017-11-02 20:05:38
tags:
	- 随笔
---
> h5页面采用rem单位，html根节点默认字体通过js计算，在安卓上，如果用户手动设置改变了系统默认字体，这个时候会造成页面rem计算出来的值不是我们想要的效果。

### 解决办法
- js再次询问确认
> 在设置完字体大小之后，再去重新获取一下html的font-size，看看实际的这个值，和我们设置的是不是一样。如果不一样，就要根据比例再设置一次。
<!-- more -->
```
function htmlFontSize(){
    var h = Math.max(document.documentElement.clientHeight, window.innerHeight || 0);
    var w = Math.max(document.documentElement.clientWidth, window.innerWidth || 0);
    var width = w > h ? h : w;
    width = width > 720 ? 720 : width
    var fz = ~~(width*100000/36)/10000
    document.getElementsByTagName("html")[0].style.cssText = 'font-size: ' + fz +"px";
    var realfz = ~~(+window.getComputedStyle(document.getElementsByTagName("html")[0]).fontSize.replace('px','')*10000)/10000
    if (fz !== realfz) {
        document.getElementsByTagName("html")[0].style.cssText = 'font-size: ' + fz * (fz / realfz) +"px";
    }
}
```

不过这种方案也不严谨，有可能我们再次执行修正后字体才被手机重置了呢？目前也不清楚重置的时机是啥时候？

再者的话异步处理，页面完成后设置一个定时,多少时间后重置一次。这个定时多少时间就看你页面渲染的耗时了，进来别让用户看见页面抖动。


- 安卓客户端设置webview
> 安卓客户端通过webview配置webview.getSettings().setTextZoom(100)就可以禁止缩放，按照百分百显示。

[参考原文](http://blog.csdn.net/fungleo/article/details/73309396)