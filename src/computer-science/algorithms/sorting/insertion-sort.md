# 选择排序

从数组头开始，比较当前数是否小于前一个数，如果是，则交换位置，并再比较前一个数，直到不小于为止

1. 索引 i = 1

2. 索引 j = i

3. 数字 arr[j] 是否小于 arr[j - 1]

   a. 是，交换 arr[j] 与 arr[j - 1] 的位置，j -= 1，循环 3

   b. 否，i += 1，循环 2

![](https://camo.githubusercontent.com/8f6fedc10da579f13b22b949f6ad29255b6d721f/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f302f30662f496e73657274696f6e2d736f72742d6578616d706c652d33303070782e676966)
## 复杂度

| 最优 | 平均             | 最差             | 空间复杂度 | 稳定性 |
| ---- | ---------------- | ---------------- | ---------- | ------ |
| n    | O(n<sup>2</sup>) | O(n<sup>2</sup>) | 1          | 是     |



## 实现

```js
function insertionSort(originArr) {
  let arr = [...originArr];
  for (let i = 1; i < arr.length; i++) {
    for (let j = i; j > 0; j--) {
      if (arr[j] < arr[j - 1]) {
        [arr[j], arr[j - 1]] = [arr[j - 1], arr[j]];
      } else {
        break;
      }
    }
  }
  return arr;
}
```



## 参考资料

1. [javascript-algorithms](https://github.com/trekhleb/javascript-algorithms/tree/master/src/algorithms/sorting/insertion-sort)