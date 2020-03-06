# webpack 打包支持 SSR

- SSR 需要避免使用 windows 和 document 等 nodejs 没有的对象、方法



## CSS 引用问题

### 方法一：CSS-in-JS



### 方法二：以浏览器构建的 html 文件为模板

此模板引用了构建出来的 css 文件，不用担心 CSS 引用问题

为模板文件中添加占位符，再将 SSR 构建结果替换占位符

```html
<div id="root">
	<!--HTML_PLACEHOLDER-->
</div>
```





