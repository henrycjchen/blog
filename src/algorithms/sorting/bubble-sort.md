# 冒泡算法

从数组头开始，相邻两个数字依次两两对比，将较大的数字往上冒泡，直到全部数字都排序完毕

![](https://camo.githubusercontent.com/383b23979d4d7f279f8fb285b36bcdd357b10a35/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f632f63382f427562626c652d736f72742d6578616d706c652d33303070782e676966)

## 复杂度

| 最优 | 平均             | 最差             | 空间复杂度 | 稳定性 |
| ---- | ---------------- | ---------------- | ---------- | ------ |
| n    | O(n<sup>2</sup>) | O(n<sup>2</sup>) | 1          | 是     |



## 实现

```js
function bubbleSort(originArr) {
  let arr = [...originArr];
  let swapped = false; // 标识是否有做顺序调整，如果没有可以立即返回数组
  for (let i = 1; i < arr.length; i++) {
    swapped = false;
    for (let j = 0; j < arr.length - i; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        swapped = true;
      }
    }
    if (!swapped) return arr;
  }
  return arr;
}

```



## 参考资料

1. [javascript-algorithms](https://github.com/trekhleb/javascript-algorithms/tree/master/src/algorithms/sorting/bubble-sort)