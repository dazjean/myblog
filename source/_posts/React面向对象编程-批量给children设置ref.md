---
title: React面向对象编程-批量给children设置ref
date: 2017-11-02 11:31:39
tags: 
	- React
---
> React中，父组件需要统一个子组件传递props属性或者给每个子组件设置ref；通过父组件调用this.refs.childrenRef.xxxx去调用子组件的实例方法。

### 组件拆分
![image](http://note.youdao.com/yws/public/resource/cdfb1ed5bf83fb28fbc863673101d92d/xmlnote/8A256CE415E14173BDD1A0F766243B4C/7937)

组件主要分为三类：<br>
A最上层为页面控制层，主要提供的功能就是点击下一页按钮，切换到下一页。
b，c是同级子组件，比如头部的提示信息，图片上传以及表单模块C
C组件提供了统一的模块名称，以及点击弹窗提示模块详情，至于D则为真正的表单组件。

### 父组件调用子组件的方法
> 页面组件拆分后，我就在想如何在点击下一页或者提交按钮的时候，去校验当前页的表单是否和发输入或获取到每一个表单组件的值发送给后端

- 直接Dom查找各个组件隐藏的input然后取值,校验
- 因为下一页按钮为父级组件，通过调用子组件的方法取值，校验（假设每个表单组件都有getValue,doCheck方法）
> ==前者==为常用实现方案，在组件渲染的时候将校验的正则写到input属性上。在父级组件上提供统一 doCheck方法调用。这种方案却有着不可扩展的缺点，如果后续某个特殊的表单校验信息并非正则，而是和业务权限相关。那么此时doCheck中将会有非常多的逻辑判断。
<br>
相比之下，==第二种方案采用面向对象编程的思想==，通过调用子组件的方法去校验，取值甚至改变子组件的状态具有更好的扩展性和维护性。各组件业务逻辑各种维护。
----
<!-- more -->
### 父组件如何实现可调用子组件方法
> 在React中如果给组件设置ref值，那么在当前组件内可以通过this.refs.xxx调用到给组件设置refxxx的组件实例。

具体实现代码：

page:
```
<Section btnName = "下一页" clickNext = "true">
     <Block title = '个人信息' titleTip = '必填'>
        <InputText  maxLength = '6' name = 'lianxiren' placeholder = '请填写您的称呼'/>
        <Number title = '手机号码' name = 'minprice' placeholder = '请输入'/>
    </Block>
</Section>
```



Section组件
```
 render(){
        const {
            btnName,
            clickNext,
            clickSubmit
        } = this.props;
        return (
            <div className = "module-section">
              {   
                  this.props.children.map((child,index)=>{
                      return (
                        React.cloneElement(child, {ref:'section'+index})
                      )
                  })
              }
              <div onClick = {clickNext?this._clickNext:this._clickSubmit} className = {ClassName({"module-button":true,"next":clickNext,"submit":clickSubmit})}>{btnName}</div>
            </div>
        )
    }
    componentDidMount(){
    }
    /**
     * _clickNext 点击下一页时校验当前页面 校验通过后显示下一页
     */
    _clickNext(){
        const self = this;
        self.refs.Section0.doCheck()
        self.refs.Section0.getValue()
        self.refs.Section0.setValue()
    }
```

在section组件中就可以通过this.refs访问到block的组件实例了。

---
同理我们给block组件的render方法中也给children设置上ref值，这样就会形成一条ref链。

```
render(){
        const {
            title,
            titleTip,
            isRequired,
            popTipShow,
            tips
        } = this.props;
        let tip = titleTip;
        if(tip == "" && isRequired){
            tip = "必填"
        }
        let children = this.props.children;
        if(children instanceof Array == false){ //修正子组件只有一个的时候children为对象
            this.props.children = [children];
        }
        return(
            <div className = "module module-block">
                <div className = "block-title">
                    <span className = "title">{title}</span>
                    <span className = "title-tips">{"("+tip+")"}</span>
                    <span onClick = {this.clickPopAction} className = {ClassName({"detail":true,'hide':!popTipShow})}>
                        <i className = {tips.icon}></i>
                        {tips.title}
                    </span>
                </div>
                <div className = "block-content">
                {   
                  this.props.children.map((child,index)=>{
                      return (
                        React.cloneElement(child, {ref:'block'+index})
                      )
                  })
              }
                </div>
            </div>
        )
    }
```

解决了父组件调用子组件方法的问题，接下来就是在最外层控制层编写业务逻辑代码。