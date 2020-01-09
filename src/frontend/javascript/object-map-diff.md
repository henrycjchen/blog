## Object 和 Map 的区别

### 同

- 存取值
- 删键
- 检查是否存在

### Map 的优势

- Maps 的 `key` 可以是任意值，包括函数、对象、基本类型，而 Object 的 `key` 只能是 `字符串` 和 `Symbols`
- Map 值键值是有序的（按插入的顺序），而对象则没有
- Map 拥有 `size` 属性直接获取 `key` 个数，而 Object 只能间接计算
- Map 可以直接迭代，而 Object 需要先转化成数组
- Map 在涉及频繁增删键值对的场景下会有些性能优势

#### map 相关操作

**生成一个 map**

```js
var map = new Map();
map.set('a',1);
map.set('b',2);
```

**map 的大小**

```js
map.size(2)
```

**map 的遍历**

```js
for (var kv of map) {
  console.log(kv); // ["a", 1], ["b", 2]
}
map.forEach((val,kay)=>console.log(val, key)) // 1 a, 2 b
```



#### Weakmap

weakMap 与 map 又稍有不同：

- 只用函数、对象为 key
- 不能遍历
- 当 key 的引用丢失时，weakMap 对应的 key 为被回收，不会造成内存泄露

最后一点写个例子

```js
var weakmap = new WeakMap();
var a = {};
weakmap.set(a, 1);
a = null;
此时 weakmap 里的 a 对应的值将会被垃圾回收
```



