## 语言

- Promise 异步操作

- Class 类

- Proxy 对象代理器

- 声明变量 let const
  - let 让 JS 的变量有了块级作用域，不允许重复声明
  - const 定义的常量不允许被修改
  - let 和 const 定义的变量不会提升

- 新的数据类型 Symbol，为对象添加属性的同时，确保不会与现有的属性冲突

  ```js
  let s = Symbol();
  let obj = {
      [s]: 'test'
  }
  ```



## 编码

- 箭头函数
- 解构赋值
- 模板字符串

  


## 数组结构&能力扩展


- 数组扩展

  - 扩展运算符 `console.log(...[1, 2, 3])`
  - `find` & `findIndex`
  - `fill`
  - `includes`

- 对象扩展

  - 简洁写法

    ```js
    var num = 1;
    var obj = { num };
    ```

  - 属性表达式

    ```js
    let propKey = 'foo';
    
    let obj = {
      [propKey]: true,
      ['a' + 'bc']: 123
    };
    ```

  - 扩展运算符

  - `keys` & `values`

- Set 类似数组，常用于去重

  ```js
  let arr = [...new Set([1,2,2,2])]
  ```

- Map 对象的扩展，`key` 支持任何基本类型



## 模块化

- Module 模块引用

