---
layout:     post
title:      "对象&原型&类"
date:       "2018-03-16 "
tags:        "javascript prototype"
author:     "guozhaodong"
---

## 对象

### 对象的定义

对象是js的一种数据类型，它是属性的无序集合，通过名/值对来表示

### 对象的创建

1、对象字面量的方式创建

``` JavaScript
    var obj = {};

    var person = {
        name:'xiaoming',
        age:'18'
    }

    var book = {
        'main title':'javascript',           //属性名中有空格，必须使用字符串来表示
        'sub-title':'the definitive guide',  //属性名中有连字符，必须使用字符串来表示
        'for':'all audiences'                //属性名使用了js保留字必须使用字符串表示，但在es5及之后可以不使用字符串  
    }
```


2、通过`new Object()`来创建

``` JavaScript
    var obj = new Object();

    var person = new Object();
    person.name = 'xiaoming';
    person.age = '18'
```

`new`后面接构造函数，除了`js`内置的构造函数外，我们也可以自己去定义一个构造函数

3、`Object.create()`方法去创建

 创建一个继承该原型的实例对象

``` JavaScript
    var obj = Object.create(Object.prototype);

    var person = Object.create(Object.prototype);
    person.name = 'xiaoming';
    person.age = '18'
```

### 对象属性的操作

可以对对象属性进行新增、修改、删除、查找、枚举、检测。

1、新增

``` JavaScript
    var obj = {};
    console.log(obj.name) //undefined

    obj.name = 'hello'
    console.log(obj.name) //hello
```

2、修改

``` JavaScript
    var obj = {
        name:'hello',
        'sub-name':'你好'
    }

    obj.name = 'hello world';
    obj['sub-name'] = '你好吗？'

    console.log(obj.name) //hello world
    console.log(obj['sub-name']) //你好吗？
```

3、删除

``` JavaScript
    var obj = {
        name:{
            main:'hello',
            sub:'hi'
        }
    }

    // var obj2 = obj.name
    delete obj.name

    console.log(obj.name);
    // console.log(obj2.main)
```

4、查找和枚举

查找上面栗子其实都展示了，下面来看下枚举

``` JavaScript
    var obj = {
        name:'hello',
        'sub-name':'你好'
    }

    for(key in obj){
        console.log(key + ":" + obj[key]);
    }
```

5、检测

通过`in`运算符

``` JavaScript
    var obj = {
        name:'hello',
        'sub-name':'你好'
    }

    console.log('name' in obj)
    console.log('sub-name' in obj)
    console.log('title' in obj)

    obj.hasOwnProperty('name')  //判断是否是对象的自有属性
```

### 对象和属性的分类

#### 三大类对象

内置对象（native object）----- es规范定义，如Object、Array、Function、Date、RegExp等

宿主对象（host object）----- js解释器所嵌入的宿主环境（如浏览器）定义的，所有的BOM和DOM都是宿主对象。

自定义对象（user-defined object）----- js代码创建的对象

#### 两大类属性

自身属性 ----- 直接在对象上定义的属性

继承属性 ----- 对象的原型对象上定义的属性


## 原型

### js有两类数据类型

在介绍原型之前有必要介绍下js的两类数据类型

#### 原始数据类

`Number`、`String`、`Boolean`、`Null`、`Undefined`，基本数据类型是按值进行访问的，值是存储在栈内存中的

``` JavaScript
var a = 10;
var b = a;
b = 20;
console.log(a);
```
上面，b获取的是a值的一份拷贝，虽然，两个变量的值相等，但是两个变量保存了两个不同的基本数据类型值。b只是保存了a复制的一个副本。所以，b的改变，对a没有影响。

下图演示了这种基本数据类型赋值的过程：

![image](/assets/img/prototype/2012062912181127.jpg)

#### 引用数据类型

也就是对象类型，如`Object`、`Array`、`Function`等等

这些类型的数据都是存储在堆内存中的，与其他语言的不同是，你不可以直接访问堆内存空间中的位置和操作堆内存空间。只能操作对象在栈内存中的引用地址。

所以，引用类型数据在栈内存中保存的实际上是对象在堆内存中的引用地址。通过这个引用地址可以快速查找到保存中堆内存中的对象。

``` JavaScript
    var obj1 = {}

    var obj2 = obj1;

    obj2.name = 'xiaoming';

    console.log(obj1.name)
```

![image](/assets/img/prototype/2012062914380085.jpg)


### 什么是原型？

首先它就是一个普通的对象。当我们创建一个函数`A`的时候，浏览器会按照一定规则在内存中创建一个对象`B`，同时这个函数`A`有一个属性叫`prototype`,它的值指向了这个对象`B`，这个对象`B`就是函数`A`的原型对象，简称函数原型。

``` JavaScript
function Hello(){}

Hello.prototype
```

![image](/assets/img/prototype/TM20180627095018.png)

上图红框处就是这个函数`Hello`的原型。这个原型对象中只有两个属性，一个叫`constructor`，这个属性的值是指向构造函数`Hello`。还有一个`__proto__`属性，这个属性的值指向的是`Object`构造函数的原型，这个`__proto__`属性其实就是用来继承的，通过它就实现了所谓的原型链。

> 下图就展示了函数和它的原型对象的关系

![image](/assets/img/prototype/20180627155732.jpg)

### 通过`new`构造函数来创建对象

把一个函数作为构造函数使用`new`实例化后，浏览器会创建一个对象，同时这个对象还会有一个属性`__proto__`，这个属性的值指向这个构造函数的原型

> ps:其实每个函数都可以作为构造函数进行实例化，不过正常情况下我们为了区分普通函数和构造函数，就把名称首字母大写的函数作为构造函数。

``` JavaScript
 var he1 = new Hello()
```

![image](/assets/img/prototype/TM20180627164502.png)

上图红框处展示了构造函数`Hello`实例化出来一个对象`he1`，这个`he1`对象有一个属性叫`__proto__`,这个属性的值指向这个构造函数`Hello`的原型对象，

> 下图展示了对象`he1`和它构造函数原型的关系

![image](/assets/img/prototype/20180627164349.jpg)

从上图中我们可以总结出当我们多次调用`new Hello()`实例化多个对象，那么这些对象的`__proto__`属性的值都会指向`Hello.prototype`，则这些对象可以共享原型对象上的方法。

``` JavaScript
    function Hello(){

    }
    //使用Hello.prototype直接给原型对象添加属性
    Hello.prototype.title = 'Hello World'; 

    var he1 = new Hello();
    //访问he1的title属性，浏览器引擎会先查找he1对象上有没有这个title属性，如果发现没有找到这个属性，那么它回去它的构造函数的原型对象上找
    console.log(he1.title)  //Hello World


    var he2  = new Hello();
    //两个对象的title都是从它构造函数的原型对象上找到的，所以是相等的
    console.log(he1.title === he2.title) //true

    //通过实例对象是不能修改原型对象的属性的，所以下面是给he1对象自身添加一个title属性，不会修改它构造函数原型对象的属性
    he1.title = 'Hello xiaoming';
    console.log(he1.title); //Hello xiaoming
    console.log(he2.title)  //Hello World
```
![image](/assets/img/prototype/20180628095923.jpg)


### 和原型相关的方法或属性

1、`__proto__`和`prototype`

1.1、首先说说`__proto__`属性，这其实是一个非标准的属性，在大部分浏览器的js引擎上会支持这个属性。`es5`将该属性称为`[[Prototype]]`它是一个不可访问的属性，但可以通过`Object.getPrototypeOf()`标准方法访问该属性，这个属性指向<b>创建它的构造函数的原型对象</b>。

1.2、`prototype`是函数才有的属性，这个属性指向<b>这个函数的原型对象</b>

总结：
（1）、所有的函数和对象都有`__proto__`这个属性，指向构造该对象的构造函数的原型。

（2）、只有函数`function`才有`prototype`属性，指向这个函数的原型对象。


``` JavaScript
    function Hello(){

    }

    var he1 = new Hello()

    //都指向构造函数的原型对象
    console.log(he1.__proto__ === Hello.prototype) //true
    console.log(Object.getPrototypeOf(he1) === Hello.prototype) //true

```

2、`constructor`属性

`constructor`属性存在原型对象中，它指向构造函数

``` JavaScript
    function Hello(){}

    console.log(Hello.prototype.constructor === Hello)
```

## 如何创建类

上面说了可以通过对象字面量，`new Object()`等方式创建单个对象，但是如果我们需要创建很多具备相同属性、方法的对象时难道还要一个一个去创建吗？

这就要提到类，类是具有共同特征的事物的抽象，是面向对象程序设计语言中的一个概念。在es5及之前，js其实是没有真正的类的。

1、传统的单个方式

``` JavaScript
    var car1 = {
        name:'golf',
        brand : '一汽大众',
        run: function(){
            console.log(car1.name+'正在行驶中')
        }
    }

    var car2 = {
        name:'smart',
        brand : '奔驰',
        run: function(){
            console.log(car1.name+'正在行驶中')
        }
    }

    car1.run()
    car2.run()
    ...
```

2、工厂模式创建

``` JavaScript
    function car(name,brand){   
        var obj = {};
        obj.name = name;
        obj.brand = brand;
        obj.run = function(){
            console.log(obj.name+'正在行驶中')
        }
        return obj;
    }

    var car1 = car('golf','一汽大众');
    var car2 = car('smart','奔驰');

    car1.run()
    car2.run()
```

优点：通过工厂模式，能够迅速创建想要的对象。简洁、明了、迅速，并且所有的对象的基本结构都是一样的。

缺点：

（1）、无法解决对象识别的问题，即怎么知道对象的类型。（书上的解释）

（2）、几个对象无法共享某一个特定的属性，对象彼此之间都是不相同的。


3、构造函数的方式

``` JavaScript
    function Car(name,brand){
        this.name = name;
        this.brand = brand;
        this.run = function(){
            console.log(this.name+'正在行驶中')
        }
    }

    var car1 = new Car('golf','一汽大众');
    var car2 = new Car('smart','奔驰');

    car1.run()
    car2.run()
```

这种方式对属性比较合适，对于方法来说不太合适，方法其实是一样的，可以共享的，只需要一份就够了。这样会造成内存的浪费和性能的低下。

4、原型模式

``` JavaScript

    function Car(){}

    Car.prototype.name = 'golf';
    Car.prototype.brand = '一汽大众';
    Car.prototype.run = function(){
        console.log(this.name+'正在行驶中')
    }

    var car1 = new Car();
    var car2 = new Car();

    car1.run()
    car2.run()
```

原型中所有的属性都是共享的，也就是说用同一个构造函数创建的对象去访问原型中的属性，大家访问的都是同一个对象。这种方式对于方法来说是比较合适的，但对于私有属性来说，并不是我们想要的结果。

5、构造函数+原型

``` JavaScript
    function Car(name,brand){
        this.name = name;
        this.brand = brand;
    }

    Car.prototype.run = function(){
        console.log(this.name+'正在行驶中')
    }

    var car1 = new Car('golf','一汽大众');
    var car2 = new Car('smart','奔驰');

    car1.run()
    car2.run()
```

原型用来封装方法，构造函数用来封装属性，两种方式结合起来可以满足我们的要求。









