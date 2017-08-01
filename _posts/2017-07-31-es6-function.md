---
layout:     post
title:      "2017-07-31-es6-function"
date:       "2017-07-31 "
author:     "guozhaodong"
---

# es6之函数


### 参数

`es5`及以前
``` js
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
``` js
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

``` js
console.log(Math.max(1,2,3,4,5));  //5
console.log(Math.max.apply(Math,[1,2,3,4,5])) //5
```

`es6`求数组最大值

``` js
console.log(Math.max(...[1,2,3,4,5]))  //5
console.log(Math.max(...[1,2,3,4],8))  //8
console.log(Math.max(10,...[2,3,4],6))  //10
```


`es5`非严格模式下，参数值改变后`arguments`值也相应改变
``` js
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
``` js
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
``` js
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

``` js
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
``` js
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

``` js
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

``` js
    let value =5;
    function add(first,second=value){
        console.log(first + second);
    }
    add(1);  //6
```

``` js
function compare(first,second,third=first===second){
    console.log(third);
}
compare(1,1);  //true
compare(1,2); // false
compare(1,"1")  //fasle
```

默认值可以使用前面的参数

``` js
let first = 6;
function add(first,second=first){
    console.log(first + second);
}
add(1);  //2
```

默认值TDZ，调用时候报错

``` js
function add(first=second,second){
    console.log(first + second);
}
add(1,1);  //2
add(undefined,1); //error
```










