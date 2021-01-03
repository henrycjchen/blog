## vue 3 改变了什么

## 解决的问题

1. 新的语言特性：proxy，解决数组、对象添加新元素无法跟踪问题
2. 编译时优化
  - 静态树提升，避免重复递归遍历和重复创建节点对象
  - 动态节点添加优化标识，提高优化效率
3. 代码模块化
  - tree-shaking
  - 模块替换（扩展到其他平台时不再需要 fork 后修改）
4. typescript
  - 开发时 bug 提前发现，修改有底气
  - IDE 集成

## 新的东西

- Composition API，类似 react hook

## 参考资料
- [尤雨溪自述：打造Vue 3背后的故事](https://www.infoq.cn/article/rTS3OweiTlA8OldIidBz)
