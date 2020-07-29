# scope hoisting

webpack 利用闭包函数将每一个模块封装起来，防止变量污染。但当模块多起来时，这层闭包将占用很多空间。如何优化掉这部分大小呢？



这里采用 scope hoisting，即将所有模块的代码按照引用顺序放在一个函数作用域中，然后适当的重命名一些变量，以防止变量名冲突



### 示例

原代码：

```js
// main.js
export default "hello leo~";
// index.js
import str from "./main.js";
```

优化前：

```js
[
  (function (module, __webpack_exports__, __webpack_require__) {
    var __WEBPACK_IMPORTED_MODULE_0__util_js__ = __webpack_require__(1);
    console.log(__WEBPACK_IMPORTED_MODULE_0__util_js__["a"]);
  }),
  (function (module, __webpack_exports__, __webpack_require__) {
    __webpack_exports__["a"] = ('hello leo~');
  })
]
```

优化后：

```js
[
  (function (module, __webpack_exports__, __webpack_require__) {
    var util = ('hello leo~');
    console.log(util);
  })
]
```

优点：

- 代码包体积更小
- 闭包函数更少后，内存开销也更小



## 原理

通过分析各模块间的依赖关系，尽可能地将打散的模块合并到一个函数中。为保证不造成代码的冗余，只有被**引用一次**的模块才能被合并。

由于 scope hoisting 是 ES 模块化的特性，因此源码必需采用 ES6 模块化语句。

（ES 模块化声明会提升，而 commonjs 模块化需要声明后才能使用）



## 使用方式

### 自动启用

```js

// webpack.config.js
module.exports = {
	mode: "production"
};
```

### 手动启用

```js
// webpack.config.js
const webpack = require('webpack');
module.exports = {
    plugins: [
        new webpack.optimize.ModuleConcatenationPlugin()
    ]
};
```

前面提到 Scope Hoisting 依赖 ES6 模块化，但由于有些第三方库用的 commonjs 语法，这里需要手动声明，优先采用 ES6 模块化语法

```js
// webpack.config.js
const webpack = require('webpack');
module.exports = {
    resolve: {
        // 针对 npm 中的第三方模块优先采用 jsnext:main 中指向的 ES6 模块化语法的文件
        mainFields: ['jsnext:main', 'browser', 'main']
    },
    plugins: [
        new webpack.optimize.ModuleConcatenationPlugin()
    ]
};
```

对于没有 ES6 模块化的第三方库，webpack 会降级处理不使用 scope hoisting 优化，我们可以通过配置查看是哪些库没有支持 ES6 模块化

```js
// package.json
{
  // ...
  "scripts": {
    "build": "webpack --display-optimization-bailout" 
  }
}
```

构建时，输出的日志中 `ModuleConcatenation bailout` 告诉我们哪些文件因为什么原因导致降级处理了



**参考资料**

1. <了不起的 Webpack Scope Hoisting 学习指南>, 2020-07-12, https://xie.infoq.cn/article/1a7193732649808678048b982
2. Are ES6 module imports hoisted, https://stackoverflow.com/questions/29329662/are-es6-module-imports-hoisted
3. ModuleConcatenationPlugin, https://webpack.js.org/plugins/module-concatenation-plugin/