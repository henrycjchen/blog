# CSS in JS

在解决 CSS 冲突的时候，不像 vue 支持的 scoped style，react 一开始在解决这方面问题的能力比较欠缺，CSS 样式都会直接应用在全局，容易存在选择器冲突的风险。

后来，react 也尝试在这个问题上进行摸索，找到了几个解决问题的方案：

- CSS 模块化（CSS Modules）：类似 Vue 的 scoped style，将模块内的选择器加上哈希字符串，实现样式的局部作用域
- BEM（block-element-modifier）：人工在选择器上添加该样式所属的模块，以实现局部作用域效果
- Shadow DOM：借助工具 direflow.io 等工具，将 React 输出为 web component，借助 shadow dom 实现组件的 CSS 样式封装
- CSS-in-JS：



## 参考资料

1. <CSS-in-JS：一个充满争议的技术方案>, 肖鹏, 2020-08-11, https://www.infoq.cn/article/95oJp6uPTI9VsyFSW2xZ