---
title: webpack中配置sass-resources-loader定义全局sass文件实现各组件@mixin@function共享
date: 2017-11-09 13:48:26
tags:
	- webpack
---
# 安装
```
npm install sass-resources-loader -save-dev
```
# 修改webpack.config.js配置
```
module:{
        loaders: [
           
            { test: /\.scss$/, loader: ExtractTextPlugin.extract("css", "css!postcss!sass!sass-resources-loader") },

        ]
    },
sassResources:  ['./src/skin/vars.scss', './src/skin/mixins.scss'],
```
> 注意：vars.scss和minxins.scss中只适合定义常量和function;禁sassResources中引入一个基础base.scss否则每一次import xxx.scss都会重复打包。