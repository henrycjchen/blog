[TOC]

# 原型 prototype

## new 的时候做了什么

```js
function Base() {};
let b = new Base();
```

new 一个函数时发生了什么，这里模拟一个构造函数：

```js
function create(baseFn, ...args) {
    let obj = {};
    obj.__proto__ = Base.prototype;
    let result = Base.apply(obj, args);
    return result instanceof Object ? result : obj;
}
```



## `__proto__` & `prototype`

```js
b.constructor == Base;
b.__proto__ == Base.prototype;
```

constructor 指向实例的构造函数

`__proto__` 指向实例的构造函数的原型对象 prototype



## contructor

contructor 通常指向实例的构造函数，但由于其可被修改，且不会影响原型链，所以通常不建议拿其做对象原型的判断



## 继承

```js
function Parent(name) {
    this.name = name;
}
Parent.prototype.say = function () {console.log(this.name)}

function Children(name) {
    Parent.call(this, name); // 属性继承
}
Children.prototype = new Parent(); // 原型链继承
```



## 类型判断

### typeof

使用方法：`typeof a`

可以得到变量对应的类型字符串，包括：`undefined`, `boolean`, `string`, `number`, `object`, `function`, `symbol` 共七种

原理：

JavaScript 的值是由一个低 1-3 位的类型值和实际数据值拼接的。

对象的类型值是 0，而 null 表示的空指针 0x00，因此 typeof null === 'object'。



### instanceof

用于判断对象的具体类型，使用方法：`a instanceof b`

原理：

判断 a 的原型链上是否存在 b 的原型 `b.prototype`





**参考资料**

1. 《浅谈 instanceof 和 typeof 的实现原理》，nicole_zhang，2018-05-28，https://juejin.im/post/5b0b9b9051882515773ae714

