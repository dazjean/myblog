---
title: 移动端滑动时候控制台警告Unable-to-preventDefault-inside-passive-event-listener
date: 2018-10-30 12:50:29
tags:
   - H5
   - js
---
> 页面有一个浮层滑动 打算禁止掉body的滑动以提升滑动效果

### 实现代码
```
document.body.addEventListener('touchmove',this.stopScroll);

this.stopScroll(e){
    e.preventDefault();阻止默认事件类型
}
```

### 页面滚动时控制台打印：
    [Intervention] Unable to preventDefault inside passive event listener due to target being treated as passive
<!-- more -->

### 解决方法
> 在touch的事件监听方法上绑定第三个参数{ passive: false }，
通过传递 passive 为 false 来明确告诉浏览器：事件处理程序调用 preventDefault 来阻止默认滑动行为。


方法1
```
document.body.addEventListener('touchmove',this.stopScroll,{ passive: false });
```

方法2
```
 * { touch-action: pan-y; } 
 使用全局样式样式去掉
```

参考链接：
[滑动时候警告：Unable to preventDefault inside passive event listener](https://www.jianshu.com/p/04bf173826aa)