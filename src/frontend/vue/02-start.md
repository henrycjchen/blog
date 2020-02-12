# 入口文件

在 web 应用下，我们需要重点关注 runtime + compiler 构建出来的 Vue.js，其构建入口文件位于 `src/platforms/web/entry-runtime-with-compiler.js`



从这个文件中 `Vue` 的定义 `import Vue from './runtime/index'`，我们顺藤摸瓜，找到其位于 `src/core/instance/index.js` 的最原本的定义

```js
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}

initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)
```

这里用 Function 来实现类，并在通过 `initMixin` 等函数为 Vue Function 的 prototype 添加属性的方式来添加类的内置方法



## initGlobalAPI

在 `src/core/index.js` 中，我们还能看到作者为 Vue 本身添加了全局的静态方法，官网中全局的 API 都可以在这里找到

```js
initGlobalAPI(Vue)
```

