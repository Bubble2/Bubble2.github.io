# es6之Classes

#### es5类的实现方法

> 通过定义一个构造函数，然后在构造函数上定义一些属性，在构造函数的原型上定义一些方法


``` js
function PersonType(name){
    this.name=name;
}

PersonType.prototype.sayName=function(){
    console.log(this.name);
}

var person = new PersonType('xiaoming');
person.sayName();   // "xiaoming"

console.log(person instanceof PersonType);  //true
console.log(person instanceof Object);   //true

```

#### es6类的实现方法

> 通过class关键字去声明一个类，并在里面constructor方法里定义一些属性，在里面直接定义的方法就是原型方法

``` js
class PersonClass{

    //等同于PersonType这个构造函数
    constructor(name){
        this.name=name;
    }

    //等同于PersonType.prototype.sayName
    sayName(){
        console.log(this.name);
    }

}

let person = new PersonClass('xiaoming');
person.sayName(); //  "xiaoming"

console.log(person instanceof PersonClass);   //true
console.log(person instanceof Object);   //true

console.log(typeof PersonClass);  // "function"
console.log(typeof PersonClass.prototype.sayName); // "function"

```






