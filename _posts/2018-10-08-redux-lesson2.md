---
layout:     post
title:      "redux进阶"
date:       "2018-10-08"
tags:        "react redux"
author:     "guozhaodong"
---


# `redux`进阶

## 异步数据流

前面我们的课程中已经介绍了`redux`的基础知识，我们再回顾一下`redux`的数据流，当用户进行某个操作时候，我们会`dispatch`一个`action`，然后`store`会去找到对应`reducer`来更新`state`，`state`的更新会触发用户界面的重新渲染。

#### 如果我们需要发一个`ajax`请求，那么我们应该把代码放在哪个地方合适呢？

**方法一：**

也是最原始的做法，我们可以在`UI`组件中去发起异步请求，然后在数据返回的回调函数中去`dispatch(action)`，

优点： 简单易行。

缺点： 这样做会把`view`层和`model`层混杂在一起，耦合度太高，后期维护起来很困难。

[栗子请戳这里](https://github.com/Bubble2/redux-lesson-demo/tree/master/demo3)

**方法二：**

把它放在`redux`数据流的某一个环节中，使用中间件，可以先来看下[中间件](#中间件)。

优点：`view`层和`model`层相互独立，易于后期代码的维护和扩展。
缺点：需要引入中间件，操作起来麻烦些

目前市面上有多款中间件，比如`redux-thunk`，`redux-promise`，`redux-saga`等都可以解决异步数据的问题。

下面我们来看下`redux-thunk`是怎么实现异步数据流的

实现原理：通过包装了`store`的`dispatch`方法，以此来让你`dispatch`一些除了普通`action`以外的东西。普通`action`本身只能是普通的对象，通过添加`redux-thunk`后，`action`可以是一个函数，然后可以通过回调函数来触发普通的`action`

``` JavaScript
    //同步数据流
    dispatch({
        type: 'RENDER_DATA'
    })

    //异步数据流
    dispatch(function(dispatch){
        $.ajax(url,function(){
            dispatch({
                type: 'RENDER_DATA'
            })
        })
    })
```

[栗子请戳这里](https://github.com/Bubble2/redux-lesson-demo/tree/master/demo4)


## 中间件

中间件就是一个函数，对`store.dispatch`方法进行了改造，在发出`action`和执行`reducer`这两部之间，添加了其它功能。

``` JavaScript
    let next = store.dispatch;
    store.dispatch = function dispatchAndLog(action){
        console.log('dispatching', action);
        next(action);
        console.log('next state', store.getState());
    }
```
上面代码对`store.dispatch`进行了重定义，在发送`action`前后添加了打印功能，这是中间件的雏形。

加了中间件后我们再来看下`redux`的数据流

![image](/assets/img/redux/redux-flow-middleware.jpg)

#### 关于中间件的使用

中间件有很多，市面上有很多别人已经写好的，当然你也可以自己去写。比如`redux-logger`、`redux-thunk`

以`redux-logger`为例看下中间件的使用
``` JavaScript
    import {createStore, applyMiddleWare} from 'redux';
    import createLogger from 'redux-logger';
    const logger = createLogger();

    const store = createStore(
        reducer,
        applyMiddleWare(logger) //如果还有其它中间件logger必须放在最后
    )
```

`createStore`方法它可以传递三个参数，第一个参数是一个`reducer`，第二个参数是初始状态，第三个参数是一个`store`的增强器，也可以是我们的中间件


``` JavaScript
    createStore(
        reducer,
        [preloadedState],
        enhancer
    )
```

`applyMiddleware`方法，它的作用是将所有中间件组成一个数组，然后依次执行。

``` JavaScript
    applyMiddleware(...middlewares);
```

感兴趣地可以去看下它的源码

``` JavaScript
/**
 * @param {...Function} middlewares The middleware chain to be applied.
 * @returns {Function} A store enhancer applying the middleware.
 */
export default function applyMiddleware(...middlewares) {
  return createStore => (...args) => {
    const store = createStore(...args)
    let dispatch = () => {
      throw new Error(
        `Dispatching while constructing your middleware is not allowed. ` +
          `Other middleware would not be applied to this dispatch.`
      )
    }

    const middlewareAPI = {
      getState: store.getState,
      dispatch: (...args) => dispatch(...args)
    }
    const chain = middlewares.map(middleware => middleware(middlewareAPI))
    dispatch = compose(...chain)(store.dispatch)

    return {
      ...store,
      dispatch
    }
  }
}

```

```JavaScript
/**
 * @param {...Function} funcs The functions to compose.
 * @returns {Function} A function obtained by composing the argument functions
 * from right to left. For example, compose(f, g, h) is identical to doing
 * (...args) => f(g(h(...args))).
 */

export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }

  if (funcs.length === 1) {
    return funcs[0]
  }

  return funcs.reduce((a, b) => (...args) => a(b(...args)))
```

## `Immutable`数据















