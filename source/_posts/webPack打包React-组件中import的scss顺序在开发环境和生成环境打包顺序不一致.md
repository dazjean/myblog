---
title: 'webPack打包React,组件中import的scss顺序在开发环境和生成环境打包顺序不一致'
date: 2017-11-24 18:40:47
tags:
	- webpack
---
## 现象
最先是由于一个安卓兼容问题引发，在开发环境中没有问题，webpack打包后提测却发现出现了不一样的效果，通过代码对比，发现了css打包后的顺序不一致，我们知道知道css样式是有优先级的，其中顺序也会影响到样式的渲染流程。
<!-- more -->
代码如下：
```
//主入口文件
//引入组件
import './../../skin/base.scss';
import './cst_tool_jq.scss';
import React,{Component} from 'react';
import ReactDom from 'react-dom';

WBAPP.cst_tool_jq = WBAPP.cst_tool_jq||{}//注册页面全局命名空间
import { createStore } from 'redux';
import { Provider,connect } from 'react-redux';
import todoApp from './redux/reducers';
import Page from './page';
import EditCarPage from './components/editCar_info/editCar_info';
import { Router, Route,hashHistory} from 'react-router';
let store = createStore(todoApp);
const render = () => {
    ReactDom.render(
        <Provider store={store}>
            <Router history={hashHistory}>
                <Route path='/' component={Page}></Route>
                <Route path='editcar' component={EditCarPage}></Route>
            </Router> 
        </Provider>
    ,document.getElementById("app"));
};
render();
store.subscribe(render);

// EditCarPage
//引入样式文件
import './editCar_info.scss';//引入组件依赖样式
import {Component} from 'react';
import QueueAnim from 'rc-queue-anim';
import FormComponent from './../../../../components/form/js/form.js';
import { hashHistory } from 'react-router';
import { connect } from 'react-redux';
import {eidtTodo} from './../../redux/actions';

class EditCar_info extends Component{
    xxxxxxxxx
}

// form.js
import './form.scss';//引入组件依赖样式
```
在编写时，我所希望的css打包后的规则是：
> editCar_info.css>form.css；即入口文件应该在合成文件的最后（因为我在editCar_info中有覆写form.css）。

通过查阅资料所知：
> webpack中scss打包的顺序和引入的顺序是相关的,即谁先被import就先打包谁。顺序是form>editCar_info




## 回到我这个问题
> 我在editCar_info.scss中覆写了某些form.scss的样式,按照打包的顺序，如果不修改class选择器的优先级，我修改覆写的样式应该是不会生效的。

可是，当我使用npm run dev 也就是在开发环境时，编译运行的css文件却达到了一个效果，即editCar_info.scss被打包到了文件的最后面。

查看webapck打包日志发现如下记录
```
[591] ./src/category/cst_tool_jq/components/editCar_info/editCar_info.js 9.02 kB {0} [built]
  [592] ./~/.1.3.1@rc-queue-anim/lib/index.js 388 bytes {0} [built]
  [593] ./~/.1.3.1@rc-queue-anim/lib/QueueAnim.js 17.4 kB {0} [built]
  [594] ./~/.1.5.3@rc-tween-one/lib/index.js 1.27 kB {0} [built]
  [595] ./~/.1.5.3@rc-tween-one/lib/TweenOne.js 10.9 kB {0} [built]
  [596] ./~/.1.5.3@rc-tween-one/lib/util.js 7.57 kB {0} [built]
  [597] ./~/.2.0.2@deep-eql/index.js 15.4 kB {0} [built]
  [598] ./~/.3.0.0@type-detect/index.js 15.3 kB {0} [built]
  [599] ./~/.0.1.13@style-utils/main.js 13.2 kB {0} [built]
  [600] ./~/.1.5.3@rc-tween-one/lib/Tween.js 13.8 kB {0} [built]
  [601] ./~/.1.5.3@rc-tween-one/lib/easing.js 1.27 kB {0} [built]
  [602] ./~/.1.2.0@tween-functions/index.js 6.4 kB {0} [built]
  [603] ./~/.1.5.3@rc-tween-one/lib/plugins.js 320 bytes {0} [built]
  [604] ./~/.1.5.3@rc-tween-one/lib/plugin/StylePlugin.js 12.1 kB {0} [built]
  [605] ./~/.1.5.3@rc-tween-one/lib/ticker.js 2.88 kB {0} [built]
  [606] ./~/.3.3.2@raf/index.js 1.88 kB {0} [built]
  [607] ./~/.2.1.0@performance-now/lib/performance-now.js 1.06 kB {0} [built]
  [608] ./~/.1.5.3@rc-tween-one/lib/TweenOneGroup.js 8.07 kB {0} [built]
  [609] ./~/.1.3.1@rc-queue-anim/lib/utils.js 2.29 kB {0} [built]
  [610] ./~/.1.3.1@rc-queue-anim/lib/animTypes.js 648 bytes {0} [built]
  [611] ./src/components/form/js/form.js 3.97 kB {0} [built]
  [612] ./src/components/form/scss/form.scss 41 bytes {0} [built]
  [613] ./src/components/inputText/js/inputText.js 7.72 kB {0} [built]
  [614] ./src/components/inputText/scss/inputText.scss 41 bytes {0} [built]
  [615] ./src/components/radioSelect/js/radioSelect.js 6.07 kB {0} [built]
  [616] ./src/components/radioSelect/scss/radioSelect.scss 41 bytes {0} [built]
  [617] ./src/components/imgUpload/js/imgUpload.js 3.94 kB {0} [built]
  [618] ./src/components/imgUpload/scss/imgUpload.scss 41 bytes {0} [built]
  [619] ./src/category/cst_tool_jq/components/editCar_info/editCar_info.scss 41 bytes {0} [built]
```
能看见，editCar_info.css的日志出现在form.css的后面。起初以为是路由影响了。所以我把路由去掉后进行了测试，发现顺序也是一样。

但入口文件中的顺序确实是按照import的顺序执行的。base.scss < st_tool_jq.scss < page.scss

## 结论
> webpack开发环境中，webpack-dev-server --devtool 启动后的编译中，编译scss的顺序在入口文件中的表现形式和被引入的组件中的顺序不一致。即如果在引入组件中的顺序并不是按照谁先import就打包谁，而是优先打包组件依赖项。



最后我把editCar_info.js中的scss顺序调整到依赖的组件下方，解决了我的问题
```
'use strict';
import {Component} from 'react';
import QueueAnim from 'rc-queue-anim';
import FormComponent from './../../../../components/form/js/form.js';
import { hashHistory } from 'react-router';
import { connect } from 'react-redux';
import {eidtTodo} from './../../redux/actions';
//引入样式文件
import './editCar_info.scss';//引入组件依赖样式,覆写依赖项组件的样式
```

## 最后我们要记住：总之webpack  -p时 scss的顺序是按照代码执行的顺序，谁先import就先打包谁