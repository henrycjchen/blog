# webpack 原理

## 基本概念

- entry: 入口
- module: 模块，webpack 中一切皆模块，一个模块对应一个文件，webpack 会从 entry 开始递归找出所有的依赖模块
- chunk: 代码块，一个代码块由多个模块组件，用于代码合并和拆分
- loader: 模块转换器，webpack 本质上只能识别 javascript，loader 用于将原内容转换成 webpack 可识别使用的 js 代码
- plugin: 扩展插件，在 webpack 构建流程中，特定时机会广播出对应的事件，插件可以监听这些事件，在特定时机做对应的事情
  - 主要事件为 compilation 事件，loader 转换模块的事件都包含其中