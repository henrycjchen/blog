[toc]

# 关于 this

## 修改 this 的三种方式

call / apply / bind

- `fn.call(that, arg1, arg2, ...)`，修改 this 并执行，参数单个传入

- `fn.apply(that, [arg1, arg2, ...])`，修改 this 并执行，参数以数组列式传入

- `fn.bind(that, arg1, arg2, ...)`，修改 this 不执行，返回新的函数 fn1，同时将 bind 参数里的 arg1, arg2... 作为原函数 fn 的预设参数，当往新函数 fn1 传参时，参数会放到预设参数后面传入。例：

  ```js
  function test(arg1, arg2, arg3) {
    console.log(arg1, arg2, arg3)
  }
  test(1, 2, 3) // 1, 2, 3
  let newTest = test.bind({}, )
  ```

  