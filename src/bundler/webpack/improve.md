# webpack 构建优化

webpack 构建时最需要关注两个问题

- 构建速度
- 构建包大小



## 构建速度优化

### SMP 采集 webpack 构建数据指标

优化构建速度，光口头上说快是没用的，需要有具体的数据，才有说服力

通过 speed-meture-webpack-plugin 采集性能指标，获得 webpack 构建过程中，在各个 plugin、loader 上花费的时间，从而有针对性地进行优化。



### 缩小检索范围

- 使用 include, exclude
- 尽量书写文件后缀
- 针对第三方包，指定 alias 为已经打包好的文件，并使用 noParse 忽略对其解析（tree-shaking 会失效）



### 缓存

可以为构建结果添加缓存，如 bable-loader 的缓存

```js
{
  loader: 'babel-loader',
    options: {
      cacheDirectory: true
    }
}
```



### 多线程

happyPack 可以开启多线程构建，从而加快构建速度

webpack 构建流程中，最耗时的当数 loader 对各个文件的转换操作了，happypack 核心就是将这部分任务拆分到多个线程（child_process）中去并行处理，从而减少总的构建时间。



### DLL 技术

将公共库通过 DLL 思想提前打包，从而避免重复编译，大幅缩减大包时间

DLL 依赖需要单独的构建配置，生成构建结果 dll.js 文件和依赖说明 manifest.json 文件，而后在主项目中使用 `DllReferencePlugin` 配置 manifest.json 文件，告知 webpack 采用 dll 依赖



### webpack 5 持久缓存

webpack 的基本工作流程是：

​	读取入口文件 -> 递归构建依赖图 -> 根据加载器和打包策略进行编译

当有文件进行修改时，上面所述流程会全部重新走一遍

**但是！**

在 webpack 5 中，通过**持续缓存**，利用依赖图，当检测到文件修改时，只对修改的部分进行编译，从而大幅提高编译速度





## 构建大小优化

### 可视化分析构建包大小

一样的，优化空口无屏，都需要有数据证明

包大小的分析通常借助 webpack-bundle-analyzeer 进行可视化分析，分析有三个角度

- 按需加载
- 提取公共代码
- 制定 cacheGroup 策略



### tree-shaking

tree-shaking，摇摇摇，摇走~~头皮屑~~ deadcode

虽说 deadcode 可以通过 tree-shaking 去除，但它还是存在于我们的代码仓库中，shaking 的工作量一点没变，需要借助一些其他的工具来去除 deadcode



### 按需使用 source map

当构建时对代码进行压缩丑化后，出 bug 我们能难找到其在原文件中的位置。source map 就是构建后代码和构建前代码的映射，对 debug 非常有帮助。

如果对这种映射要求不高的话，可以采用更轻量的配置（减少映射信息，或者不用映射信息）



### 统计信息 | stats

webpack 编译原文件时，可以生成关于模块统计数据的 JSON 文件，有助于开发者进行依赖分析

```bash
webpack --profile --json > compilation-stats.json
```

如果觉得上面 json 文件数据量大，难以分析，还可以借助 `webpack-deadcode-plugin` 插件进行 deadcode 分析。

该插件可以筛选出：

- 未使用的文件
- 未使用的已暴露变量



### eslint

借助 eslint 也可以分析代码中存在的无用变量，自动清理，让 deadcode 去无踪。



**参考资料**

1. 基于 webpack 的应用治理, 阿里文娱技术团队, 2020-03-25, https://www.infoq.cn/article/mr9ALQzALxW9fyBZdvKV
2. Webpack 5有哪些值得期待, 英俊潇洒你冲哥, https://zhuanlan.zhihu.com/p/75353721