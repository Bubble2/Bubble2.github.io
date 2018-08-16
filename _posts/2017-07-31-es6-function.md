---
layout:     post
title:      "es6函数"
date:       "2017-07-31 "
categories: "es6"
author:     "guozhaodong"
---

# es6之函数


### 参数

`es5`及以前
``` JavaScript
function pick(object){
    var result=Object.create(null);
    for(var i=1,len=arguments.length;i<len;i++){
        result[arguments[i]]=object[arguments[i]]
    }
    return result;
}
var  book = {
	title: "Understanding ECMAScript 6",
	author:"Nichalas C. Zakas",
	year: 2017
};

var bookData=pick(book,"author","year");
console.log(bookData.author);  //Nichalas C. Zakas
console.log(bookData.year);  //2017
```



`es6` rest paramter
``` JavaScript
function pick(object,...keys){
	let result=Object.create(null);
	for(let i=0,len=keys.length;i<len;i++){
		result[keys[i]]=object[keys[i]];
	}
	return result;
}

let book = {
	title: "Understanding ECMAScript 6",
	author:"Nichalas C. Zakas",
	year: 2017
};

let bookData=pick(book,"author","year");
console.log(bookData.author);  //Nichalas C. Zakas
console.log(bookData.year);  //2017
```

`es5`求数组最大值

``` JavaScript
console.log(Math.max(1,2,3,4,5));  //5
console.log(Math.max.apply(Math,[1,2,3,4,5])) //5
```

`es6`求数组最大值

``` JavaScript
console.log(Math.max(...[1,2,3,4,5]))  //5
console.log(Math.max(...[1,2,3,4],8))  //8
console.log(Math.max(10,...[2,3,4],6))  //10
```


`es5`非严格模式下，参数值改变后`arguments`值也相应改变
``` JavaScript
function mixArgs(first,second){
	console.log(first===arguments[0]); 
	console.log(second === arguments[1]); 
	first="c";
	second="d";
	console.log(first===arguments[0]); 
	console.log(second === arguments[1]); 
}
mixArgs("a","b");
//true
//true
//true
//true
```

`es5`严格模式下，参数值改变后`arguments`值不会改变
``` JavaScript
function mixArgs(first,second){
    "use strict";
	console.log(first===arguments[0]);  
	console.log(second === arguments[1]);
	first="c";
	second="d";
	console.log(first===arguments[0]); 
	console.log(second === arguments[1]); 
}
mixArgs("a","b");
//true 
//true
//false 
//false
```

函数有了默认参数之后就会和严格模式一样
``` JavaScript
function mixArgs(first,second="b"){
    console.log(arguments[0] + " " + arguments[1]);
	console.log(first===arguments[0]);  
	console.log(second === arguments[1]); 
	first="c";
	second="d";
	console.log(first===arguments[0]);  
	console.log(second === arguments[1]);  
}
mixArgs("a","b");
//a b
//true
//true
//false
//false
mixArgs("a");
//a  undefined
//true
//false
//false
//false
```

### 默认参数基本用法

默认参数基本用户

``` JavaScript
function makeRequest(url,timeout=5000,callback=function(){}){
    console.log(url);
    console.log(timeout);
    console.log(callback);
}
makeRequest("/foo"); //  /foo 5000 function(){}
makeRequest("/foo",200); //  /foo 200 function(){}
makeRequest("/foo",200,function(){
    console.log("hello")
});
//  /foo 200 function(){console.log("hello")}
```

默认参数在中间
``` JavaScript
function makeRequest(url,timeout=5000,callback=function(){}){
    console.log(url);
    console.log(timeout);
    console.log(callback);
}
makeRequest("/foo",undefined,function(){
    console.log("hello");
});
// /foo 5000 function(){console.log("hello")}

makeRequest("/foo",null,function(){
    console.log("hello")
})
// /foo null function(){console.log("hello")}
```

默认参数可以接受表达式，在调用时执行

``` JavaScript
let value=5;
function getValue(){
    return value++;
}
function add(first,second=getValue()){
    console.log(first + second);
}

add(1,1);  //2
add(1);  //6
add(1)  //7
```

``` JavaScript
    let value =5;
    function add(first,second=value){
        console.log(first + second);
    }
    add(1);  //6
```

``` JavaScript
function compare(first,second,third=first===second){
    console.log(third);
}
compare(1,1);  //true
compare(1,2); // false
compare(1,"1")  //fasle
```

默认值可以使用前面的参数

``` JavaScript
let first = 6;
function add(first,second=first){
    console.log(first + second);
}
add(1);  //2
```

默认值TDZ，调用时候报错

``` JavaScript
function add(first=second,second){
    console.log(first + second);
}
add(1,1);  //2
add(undefined,1); //error
```

### 箭头函数

#### 根据参数个数来区分 

只有一个参数

``` JavaScript
let oneParam = value => console.log(value);
oneParam("oneParam")  //oneParam
```

多个参数或者没有参数

``` JavaScript
let multiParam = (value1,value2) => console.log(value1 + value2);
multiParam(1,2);  //3

let noParam=() => console.log("noParam");
noParam();  //noParam
```

#### 根据函数体来区分

函数体只有一条语句

``` JavaScript
let oneBody=value=> value+1;
oneBody(2);  //3
```

函数体有多条语句

``` JavaScript
let multiBody = (isShow)=>{
    if(isShow){
        console.log("show body");
    }
}
multiBody(true);  //"show body"
```

函数体是一个对象

``` JavaScript
let objBody = (value)=>(
    {
        value:value
    }
)
objBody('hello').value  //"hello"
```

#### 箭头函数的特性

1、箭头函数没有prototype属性
``` JavaScript
//传统函数
let cFunc = function(){
    console.log("1");
}
console.log(cFunc.prototype); //Object {constructor: function}

//箭头函数

let aFunc = ()=> console.log("1");
console.log(aFunc.prototype); //undefined
```

2、箭头函数不绑定this，this的值由复函数作用域中的this决定

``` JavaScript
//传统函数
let cFunc=function(){
    console.log(this.name);
}
cFunc.apply({
    name:"Arrow Function"
})

//Arrow Function

//箭头函数
let aFunc = ()=>console.log(this.name);
aFunc.apply({
    name:"Arrow Function"
});

//空，this指向window
```

3、箭头函数不绑定arguments

``` JavaScript
//传统函数
let cFunc =function(){
    console.log(arguments);
}
cFunc("test");
//["test", callee: function, Symbol(Symbol.iterator): function]

//箭头函数
let aFunc=()=>console.log(arguments);
aFunc("test")

//报错
```

4、箭头函数不能使用new关键词来实例化
``` JavaScript
let aFunc =() => console.log("1");
new aFunc();
//Uncaught TypeError: aFunc is not a constructor
```

#### 如何理解this的指向

``` JavaScript
let cFunc=function(){
    this.name="a";
    let bFunc=function(){
        this.name="b";
    }
    bFunc.prototype.displayName =() => console.log(this.name);
    new bFunc().displayName();
}
new cFunc();

//a
//cFunc {name: "a"}
```

``` JavaScript
let obj={
    name:"obj name",
    displayName: ()=>console.log(this.name)
}
obj.displayName();

//空 ，this指向window
```

``` JavaScript
let obj={
    name:"obj name",
    displayName:function(){
        return (()=>console.log(this.name))()
    }
}
obj.displayName();
//obj name
```

``` JavaScript
let obj={
    name:"obj name",
    displayName:function(){
        return (()=>console.log(this.name))()
    }
}
let obj2={
    name:"obj2 name"
}
obj.displayName.call(obj2);
//obj2 name
```

运用场景

// setTimeout
// add event


有点意思的东西
``` JavaScript
var name="Arrow Function";
let aFunc=()=>console.log(this.name);
aFunc();
//Arrow Function，同时window.name在刷新页面的情况下也不会改变，除非关闭浏览器窗口
```

``` JavaScript
let aFunc=()=>()=>2;
aFunc()();
//2
```

### 其它

name properties,主要是调试的时候定位name属性
``` JavaScript
function doSomething(){

}
var doAnotherThing = function(){

}
console.log(doSomething.name)  //doSomething
console.log(doAnotherThing.name) //doAnotherThing 
``` 

``` JavaScript
var doSomething=function(){

}
console.log(doSomething.bind().name) //"bound doSomething"
console.log(new Function().name);   //anonymous
```












