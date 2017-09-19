---
layout:     post
title:      "2017-09-07-immutable"
date:       "2017-09-07 "
author:     "guozhaodong"
---

### 为什么会出现`immutable`

在`javascript`中对象都是引用类型的，新的对象引用了原始对象，改变新对象原始对象也会改变。

``` js
let foo = {a:1}
let bar = foo;
bar.a=2;
console.log('bar.a:'+bar.a);
console.log('foo.a:'+foo.a);
```

为了解决这个问题我们一般采用的是<strong>浅拷贝</strong>(`shadowCopy`)或<strong>深拷贝</strong>(`deepCopy`)来避免这种问题，但是这种做法会导致`cpu`和内存的浪费。

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

![image](/img/immutable/TB1zzi_KXXXXXctXFXXbrb8OVXX-613-575.gif)

深拷贝
![image](/img/immutable/1234637-4f59ca4e0ff61af4.png)

浅拷贝
![image](/img/immutable/1234637-f14424d82fc9855e.png)

`immutable`方式
![image](/img/immutable/1234637-a58fde3fc627a0dd.png)


### `immutable`的`api`介绍

`immutable`内部实现了一整套的持久化数据结构

- 数据类型，比如`Collection`、`List`、`Map`、`OrderedMap`、`Set`、`OrderedSet`、`Stack`、`Record`、`Seq`。

- 全局方法(挂在`Immutable`对象下面的)，`fromJS()`、`is()`、`hash()`、`isImmutable()`、`isCollection`、`isKeyed()`、`isIndexed()`、`isAssociative()`、`isOrdered()`、`isValueObject()`、`Range()`、`Repeat()`等。

- 静态方法（挂在`Immutable Data`上的），如`List.isList()`、`List.of()`、`Map.isMap()`等

- 实例方法（挂在`Immutable Data`的实例上的），如`map()`、`filter()`、`groupBy()`、`reduce()`、`find()`等等，方法非常多具体查看官方`api`。

比较重要且用的比较多的数据类型：

- `List`：有序可重复的列表，对应`Array`。
- `Map`：键值对集合，对应`Object`，`ES6`也有专门的`Map`对象。
- `Set`：无序且不可重复的列表，`ES6`中也有专门的`Set`对象。

简单介绍几个常用的方法

`fromJS()`：接受原生`js`类型数据，返回`immutable`类型数据。

``` js
import Immutable from 'immutable';
console.log(Immutable.fromJS([1,{a:2},{b:[3,4,5]}]))
```

`is()`：接受两个`immutable`数据,返回一个布尔值，表示这两个数据是否相等

``` js
import Immutable from 'immutable';
const obj1 = Map({a:1,b:2});
const obj2 = Map({a:1,b:2});
console.log(obj1==obj2);
console.log(Immutable.is(obj1,obj2));
```

`hash()`：接受一个`immutable`数据,返回一个31位的整数，这个值就是每一个`immutable`数据的唯一表示，用来判断两个数据是否相等的。

``` js
import Immutable from 'immutable';
const obj1 = Map({a:1,b:2});
const obj2 = Map({a:1,b:2});
console.log('obj1.hash:'+Immutable.hash(obj1));
console.log('obj2.hash:'+Immutable.hash(obj2));
```

`List()`：接受原生`js`的`Array`，返回`immutable`的`List`

``` js
import Immutable from 'immutable';
console.log(Immutable.List([1,2,[3,4]]))
```

`Map()`：接受原生`js`的`Object`，返回`immutable`的`Map`

``` js
import Immutable from 'immutable';
console.log(Immutable.Map({a:1,b:{c:2}}))
```

`toJS()`：接受`immutable`类型数据，返回原生`js`类型数据，与`fromJS()`相反。

``` js
import Immutable from 'immutable';
const data1 = Immutable.fromJS([1,2,3])
console.log(data1.toJS())
```

`set()`：接受两个参数，第一个是`List`的索引值或者`Map`的`key`值，第二个是需要设定的值；返回一个新的`List`或者`Map`。

``` js
import Immutable from 'immutable';
const list = Immutable.List([1,2,3])
console.log(list.set(3,4))

const map = Immutable.Map({a:1,b:2,c:3})
console.log(map.set('d',4))
```

`get()`：接受一个参数，`List`的索引值或者`Map`的`key`值

``` js
import Immutable from 'immutable';
const list = Immutable.List([1,2,3])
console.log(list.get(2))  //3

const map = Immutable.Map({a:1,b:2,c:3})
console.log(map.get('a'))  //1
```

用一张图来罗列下`immutable`数据之前的继承关系

![image](/img/immutable/TM20170906144039.png)


### 在`React`中使用`immutable`

#### 为什么要在`React`中使用？

一、性能的提升

1、对复杂数据进行操作时，如果比较深层次的数据发生变化，我们只能用深拷贝，但是这样很耗性能，所以`immutable`就派上用场了。

2、熟悉`React.js`的都应该知道,`React.js`是一个`view`层的框架，它使用了`virtual dom`,同时通过`diff`来比较`dom`是否变化，判断是否需要更新`dom`，以此实现了高效的`dom`更新。

有两种情况：

- 当`state`更新时，但是数据没有变化，`react`也仍然会对`virtual dom`去做`diff`，这样就产生了浪费。

- 一个父组件下面有多个子组件时，如果父组件发生了`re-render`，那么它下面的所有子组件也会`re-render`，其实很多子组件的`props`和`state`是没有变化的，所有这样也会造成资源浪费。

二、丰富的api

``` js
//普通的的浅拷贝
var state = Object.assign({},state,{
    key:value
});

//immutable
var state = state.set('key',value);
```


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
                b:{
                    f:1
                }
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
                b:{
                    f:3
                }
            },
            d:{
                e:4
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
                {this.props.b.f}
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
                b:{
                    f:1
                }
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
                b:{
                    f:3
                }
            },
            d:{
                e:4
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
                {this.props.b.f}
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
                b:{
                    f:1
                }
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
                b:{
                    f:3
                }
            },
            d:{
                e:4
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
                {this.props.b.f}
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

``` js
class Counter extends React.Component{
    
    render(){
        return(
            <div>
                <h1>{this.props.value}</h1>
                <button onClick={this.props.onIncrease}>+</button>
                <button onClick={this.props.onDecrease}>-</button>
            </div>
        )
    }
}

const reducer = (state=0,action)=>{
    switch(action.type){
        case 'INCREASE':return state+1;
        case 'DECREASE':return state-1;
        default: return state;
    }
}

const store = createStore(reducer);

const render=()=>{
    ReactDOM.render(
        <Counter value={store.getState()} onIncrease={()=>store.dispatch({type:'INCREASE'})} onDecrease={()=>store.dispatch({type:'DECREASE'})}/>,
        document.getElementById('root')
    )
}

render();
store.subscribe(render)
```


``` js
class Counter extends React.Component{
    
    render(){
        return(
            <div>
                <h1>{this.props.value}</h1>
                <button onClick={this.props.onIncrease}>+</button>
                <button onClick={this.props.onDecrease}>-</button>
            </div>
        )
    }
}

const initialState = List([0]);
const reducer = (state=initialState,action)=>{
    switch(action.type){
        case 'INCREASE':return state.update(0,v=>v+1);
        case 'DECREASE':return state.update(0,v=>v-1);
        default: return state;
    }
}

const combineReducer=combineReducers({
    reducer
})

const store = createStore(combineReducer);

const render=()=>{
    ReactDOM.render(
        <Counter value={store.getState().reducer.toJs()} onIncrease={()=>store.dispatch({type:'INCREASE'})} onDecrease={()=>store.dispatch({type:'DECREASE'})}/>,
        document.getElementById('root')
    )
}

render();
store.subscribe(render)
```






