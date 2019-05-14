---
title: 浅入React Hooks
date: 2019-03-05 20:22:33
tags:
    -  React
---

# 引言
> React Hooks是`React16.7.0-alpha`版本推出的新特性，在`16.8.0`版本正式发布。它可以在不使用class编写组件的情况下使用state和类生命周期effect、context、redux等功能。

# Hooks 的使用
## 基础API参考
- useState
- useEffect
- useContext
<!-- more -->
## useState
> 在使用class组件时，我们初始化state 然后在生命周期或者事件类型中调用setState去维护和使用状态。

```
import  { useState } from 'react';

const [state, setState] = useState(initialState);

```

useState函数返回一个状态值，以及更新函数。在初始渲染时，state返回的值和initialState相等。当setState被调用后，返回的第一个值就是组件更新后的最新状态值。


### 具体使用方法
```
let APP = initProps => {
    const [count, setCount] = useState(initProps.count);
    return (
        <div>
            <div>
                <span>Count: {count}</span>
            </div>
            <button onClick={() => setCount(0)}>Reset</button>
            <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
            <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
        </div>
    );
};
```

### 特性
- useState不会自动合并更新对象
```
let APP = () => {
    const [books, setBooks] = useState({ name: '语文', id: '1' });
    return (
        <div>
            <div>
                <span>索引: {books.id}</span>
                <span>书名: {books.name}</span>
            </div>
            <button onClick={() => setBooks({ name: '数学' })}>修改书名称</button>
        </div>
    );
};
```
> 当调用setBooks时，如果只修改了对象的一个属性name,索引id是不会自动合并到新的state对象中，且会返回空字符串。

可调用setBooks时传递函数去扩展合并。

```
<button onClick={() => setBooks((prevState)=>{
                //或者通过Object.assign合并对象
                return {...prevState,...{name:'数学'}}
            })}>修改书名称 索引不变</button>
```
---------
- useState初始化值可传递函数
> 当组件初始化渲染时,通过useState方法初始化一个state值,这将在组件首次渲染时获得初始值。如果初始化值获取比较复杂耗时，那么可以再初始化时传递功能函数处理

```
import { useState } from 'react';
const books = [{ name: '语文', id: '1' ,state:'0'},{ name: '数学', id: '2' ,state:'1'},{ name: '英文', id: '3',state:'0' }]
let APP = () => {
    const [book, setBooks] = useState(()=>{
        let findBook = {};
        books.map((_book,_index)=>{
            if(_book.state=='1'){
                findBook = _book;
            }
        })
        return findBook;
    });
    return (
        <div>
            <div>
                <span>索引: {book.id}</span>
                <span>书名: {book.name}</span>
                <span>状态: {book.name}</span>
            </div>
        </div>
    );
};
```

## useEffect
> useEffect(didUpdate);默认情况下,didUpdate函数在每次渲染和组件卸载后会被执行。

### 替代一些生命周期函数
> 在组件更新渲染之后执行

```
import { useState ,useEffect} from 'react';
let APP = () => {
    const [count, setCount] = useState(0);
    useEffect(()=>{
        document.title = `计数是${count}`;
    })
    return (
        <div>
            <div>
                <span>Count: {count}</span>
            </div>
            <button onClick={() => setCount(prevCount => prevCount + 1)}>加一</button>
        </div>
    );
};
```
useEffect的执行时机默认是在每一次组件渲染后背执行。对比class模式中的两个生命周期`componentDidMount`,`componentDidUpdate`和`ComponentWillUnmount` 所以我们可以通过设置useEffect去替代一些生命周期。

### 特性
- useEffect中可返回函数执行清除
> 某些订阅或者事件绑定、定时任务等我们会放到组件渲染完成之后去执行，但为了更好的释放内存，我们需要在组建下一次挂载之前先去去清除

```js
import { useState ,useEffect} from 'react';
let APP = () => {
    const [time, setTime] = useState(new Date().getTime());
    useEffect(()=>{
       let timer = setTimeout(function(){
            setTime(new Date().getTime());
        },0)
        return function(){
            clearTimeout(timer);
        }
    })
    return (
        <div>
            <div>
                <span>当前时间: {time}</span>
            </div>
        </div>
    );
};
```

`clearTimeout`执行时机是在下一次`useEffect`被执行之前。

-----------
- 自定义控制useEffect执行时机
> 默认在首次组件渲染和运行期每一次渲染更新时都会触发useEffect执行函数;但我们可以通过useEffect的第二个参数控制

1. `useEffect(didupdate,[]) ` 
> 如果想要当前的useEffect只在mount和unmount的时候执行一次，则可以将空数组[]作为第二个参数传递。这表示useEffect函数执行不依赖任何props和state值。这相当于class时componentDidMount和componentWillUnmount的效果。

```js
import { useState ,useEffect} from 'react';
let APP = () => {
    const [time, setTime] = useState(new Date().getTime());
    useEffect(()=>{
       let timer = setTimeout(function(){
            setTime(new Date().getTime());
        },0)
        return function(){
            clearTimeout(timer);
            console.log('被卸载了!')
        }
        console.log('初始渲染!')
    },[])
    return (
        <div>
            <div>
                <span>当前时间: {time}</span>
            </div>
        </div>
    );
};
let Parent = ()=>{
    const [flag, setFlag] = useState(true);
    return (
        <div>
            {flag&&<APP/>}
             <button onClick={()=>{setFlag(!flag)}}>remove</button>
        </div>
    )
}
module.exports = Parent;
```

2. `useEffect(didupdate,[state]) `
> 通常`[state]`作为性能优化的手段，在类组件中，往往通过内部`prevProps`或者`prevState`的比较来实现。

**class组件中**

```js
componentDidUpdate(prevProps, prevState) {
  if (prevState.flag !== this.state.flag) {
     this.timer = setTimeout(function(){
            this.setState({time:new Date().getTime()})
        },0)
     
  }
}
componentWillUnmount(){
     clearTimeout(this.timer);
}
```

**Hooks版本**

```js
import { useState ,useEffect} from 'react';
let APP = () => {
    const [time, setTime] = useState(new Date().getTime());
    const [flag, setFlag] = useState(true);
    useEffect(()=>{
       let timer = setTimeout(function(){
            setTime(new Date().getTime());
        },0)
        console.log('effect has be run!')
        return function(){
            clearTimeout(timer);
        }
    },[flag])//只有flag参数变化后才会重现执行effect
    return (
        <div>
            <div>
                <span>当前时间: {time}</span>
                <button onClick={()=>{setFlag(!flag)}}>update</button>
            </div>
        </div>
    );
};
module.exports = APP;
```

###  useEffect不支持传递async()=>{await ..} 
> 如果使用你会得到警告信息

```
It looks like you wrote useEffect(async () => ...) or returned a Promise. Instead, you may write an async function separately and then call it from inside the effect:

async function fetchComment(commentId) {
  // You can await here
}

useEffect(() => {
  fetchComment(commentId);
}, [commentId]);

In the future, React will provide a more idiomatic solution for data fetching that doesn't involve writing effects manually.
```

如提示所言，我们需要把异步提取成单独函数，然后再effect中通过promise.then调用 [Issues#30551](https://github.com/DefinitelyTyped/DefinitelyTyped/issues/30551#issuecomment-461909107)

```js
const [thing, setThing] = useState(null);

useEffect(() => {
  Api.makeRequest()
    .then(res => {
      // do what you need to do with res here
      setThing(res);
    });
});
```


## useContext
> 接受上下文对象，从React.creatContext返回的值并返回当前的上下文值，当组件更新时，Hook将使用最新的上下文值触发更新。

以下实现一个点击切换主题的例子,通过Context.Provider
更改切换上下文中的主题,useContext获取到最新更改后的上下文值。
```js
import React, { useState, useEffect, useContext } from 'react';
const themes = {
    light: {
        color: '#000000',
        background: '#eeeeee'
    },
    dark: {
        color: '#ffffff',
        background: '#222222'
    }
};

const ThemeContext = React.createContext(
    themes.dark // default value
);
let APP = () => {
    const [time, setTime] = useState(new Date().getTime());
    const context = useContext(ThemeContext);
    console.log(context);
    return (
        <div style={context}>
            <span>当前时间: {time}</span>
        </div>
    );
};
let Parent = () => {
    const [flag, setFlag] = useState(true);
    return (
        <ThemeContext.Provider value={flag?themes.light:themes.dark}>
            <div>
                <APP/>
                <button
                    onClick={() => {
                        setFlag(!flag);
                    }}>
                    切换主题
                </button>
            </div>
        </ThemeContext.Provider>
    );
};
module.exports = Parent;

```

## useReducer
> 替代useState,useReducer返回redux更新后的newState和dispatch.
```js
    const [reduxState, dispatch] = useReducer(Reducer, { time: 100000 },init);

```
其中`{time:100000}`可以传递默认初始state,第三个参数init为函数，将会这样调用：`init({time:100000})`，返回初始值。

### dispatch调用action

```
import React, { useReducer } from 'react';
import TimeReducer from './../reducer/timeReducer';
import { getTime, resetTime, addTime, fetchTime } from './../action/timeAction';

function Clock() {
    const [reduxState, dispatch] = useReducer(TimeReducer, { time: 100000 });
    return (
        <div>
            Seconds: {reduxState.time}
            <button
                onClick={() => {
                    dispatch(resetTime(0));
                }}>
                resetTime
            </button>
            <button
                onClick={() => {
                    dispatch(addTime());
                }}>
                add
            </button>
        </div>
    );
}
```
可在最外层父组件中，把state和dispatch通过React.createContext存入到context中，再通过useContext api获取。

> 相比较传统redux,减少了在最外层Provider包装,以及通过connect中间层把redux状态数据转换成组件props的步骤。对于redux异步请求，还是需要依赖中间层，[参考文章](https://segmentfault.com/a/1190000017209855)
--------------------

## [其他钩子](https://reactjs.org/docs/hooks-reference.html) 
- useCallback
- useMemo
- useRef
- useImperativeHandle
- useLayoutEffect
- useDebugValue


## 自定义hooks
> 当我们想要在多个组件内共用同一个业务逻辑时，我们就可以把hooks提取成一个单独的函数。这个时候我们在多个组件中都可以直接调用它。

自定义Hook是一个JavaScript函数，其名称以“ use” 开头，可以调用其他Hook。例如,`useGetUserAllowCount`是自定义的一个获取用户抽奖次数的一个自定义Hook

```js
import { useState, useEffect } from 'react';

/**获取用户剩余多少抽奖次数
 * @returns count
 */
const useGetUserAllowCount = (userID) => {
    const [count, setCount] = useState(0); // 抽奖次数 接口拉取 默认100次
    useEffect(() => {
        //事件钩子 组件被render之后会触发
        fetchGetCount(); //获取抽奖数
    }, []);
    const fetchGetCount = () => {
        fetch('https://localhost:8001/getcount'+userID, {
            method: 'GET',
            credentials: 'include'
        })
            .then(function(response) {
                return response.json();
            })
            .then(res => {
                setCount(res.respData.count);
            });
    };
    return count;
};

module.exports = useGetUserAllowCount;

```
自定义Hook返回count当前用户剩余次数，那么在另外别的页面，我们就可以通过组件直接调用这部分的逻辑。

```js
import useGetUserAllowCount from './../hooks/useGetUserAllowCount';
let APP = (props) => {
    let { count } = useGetUserAllowCount(props.userID);
    return (
       <div className="count">
            抽奖次数：<span>{count}</span>(次日更新)
        </div>
    );
};

```

### 特性

- 必须以`use`开头命名自定义Hook
- 两个使用相同自定义Hook的组件不共享一个Hook状态，他们的useState和useEffect都是完全隔离的

--------

## Hooks使用规则
> Hook是一个js函数，但使用它有两个规则，以及发布了一个ESlint插件
- **Only Call Hooks at the Top Level**
不要在循环条件和嵌套的函数中调用Hook,只能始终在React函数的顶层使用Hook,因为这样React才能允许再多个`useState`和`useEffect`之间正确保留Hook状态。

-------
- **Only Call Hooks from React Functions** 不能在常规的js函数中调用Hook,只能从React功能组件或者自定义Hooks中调用Hooks,

- **[ESLint Plugin](https://reactjs.org/docs/hooks-rules.html#eslint-plugin)** 官方提供了esLint插件去校验这些规则。

## 从class类组件生命周期与Hooks如何对应

- constructor：函数组件不需要构造函数。您可以在初始化时获取useState中的状态。如果计算初始状态很昂贵，可以将函数传递给useState。

- getDerivedStateFromProps：改为在渲染时安排更新（16.4发布）。

- shouldComponentUpdate：通过[React.memo](https://reactjs.org/docs/hooks-faq.html#how-do-i-implement-shouldcomponentupdate)。

- render：这是组件主体。

- componentDidMount，componentDidUpdate，componentWillUnmount：useEffect挂钩可替代这些组合事件。

- componentDidCatch并且getDerivedStateFromError（16.6）：这些方法还没有Hook对应，但很快就会添加它们

## 总结
> 读到这儿我想大概都应该能明白Hooks是怎么用的了，那我们就可以讨论下为什么要推出Hooks

1. 状态有关的业务逻辑组件共享隔离
>  通过自定义Hooks可以把组件中维护状态的代码抽离出渲染组件。再此之前习惯通过[Render Props](https://reactjs.org/docs/render-props.html) 和[高阶组件](https://reactjs.org/docs/higher-order-components.html)去解决

2. 状态和UI分开使组件变得更清晰
> 在大型复杂React应用程序中，状态管理一直是一个比较头疼的问题，程序越来越复杂,交互更多之后我们需要去维护一个又一个的state,所以redux这类状态管理的框架变得很受大型引用程序的喜爱。

在上面介绍的例子中，可以看到APP组件是无状态的，而状态部分我们都提取成自定义的Hooks以便于逻辑被复用。更好的编写出`有状态的组件没有渲染，有渲染的组件没有状态`的组件。



## 缺陷
> 目前官方还没有可以取代getSnapshotBeforeUpdate 和 componentDidCatch 生命周期的Hooks，使用这两部分功能受到了限制。可能第三方库还不支持。

## 服务端渲染
> 服务端渲染支持通过useState获取获取初始状态，也可通过useState传递函数方式同步获取服务端数据，不支持async await异步函数调用。但官方已经有计划在不久会发布另外一个api去支持[#14986](https://github.com/facebook/react/issues/14986)
  - useEffect在服务端渲染不会被执行
  - Hooks组件中需要`import React, { useState, useEffect } from 'react';即使你没有用到React变量，因为编译后服务端渲染执行会将JSX变成`React.createElement('dev'...)`
`
 
## 使用前景
官方也不建议现在就去用Hooks把原有项目重构一遍，但建议更多在新项目中优先使用Hooks。只要你使用过一次你就能感受到使用起来真的真的很简单易懂.


