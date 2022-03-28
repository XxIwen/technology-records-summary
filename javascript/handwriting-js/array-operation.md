## 去重

### 1. Object

> 开辟一个外部存储空间用于标示元素是否出现过。

```js
var unique = (array) => {
  const container = {};
  return array.filter((e) =>
    container.hasOwnProperty(e) ? false : (container[e] = true)
  );
};
```

### 2. indexOf + filter

```js
var unique = (array) => array.filter((e, i) => array.indexOf(e) === i);
```

### 3. Set

```js
var unique = (array) => Array.from(new Set(array));
```

```js
var unique = (array) => [...new Set(array)];
```

### 4. 排序

> 通过*比较相邻数字*是否重复，将排序后的数组进行去重。

```js
var unique = (array) => {
  array.sort((a, b) => a - b);
  const result = [];
  let pre = 0;
  for (let i = 0; i < array.length; i++) {
    if (!i || array[i] !== array[pre]) {
      result.push(array[i]);
    }
    pre = i;
  }

  return result;
};
```

### 5. 去除重复的值

> 不同于上面的去重，这里是只要数字出现了重复次，就将其移除掉。

```js
var unique = (array) =>
  array.filter((e) => array.indexOf(e) === array.lastIndexOf(e));
```

## 扁平

### 1. 基本实现

```js
var flat = (array) => {
  let result = [];
  for (let i = 0; i < array.length; i++) {
    if (Array.isArray(array[i])) {
      result = result.concat(flat(array[i]));
    } else {
      result.push(array[i]);
    }
  }

  return result;
};
```

### 2. 使用 reduce 简化

```js
var flatten = (array) =>
  array.reduce(
    (target, current) =>
      Array.isArray(current)
        ? target.concat(flatten(current))
        : target.concat(current),
    []
  );
```

### 3. 根据指定深度扁平数组

```js
var flattenByDeep = (array, deep = 1) => {
  return array.reduce(
    (target, current) =>
      Array.isArray(current) && deep > 1
        ? target.concat(flattenByDeep(current, deep - 1))
        : target.concat(current),
    []
  );
};
```

## 最值

### 1. reduce

```js
var max = (array) => array.reduce((pre, val) => Math.max(pre, val));
```

### 2. Math.max

> Math.max 参数原本是一组数字，只需要让他可以接收数组即可。

```js
var max = (array) => Math.max.apply(null, array);
```

```js
var max = (array) => Math.max(...array);
```

## 使用 reduce 实现 map

```js
Array.prototype.reduceToMap = function (handler) {
  return this.reduce((target, current, index) => {
    target.push(handler.call(this, current, index));
  }, []);
};
```

## 使用 reduce 实现 filter

```js
Array.prototype.reduceToFilter = function (handler) {
  return this.reduce((target, current, index) => {
    if (handler.call(this, current, index)) {
      target.push(current);
    }

    return target;
  }, []);
};
```
