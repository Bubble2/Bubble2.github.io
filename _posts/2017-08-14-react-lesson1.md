---
layout:     post
title:      "2017-08-14-react-lesson1"
date:       "2017-08-14 "
author:     "guozhaodong"
---

## react入门

### 安装

#### 方法一

``` html
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

看起来很像`html`的东西

``` jsx

//传统的html标签
<div>I am a div tag</div>

//组件
<Hello>I am a React Component</Hello>

//标签嵌套
<div>
  <Hello color="blue">I am a nested tag</Hello>
</div>

```

> 一种JavaScript的语法扩展,看起来像模板语言，和我们平常写的html也十分相似，其实完全是js内部实现的。

如果不用jsx也是可以的，jsx本质上是为`React.createElement(Component,props,...children)`方法提供的语法糖。

``` js
<ButtonComponent size="xl" color="primary">我是一个小小的按钮组件</ButtonComponent>
```

上面的jsx语法编译以后就是下面这个样子的
``` jsx
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
``` jsx
<div></div>
<span></span>
```

2、大写字母开头的标签表示一个组件元素
``` jsx
<MyComponent>我是一个react组件</MyComponent>
```

##### 最外层只能有一个标签
``` jsx
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

``` jsx
import React from 'react';

const MyComponents = {
  DatePicker(props){
    return(
      <div>This is a {props.color} Datepicker!</div>
    )
  }
}

const BlueDatePicker = () => {
  return <MyComponents.DatePicker color="blue" />
}
```

##### 注释

因为是`JavaScript`的语法，所以`JavaScript`的注释依然可以使用，但是有点需要注意，在一个组件的内部使用注释要用`{}`包起来

``` jsx
const App = (
  <Nav>
    {/* 这是一个注释 */}

    {/*<h1>h1</h1>*/}

    {
    //<h1>h1</h1>
    }
  </Nav>
)
```

#### 关于元素属性

DOM元素的属性是标准规范属性，但是有两个例外，分别是`class` 和 `for`,这两个属性是`JacaScript`的关键词,我们将其转换,`class`=>`className`,`for`=>`htmlFor`。

组件元素的属性是完全自定义的，也可以理解为实现组件所需要的参数。

##### 传递`JavaScript`表达式

``` jsx
<MyComponent foo={1+2}></MyComponent>

<MyComponent foo={window.name?1:2}></MyComponent>
```

##### 传递字符串常量

``` jsx
//下面两种方式是相等的
<MyComponent msg="hello"></MyComponent>
<MyComponent msg={"hello"}></MyComponent>

```

##### 传递布尔值

``` jsx
//默认不传则为true，一下两种方式相等
<MyComponent foo></MyComponent>//不建议
<MyComponent foo={true}></MyComponent>//建议

```

##### 扩展属性

``` jsx
const App=(props)=>{
  return(
    <div>
      <p>{this.props.title}</p>
      <p>{this.props.name}</p>
    </div>
  )
}

const MyApp=()=>{
  const obj = {
    title:'I am title',
    name:'I am name'
  }
  return(
    <App {...obj}/>
  )
}
```

#### 关于子元素

##### 可以接受字符串常量

``` jsx
<MyComponent>Hello</MyComponent>
```

##### 可以接受子组件

``` jsx
<MyComponent>
  <MyFirstComponent />
  <MySecondComponent />
</MyComponent>
```

##### 可以接受`JavaScript`表达式

``` jsx
<MyComponent>{'Hello'}</MyComponent>

<MyComponent>{1+2}</MyComponent>

<MyComponent>{window.name?<MyFirstComponent />:<MySecondComponent />}</MyComponent>
```

##### 可以通过`props.children`来访问该组件的孩子

``` jsx
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

``` jsx
//一下几种方式都是等价的
<div />
<div></div>
<div>{true}</div>
<div>{false}</div>
<div>{null}</div>
<div>{undefined}</div>
```


除了`false`以外,0、""、null、undefined、NaN,也会认为是假，其中0和NaN会输出自身。
``` jsx
 const isShow=true;
 const TopBar =()=>{
   return(
     <div>我是topbar</div>
   )
 }
 const Top=()=>{
   return(
     <div>
      {isShow&&<TopBar />}
     </div>
   )
 }
```

### React组件

#### 组件的构建方法

方法一、`React.createClass`

最传统、也是兼容性最好的方法，`v0.14`版本之前，官方唯一指定的组件写法

``` jsx
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

``` jsx
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

``` jsx
function Button(){
  getDefaultProps(){
    color:'blue',
    text:'Confirm'
  }
  return (
    <button className={`btn btn-${color}`}>
      <span>{text}</span>
    </button>
  )
}
```

这种方式创建的组件没有`state`和生命周期，组件本身就是上面两种方式的`render`方法，在合适的情况下,比较推荐使用这种方式去构建组件。以上两种方式在构建组件时不会创建实例，但是在调用的时候会创建实例，而无状态组件创建的时始终保持一个实例，避免了不必要的内存检查和内存分配,做到了内部优化。

#### 组件的渲染

``` jsx
function Hello(props){
  return <h1>Hello {props.name}!</h1>
}

ReactDOM.render(
  <Hello name="xiaoming" />,
  document.getElementById('root')
)
```

### `React`数据流

#### `props`

##### 作用 

父级向子级传递数据的一种方式。

##### 特点
<strong>只读的</strong>，组件内部不能修改自己的`props`


#### `state`

##### 作用
将用户的交互行为反馈到用户界面,使得界面和数据保持一致。更新`state`会导致重新渲染用户界面,即用户界面会随着`state`的变化而变化。

##### 特点
它只存在于组件的内部,可以通过`setState()`方法来修改

##### 使用注意点

不要直接显示地更新组件的状态

``` jsx
//这种方式不会重新渲染组件
this.state.msg="Hello";

//应该使用这种方式
this.setState({
  msg:'Hello'
})
```

状态的更新可能是异步的











