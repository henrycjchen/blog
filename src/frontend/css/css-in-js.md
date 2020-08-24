# CSS in JS

在项目中维护一个巨大的样式文件是让人头疼的一件事。

CSS in JS 是为组件化管理 CSS 而生，也能很好地解决 CSS 冲突问题。

目前解决 CSS 冲突问题有几个方案：

- CSS 模块化（CSS Modules）：类似 Vue 的 scoped style，将模块内的选择器加上哈希字符串，实现样式的局部作用域
- BEM（block-element-modifier）：人工在选择器上添加该样式所属的模块，以实现局部作用域效果
- Shadow DOM：借助工具 direflow.io 等工具，将 React 输出为 web component，借助 shadow dom 实现组件的 CSS 样式封装
- CSS-in-JS：使用 JS 维护 CSS
  - 解决方案：emotion，styled components，JSS
  - 不像内联样式，而是会生成 style 标签，并自动生成 css 类名



## 为什么 CSS in JS 不是通过内联样式来实现

并不是所有的 CSS 特性在 JS 中都有对应的 API，很多伪类选择器就不能通过 JS API 来实现内联样式



## CSS in JS 有什么好处

- 组件化管理
- 自动生成类名，以实现局部作用域
- 自动添加引擎前缀
- tree-shaking
- JS 和 CSS 间变量、函数共享



## CSS in JS 有什么不足

- 学习曲线比较陡



## 参考资料

1. 《CSS-in-JS：一个充满争议的技术方案》，肖鹏, 2020-08-11，https://www.infoq.cn/article/95oJp6uPTI9VsyFSW2xZ
2. 《你需要知道的CSS-in-JS》，薛命灯，2017-11-24，https://mp.weixin.qq.com/s?__biz=MzUxMzcxMzE5Ng==&mid=2247485491&idx=1&sn=1f161bfad70d943d87a3710cac34feaf&chksm=f951bd70ce26346603031bcef4d370aa6f32b65a03df6615290ee952279d54d23d817dbd4895&scene=27#wechat_redirect
3. 《Why I Write CSS in JavaScript》，2019-02-18，https://mxstbr.com/thoughts/css-in-js