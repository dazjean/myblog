---
layout: react-router
title: React-router V4 中BrowserRouter和HashRouter的区别
date: 2018-11-30 19:47:01
tags:
   - React-Router
   - React
---


# HashRouter
> 它使用URL的哈希部分（即`window.location.hash`）来保持页面的UI与URL同步。

**重要说明：**哈希历史记录不支持`location.key`或`location.state`。在以前的版本中，我们试图填充行为，但是有一些我们无法解决的边缘情况。任何需要此行为的代码或插件都不起作用。由于此技术仅用于支持旧版浏览器，因此我们建议您将服务器配置为使用“<BrowserHistory>”。
<!-- more -->
```JSX
import { HashRouter } from 'react-router-dom'
<HashRouter>
  <App/>
</ HashRouter>
```

## basename：string

所有位置的基本URL。格式正确的基本名称应该有一个前导斜杠，但没有尾部斜杠。

```JSX
<HashRouter basename =“/ calendar”/>
<Link to =“/ today”/> //<a href="#/calendar/today">
```

## getUserConfirmation：func

用于确认导航的功能。默认使用[`window.confirm`]（https://developer.mozilla.org/en-US/docs/Web/API/Window/confirm）。

```JSX
//这是默认行为
const getConfirmation =（message，callback）=> {
  const allowTransition = window.confirm（message）
  callback（allowTransition）
}

<HashRouter getUserConfirmation = {getConfirmation} />
```

## hashType：string

用于`window.location.hash`的编码类型。可用值包括：

- "slash" - Creates hashes like #/ and #/sunshine/lollipops
- "noslash" - Creates hashes like # and #sunshine/lollipops
- "hashbang" - Creates [ajax crawlable](https://developers.google.com/webmasters/ajax-crawling/docs/learn-more) (deprecated by Google) hashes like #!/ and #!/sunshine/lollipops

默认为“斜线”`。

# BrowserRouter
> 使用HTML5历史API（ pushState，replaceState和popstate事件），让页面的UI同步与URL

```
import  { BrowserRouter }  form 'react-router-dom';
< BrowserRouter 
  basename = { optionalString } //所有位置的基本UR 
  forceRefresh = { optionalBool } //如果true路由器将在页面导航上使用整页刷新 通常用于不支持html5 history api的浏览器上
  getUserConfirmation = { optionalFunc }// 用户确认导航的功能回调函数
  keyLength = { optionalNumber } //location.key的长度 默认6
>
  < App />
</ BrowserRouter >
```

以上是直接在官方api中可以直接查看到的说明，直接拿了过来，也可以去查看一些更好的博客介绍，比如[初探 React Router 4.0](https://www.jianshu.com/p/e3adc9b5f75c/);那么接下来我们来总结一下在使用过程中这两者到底有什么不同。
# 区别
## url上表现不一致
 - BrowserRouter使用HTML5 history API，保证UI界面和URL保存同步
```
1、比如入口文件是：http://localhost:9991/page/with-react-browserRouter/with-react-browserRouter.html
2、访问about路由url地址是：http://localhost:9991/hmbird_router/with-react-browserRouter/about  
3、basename设置为：basename="/hmbird_router/with-react-browserRouter"
```
  - HashRouter使用URL（即window.location.hash）的哈希部分来保持UI与URL同步的。哈希历史记录不支持location.key和location.state  [详情查看history](https://github.com/ReactTraining/history) 用来支持旧版浏览器，官方不建议使用
  ```
1、比如入口文件是：http://localhost:9991/page/with-react-hashRouter/with-react-hashRouter.html
2、访问about路由url地址是：http://localhost:9991/page/with-react-hashRouter/with-react-hashRouter.html#/hmbird_router/with-react-hashRouter/about  
3、basename设置为：basename="hmbird_router/with-react-hashRouter"
```

## hashRouter不支持location.key 、location.state
> 从其他资料来看，大多只有这一句说明，那如果我们的项目使用了hashRouter之后不支持location.key location.state对我们的应用有什么影响呢？
  
首先我们来看下在使用BrowserRouter或者HashRouter时是如何传递参数的。

###  在跳转路由的链接中通过‘？’传递参数
```jsx
<Switch>
    <Route exact path="/" component={Home} />
    <Route exact path="/about" component={About} />
    <Route component={Home} />
</Switch>


//link方式跳转
<Link to="/about?msg='我是url参数'">去关于我的页面 url传递参数</Link>
//js方式跳转
 this.props.history.push({ pathname:"/about?msg='我是url参数'"});
//about中获取参数
console.log(this.props.location)
//{pathname: "/about", search: "?msg='我是url参数'", hash: "", state: undefined}


```
优缺点：参数比较灵活，参数直接在url中暴露，刷新路由页面时传递参数依然可以正常访问。缺点是还需要js通过search中解析类似getParameter(msg)方式获取真实值
### 路由表中通过:id方式
```jsx
<Route exact path="/about/:msg" component={About} />
//link方式
<Link to="/about/我是url参数">去关于我的页面 路由配置传递参数</Link>
//js方式跳转
 this.props.history.push({ pathname:"/about/'我是url参数'"});
//about中获取参数
console.log(this.props.match.params.msg)
//我是url参数

```
优缺点：参数比较灵活，参数直接在url中暴露,刷新路由页面时传递参数依然可以正常访问。但每增加一个参数需要在Route中注册一个，而且顺序需要一致。

### 其他query和state
> 这两种传值方式都是在js通过逻辑判断去触发的。
```js
// query 传递参数
this.props.history.push({
    pathname: '/about',
    query: {
        msg: '来自首页的问候！by query'
    }
});
// state 传递参数
this.props.history.push({
    pathname: '/about',
    state: {
        msg: '来自首页的问候！by state'
    }
});

// query 接受参数
console.log(this.props.location.query.msg)//来自首页的问候！by query
// state 接受参数
console.log(this.props.location.state.msg)//来自首页的问候！by state
```
优缺点：参数灵活，不用给Route额外的配置，参数是加密的，不暴露在url上。

>以上测试结果方式对HashRouter和BowserRouter方式都有效
------------

### 总结 
>神马！不是说找HashRouter和BowserRouter的区别吗？接下来再看.....通过state传参到about路由页面之后，我们刷新页面~~~你可能已经猜到结果了！

刷新页面或者直接浏览器回退一步，然后再前进时，BowserRouter的页面依然可以获取到state中的值，也就是进入路由后即使我们刷新页面上一个页面传递过来的参数依然有效！ 看以下打印结果~

** 测试步骤是先通过点击出发js跳转，然后回退上一页然后再点击下一页回到路由about页面

```jsx
class About extends Component {
    constructor(props) {
        super(props);
    }
    render() {
        console.log(this.props.location);
        return (
            <div className="demo">
                我是一个路由跳转后的子页面
                <br />
                <div>
                    参数：{JSON.stringify(this.props.location)}
                </div>
                <Link to="/">回首页</Link>
            </div>
        );
    }
}
```
通过js执行跳转
```js
this.props.history.push({
    pathname: '/about',
    state: {
        msg: '来自首页的问候！by state'
    }
});
```
        
`HashRouter`
```
//第一次进入页面打印结果
{"pathname":"/about","state":{"msg":"来自首页的问候！by state"},"search":"","hash":""}
//刷新页面或者后退再前进
{"pathname":"/about","search":"","hash":""}
```

`BowserRouter`
```
//第一次进入页面打印结果
{"pathname":"/about","state":{"msg":"来自首页的问候！by state"},"search":"","hash":"","key":"1m6gz4"}
//刷新页面或者后退再前进
{"pathname":"/about","state":{"msg":"来自首页的问候！by state"},"search":"","hash":"","key":"1m6gz4"}
```
到这儿，应该知道为啥说location不支持key和state了吧，当我们通过state传递参数的时候，因为hashRouter没有使用html5中history的api，无法从历史记录中获取到key和state值，所以当刷新路由后state值会丢失导致页面显示异常。

## 总结，实现路由页面页面刷新数据不丢失的方案
- BorwserRouter有三种方式（url传值，路由参数传值，以及state）
- HashRouter有两种方式（url传值，路由参数传值）

- 本地缓存或者状态管理方案




-------------
** 在测试过程中还发现了 通过js传递参数时，除了query和state 还可以自定义属性；

eg:
```
 this.props.history.push({
     pathname: '/about',
     custom: {
         msg: '来自首页的问候！by custom'
     }
 });
 
 //取值
 this.props.location.custom.msg
````
当然，对于BorwserRouter这也是不会支持历史记录功能的。

### [附上代码地址](https://github.com/dazjean/hmbird-ssr/tree/master/src/page)

*安利两篇讲解ReactRouter的译文

- [
React Router 中文文档（一）](https://segmentfault.com/a/1190000014294604)
- [
React Router 中文文档（二）](https://segmentfault.com/a/1190000014342764)