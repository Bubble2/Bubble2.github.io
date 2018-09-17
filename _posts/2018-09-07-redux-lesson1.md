---
layout:     post
title:      "redux入门"
date:       "2018-09-07"
tags:        "react redux"
author:     "guozhaodong"
---


# `redux`入门

## 为什么会出现`redux`？

通过前面的`React`的课程我们了解到，**`React`通过组件状态(`state`)的变化来更新组件对应的用户界面**，这些状态可能包括服务器的数据、缓存数据、本地生成尚未持久化到服务器的数据，也包括`UI`的状态，如激活的路由、被选中的标签、是否显示加载中效果等等。
    
当项目比较小或者业务比较简单的时候，通过单纯地`javascript`去管理它们还是能应付得过来的，但是如果项目变得庞大且复杂的时候，我们去**管理这些状态就会变得非常困难**。而且我们**没有办法去追踪这些状态的变化**。

这个时候我们就需要一个专门的东西去管理组件的状态，`redux`就是一个专门用来管理状态的库。

## `redux`的核心概念

`redux`本身很简单

- 它通过普通的对象去描述`state`。
- 通过`action`去描述`state`的变化。
- 通过`reducer`去改变`state`。
- 通过`store`去存储所有的`state`并且把`action`,`reducer`联系到一起。

### `action`

`action`是一个普通对象，必须包含`type`属性，其它属性一般就是进行操作需要的数据。

`action`：
``` JavaScript
    {
        type: 'ADD_TODO',
        text: 'Hello World!'
    }
```

#### `action`创建函数

用来创建`action`的函数，其实就是一个简单的函数，返回结果是一个普通的对象，也就是`action`对象

``` JavaScript
    const addToDo = (text) => ({
        type: 'ADD_TODO',
        text: 'Hello World!'
    })
```

### `reducer`

`reducer`是一个纯函数，接受两个参数，第一个参数是旧的`state`，第二个参数是`action`对象；函数体是根据`action.type`的值进行相应的操作，然后返回一个新的`state`。

`reducer`：
```JavaScript
    const todos = (state = [], action) => {
        switch(action.type){
            case 'ADD_TODO':
                return [
                    ...state,
                    {
                        text: action.text,
                        completed: false
                    }
                ] 
            default:
                return state;
        }
    }
```

### `store`

`store`是一个对象，用来存储应用的所有`state`。

通过`redux`提供的`createStore()`方法来创建一个store，这个方法可以接受三个参数，返回一个`store`对象；第一个参数是一个`reducer`，这个也是我们最常用的，后面两个参数暂不做介绍，感兴趣可以自己去官网看下[createStore](http://www.redux.org.cn/docs/api/createStore.html)

它提供了三个方法：
- `getState()`：用于获取`store`里存储的`state`。
- `dispatch(action)`：`store`里的`state`不能直接去修改，必须通过触发`action`来进行修改，这个方法就是用来触发`action`的。
- `subscribe(listener)`：订阅`store`里`state`的变化，当`state`变化后会执行订阅的`listener`，同时还会返回一个函数用于取消订阅。

## `redux`数据流

![image](/assets/img/redux/redux-flow.jpg)

1. 当用户点击某个按钮的时候，我们会去`dispatch(action)`;
2. 然后这个`action`会转发给`store`，`store`带着存储的`state`和`action`调用对应的`reducer`；
3. `reducer`执行完成后会返回一个新的`state`给`store`；
4. 因为我们之前已经订阅了`UI`，所以当`state`变化时会触发`UI`重新渲染。

但其实`store`内的一些操作我们是看不到的，我们能看到的就是

用户`dispatch(action)` => `reducer`更新`state` => 页面重新渲染

## `搭配react来个小栗子热下身`

`CounterApp.js`
``` javascript
import React from 'react';
import ReactDOM from 'react-dom';
import {createStore, combineReducers} from 'redux';
import './index.css';
import Counter, {countNum} from './Counter';

//创建store
const store = createStore(combineReducers({countNum}))

//订阅react渲染方法
store.subscribe(render);

render()
function render(){
    ReactDOM.render(<Counter store={store}/>,
        document.getElementById('root'))
}
```

`Counter.js`
``` javascript
import React, { Component } from 'react';

//action
const increase = () => ({
  type: 'INCREASE'
});

const decrease = () => ({
  type: 'DECREASE'
});

//reducer
export const countNum = (state = 0, action) => {
  switch(action.type){
      case 'INCREASE':
          return state+1;
      case 'DECREASE':
          return state-1;
      default:
          return state;
  }
}

//Counter
class Counter extends Component {
  constructor(props){
    super(props)
    this.handleReduce = this.handleReduce.bind(this);
    this.handleAdd = this.handleAdd.bind(this);
  }
  handleReduce(){
    const {store} = this.props;
    store.dispatch(decrease());
  }
  handleAdd(){
    const {store} = this.props;
    store.dispatch(increase());
  }
  render() {
    const {getState} = this.props.store
    return (
      <div className="wrap">
        <button onClick={this.handleReduce}>-</button>
        <span>{getState()}</span>
        <button onClick={this.handleAdd}>+</button>
      </div>
    );
  }
}

export default Counter;
```

### 使用`react-redux`来连接`redux`和`react`

`redux`和`react`之间没有关系，`redux`可以搭配`react`、`angular`、`Ember`、`jQuery `甚至纯`JavaScript`。

尽管如此，`redux`和`react`搭配还是很好的。

安装`react-redux`库

``` JavaScript
npm install --save react-redux
```

之前我们把`store`作为属性传递给我们的`Counter`组件，如果组件比较简单还能勉强应付，但是如果状态比较多，且组件层级比较深，难道我们要把这些东西直接都放在属性上传递到子组件吗？而且如果组件层级比较深，难道要一级一级地向下传递？


不必这么费事，`react-redux`帮我们解决了这些问题，它提供了两个


#### `connect()`
`connect()`方法，这个方法接受四个可选参数`mapStateToProps`、`mapDispatchToProps`、`mergeProps`、`options`，这边只给大家讲前面两个参数，

`mapStateToProps(state, [ownProps])`：用来从`store`中获取`state`。

用法：是一个函数，如果使用了它，组件将会监听`store`的变化，只要`store`发生改变，这个函数就会被调用，该函数必须返回一个纯对象，这个对象会和组件的`props`合并。
第二个参数是传递到组件的`props`，所有如果有新的`props`，这个方法也会被重新调用。

``` JavaScript
    const mapStateToProps = state => {
        return {
            countNum: state.countNum
        }
    }
```

`mapDispatchToProps(dispatch, [ownProps])`：用来`dispatch`action。

用法：是一个函数，如果使用了它，返回一个对象，里面的属性就是被`dispatch`过的`action`。

```JavaScript
    const mapDispatchToProps = dispatch => {
        return {
            increase: () => {
                dispatch(increase())
            }
        }
    }
```

我们还可以使用`redux`给我们提供的一个辅助函数`bindActionCreators`来自动`dispatch`我们的`action`
```JavaScript
    const mapDispatchToProps = dispatch => {
        return bindActionCreators({
            increase
        },dispatch)
    }
```

#### `Provider()`
`Provider()`这个方法可以使得各个层级的组件的`connect()`方法能够获取到`store`对象

我们用`react-redux`来改造下`Counter`的代码

`CounterApp.js`
```JavaScript
import React from 'react';
import ReactDOM from 'react-dom';
import {createStore, combineReducers} from 'redux';
import {Provider} from 'react-redux';
import './index.css';
import Counter, {countNum} from './Counter';

//创建store
const store = createStore(combineReducers({countNum}))

ReactDOM.render(<Provider store={store}>
    <Counter/>
</Provider>,
document.getElementById('root'))
```

`Counter.js`
``` javascript
import React, { Component } from 'react';
import { bindActionCreators } from 'redux';
import {connect} from 'react-redux';

//action
const increase = () => ({
  type: 'INCREASE'
});

const decrease = () => ({
  type: 'DECREASE'
});

//reducer
export const countNum = (state = 0, action) => {
  switch(action.type){
      case 'INCREASE':
          return state+1;
      case 'DECREASE':
          return state-1;
      default:
          return state;
  }
}

//Counter
class Counter extends Component {
  constructor(props){
    super(props)
    this.handleReduce = this.handleReduce.bind(this);
    this.handleAdd = this.handleAdd.bind(this);
  }
  handleReduce(){
    this.props.decrease();
  }
  handleAdd(){
    this.props.increase();
  }
  render() {
    return (
      <div className="wrap">
        <p>demo2</p>
        <button onClick={this.handleReduce}>-</button>
        <span>{this.props.countNum}</span>
        <button onClick={this.handleAdd}>+</button>
      </div>
    );
  }
}

const mapStateToProps = state => {
  return {
    countNum: state.countNum
  }
}

const mapDispatchToProps = dispatch => {
  return bindActionCreators({
    increase,
    decrease
  }, dispatch)
}

export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```








