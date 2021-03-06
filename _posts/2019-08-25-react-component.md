---
layout: post
title: 'react进阶之组件抽象与复用'
date: '2019-09-24'
tags: 'react'
author: 'guozhaodong'
---

# react进阶之组件抽象与复用


### 关于组件抽象与复用

不管是什么应用、什么项目都需要对公共代码进行抽取，提取出公共的视图或行为逻辑，我们可称之为模块或组件。其对外部使用者来说是一个黑盒，不过可以通过提供的相关参数或接口进行调用。

抽取公共组件其实也是我们经常说的组件化、模块化开发的一部分。

`react`完全基于组件化开发，所以其组件化的程度会更高，对于项目中可复用的东西我们都可以将其抽取出来以方便我们的开发，对于开发效率的提升是可观的，同时对于项目后期的维护很有帮助。

### 抽取公共组件的注意点

在抽象公共功能时，要尽可能通用、灵活，高内聚、低藕合，对于React 组件可以总结出
下面一些经验和规则。

- 组件应该只通过属性输入，避免通过context，更要避免读取全局变量、系统1/0 等。
- 组件的属性应该有默认值，这样使用起来更简单，在大多数情况下不需要传递很多参数。
- 组件的属性应该使用简单值， 尽量避免使用对象等复杂的数据结构，简单的属性值更容
易理解和维护。
- 组件要足够健壮，考虑边界异常情况，要做好属性的类型验证， 不可缺省。
- 组件要有灵活的适用能力，不要限制使用环境，而要适用于一切环境， 比如组件不要给
自己设置宽度，要适用于所有的宽度。

### react公共组件封装方法

#### 组合与继承

react官网和社区推荐的方式是组合，继承的话需要慎用，除非是不得不用的。

React希望组件是按照最小可用的思想来进行封装的，理想的说，就是一个组件只做一件的事情，且把它做好，DRY。在OOP原则，这叫单一职责原则。如果要对组件增强，首先应该先思路这个增强的组件需要用到哪些功能，这些功能由哪些组件提供，然后把这些组件组合起来。

#### 组合

##### 包含关系 

有时候组件内部是什么样子我们并不知道，只是外壳相同，这个时候你可以考虑通过`props.children`传递它的内容到渲染结果中

`Panel`组件

![image](/assets/img/react-component/TM20190925162458.png)

``` JavaScript
//编写组件
class Panel extends React.Component{
  render(){
    let titleStr = null,
        extraStr = null;
    if(this.props.title){
        titleStr = (
            <div className={cx("panel-title")}>
                {this.props.title}
            </div>
        )
    }
    if(this.props.extra){
        extraStr = (
            <div className={cx("panel-extra")}>
                {this.props.extra}
            </div>
        )
    }
    return (
        <div className={cx(['panel',this.props.className])}>
            {
                titleStr&&titleStr?(<div className={cx("panel-hd")}>
                    {titleStr}
                    {extraStr}
                </div>):null
            }
            <div className={cx("panel-bd")}>
                {this.props.children}
            </div>
        </div>
    )
  }
}

//调用组件
class Home extends React.Component{
  render(){
    return(
      <React.Fragment>
        <Panel title="销售收入统计">
            销售收入统计内容
        </Panel>
        <Panel title="采购收入统计">
            采购收入统计内容
        </Panel>
        <Panel title="公告">
            公告内容
        </Panel>
      </React.Fragment> 
    )
  }
}

```


如果我们希望对`props.children`传递的内容做一些限制，比如我们希望它只能传递单个节点，可以使用`React.Children`提供的方法`React.Children.only`来判断。如果传递的是多个节点，我们也可以通过`React.Children.map`方法来遍历节点，以及通过`React.Children.count`计算子节点的个数。


##### 特例关系

``` JavaScript
//编写组件
class SuggestSearch extends React.Component {

    handleChange = (value) => {
        ...
    };

    handleSelect = (value) => {
        ...
    };

    render() {

        return (
            <div className={cx("suggest-search")}>
                <Select
                    showSearch
                    allowClear={true}
                    onChange={this.handleChange}
                    placeholder={this.props.placeholder}
                    className={cx("suggest")}
                    dropdownClassName={cx("suggest-dropdown")}
                    onSelect={this.handleSelect}
                >
                            <Option>
                            ...
                            </Option>
                </Select>
                <Button 
                  type="primary" 
                  icon="search" 
                  className={cx("search-btn")} 
                  onClick={()=>this.props.onSearch && this.props.onSearch(this.state.value?this.state.value:'')}/>
            </div>
        )
    }
}

//调用组件
class List extends React.Component{
  render(){
    return(
      <React.Fragment>
        <SuggestSearch />
      </React.Fragment> 
    )
  }
}
```
`SuggestSearch`组件属于特殊的输入框，它可以根据输入值进行下拉匹配，它是通过`Select`组件和`Button`组件组合而成。

包含关系和特例关系有经常会混合使用，在我们抽取组件的过程中，其实这两种方式及其混合使用是最常用组件抽取手段。

但是这两种方式只能提取组件UI以及UI自带的逻辑，如果我只想复用逻辑代码，可以尝试下面的几种方式。


#### mixins

对于逻辑代码的组合，在之前老版本的`react`使用`createClass`来创建组件中可以通过`mixins`功能对逻辑代码进行抽取。

 ``` JavaScript
import React from 'react'
import ReactDOM from 'react-dom'

const MouseMixin = {
  getInitialState() {
    return { x: 0, y: 0 }
  },

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    })
  }
}

const App = React.createClass({
  // Use the mixin!
  mixins: [ MouseMixin ],
  
  render() {
    const { x, y } = this.state

    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        <h1>The mouse position is ({x}, {y})</h1>
      </div>
    )
  }
})
 ```


`es6` 通过`class`关键字来声明组件的方式逐渐取代了`createClass`的方式，在`react16`之后`mixin`方式不再被支持。

同时`mixins`方式也存在一些问题：

- 难以溯源，`mixins`修改`state`，在组件内部你无法知道`state`从哪来，尤其是有多个`mixins`的情况。
- 命名空间，多个`mixins`修改同一个`state`导致的命名冲突。

#### 高阶组件

不得不承认`react`社区确实很繁荣，高阶组件并不是`react api`的一部分，它是社区开发人员在长期开发中总结出来的一种复用组件逻辑的模式或者技巧。

其实它是借助了`js`中高阶函数的概念

高阶函数是一个函数，它接受函数作为参数或将函数作为输出返回。例如`Array.prototype.map`，`Array.prototype.filter`和`Array.prototype.reduce`是语言中内置的一些高阶函数，第三方的比如我们的防抖与节流，

```JavaScript
function debounce(func, wait = 300) {
  // 缓存一个定时器
  var timer;
  // 这里返回的函数是每次用户实际调用的防抖函数
  // 如果已经设定过定时器了就清空上一次的定时器
  // 开始一个新的定时器，延迟执行用户传入的方法
  return function(...args) {
    if (timer) clearTimeout(timer)
    timer = setTimeout(function() {
      func.apply(this, args)
    }, wait)
  }
}


handleChange = debounce(()=>{
    console.log('用户输入了')   
})

<Input onChange={this.handleChange}/>
```

下面我们来看下[高阶组件](https://bubble2.github.io/2018/10/26/react-hoc.html)

但是，回到之前`mixins`存在的问题，我们想一想，`HOC`有上述的问题么？我们来看下：

- 难以溯源，跟`mixins`不同的是，我们不再纠结`state`的源头，我们现在要纠结的是`HOC`的`props`里提供了些啥...
- 命名空间的冲突，这个问题依然存在，`props`里属性名可能会被多个`HOC`重复使用。


#### `render prop`

`render prop`是在调用组件时，引入一个函数类型的prop ， 这个prop 定义了组件的渲染
方式。我认为其实就是父组件通过回调函数的方式获取子组件的状态的方式。

``` JavaScript

import React from 'react'
import ReactDOM from 'react-dom'
import PropTypes from 'prop-types'

class Mouse extends React.Component {
  static propTypes = {
    render: PropTypes.func.isRequired
  }

  state = { x: 0, y: 0 }

  handleMouseMove = (event) => {
    this.setState({
      x: event.clientX,
      y: event.clientY
    })
  }

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    )
  }
}

const App = React.createClass({
  render() {
    return (
      <div style={{ height: '100%' }}>
        <Mouse render={({ x, y }) => (
          <h1>The mouse position is ({x}, {y})</h1>
        )}/>
        <Mouse>{({ x, y }) => (
          <h1>The mouse position is ({x}, {y})</h1>
        )}</Mouse>
      </div>
    )
  }
})
```

与高阶组件不同的是我们可以根据不同的状态在消费层面对组件的视图进行渲染。

这种方式可以解决前面`mixins`和`HOC`带来的问题。

但是这种方式有一个问题是：

`this.props.render()`或`this.props.children()`渲染的组件部分，我们难以直接使用
`shouldComponnetUpdate`来进行性能优化。因为在每次渲染周期中都声明了一个新的函数，在
使用`shouldComponnetUpdate` 进行`props`浅比较时，都会永远返回`true`

为了解决这个问题，有时你可以定义一个 `prop` 作为实例方法，类似这样

``` JavaScript
renderMouse({X, Y}) {
  return <h1>The mouse position is ({x}, {y})</h1>;
}

  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={this.renderMouse} />
      </div>
    );
  }
```









