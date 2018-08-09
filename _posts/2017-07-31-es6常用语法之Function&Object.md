---
layout:     post
title:      "es6常用语法之Function&Object"
date:       "2018-08-08 "
author:     "guozhaodong"
---

# es6常用语法之Function&Object

## 函数的默认参数设置

### es5设置默认参数

``` js
    function defaultArgFn(x){
        x = x || 10;
        console.log(x); 
    }

    //正常是没有问题的
    defaultArgFn();
    defaultArgFn(2);

    //如果传入的参数是false或者可以转换成false的值，结果就不是我们所想要的了
    defaultArgFn(false);
    defaultArgFn(0);
```

### es6设置默认参数

``` js
    function defaultArgFn(x=10){
        console.log(x); 
    }

    defaultArgFn();
    defaultArgFn(2);

    defaultArgFn(false);
    defaultArgFn(0);
```

#### 参数默认值不是原始值

``` js
    //是一个变量
    let a = 20;
    function defaultArgFn(x=a){
        console.log(x); 
    }

    defaultArgFn();

    //也可以是前面一个参数的值
    function defaultArgFn(x, y = x){
        console.log(x,y); 
    }

    defaultArgFn();

    //甚至是一个函数
    function getVal(){
        return 10
    }
    function defaultArgFn(x = getVal()){
        console.log(x); 
    }

    defaultArgFn();

```

## 参数默认值与解构默认值

``` js
    function defaultArgAndDest({x = 0, y = 0} = {}) {
        console.log(x, y);
    }

    defaultArgAndDest({x: 3, y: 8}); 
    defaultArgAndDest({x: 3}); 
    defaultArgAndDest({}); 
    defaultArgAndDest(); 

    // --------------------------------------------

    function defaultArgAndDest({x, y} = {x : 0, y : 0}) {
        console.log(x, y);
    }

    defaultArgAndDest({x: 3, y: 8}); 
    defaultArgAndDest({x: 3}); 
    defaultArgAndDest({}); 
    defaultArgAndDest();
```

1、先看是否使用函数参数的默认值，再看是否使用解构的默认值。

2、将其转换成声明变量的方式去理解。


## `rest`参数

用于获取函数的参数，类似于以前的`arguments`，它会把函数的<b>一个或多个参数序列</b>转换成<b>一个数组</b>

``` js
    //所以参数
    function restFn(...params){
        console.log(params)
    }

    restFn(1,2,3)

    //多余参数
    function restFn(x, y, ...params){
        console.log(params)
    }

    restFn(1,2,3)
```

注意：`rest`参数必须是最后一个参数，否则会报错

``` js
    function restFn(x, y, ...params, z){
        console.log(params)
    }

    restFn(1,2,3,4,5)
```

## 箭头函数

``` js
    const fn = function(){
        console.log('I am a fn')
    }
    fn();

    const arrowFn = () => {
        console.log('I am a arrowFn')
    }
    arrowFn();
```

### 几种方式

``` js
    //正常参数放在圆括号里，函数体放在箭头后面的花括号里
    const arrowFn1 = (x) => {
        return x
    }
    arrowFn1(1);

    //如果参数只有一个，圆括号可以省略
    const arrowFn2 = x => {
        return x
    }
    arrowFn2(1);

    //如果函数体只有一行代码，可以直接省略掉花括号，把代码写到一行，同时会直接return函数体 
    const arrowFn3 = x => x
    arrowFn3(1)

    //
    const arrowFn4 = () => ({
        x:1,
        y:2
    });
    arrowFn4();
```

### 箭头函数的一些特性

· `this`的指向是<b>定义</b>函数时候的上下文环境所定的，不能用`call`,`apply`、`bind`等方法去改变。

``` js
    const cFunc = () => {
        console.log(this)
    }
    cFunc();
    cFunc.call({a:1});
```

· 不能用`new`关键字实例化，也没有原型

``` js
    const cFunc = () => {
        console.log('hello')
    }

    new cFunc()

    cFunc.prototype
```

· 不支持`arguments`对象

``` js
    const cFunc = (x,y) => {
        console.log(x,y);
        console.log('arguments:'+arguments)
    }

    cFunc(1,2)

```

### 关于箭头函数的`this`指向

``` js
    const obj1 = {
        title:'xiaoming',
        sayTitle:function(){
            console.log(this.title)
        }
    }

    obj1.sayTitle();

    // -----------------------------
    const obj2 = {
        title:'xiaoming',
        sayTitle:()=>{
            console.log(this.title)
        }
    }

    obj2.sayTitle();

```

``` js
     function Func(){
        this.a = 1;
        const bFunc = function() {
            console.log('this.a:'+this.a)
        }
        return bFunc;
    }

    const func = new Func()
    func();

    // -----------------------------
    function Func(){
        this.a = 1;
        const bFunc = () => {
            console.log('this.a:'+this.a)
        }
        return bFunc;
    }

    const func = new Func()
    func();
```

## 对象属性和方法简写

### 属性的简写
``` js
    //es5
    function getVal(x, y){
        return {
            x: x,
            y: y
        }
    }
    getVal(1, 2)

    //es6
    function getVal(x, y){
        return {
            x,
            y
        }
    }
    getVal(1, 2)

```

### 方法的简写

``` js
    //es5
    const person = {
        name: 'xiaoming',
        sayName: function(){
            console.log(this.name)
        }
    }

    //es6
    const person = {
        name: 'xiaoming',
        sayName(){
            console.log(this.name)
        }
    }
```

## 计算属性名

``` js
    //es5
    const obj = {
        firstName: 'jj',
        'last Name': 'ss',
    };
    
    //es6
    const fullName = 'full Name';
    const obj = {
        [fullName]:'full name',
        [fullName+'abc']:'full name abc'
    }
``` 

## 对象新增方法

### `Object.assign()`

将源对象上的所有对象合并，复制到目标对象上，类似于`jquery`的`$.extend()`

``` js
    const sourceObj1 = {
        a: 1
    }

    const sourceObj2 = {
        a: 2,
        b: 3
    }

    const targetObj = {
        title: 'hello'
    } 

    Object.assign(targetObj, sourceObj1, sourceObj2);

    console.log(targetObj);
    console.log(sourceObj1);
    console.log(sourceObj2);

```

### `Object.keys()`(es5已经有的属性)、`Object.values()`、`Object.entries()`

``` js
    
    const obj = {
        a: 1,
        b: 'www',
        c: 'hello'
    }

    //Object.keys()返回一个包含对象所有属性名的数组
    Object.keys(obj)

    //Object.values()返回一个包含对象所有属性值的数组
    Object.values(obj)

    //Object.entries()返回一个包含对象所有属性名和属性值的二维数组
    Object.entries(obj)
```













