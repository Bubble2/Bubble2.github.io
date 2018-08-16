---
layout:     post
title:      "es6常用语法之Function&Object"
date:       "2018-08-08 "
tags:       "js es6"
author:     "guozhaodong"
---

## 函数的默认参数设置

### es5设置默认参数

``` JavaScript
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

``` JavaScript
    function defaultArgFn(x=10){
        console.log(x); 
    }

    defaultArgFn();
    defaultArgFn(2);

    defaultArgFn(false);
    defaultArgFn(0);
```

#### 参数默认值不是原始值

``` JavaScript
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

``` JavaScript
    function defaultArgAndDest({x = 0, y = 0} = {}) {
        console.log(x, y);
    }

    defaultArgAndDest({x: 3, y: 8});  // const {x: x = 0, y: y = 0} = {x: 3, y: 8}
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

``` JavaScript
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

``` JavaScript
    function restFn(x, y, ...params, z){
        console.log(params)
    }

    restFn(1,2,3,4,5)
```

## 箭头函数

``` JavaScript

    //es5
    const fn = function(){
        console.log('I am a fn')
    }
    fn();

    //es6
    const arrowFn = () => {
        console.log('I am a arrowFn')
    }
    arrowFn();
```

### 几种方式

``` JavaScript
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

    //如果函数体返回的是一个对象
    const arrowFn4 = () => ({
        x:1,
        y:2
    });
    arrowFn4();
```

### 箭头函数的一些特性

1、`this`的指向是<b>定义</b>函数时候的上下文环境所定的，不能用`call`,`apply`、`bind`等方法去改变。

``` JavaScript
    const cFunc = () => {
        console.log(this)
    }
    cFunc();
    cFunc.call({a:1});
```

2、不能用`new`关键字实例化，也没有原型

``` JavaScript
    const cFunc = () => {
        console.log('hello')
    }

    new cFunc()

    cFunc.prototype
```

3、不支持`arguments`对象

``` JavaScript
    const cFunc = (x,y) => {
        console.log(x,y);
        console.log('arguments:'+arguments)
    }

    cFunc(1,2)

```

### 关于箭头函数的`this`指向

``` JavaScript
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

``` JavaScript
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
``` JavaScript
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

``` JavaScript
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

``` JavaScript
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

将源对象上的所有对象合并，复制到目标对象上，类似于`jquery`的`$.extend()`，但是注意它只能用来进行浅拷贝

``` JavaScript
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

``` JavaScript
    
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

### 展开运算符

展开运算符是把对象或数组展开成一个序列， 相当于函数rest参数的逆运算

展开对象
``` JavaScript
    const obj = {
        a: 1,
        b: 'x',
        c: 'xiaoming'
    }

    const obj2 = {
        x: 'nihao',
        y: 44,
        ...obj
    }

    console.log(obj2);
```

展开数组

``` JavaScript
    const arr = [2, 76, 33, 8];
    const arr2 = [1, 3, 5, ...arr];
    console.log(arr2);
```

可以用于合并对象，类似于`Object.assign()`

``` JavaScript
    const obj1 = {
        a: 1,
        b: 2
    }
    const obj2 = {
        a: 3,
        d: 4
    }
    const obj3 = {...obj1, ...obj2};
```

和解构一起使用

``` JavaScript
    const {x, y, ...z} = {x: 1, y: 2, a: 33, b: 4} 
    console.log(z)
```

运用：求数组中最大值、最小值

``` JavaScript
    const arr = [1, 3, 4, 55, 66, 31]
    
    //es5用apply可以实现，但是很难让人看懂代码的真实意图
    Math.max.apply(Math, arr);
    
    //es6
    Math.max(...arr);
```















