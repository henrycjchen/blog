# 数据驱动

数据驱动视图，也叫**响应式**修改视图。指修改视图时，不需要直接操作 DOM，而是通过修改数据，间接驱动 DOM 进行修改。



## vm.$mount 挂载 vm

当我们实例化一个 Vue 实例时，从源码可以得知 Vue 函数会执行一个初始化函数 `_init`，做了一些初始化操作，最后通过 `vm.$mount` 方法把模板渲染到 DOM 上

```js
vm.$mount(vm.$options.el)
```



## mount

具体看下 web 版本下带 compiler 版本的 mount 实现：`src/platform/web/entry-runtime-with-compiler.js`

