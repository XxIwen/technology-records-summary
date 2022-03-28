## 使用 promise + async await 实现异步循环打印
> 在 ECMAScript 2017 标准中, 时序组合可以通过使用 async/await 而变得更简单
`````js
// 使用async...await时序运行多个异步操作
const sleep = function (time, i) {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
      resolve(i);
    }, time);
  });
};

const start = async function () {
  for (let i = 0; i < 6; i++) {
    // console.log("````", i);
    const result = await sleep(1000, i);
    console.log(result);
  }
};

start();
`````

- 以下代码实际是同步创建多个 Promise，并行运行了异步操作；
- Promise.all() 和 Promise.race() 是并行运行异步操作的两个组合式工具

`````js
const sleep = function (time, i) {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
      resolve(i);
    }, time);
  });
};

const start = function () {
  for (let i = 0; i < 6; i++) {
    console.log("````", i);
    sleep(1000, i).then(function (result) {
      console.log(result);
    });
  }
};

start();
`````

- 不使用 async...await 时序运行多个异步操作

```js
const sleep = function (time, i) {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
      resolve(i);
    }, time);
  });
};

const getAsyncSet = function () {
  const result = [];
  for (let i = 0; i < 6; i++) {
    result.push(function (val) {
      if (val !== -1) {
        console.log(val);
      }
      return sleep(1000, i);
    });
  }

  return result;
};

const composeP = function (...args) {
  const init = args.shift();
  return (...rest) => {
    return args.reduce((sequence, func) => {
      return sequence.then((result) => {
        return func(result);
      });
    }, init(...rest));
  };
};

const start = composeP(...getAsyncSet());

start(-1).then((val) => {
  console.log(val);
});
```

- 改进 1 - 简化 composeP

```js
const sleep = function (time, i) {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
      resolve(i);
    }, time);
  });
};

const getAsyncSet = function () {
  const result = [];
  for (let i = 0; i < 6; i++) {
    result.push(function (val) {
      if (val !== -1) {
        console.log(val);
      }
      return sleep(1000, i);
    });
  }

  return result;
};

const composeP =
  (...funcs) =>
  (...rest) =>
    funcs.reduce(
      (sequence, func) => sequence.then(func),
      Promise.resolve(...rest)
    );

const start = composeP(...getAsyncSet());

start(-1).then((val) => {
  console.log(val);
});
```

- 改进 2

```js
const sleep = function (time, i) {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
      resolve(i);
    }, time);
  });
};

const start = function (x) {
  let head = Promise.resolve(x);
  for (let i = 0; i < 6; i++) {
    const func = (val) => {
      if (val !== -1) {
        console.log(val);
      }
      return sleep(1000, i);
    };
    head = head.then(func);
  }

  head.then((val) => {
    console.log(val);
  });
};

start(-1);
```
