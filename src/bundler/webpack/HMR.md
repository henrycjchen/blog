# 热更新

webpack-dev-server 不输出文件，而是放在内存中，结合 HotModuleReplacementPlugin 插件可以实现热更新



## 配置

```js
{
    // ...
    plugins: [
        new webpack.HotModuleReplacementPlugin()
    ],
    devServer: {
        contentBase: './dist',
        hot: true
    }
}
```



## 原理

### 启动过程

1. `webpack compile` 将 JS 编译成 Bundle 传输给 `Bundle Server`
2. `Bundle Server` 提供文件服务，供浏览器访问
3. `HMR Runtime` 会被注入到浏览器，提供更新文件的能力

### 热更新过程

1. `webpack compile` 将 JS 编译成 Bundle，传输给 `HMR Server` 
2. `HMR Server` 将热更新的文件输出给 `HMR Runtime`
3. `HMR Runtime` 更新文件