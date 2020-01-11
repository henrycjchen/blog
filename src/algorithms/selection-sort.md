# 选择排序

从数组头开始，找出小于当前数字的最小数字，并与之交换位置

![](https://camo.githubusercontent.com/adfa2cdcc3825092dc405aadd87453571d6e0dc4/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f392f39342f53656c656374696f6e2d536f72742d416e696d6174696f6e2e676966)



## 复杂度

时间复杂度：O(n<sup>2</sup>)

空间复杂度：1

稳定：否（跨距离换位导致不稳定）



## 实现

```js
function selectionSort(originArr) {
  let arr = [...originArr];
  let minIndex = 0;
  for (let i = 0; i < arr.length; i++) {
    minIndex = i;
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[j] < arr[minIndex]) {
        minIndex = j;
      }
    }
    if (minIndex !== i) { // 最小数位置有变化再交换
      // 跨距离换位导致不稳定
      [arr[minIndex], arr[i]] = [arr[i], arr[minIndex]]; 
    }
  }
  return arr;
}

```



## 参考资料

1. [javascript-algorithms](https://github.com/trekhleb/javascript-algorithms/tree/master/src/algorithms/sorting/selection-sort)