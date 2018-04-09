---
title: 父元素定宽高，如何使用css自动缩放图片
date: 2018-04-09 18:57:26
tags: zhang dajia
  -css
---
> 图片等比例缩放在前端布局中是很常见的，通常拿到的原型设计稿尺寸要求某一个图片位置的容器的高宽我们又不能顺便调整，后端传过来的图片又不是一个固定尺寸，各种横拍竖拍的图片都有；往往这个时候PM就要求‘你让所有大小的图片都在这快区域显示呗’......

不拐弯抹角了，相信大家遇到这事都是先扔width:100%;height:100%。结果是：![image](https://note.youdao.com/yws/public/resource/666cb58986a774d87ba90c0b9ae38993/xmlnote/D3293F9E95D64BEB95B273A22FFB54DA/10181)
图片变形了...
<!-- more -->
### **总结了以下几种方案：**
- 父元素overflow:hidden,图片设置宽度100%
```
div {
    width: 7.5rem;
    height: 5.625rem;
    overflow: hidden;
}
img{
    widht:100%;
}
```
效果：![image](https://note.youdao.com/yws/public/resource/666cb58986a774d87ba90c0b9ae38993/xmlnote/7EA5A02B4F1A428A9D56D8253D05AB7D/10246)
- 高宽auto，设置max-width,max-height
```
    width: auto;
    height: auto;
    max-width: 100%;
    max-height: 100%;
    
```
效果：![image](https://note.youdao.com/yws/public/resource/666cb58986a774d87ba90c0b9ae38993/xmlnote/F9B1485329154B119158142D6DE7A5E1/10199)

- 设置object-fit:cover
```
    width: 7.5rem;
    height: 5.625rem;
    object-fit: cover;
```
效果：![image](https://note.youdao.com/yws/public/resource/666cb58986a774d87ba90c0b9ae38993/xmlnote/04DA601F2BEF492289274B862E94C8B6/10207)
[object-fit资料链接](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit/)

- 不使用img标签，把图片作为背景，通过padding百分比实现
```
.bunner{
      width:265px;
      height:200px;
      background-color:#3590ea;
      overflow: hidden;
    }
.imgCover{
      width:100%;
      height:0;
      padding-bottom: 100%;
      overflow:hidden;
      background-position: center center;
      background-repeat: no-repeat;
      -webkit-background-size:cover;
      -moz-background-size:cover;
      background-size:cover;
    }
    
<div class='bunner'>
    <div class='imgCover' style='background-image:url(http://pic1.58cdn.com.cn/mobile/big/n_v252a722b32a5a4249adcbef14632ebe73.jpg)'>
 </div>
```
> 关键点：width:100%;height:0;padding-bottom: 100%;overflow:hidden;padding百分比是根据父元素高宽进行计算的。

>相关资料
- [如何让图片按比例响应式缩放、并自动裁剪的css技巧](https://blog.csdn.net/oulihong123/article/details/54601030)
- [CSS百分比padding实现比例固定图片自适应布局](http://www.zhangxinxu.com/wordpress/2017/08/css-percent-padding-image-layout/)

### 链接 [demo](http://zhangdajia.com/fe-demo/imgautoscalbycss.html)