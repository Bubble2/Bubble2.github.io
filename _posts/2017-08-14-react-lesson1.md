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
``` js
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
``` js
<div></div>
<span></span>
```

2、大写字母开头的标签表示一个组件元素
``` js
<MyComponent>我是一个react组件</MyComponent>
```

##### 最外层只能有一个标签
``` js
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

``` js
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

``` js
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

``` js
<MyComponent foo={1+2}></MyComponent>

<MyComponent foo={window.name?1:2}></MyComponent>
```

##### 传递字符串常量

``` js
//下面两种方式是相等的
<MyComponent msg="hello"></MyComponent>
<MyComponent msg={"hello"}></MyComponent>

```

##### 传递布尔值

``` js
//默认不传则为true，一下两种方式相等
<MyComponent foo></MyComponent>//不建议
<MyComponent foo={true}></MyComponent>//建议

```

##### 扩展属性

``` js
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

``` js
<MyComponent>Hello</MyComponent>
```

##### 可以接受子组件

``` js
<MyComponent>
  <MyFirstComponent />
  <MySecondComponent />
</MyComponent>
```

##### 可以接受`JavaScript`表达式

``` js
<MyComponent>{'Hello'}</MyComponent>

<MyComponent>{1+2}</MyComponent>

<MyComponent>{window.name?<MyFirstComponent />:<MySecondComponent />}</MyComponent>
```

##### 可以通过`props.children`来访问该组件的孩子

``` js
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

``` js
//一下几种方式都是等价的
<div />
<div></div>
<div>{true}</div>
<div>{false}</div>
<div>{null}</div>
<div>{undefined}</div>
```


除了`false`以外,0、""、null、undefined、NaN,都不会认为是假，即不会像原生`js`一样
``` js
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

### React数据流





