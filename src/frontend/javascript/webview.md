[toc]

## UIWebView VS WKWebView

UIWebView: iOS2+

WKWebView: iOS8+

WKWebView 优点：

- 内存占用减少
- 网页加载速度提升
- 更多的 html5 特性
- 60fps & 内置手势
- 加载进度属性: estimatedProgress



## jsbridge

### js 如何调用 native

**iOS**

- UIWebview schema 方式请求

- WKWebview 可直接在 js 全局注入原生方法，供 js 直接调用

**android**

安卓也支持 2 种

- schema
- 往 webview 注入原生方法

**注意**

在 schema 调用中，由于 location.href 在多次调用中只会执行最后一次，故 schema 需要用 iframe 的方式调用

### native 调用 js

通过 webview 的提供的方法，直接执行执行 webview 里的 js 方法



### 回调函数

js 调用 native 的时候，回调函数绑定在全局上，并把回调函数名传递给 native，尔后 native 回调时执行全局上的回调函数名