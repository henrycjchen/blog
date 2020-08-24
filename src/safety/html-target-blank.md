# target="_blank" 存在的漏洞

`iframe` 可以通过 `window.opener` 或 `window.parent` 操作父页面。



为了避免上述问题，需要在添加标签时，给 rel 加上限制属性：

- noreferrer
- noopener

使用方式：

```html
<a href="https://an.evil.site" target="_blank" rel="noopener noreferrer nofollow">Enter an "evil" website</a>
```



对于不支持上述属性的老浏览器，还可以通过 JS 代码来屏蔽子页面对父页面的操作

```js
function openUrl(url) {
  var newTab = window.open();
  newTab.opener = null;
  newTab.location = url;
}
```





## 参考资料

1. 《target = “_ blank”背后的隐藏漏洞》，2020-08-24，https://www.infoq.cn/article/lmdiIzFzb8JvU9lkKTbi