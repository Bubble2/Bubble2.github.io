---
layout:     post
title:      "react入门，jsx&props&state"
date:       "2017-08-14 "
tags:       "javascript react"
author:     "guozhaodong"
---

## react入门

### 安装

#### 方法一

``` HTML
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello World</title>
    <script src="https://unpkg.com/react@latest/dist/react.js"></script>
    <script src="https://unpkg.com/react-dom@latest/dist/react-dom.js"></script>
    <script src="https://unpkg.com/babel-standalone@6.15.0/babel.min.js"></script>
  </head>
  <body>
    <div id="root"></div>
    <script type="text/babel">

      ReactDOM.render(
        <h1>Hello, world!</h1>,
        document.getElementById('root')
      );

    </script>
  </body>
</html>
```

#### 方法二

通过webpack模块化构建工具来管理

<a href="https://github.com/Bubble2/react-start">webpack搭建react开发环境</a>


### jsx语法


#### `JSX`是什么?

> 一种JavaScript的语法扩展,看起来像模板语言，和我们平常写的html也十分相似，其实完全是js内部实现的。

``` JavaScript

//传统的html标签
const element = <div>I am a div tag</div>

//组件
const Hello = <Hello>I am a React Component</Hello>

//标签嵌套
const MyComponent=(
  <div>
    <Hello color="blue">I am a nested tag</Hello>
  </div>
)

```

#### 为什么会出现`JSX`?

`React`为了方便`View`层组件化，承载了构建`HTML`结构化页面的职责，说白了`JSX`就是为了创建元素方便、快捷、直白，更易于我们的编码和维护。

> 如果不用jsx也是可以的，jsx本质上是为`React.createElement(type,props,...children)`方法提供的语法糖。

``` JavaScript
<ButtonComponent size="xl" color="primary">我是一个小小的按钮组件</ButtonComponent>
```

上面的jsx语法编译以后就是下面这个样子的
``` JavaScript
React.createElement(
  ButtonComponent,
  {size:'xl',color:'primary'},
  '我是一个小小的按钮组件'
)
```

#### 关于元素

##### 元素类型

标签名决定了`React`元素的类型

1、小写字母开头的标签表示DOM元素
``` JavaScript
<div></div>
<span></span>
```

2、大写字母开头的标签表示一个组件元素
``` JavaScript
<MyComponent>我是一个react组件</MyComponent>
```

##### 最外层只能有一个标签
``` JavaScript
//错误
const element=(
    <span>hello</span>
    <span>world</span>
)

//正确
const element=(
    <div>
        <span>hello</span>
        <span>world</span>
    </div>
)
```

##### 标签一定要闭合

（1）普通`html`标签`<div></div>`

（2）`html`中自闭和标签`<img />`,必须要有

（3）对于`React`组件

如果组件有子元素或者子组件那么必须有闭合标签 `<App>App</App>`、`<App><div>App</div></App>`

否则就可以用这种带闭合的单标签`<App />`


##### 支持命名空间，可以用点表示法引用`React`组件

``` JavaScript
import React from 'react';

const NameSpaceComponent = {
  DatePicker(){
    return(
      <div>This is a Datepicker!</div>
    )
  }
}

const BlueDatePicker = () => {
  return <NameSpaceComponent.DatePicker />
}
```

##### 注释

因为是`JavaScript`的语法，所以`JavaScript`的注释依然可以使用，但是有点需要注意，在一个组件的内部使用注释要用`{}`包起来

``` JavaScript
const App = (
  // 这个是一个单行注释

  /*这是一个多行注释
  这是一个多行注释
  这是一个多行注释
  这是一个多行注释*/

  <div>
    {/*这是一个多行注释
     这是一个多行注释
     这是一个多行注释
    这是一个多行注释*/}

    {
    //这是一个单行注释
    }

  </div>
)
```

#### 关于元素属性

DOM元素的属性是标准规范属性，同时采用驼峰命名的方式来定义属性名称。但是有两个例外，分别是`class` 和 `for`,这两个属性是`JacaScript`的关键词,我们将其转换,`class`=>`className`,`for`=>`htmlFor`。

组件元素的属性是完全自定义的，也可以理解为实现组件所需要的参数。

##### 传递`JavaScript`表达式

``` JavaScript
<PropExpression title={1+2} />
<PropExpression title={window.name?window.name:'window.name is null'} />
```

##### 传递字符串常量

``` JavaScript
//下面两种方式是相等的
<PropString msg="hello1" />
<PropString msg={"hello2"} />

```

##### 传递组件

``` JavaScript
<PropComponent right={<PropComponentRight />} left={<PropComponentLeft />} />
```

##### 传递布尔值

``` JavaScript
//默认不传则为true，以下两种方式相等
<PropBool msg />//不建议
<PropBool msg={true} />//建议

```

##### 扩展属性

``` JavaScript
const PropSpread = (props) => {
  return (
    <div>
        <p>{props.title}</p>
        <p>{props.name}</p>
    </div>
  )
}

const MyApp=()=>{
  const obj = {
      title:'I am title',
      name:'I am name'
  }
  return(
      <PropSpread {...obj} />
  )
}
```

#### 关于子元素

##### 可以接受字符串常量

``` JavaScript
<ChildString>I am ChildString's children</ChildString>
```

##### 可以接受子组件

``` JavaScript
<ChildComponent>
    <FirstChild></FirstChild>
    <SecondChild></SecondChild>
</ChildComponent>
```

##### 可以接受`JavaScript`表达式

``` JavaScript
<ChildExpression>{'Hello'}</ChildExpression>
<ChildExpression>{1+2}</ChildExpression>
<ChildExpression>{window.name?<FirstChild />:<SecondChild />}</ChildExpression>
```

##### 可以通过`props.children`来访问该组件的孩子

``` JavaScript
const App=(props)=>{
  return(
    <div>{props.children}</div>
  )
}

const MyApp=()=>{
  return(
    <App>我是App组件的孩子</App>
  )
}
```

##### 布尔值、Null 和 Undefined 被忽略

``` JavaScript
//一下几种方式都是等价的
<div />
<div></div>
<div>{true}</div>
<div>{false}</div>
<div>{null}</div>
<div>{undefined}</div>
```


除了`false`以外,0、""、null、undefined、NaN,也会认为是假，但是它们会把自身输出
``` JavaScript
const FalsyComponent = () => {
  return (
      <div>
          <div>If you see this,the value is true</div>
      </div>
  )
}

const value=false;
return(
    <div>
        {value && <FalsyComponent />}
    </div>
)
```

### React组件

#### 组件的构建方法

方法一、`React.createClass`

最传统、也是兼容性最好的方法，`v0.14`版本之前，官方唯一指定的组件写法，但是目前版本已经不建议去使用了。

``` JavaScript
const Button = React.createClass({
  getDefaultProps(){
    return {
      color: 'blue',
      text: 'Confirm'
    }
  },
  render(){
    const {color,text}=this.props;

    return (
      <button className={`btn btn-${color}`}>
        <span>{text}</span>
      </button>
    );
  }
})
```

方法二、`ES6 classes`

``` JavaScript
class Button extends React.Component{
  constructor(props){
    super(props);
  }

  static defaultProps={
    color:'blue',
    text:'Confirm'
  }

  render(){
    const {color,text}=this.props;
    return (
      <button className={`btn btn-${color}`}>
        <span>{text}</span>
      </button>
    )
  }
}
```

方法三、无状态函数

使用无状态函数构建的是无状态的组件,这个是`v0.14`版本之后新增的方式，

``` JavaScript
function Button({color='blue',text='Confirm'}){
  return (
    <button className={`btn btn-${color}`}>
      <span>{text}</span>
    </button>
  )
}
```

这种方式创建的组件没有`state`和生命周期，组件本身就是上面两种方式的`render`方法，在合适的情况下,比较推荐使用这种方式去构建组件。第一和第二两种方式在构建组件时不会创建实例，但是在调用的时候会创建实例，而无状态组件创建的时始终保持一个实例，避免了不必要的内存检查和内存分配,做到了内部优化。

#### 组件的渲染

``` JavaScript
function ComponentRender(){
  return <h1>把这个组件渲染到页面中</h1>
}

ReactDOM.render(
  <ComponentRender />,
  document.getElementById('root')
)
```

#### 组合组件

``` JavaScript

function SingleComponent(props){
  return <h1>{props.name}组件</h1>
}

function CombineComponent(){
  return(
    <div>
      <SingleComponent name='第一个' />
      <SingleComponent name='第二个' />
      <SingleComponent name='第三个' />
    </div>
  )
}

ReactDOM.render(
  <ComponentRender />,
  document.getElementById('root')
)
```

#### 提取组件

``` JavaScript
function Comment(props){
  return(
    <div className="comment">
      <div className="userinfo">
        <img className="avatar" src={props.author.avatarUrl} alt={props.author.name} />
        <div className="userinfo-name">{props.author.name}</div>
      </div>
      <div className="comment-txt">{props.text}</div>
      <div className="comment-text">{formatDate(props.date)}</div>
    </div>
  )
}
```

对用户头像部分进行提取

``` JavaScript
function Avatar(props){
  return(
    <img className="avatar" src={props.user.avatarUrl} alt={props.user.name} />
  )
}

function Comment(props){
  return(
    <div className="comment">
      <div className="userinfo">
        <Avatar user={props.author} />
        <div className="userinfo-name">{props.author.name}</div>
      </div>
      <div className="comment-txt">{props.text}</div>
      <div className="comment-text">{formatDate(props.date)}</div>
    </div>
  )
}
```

用户信息部分进行提取

``` JavaScript
function Avatar(props){
  return(
    <img className="avatar" src={props.user.avatarUrl} alt={props.user.name} />
  )
}

function UserInfo(props){
  return(
    <div className="userinfo">
        <Avatar user={props.user} />
        <div className="userinfo-name">{props.user.name}</div>
    </div>
  )
}

function Comment(props){
  return(
    <div className="comment">
      <UserInfo user={props.author} />
      <div className="comment-txt">{props.text}</div>
      <div className="comment-text">{formatDate(props.date)}</div>
    </div>
  )
}
```


### `React`数据流

#### `props`

##### 作用

父级向子级传递数据的一种方式。

##### 特点

<strong>只读的</strong>，组件内部不能修改自己的`props`

##### 父组件改变子组件的`props`

``` JavaScript
const ChildComponent = (props) =>{
    return(
        <div>{props.value}</div>
    )
}

export class ParentComponent extends React.Component{
    constructor(props){
      super(props);
      this.state={
        value:0
      }
      this.handleChange=this.handleChange.bind(this);
    }
  
    handleChange(e){
      this.setState({
        value:e.target.value
      })
    }
  
    render(){
      return(
        <div>
          <input type="text" onChange={this.handleChange} />
          <ChildComponent value={this.state.value} />
        </div>
      )
    }
}
```

##### 子组件改变父组件的`state`

``` JavaScript
class ChildComponent2 extends React.Component{
    constructor(props){
      super(props);
      this.handleChange=this.handleChange.bind(this);
    }
  
    handleChange(e){
      this.props.onValueChange(e.target.value);
    }
  
    render(){
      return(
        <div>
          <input type="text" onChange={this.handleChange}/>
        </div>
      )
    }
}

export class ParentComponent2 extends React.Component{
    constructor(props){
      super(props);
      this.state={
        value:0
      }
      this.changeHandle=this.changeHandle.bind(this);
    }
  
    changeHandle(value){
      this.setState({
        value
      })
    }
  
    render(){
      return(
        <div>
          <ChildComponent2 onValueChange={this.changeHandle} />
          {this.state.value}
        </div>
      )
    }
}
```


##### 使用`PropTypes`进行类型检查

``` JavaScript
import PropTypes from 'prop-types';

const PropTypesComponent = (props) =>{
  return(
    <div>{props.num}</div>
  )
}

PropTypesComponent.propTypes={
  num : PropTypes.number
}
```

#### `state`

##### 作用

将用户的交互行为反馈到用户界面,使得界面和数据保持一致。更新`state`会导致重新渲染用户界面,即用户界面会随着`state`的变化而变化。

##### 特点

它只存在于组件的内部,可以通过`setState()`方法来修改

##### 使用注意点

不要直接显示地更新组件的状态

``` JavaScript
//这种方式不会重新渲染组件
this.state.msg="Hi";

//应该使用这种方式
this.setState({
  msg:'Hi'
})
```

状态的更新可能是异步的，`React`可以将多个`setState()`调用合并成一个调用来提高性能。因为`this.props`和`this.state`可能是异步更新的，所以不应该依赖这些值来计算下一个状态。


``` JavaScript
//不建议这么使用
this.setState({
    counter: this.state.counter + 1 
})

this.setState({
    counter: this.state.counter + 1 
})

//建议这么使用
this.setState((prevState)=>{
    return {counter: prevState.counter + 1 }
})

this.setState((prevState)=>{
    return {counter: prevState.counter + 1 }
})
```

`setState()`用法

``` JavaScript
setState(updater[,callback]);
```

第一个参数`updater`可以直接传递一个<strong>对象</strong>，也可以传递一个<strong>函数</strong>，但是这个函数还是得返回一个对象，同时这个函数会带两个参数，分别是前一个状态`prevState`和属性`props`。

``` JavaScript
//对象
this.setState({
  msg:'hello'
})

//函数
this.setState((prevState,props)={
  return {msg:'Hi'}
})

```

第二个参数是一个可选的回调函数,其将会在`setState`执行完成同时组件被重新渲染之后执行。












