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

constructor 指向其构造函数

`__proto__` 指向其构造函数的原型对象 prototype



## contructor

contructor 通常指向实例的构造函数，但由于其可被修改，且不会影响原型链，所以通常不建议拿其做对象原型的判断



## 继承

```js
function Parent(name) {
    this.name = name;
}
Parent.prototype.say = function () {console.log(this.name)}

function Children(name) {
    Parent.call(this, name);
}
Children.prototype = new Parent();
```

