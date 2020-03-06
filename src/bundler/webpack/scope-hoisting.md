# scope hoisting

webpack 利用闭包函数将每一个模块封装起来，防止变量污染。但当模块多起来时，这层闭包将占用很多空间。如何优化掉这部分大小呢？



这里采用 scope hoisting，即将所有模块的代码按照引用顺序放在一个函数作用域中，然后适当的重命名一些变量，以防止变量名冲突



## 配置

```js
{
    //...
    plugins: [
        new webpack.optimize.ModuleConcatenationPlugin()
    ]
}
```

上面是 webpack 3 的配置，webpack 4 提供了 mode 配置，production 模式默认就配置了这个插件