# 前情提要

## flow

vue 借助 flow 进行静态类型检查。

### flow 干嘛的

[官网](https://flow.org/)的简介：

- code faster：为类、函数声明了属性和参数，提供**代码提示**功能，让你码得快
- code confidently：添加静态属性声明后，能提前在**编译时发现错误**，让你码得自信
- code bigger：特别是针对大型项目，有助于**代码使用和理解**

### 为什么不是 typescript

[作者答](https://www.zhihu.com/question/46397274)

flow 是 vue 2.0 时加入的，最根本的原因是出于**工程成本和收益**考虑，flow 相对于 typescript 的代码入侵程度较低，修改量较小，在快速迭代要求的情况下，优先考虑 flow。

但因为 typescript 强大的功能和社区，再结合 vscode 开发，作者将会在 vue 3.0 改用 typescript 进行静态类型检查。作者原话：**真香**。



## vue 目录

源码 src 目录结构

```
src
├── compiler  # 编译相关
├── core      # 核心代码 
├── platforms # 不同平台的支持
├── server    # 服务端渲染
├── sfc       # .vue 文件解析
├── shared    # 公共代码库
```

### compiler 编译

compiler 负责把模板编译成语法树（ast），包括语法树优化、代码生成等功能。

为了优化页面性能，通常会把编译的工作放在构建时处理（去除编译库以**减少包大小**，提前编译以**减少首屏时间**）

### core

vue 的核心代码，包括内置组件、全局 api、vue 实例化、观察者、虚拟 DOM 等

### platform

这里是 vue 的主入口，里面有两个目录，分别对应打包成运行在 web 和  weex 上的 vue.js

### server

服务端渲染

### sfc

读取单文件组件 .vue 文件，并解析成一个 JavaScript 对象

### shared

一些公共方法



## 参考资料

1. 《vue 技术揭秘》, ustbhuangyi, https://ustbhuangyi.github.io/vue-analysis/