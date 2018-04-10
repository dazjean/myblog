---
title: 使用Prettier eslint pre-commit进行js代码自动检测,格式化统一风格
date: 2018-04-10 21:36:51
tags:
    - js
    - eslint
    - pre-commit
---
### Prettier是什么？
> Prettier是一款支持JavaScript，TypeScript，CSS，GraphQL，JSON和Markdown的独立代码格式器。它有很少的设置，大部分代码样式规则都是内置的。漂亮的从您的代码中删除任何现有的格式，并打印自己的版本，使代码完全一致。

### 为什么使用Prettier?
> 在多人项目中，定义一套所有人都认可和规范的规则是非常耗时的，即使配置了许多的eslint规则，后期团队强制去统一一套样式也很让人分心。而Prettier可以自动格式化代码统一格式和排版。==开发人员应该关注真正的问题上==-[源自Prettier作者的译文](https://segmentfault.com/a/1190000008861384)
<!-- more -->
### 如何使用
- 安装依赖
```
npm install prettier
```
- 配置package.json
```
script:{
    'format':"prettier --write '**/*.{js,css,md}' "
}
```
- 手动执行命令
```
npm run format
```
- 格式化效果
```
//格式化前
 constructor(props) {
  super(props)
  this.state = { chepaihao: '', vin: "", engineNum: "" };
  }
  static navigationOptions = {
    //不推荐使用，设置页面导航标题 headerTitle: "违章查询",
    title: '违章查询', headerTruncatedBackTitle: "返回",gesturesEnabled: true //是否支持滑动返回手势，iOS默认支持，安卓默认关闭
  };
  
  //格式化后
  constructor(props) {
    super(props);
    this.state = { chepaihao: "", vin: "", engineNum: "" };
  }
  static navigationOptions = {
    //不推荐使用，设置页面导航标题 headerTitle: "违章查询",
    title: "违章查询",
    headerTruncatedBackTitle: "返回",
    gesturesEnabled: true //是否支持滑动返回手势，iOS默认支持，安卓默认关闭
  };
```
> 可见prettier自动会给代码进行格式化，默认添加分号，代码缩进，行宽过长自动分行；这些在开发阶段我们可以完全不用关心这类格式问题，从代码结构上完全可以统一团队的代码风格一致性。

### Prettier和ESLint配合使用
> [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)插件安装配置 
```
npm install prettier eslint-plugin-prettier --save-dev
//修改.eslintrc
{
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": [
      "error",
      {
        "singleQuote": true
      }
    ]
  }
}
```
============如果项目是全局安装的eslint==========  那么plugins也需要全局安装，要不然会报错==

- 执行命令
```
eslint . --fix
```
> 然后查看你的代码 发现prettier/prettier中设置的代码检测项都会按照所配置的方式进行自动修正，比如把所有双引号格式化成单引号(因为prettier默认是格式化成双引号。)

通常情况下项目中已经使用配置了eslint规则，这个时候为了不影响已配置项目，在prettier中可以把相应的检测项目重置；[prettier参数列表参考地址](https://segmentfault.com/a/1190000012909159) --》 [官方地址](https://prettier.io/docs/en/options.html)
> 以下是我采用的配置参数：
```
"rules": {
    "prettier/prettier": [
      "error",
      {
        printWidth: 100,//设置单行字符数
        tabWidth: 4,//缩进空格数
        semi: true,//代码行后面需不需要生成分号
        trailingComma: "none",//数组后面最后一个索引要不要添加逗号
        singleQuote: false,//需不需要把双引号格式化成单引号
        bracketSpacing: true,//在对象字面量声明所使用的的花括号后（{）和前（}）输出空格
        jsxBracketSameLine: true,//在多行JSX元素最后一行的末尾添加 > 而使 > 单独一行（不适用于自闭和元素）
        alwaysParens:'avoid',//为单行箭头函数的参数添加圆括号
      }
    ]
  }
```

---
### 引入pre-commit
now!经过上面配置 我们可以在项目进行--fix的同时可以得到一份格式化统一的代码，但是千万别怀疑团队开发人员的惰性，==不要相信每个人在提交代码之前都去执行eslint . --fix==
> 接下来我们需要引入pre-commit,在代码提交之前自动去完成这一步骤
- 安装 npm i pre-commit
- 修改package.json
```
//增加如下配置 使其可以通过npm run lint:js调用eslint --fix自动修复命令
"script":{
    "lint:js": "eslint . --fix"
},
"pre-commit": [
    "lint:js"
  ]
```
然后我们在项目中试着commit代码，如果有检测不通过的可在命令行中查询到相关信息并中断提交过程
```
$ git commit -m '提交代码检测工具prettier' -a
Ignored unknown option `{ "alwaysParens": "avoid" }`.
Ignored unknown option `{ "alwaysParens": "avoid" }`.
Ignored unknown option `{ "alwaysParens": "avoid" }`.
Ignored unknown option `{ "alwaysParens": "avoid" }`.
Ignored unknown option `{ "alwaysParens": "avoid" }`.
Ignored unknown option `{ "alwaysParens": "avoid" }`.
Ignored unknown option `{ "alwaysParens": "avoid" }`.
Ignored unknown option `{ "alwaysParens": "avoid" }`.
Ignored unknown option `{ "alwaysParens": "avoid" }`.
Ignored unknown option `{ "alwaysParens": "avoid" }`.

/Users/admin/Workspaces/rn-speed-project/rn-app/index.js
  1:7  error  'Throttle' is assigned a value but never used  no-unused-vars
  2:7  error  'delay' is assigned a value but never used     no-unused-vars

/Users/admin/Workspaces/rn-speed-project/rn-app/lib/checker.js
   33:41  error  'array' is defined but never used             no-unused-vars
   73:5   error  'throwCheckerError' is a function             no-func-assign
   73:25  error  'throwRequrementError' is a function          no-func-assign
   73:48  error  'throwError' is a function                    no-func-assign
   94:9   error  Unexpected lexical declaration in case block  no-case-declarations
   94:9   error  Unexpected lexical declaration in case block  no-case-declarations
  110:9   error  Unexpected lexical declaration in case block  no-case-declarations

/Users/admin/Workspaces/rn-speed-project/rn-app/sdk/toast.js
  13:35  error  'ToastAndroid' is defined but never used  no-unused-vars

/Users/admin/Workspaces/rn-speed-project/src/component/tabBarItem.js
  1:8   error  'React' is defined but never used  no-unused-vars
  2:10  error  'Image' is defined but never used  no-unused-vars

/Users/admin/Workspaces/rn-speed-project/src/index.js
  8:8  error  'MainScreen' is defined but never used  no-unused-vars
  
✖ 35 problems (35 errors, 0 warnings)

pre-commit:
pre-commit: We've failed to pass the specified git pre-commit hooks as the `lint:js`
pre-commit: hook returned an exit code (1). If you're feeling adventurous you can
pre-commit: skip the git pre-commit hooks by adding the following flags to your commit:
pre-commit:
pre-commit:   git commit -n (or --no-verify)
pre-commit:
pre-commit: This is ill-advised since the commit is broken.
pre-commit:
```
----成功！

### 后续优化
> 上述方法原理是通过pre-commit钩子在提交前执行script命令，如果检测到未发代码规则则返回1，导致git commit失败；但每次执行commit时会整个文件全部扫描一遍，++==实际上每次commit时，只需要检测有改动的文件即可==++

#### 方案：
- [Husky](https://www.npmjs.com/package/husky):可以方便的让你通过npm scripts来调用各种git hooks。
- [lint-staged](https://www.npmjs.com/package/lint-staged):利用git的staged特性，可以提取出本次提交的变动文件，让prettier只处理这些文件。

```
//分别安装依赖模块
npm install husky --save-dev
npm install lint-staged --save-dev

//在package.json中配置pre-commit的hook任务
 "scripts": {
    "precommit": "lint-staged"//precommit这个值不能改变，这是husky默认的对应的pre-commit-hook的任务名。
  },
  "lint-staged": {
    "*.{js,json,css}": ["prettier --write", "git add"]
  }
```
到这儿项目就可以自动prettier了。大家可以试一试

---
参考资料：

- [https://www.jianshu.com/p/8b49e268fe69](https://www.jianshu.com/p/8b49e268fe69)
- [https://www.npmjs.com/package/pre-commit](https://www.npmjs.com/package/pre-commit)
- [https://survivejs.com/maintenance/code-quality/code-formatting/](https://survivejs.com/maintenance/code-quality/code-formatting/)
- [https://blog.fundebug.com/2017/10/23/format-code-use-Prettier/](https://blog.fundebug.com/2017/10/23/format-code-use-Prettier/)
- [http://ju.outofmemory.cn/entry/335890](http://ju.outofmemory.cn/entry/335890)
- [https://segmentfault.com/a/1190000008861384](https://segmentfault.com/a/1190000008861384)

---
