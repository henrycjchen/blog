## vue-scoped

vue 中 CSS 模块化 scoped 指令使用方式：

```vue
<template>
  <BasePanel class=”pricing-panel”>
    content
  </BasePanel>
</template>

<style scoped>
  .pricing-panel {
    width: 300px;
    margin-bottom: 30px;
  }
</style>
```

会转换成如下内容

```html
<style>
  .base-panel[data-v-d17eko1] {
    ...
  }
  .pricing-panel[data-v-b52c41] {
    width: 300px;
    margin-bottom: 30px;
  }
</style>

<div class=”base-panel pricing-panel” data-v-d17eko1 data-v-b52c41>
  content
</div>
```

转换时做了两个事情：

- 在 CSS 类后加元素限制：`[data-v-***]`
- 在元素节点末尾加模块 hash：`[data-v-***]`



## React Modlues

转换前：

```react
<template>
  <button :class="$style.button" />
</template>

<style module>
  .button {
    color: red
  }
</style>
```

转换后

```html
<style>
  .ComponentName__button__2Kxy {
    color: red;
  }
</style>

<button class=”ComponentName__button__2Kxy”></button> 
```







## 参考资料

1. https://juejin.im/post/6844903673517211655