# tree-shaking（孙世吉）

aka，dead code elimination

- 代码不会被执行，不可到达
- 代码执行的不会被用到
- 代码只会影响死变量（只写不读）



## more

### 优化代码触发 tree-shaking

运行时环境判断 -> 编译时常量环境判断，打包出不同平台对应不同的代码

- isApp() -> RUNTIME_ENV



### 并行打包

**parallel-webpack**



## tree-shaking 多与少

是否生效

是否去除多了