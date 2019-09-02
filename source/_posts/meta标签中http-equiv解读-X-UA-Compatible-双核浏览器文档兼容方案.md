---
title: meta标签中http-equiv解读&X-UA-Compatible&双核浏览器文档兼容方案
date: 2019-09-02 15:56:28
tags:
    - html
    - meta
---
#### 前言
> 在统计一个PC端站点的浏览器分布情况时，发现大量IE7用户且指标排名第二，吓死个人。而从百度研究院数据来看，IE7用户占比非常低。故此查询了相关资料，360浏览器默认打开为兼容模式，随便找台电脑查看了一下内核信息如下
`"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E; rv:11.0) like Gecko" 
`  官方提供了一个类似X-UA-Compatible的属性renderer。故此延伸出此文。

#### http-equiv
> http-equiv是一个枚举属性，它定义了能改变服务器和用户浏览器引擎行为的编译方式，值使用content来定义。

- ~~content-language~~ 已删除
> 定义页面使用的默认语言，通过`html`上全局`lang`代替

- content-security-policy 内容安全策略
> 定义当前页指定允许的服务器源和脚本端，用于防止跨站点脚本攻击。[示例](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP#Examples_Common_use_cases)


```js
// 禁用不安全的内联/ eval，仅允许通过https加载资源（图像，字体，脚本等)
<meta http-equiv="Content-Security-Policy" content="default-src https:">

```

- content-type 定义文档字符编码
> 它遵循HTTP `contnet-type`头部字段相同语法，但由于位于HTML内，因此除了text/html之外的大多数值都不能使用。使用 http-equiv 已经不是规定HTML文档的字符集的唯一方式：HTML5直接使用charset代替


```html
// html4.01
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">

// html5
<meta charset="UTF-8">
```

- default-style 页面首选样式表
> 这个属性指定了在页面上使用的首选样式表. content属性必须包含`<link> `元素的标题, href属性链接到CSS样式表或包含CSS样式表的`<style>`元素的标题.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!--  Meta  -->
  <meta charset="UTF-8" />
  <title>default-style</title>
  <mate http-equiv="default-style" content="myself-style-red">
  <!--  Styles  -->
   <style title="myself-style-red">
    p {
      color:red;
    }
  </style>
   <style title="myself-style">
    p {
      color:blue;
    }
  </style>
  <link rel="stylesheet" title="myself-stylelink" href="./../styles/index.processed.css">
</head>
<body>
  <p>defult-style-red</p>  //will be render red color
</body>
</html>
```

- refresh 重新载入页面时间间隔
> - 如果content 只包含一个正整数,则是重新载入页面的时间间隔(秒);
> - 如果content 包含一个正整数并且跟着一个字符串,则是重定向到指定链接的时间间隔(秒)

- ~~set-cookie~~ 页面定义cookie 
> 为页面定义cookie。其内容必须遵循 IETF HTTP Cookie 规范中定义的语法。`Note: 警告：请勿使用此说明，因为它已过时。请改用HTTP的Set-Cookie头部。`



--------- 

- [X-UA-Compatible](https://docs.microsoft.com/en-us/previous-versions/windows/internet-explorer/ie-developer/compatibility/jj676915%28v%3dvs.85%29) 指定浏览器加载文档模式
> `X-UA-Compatible`是从IE8开始，IE支持传统文档模式，用于为IE8指定不同的页面渲染模式。content属性可以接受多个值,逗号分隔

#### 解决问题场景（X-UA-Compatible）：
* 对应于特定文档模式的值，例如IE9模式，IE8标准模式或IE7标准模式。
```html
<meta http-equiv="x-ua-compatible" content="IE=9" >
<meta http-equiv="x-ua-compatible" content="IE=8" >
<meta http-equiv="x-ua-compatible" content="IE=7" >
<!--通过使用其中一个值，您将网页限制为相应版本的Internet Explorer的标准模式。在许多情况下，这意味着您将Internet Explorer限制为该版本支持的功能-->
```

* ~~希望Internet Explorer使用文档类型声明将网页限制为特定标准模式，或使用表示较旧版本浏览器的文档模式，例如Internet Explorer 5.5~~  太老了 几乎不考虑 了解一下
```html
<meta http-equiv="x-ua-compatible" content="IE=EmulateIE9" >
<meta http-equiv="x-ua-compatible" content="IE=EmulateIE8" >
<meta http-equiv="x-ua-compatible" content="IE=EmulateIE7" >
```
* 以EdgeHTML模式显示网页，这是Internet Explorer支持的最高标准模式，从Internet Explorer 6到IE11
```html
<meta http-equiv="x-ua-compatible" content="IE=edge" >
<meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1" > 
 <!--如果IE有安装Google Chrome Frame，那么就走安装的组件，如果没有就和<meta http-equiv="X-UA-Compatible" content="edge" />一样-->
```

#### 国产浏览器双核引擎下文档兼容方案
```html
<meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1" >  <!--IE浏览器将总是使用最新版本的文档模式-->

<meta name="renderer" content="webkit"> 
<!--极速内核 360浏览器、QQ浏览器等国产双核浏览器均实现-->

<meta name="force-rendering" content="webkit">
<!--传说其他国产浏览器，笔者全网搜索没有找到相关资料出处-->



```
相关链接：
- [360浏览器内核控制标签说明](浏览器内核控制标签meta说明
)
- [QQ浏览器网页兼容性-内核切换](https://browser.qq.com/faq/#/detail/36)
- [X-UA-Compatibility Meta Tag ](https://docs.microsoft.com/en-us/openspecs/ie_standards/ms-iedoco/380e2488-f5eb-4457-a07a-0cb1b6e4b4b5)


