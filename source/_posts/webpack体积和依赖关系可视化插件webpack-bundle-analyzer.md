---
title: webpack体积和依赖关系可视化插件webpack-bundle-analyzer
date: 2019-05-14 19:58:33
tags:
    - webpack
---
> webpack打包后体积太大，在优化过程中借助 ` webpack-bundle-analyzer`可视化工具可快速定位文件体积过大的包。

### 安装
```
npm install --save-dev webpack-bundle-analyzer

```
<!-- more -->


### 配置webpack插件
```
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');

module.exports = merge(common, {
  // ...
  plugins: [
    new BundleAnalyzerPlugin({ analyzerPort: 8919 })
  ],
});

```


启动项目后会自动打开一个8919的站点。http://127.0.0.1:8919/
![http://127.0.0.1:8919/](/images/webpack-bandle-analyzer.png)


通过可视化界面我们很直观可以找到需要优化的模块。


### 常用优化手段
- 去掉开发环境配置，比如代码压缩等
- 提取CSS文件ExtractTextPlugin
- CommonsChunkPlugin提取公共模块
- externals通过模板中全局引入SDK方式忽略一些固定不变的模块包 比如react react-dom react-router-dom等
- 按需加载引入的第三方组件库比如ant等