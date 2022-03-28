## 节流

### 定义

节流（`throttle`）: 不管事件触发频率多高，只在单位时间内执行一次。
![debounce-throttle](./assets/debounce-throttle.gif)

### 实现

有两种方式可以实现节流，使用时间戳和定时器。

#### 时间戳实现

> 第一次事件肯定触发，最后一次不会触发

```js
function throttle(handler, time) {
  let pre = 0;
  return function (...args) {
    if (Date.now() - pre > time) {
      pre = Date();
      handler.apply(this, args);
    }
  };
}
```

#### 定时器实现

> 第一次事件不会触发，最后一次一定触发

```js
function throttle(handler, time) {
  let timer = null;
  return function (...args) {
    if (!timer) {
      timer = setTimeout(() => {
        timer = null;
        handler.apply(this, args);
      }, time);
    }
  };
}
```

#### 结合版

> 定时器和时间戳的结合版，也相当于节流和防抖的结合版，第一次和最后一次都会触发

```js
function throttle(handler, time) {
  let pre = 0;
  let timer = null;
  return function (...args) {
    if (Date.now() - pre > time) {
      pre = Date.now();
      clearTimeout(timer);
      timer = null;
      handler.apply(this, args);
    } else if (!timer) {
      timer = setTimeout(() => {
        handler.apply(this, args);
      }, time);
    }
  };
}
```
