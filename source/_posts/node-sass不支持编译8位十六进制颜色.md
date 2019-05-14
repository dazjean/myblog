---
title: node-sass不支持编译8位十六进制颜色
date: 2019-05-14 19:45:06
tags:
 - 随笔
 - node-sass
---

# 背景
> 在实现一个元素背景颜色透明时，sass文件中使用了8位十六进制颜色`#0000008c`,在本地开发环境是可以正常渲染颜色的，但编译之后颜色却失效。查看元素发现颜色被编译成了`background-color:0 0;`;

查询资料发现在node-sass [issues1](https://github.com/sass/node-sass/issues/2126) [issues2](https://github.com/webpack-contrib/sass-loader/issues/503)中有一个相同的问题，得到的答复是node-sass目前不支持8位十六进制颜色。

最后使用了RGBA格式定义了颜色属性解决 `rgba(0, 0, 0, 0.5)`


