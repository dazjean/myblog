---
title: webpack配置eslint时如果在.eslintignore中添加注释会导致eslint无效
date: 2017-11-16 11:44:57
tags:
  - webpack
---
> 现象是这样的，之前按照常规方法配置eslint-loader,在打包前对代码进行检测。

```
 eslint: {
        configFile: './.eslintrc'
    },
    
module: {
        preLoaders: [
            // 配置 eslint-loader
                {  
                test: /\.(js|jsx)$/,
                loader: "eslint-loader",
                include:/src/,
                exclude: /node_modules/
                }
        ],
        loaders: [
            { test: /\.(js|jsx)$/, loader: "jsx!babel", include: /src/ ,exclude: /node_modules/},
            { test: /\.css$/, loader: ExtractTextPlugin.extract("css", "css!postcss") },
            { test: /\.scss$/, loader: ExtractTextPlugin.extract("css", "css!postcss!sass!sass-resources-loader") },
            { test: /\.(png|jpg)$/, loader: 'url?limit=10240' }
        ]
    }
    
```
<!-- more -->
.eslintrc文件中添加配置：
```
/*
 * @Author: zhang dajia 
 * @Date: 2017-11-16 13:28:26 
 * @Last Modified by:   zhang dajia 
 * @Last Modified time: 2017-11-16 13:28:26 
 * @description  
 */
{
    "env": {
        "browser": true,
        "es6": true,
        "node": true,
        "commonjs": true
    },
    "extends": "eslint:recommended",
    //"installedESLint": true, //使用全局eslint安装 不知道为啥会报错
    "parserOptions": {
        "ecmaFeatures": {
            "experimentalObjectRestSpread": true,
            "jsx": true,
            "arrowFunctions": true,
            "classes": true,
            "modules": true,
            "defaultParams": true
        },
        "sourceType": "module"
    },
    "parser": "babel-eslint",
    "plugins": [
        "react"
    ],
    "rules": {
        "linebreak-style": [
            "error",
            "windows"
        ],
        //"semi":["error","always"],
        "no-empty": 0,
        "indent":2,//缩写格式的一致性
        "comma-style": ["error", "last"],//方数组元素、变量声明等直接需要逗号隔开
        "comma-dangle":["error", "never"],//这个规则强制在对象和数组中使用尾随逗号 设置不强制
        "no-unused-vars": 2,//不允许定义的变量在后面的代码中没有被使用到
        "no-use-before-define": 2,//所有的变量都应该先定义后使用
        "no-console": 2,
        "camelcase":2,//变量驼峰命名
        "no-const-assign": 2,
        "no-dupe-class-members": 2,
        "no-duplicate-case": 2,
        "no-extra-parens": [2, "functions"],
        "no-self-compare": 2,
        "accessor-pairs": 2,
        "no-unreachable": 2,//禁止有执行不到的代码
        "no-sparse-arrays": 2, //禁止稀疏数组，清除多余的逗号申明  比如[1,,2]
        "dot-notation":"error",//不允许关键字出现在变量中
        "no-redeclare":"error",//不允许重复声明
        "no-return-assign":"error",//不允许在return语句中任务
        //"semi":"error",// 语句以分号结尾
        "comma-spacing": [2, { //不允许逗号之前出现空格
            "before": false,
            "after": true
        }],
        "constructor-super": 2,
        "new-cap": [2, { //构造函数首字母需要大写
            "newIsCap": true,
            "capIsNew": false
        }],
        "new-parens": 2,//没有参数时，构造函数也需要添加括号
        "no-array-constructor": 2,
        "no-class-assign": 2,
        "react/jsx-equals-spacing":[2, "always"],//jsx中变量等号两边需要有空格
        "no-cond-assign": 2
    }
}
```

运行 npm run dev后控制台正常打印出来没有通过检测规则的错误信息。

突然有一天，因为引用了之前写的代码，但不需要进行eslint规则校验，所以新建了一个.eslintignore文件忽略某些文件

```
/*
 * @Author: zhang dajia 
 * @Date: 2017-10-19 13:26:39 
 * @Last Modified by: zhang dajia
 * @Last Modified time: 2017-11-16 12:36:09
 */
/node_modules/*
.idea
dist
src/utils/cookie.js
src/utils/util.js
```
在文件头部默认生成了一个注释 这个时候也没有出现问题，因为确实eslint已经没有检查了.

不知道过了好久.............突然发现，诶我其它组件的代码怎么没有规则检查了呢？ 哭晕.....

然后各种查阅资料都找不到问题出现在哪儿.这可郁闷坏了。也不知道是为啥突发奇想，反正配置都没问题，下意识猜想，我擦 是不是这行注释搞的事！ 

好吧 结果大家都知道了。。 就是它！ 删掉注释就好了。


---
因为几个星期的代码都没有经过检测，所以现在N多个文件全是error......让我先去哭一会儿再一个一个修改。