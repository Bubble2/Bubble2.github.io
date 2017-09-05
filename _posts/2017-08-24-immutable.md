---
layout:     post
title:      "2017-09-07-immutable"
date:       "2017-09-07 "
author:     "guozhaodong"
---

### 为什么会出现`immutable`

在`javascript`中对象都是引用类型的，新的对象对象引用了原始对象，改变新对象原始对象也会改变。

``` js
let foo = {a:1}
let bar = foo;
bar.a=2;
console.log('bar.a:'+bar.a);
console.log('foo.a:'+foo.a);
```

为了解决这个问题我们一般采用的是<strong>浅拷贝</strong>(`shadowCopy`)或<strong>深拷贝</strong>(`deepCopy`)来避免这种问题，但是这种做`cpu`和内存的浪费。

这时`Immutable`的出现很好地解决了这个问题，既能对数据进行深拷贝，同时也不会很耗性能。


### `immutable`是什么？

持久化数据或不可变数据，可以理解为另一种数据结构的库。通过`Immutable`创建出来的数据，一旦被创建就不能再被更改；对`Immutable`对象的任何添加、修改、删除操作都会返回一个新的`Immutable`对象。

``` js
import {Map} from 'immutable';

//Map
const map1 = Map({a:1});
const map2 = map1.set('a',2);
console.log('map1.a:'+map1.get('a'))
console.log('map2.a:'+map2.get('a'))
```

### `immutable`的实现原理

`Immutable`的实现原理是`Persistent Data Structure`（持久化数据结构），即使用旧数据创建新数据时，会保证旧数据可用且不变，同时为了避免`deepCopy`把所有节点都复制一遍带来的性能损耗，`Immutable`使用了`Structural Sharing`（结构共享），即如果对象数中一个节点发生变化，只修改这个节点和受它影响的父节点，其它节点则进行共享，如下图：

![image](../img/immutable/TB1zzi_KXXXXXctXFXXbrb8OVXX-613-575.gif)


### `immutable`的`api`介绍

`immutable`内部实现了一整套的持久化数据结构，

- 数据类型，比如`Collection`、`List`、`Map`、`OrderedMap`、`Set`、`OrderedSet`、`Stack`、`Record`、`Seq`。

- 全局方法(挂在`Immutable`对象下面的)，`fromJs()`、`is()`、`hash()`、`isImmutable()`、`isCollection`、`isKeyed()`、`isIndexed()`、`isAssociative()`、`isOrdered()`、`isValueObject()`、`Range()`、`Repeat()`

- 实例方法（挂在`Immutable Data`上的），如`map()`、`filter()`、`groupBy()`、`reduce()`、`find()`等等，方法非常多具体查看官方`api`。

比较重要且用的比较多的数据类型：

- `List`：有序可重复的列表，对应`Array`。
- `Map`：键值对集合，对应`Object`，`ES6`也有专门的`Map`对象。
- `Set`：无须且不可重复的列表，`ES6`中也有专门的`Set`对象。

### 在`React`中使用`immutable`

#### 为什么要在`React`中使用？

熟悉`React.js`的都应该知道,`React.js`是一个`view`层的框架，它使用了`virtual dom`,同时通过`diff`来比较`dom`是否变化，判断是否需要更新`dom`，以此实现了高效的`dom`更新。

有两种情况：

- 当`state`更新时，但是数据没有变化，`react`也仍然会对`virtual dom`去做`diff`，这样就产生了浪费。

- 一个父组件下面有多个子组件时，如果父组件发生了`re-render`，那么它下面的所有子组件也会`re-render`，其实很多子组件的`props`和`state`是没有变化的，所有这样也会造成资源浪费。


#### 如何使用`immutable`优化`react`?

`React`组件生命周期中有一个钩子函数`shouldComponentUpdate`方法,在这里面对数据进行比较，如果数据没有变化就不继续往下执行。

没有优化之前
``` js
import React from 'react';
import ReactDOM from 'react-dom';

class ParentComponent extends React.Component{
    constructor(props){
        super(props);
        this.clickHandle=this.clickHandle.bind(this);
        this.state={
            a:{
                b:1
            },
            d:{
                e:2
            }
        }
    }
    clickHandle(){
        console.log('我点击了一次按钮')
        this.setState({
            a:{
                b:3
            }
        })
    }
    render(){
        
        return(
            <div>
                 <ChildComponent1 {...this.state.a}/>
                 <ChildComponent2 {...this.state.d}/>
                 <button onClick={this.clickHandle}>点击我改变父组件的state</button>
            </div>
        )
    }
}

class ChildComponent1 extends React.Component{
    constructor(props){
        super(props);
    }
    render(){
        console.log('子组件1的render被执行了')
        return(
            <div>
                我是子组件1：
                {this.props.b}
            </div>
        )
    }
}
class ChildComponent2 extends React.Component{
    constructor(props){
        super(props);
    }

    render(){
        console.log('子组件2的render被执行了')
        return(
            <div>
                我是子组件2：
                {this.props.e}
            </div>
        )
    }
}

ReactDOM.render(<ParentComponent />,document.getElementById('root'))
```

方法一：可以通过<a href="https://facebook.github.io/react/docs/pure-render-mixin.html" target="_blank">`PureRenderMixin`</a>来做比较，但是这种方式只能做浅比较

``` js
import React from 'react';
import ReactDOM from 'react-dom';
import PureRenderMixin from 'react-addons-pure-render-mixin';

class ParentComponent extends React.Component{
    constructor(props){
        super(props);
        this.clickHandle=this.clickHandle.bind(this);
        this.state={
            a:{
                b:1
            },
            d:{
                e:2
            }
        }
    }
    clickHandle(){
        console.log('我点击了一次按钮')
        this.setState({
            a:{
                b:3
            }
        })
    }
    render(){
        
        return(
            <div>
                 <ChildComponent1 {...this.state.a}/>
                 <ChildComponent2 {...this.state.d}/>
                 <button onClick={this.clickHandle}>点击我改变父组件的state</button>
            </div>
        )
    }
}

class ChildComponent1 extends React.Component{
    constructor(props){
        super(props);
        this.shouldComponentUpdate = PureRenderMixin.shouldComponentUpdate.bind(this);
    }
    render(){
        console.log('子组件1的render被执行了')
        return(
            <div>
                我是子组件1：
                {this.props.b}
            </div>
        )
    }
}
class ChildComponent2 extends React.Component{
    constructor(props){
        super(props);
        this.shouldComponentUpdate = PureRenderMixin.shouldComponentUpdate.bind(this);
    }

    render(){
        console.log('子组件2的render被执行了')
        return(
            <div>
                我是子组件2：
                {this.props.e}
            </div>
        )
    }
}

ReactDOM.render(<ParentComponent />,document.getElementById('root'))
```

方法二：通过`immutable`优化

``` js
import React from 'react';
import ReactDOM from 'react-dom';
import {is,fromJS } from 'immutable';

class ParentComponent extends React.Component{
    constructor(props){
        super(props);
        this.clickHandle=this.clickHandle.bind(this);
        this.state={
            a:{
                b:1
            },
            d:{
                e:2
            }
        }
    }
    clickHandle(){
        console.log('我点击了一次按钮')
        this.setState({
            a:{
                b:3
            }
        })
    }
    render(){
        
        return(
            <div>
                 <ChildComponent1 {...this.state.a}/>
                 <ChildComponent2 {...this.state.d}/>
                 <button onClick={this.clickHandle}>点击我改变父组件的state</button>
            </div>
        )
    }
}

class ChildComponent1 extends React.Component{
    constructor(props){
        super(props);
    }
    
    shouldComponentUpdate(nextProps,nextState){
        const $$nextProps=fromJS(nextProps),
            $$nextState=fromJS(nextState),
            $$thisProps=fromJS(this.props),
            $$thisState=fromJS(this.state);
        return !(this.props===nextProps|| is($$thisProps,$$nextProps)) || !(this.state === nextState || is($$thisState,$$nextState))
    }

    render(){
        console.log('子组件1的render被执行了')
        return(
            <div>
                我是子组件1：
                {this.props.b}
            </div>
        )
    }
}
class ChildComponent2 extends React.Component{
    constructor(props){
        super(props);
    }

    shouldComponentUpdate(nextProps,nextState){
        const $$nextProps=fromJS(nextProps),
            $$nextState=fromJS(nextState),
            $$thisProps=fromJS(this.props),
            $$thisState=fromJS(this.state);
        return !(this.props===nextProps|| is($$thisProps,$$nextProps)) || !(this.state === nextState || is($$thisState,$$nextState))
    }

    render(){
        console.log('子组件2的render被执行了')
        return(
            <div>
                我是子组件2：
                {this.props.e}
            </div>
        )
    }
}

ReactDOM.render(<ParentComponent />,document.getElementById('root'))
```

### 如何结合`redux`项目使用？




