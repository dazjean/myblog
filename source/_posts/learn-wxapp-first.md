---
title: 微信小程序学习笔记
date: 2017-10-16 12:54:41
tags:
	-小程序
---
# 目录结构
 > 一个小程序的目录结构，包含app.js,app.json,app.wxss和pages,utils等文件
其中：
app.js为脚本文件，在这个文件中处理小程序的生命周期函数，申明全局变量，调用框架API和定义通用函数等。
<!-- more -->
```
//app.js
App({
  onLaunch: function () {
    //调用API从本地缓存中获取数据var logs = wx.getStorageSync('logs') || []
    logs.unshift(Date.now())
    wx.setStorageSync('logs', logs)
  },
  getUserInfo:function(cb){
    var that = this;
    if(this.globalData.userInfo){
      typeof cb == "function" && cb(this.globalData.userInfo)
    }else{
      //调用登录接口
      wx.login({
        success: function () {
          wx.getUserInfo({
            success: function (res) {
              that.globalData.userInfo = res.userInfo;
              typeof cb == "function" && cb(that.globalData.userInfo)
            }
          })
        }
      });
    }
  },
  globalData:{
    userInfo:null
  }
})
```
app.json为小程序配置文件，可以配置小程序的页面组成，窗口背景色，导航条样式，默认标题等。注：不能添加任何注释。
```
{
  "pages":[
    "pages/index/index",
    "pages/logs/logs"
  ],
  "window":{
    "backgroundTextStyle":"light",
    "navigationBarBackgroundColor": "#fff",
    "navigationBarTitleText": "WeChat",
    "navigationBarTextStyle":"black"
  }
}
```
app.wxss是整个小程序的公共样式表，我们可以像在css中定义class属性的样式一样使用app.wxss中声明的样式规则。

# 页面结构
> 每一个小程序页面是由同路径下同名的四个不同后缀文件的组成，如：index.js、index.wxml、index.wxss、index.json。.js后缀的文件是脚本文件，.json后缀的文件是配置文件，.wxss后缀的是样式表文件，.wxml后缀的文件是页面结构文件。
*指定小程序的首页，默认app.json中的pages中的第一项为小程序的首页。

总的来说，小程序由三个部分组成:配置，逻辑，和视图
# 配置
> *.json文件为配置文件，主要配置当前小程序的基本信息，比如tab，标题，背景，导航样式等信息。
其中，page.json为全局配置，在page中也有相应的.json,对于同属性配置页面级覆盖page.json
逻辑：
*.js文件为逻辑层，逻辑层将数据进行处理后发送给视图层，同时接受视图层的事件反馈。 在 JavaScript 的基
础上，我们做了一些修改，以方便地开发小程序。
    - 增加 App 和 Page 方法，进行程序和页面的注册。
    - 增加 getApp 和 getCurrentPages 方法，分别用来获取 App 实例和当前页面栈。
    - 提供丰富的 API，如微信用户数据，扫一扫，支付等微信特有能力。
    - 每个页面有独立的作用域，并提供模块化能力。
    - 由于框架并非运行在浏览器中，所以 JavaScript 在 web 中一些能力都无法使用，如 document，window 等。
    - 开发者写的所有代码最终将会打包成一份 JavaScript，并在小程序启动的时候运行，直到小程序销毁。类似 ServiceWorker，所以逻辑层也称之为 App Service。
# 视图
> *.WXML 与 *.WXSS
框架的视图层由 WXML 与 WXSS 编写，由组件来进行展示。
将逻辑层的数据反应成视图，同时将视图层的事件发送给逻辑层。
WXML(WeiXin Markup language)用于描述页面的结构。
WXSS(WeiXin Style Sheet)用于描述页面的样式。
组件(Component)是视图的基本组成单元。

# demo
> [项目地址](https://github.com/dazjean/wx_webapp_demo)
         
