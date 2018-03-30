---
title: ':active伪类在移动端ios中无效问题解决办法'
date: 2018-03-30 19:30:09
tags:
    - css
---
> 在移动端网页中，实现一个按钮按压点击的时候改变背景颜色。

## 实现方案
- 监听touch事件，在start 和 end时机分别添加和remove特定样式

- 使用css伪类：active ,当元素被触发点击的时候触发此样式，代码如下：
```
<style>
a:active{
    background-color:red;
}
</style>
<body>
<a>tap me</a>
</body>
```
> 当我使用如下方案的时候，在pc，移动安卓环境下测试的时候有表现出设置的active样式，但在ios safari中却发现该功能不能作用。接下来就是Google Baidu了，在stackoverflow上找到一篇文章[active-pseudo-class-doesnt-work-in-mobile-safari](https://stackoverflow.com/questions/3885018/active-pseudo-class-doesnt-work-in-mobile-safari/33681490#33681490) 找到了答案
<!-- more -->
## :active伪类在移动端ios中无效问题解决办法
> 在body或者目标元素a上设置触摸事件[demo](http://output.jsbin.com/EjiWILe/3/)
```
<body ontouchstart=''>
</body>
or
<a ontouchstart=''></a>
```

>或者使用js设置触摸事件
```
<script type='application/javascript'>
  document.addEventListener("touchstart", function() {}, false);
</script>
```

>还可以配合-webkit-tap-highlight-color进行使用
```
<button class="action" ontouchstart=""
        style="-webkit-tap-highlight-color: rgba(0,0,0,0);">
    Testing Touch on iOS
</button>
```

## 造成失效的原因
>在iOS上，鼠标事件的发送速度非常快，从而不会收到向下或活动状态。因此，:active只有在HTML元素上设置了触摸事件时，才会触发伪状态 .参考链接[developer.apple](https://developer.apple.com/library/content/documentation/AppleApplications/Reference/SafariWebContent/AdjustingtheTextSize/AdjustingtheTextSize.html)