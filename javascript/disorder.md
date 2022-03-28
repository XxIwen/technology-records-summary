## 数组乱序-洗牌算法

```js
function disorder(array) {
  let length = array.length;
  let current = length - 1;
  let random;
  while (current > -1) {
    random = Math.floor(length * Math.random());
    [array[current], array[random]] = [array[random], array[current]];
    current--;
  }

  return array;
}

// let arr = [11, 09, 10, 13, 14, 08, 16, 02, 05, 12, 06, 03, 07, 04, 01, 15];
// const result = disorder(arr);
// console.log(result);
```
