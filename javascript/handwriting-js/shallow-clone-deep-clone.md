## 浅拷贝

```js
// Array
arr.slice();
arr.concat();
[...arr]

// Object
Object.assign({}, object);
{...object}
```

## 深拷贝

思路：

- 原始类型（Number, String, Boolean, null, undefined, Symbol）
- 如果不为原始类型，即为引用了类型（对象类型）, 共有 13 种：
  - 基本引用类型 Object（{}，[]，function）
  - 包装类型（Number, String, Boolean, Symbol）
  - 其他类型：Map, Set, Arguments, Date, RegExp, Error
- 引用类型又可以分为：
  - 可继续遍历的数据类型 Object, Array, Map, Set, Arguments
  - 不可继续遍历的数据类型 Number, String, Boolean, Error, Date, Symbol, RegExp, Function
- 可继续遍历的数据类型
  - 使用原对象的构造函数，创建初始化数据
- 不可继续遍历的数据类型
  - Number, String, Boolean, Error, Date 直接用构造函数和原始数据创建一个新对象
  - Symbol, RegExp, Function 各自处理

### 乞丐版

```js
JSON.parse(JSON.stringify(obj));
```

写法非常简单，而且可以应对大部分的应用场景（原始类型，{}，[]），
缺陷：拷贝其他引用类型（Set，Map，RegExp）、拷贝函数、循环引用等情况

### 基础版本

浅拷贝:

```js
// 浅拷贝Object
function clone(target) {
  const result = {};
  for (let key in target) {
    result[key] = target[key];
  }

  return result;
}
```

深拷贝:

- 如果是原始类型，无需继续拷贝，直接返回
- 如果是引用类型，创建一个新的对象，遍历需要克隆的对象，将需要克隆对象的属性执行深拷贝后依次添加到新对象上。

```js
// 深拷贝原始类型和Object
function clone(target) {
  const isObject = target !== null && typeof target === "object";
  if (isObject) {
    const cloneTarget = {};
    for (let key in target) {
      cloneTarget[key] = clone(target[key]);
    }
    return cloneTarget;
  } else {
    return target;
  }
}
// test
const target = {
  field1: 1,
  field2: undefined,
  field3: "ConardLi",
  field4: {
    child: "child",
    child2: {
      child2: "child2",
    },
  },
};
```

- 这是一个最基础版本的深拷贝，这段代码可以让你向面试官展示你可以用递归解决问题，但是显然，他还有非常多的缺陷，比如，还**没有考虑数组**

### 考虑数组

```js
// 深拷贝原始类型, Object, Array
function clone(target) {
  const isObject = target !== null && typeof target === "object";
  if (isObject) {
    const cloneTarget = Array.isArray(target) ? [] : {};
    for (let key in target) {
      cloneTarget[key] = clone(target[key]);
    }
    return cloneTarget;
  } else {
    return target;
  }
}
// test
const target = {
  field1: 1,
  field2: undefined,
  field3: {
    child: "child",
  },
  field4: [2, 4, 8],
};
```

###　循环引用问题

```js
// test
const target = {
  field1: 1,
  field2: undefined,
  field3: {
    child: "child",
  },
  field4: [2, 4, 8],
};
target.target = target;
```

可以看到下面的结果：

```js
// function clone(target) {
//               ^

// RangeError: Maximum call stack size exceeded
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:2:15)
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:7:26)
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:7:26)
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:7:26)
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:7:26)
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:7:26)
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:7:26)
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:7:26)
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:7:26)
//     at clone (D:\Work Space\GitHub\Xavier\leetcode-daily\edit-code11.js:7:26)
```

很明显，因为递归进入死循环导致栈内存溢出了。
原因就是上面的对象存在循环引用的情况，即对象的属性间接或直接的引用了自身的情况：

#### 解决循环引用问题

我们可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题。

选择 `Map` 数据结构

- 检查`map`中有无克隆过的对象
- 有 - 直接返回
- 没有 - 将当前对象作为`key`，克隆对象作为`value`进行存储
- 继续克隆

```js
function clone(target, map = new Map()) {
  const isObject = target !== null && typeof target === "object";
  if (map.has(target)) {
    return map.get(target);
  }
  if (isObject) {
    const cloneTarget = Array.isArray(target) ? [] : {};
    map.set(target, cloneTarget);
    for (let key in target) {
      cloneTarget[key] = clone(target[key], map);
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```

- 我们可以使用，[WeakMap](https://es6.ruanyifeng.com/?search=weakmap&x=0&y=0#docs/set-map#WeakMap)提代`Map`来使代码达到画龙点睛的作用。

### 性能优化

- 常见的三种循环`for`、`while`、`for in`中，`while`的效率是最好，所以，把`for in`遍历改变为`while`遍历

```js
function forEach(target, fn) {
  let index = -1;
  while (++index < target.length) {
    fn(target[index], index);
  }
}
```

- 对`clone`函数进行改写: 当遍历数组时，直接使用 forEach 进行遍历，当遍历对象时，使用 Object.keys 取出所有的 key 进行遍历，然后在遍历时把 forEach 会调函数的 value 当作 key 使用

```js
function clone(target, map = new WeakMap()) {
  const isObject = target !== null && typeof target === "object";
  if (map.has(target)) {
    return map.get(target);
  }
  if (isObject) {
    const isArray = Array.isArray(target);
    const cloneTarget = isArray ? [] : {};
    const keys = isArray ? null : Object.keys(target);
    map.set(target, cloneTarget);
    forEach(keys || target, function (value, key) {
      if (keys) {
        key = value;
      }
      cloneTarget[key] = clone(target[key], map);
    });
    return cloneTarget;
  } else {
    return target;
  }
}
```

测试用例

```js
function forEach(target, fn) {
  let index = -1;
  while (++index < target.length) {
    fn(target[index], index);
  }
}

function clone1(target, map = new Map()) {
  const isObject = target !== null && typeof target === "object";
  if (map.has(target)) {
    return map.get(target);
  }
  if (isObject) {
    const cloneTarget = Array.isArray(target) ? [] : {};
    map.set(target, cloneTarget);
    for (let key in target) {
      cloneTarget[key] = clone1(target[key], map);
    }
    return cloneTarget;
  } else {
    return target;
  }
}

function clone2(target, map = new WeakMap()) {
  const isObject = target !== null && typeof target === "object";
  if (map.has(target)) {
    return map.get(target);
  }
  if (isObject) {
    const isArray = Array.isArray(target);
    const cloneTarget = isArray ? [] : {};
    const keys = isArray ? null : Object.keys(target);
    map.set(target, cloneTarget);
    forEach(keys || target, function (value, key) {
      if (keys) {
        key = value;
      }
      cloneTarget[key] = clone2(target[key], map);
    });
    return cloneTarget;
  } else {
    return target;
  }
}
const target = {
  field1: 1,
  field2: undefined,
  field3: {
    child: "child",
  },
  field4: [2, 4, 8],
  f: {
    f: { f: { f: { f: { f: { f: { f: { f: { f: { f: { f: {} } } } } } } } } } },
  },
};

target.target = target;

console.time();
const result = clone1(target);
console.timeEnd();

console.time();
const result2 = clone2(target);
console.timeEnd();
```

### 其他数据类型

- 在上面的代码中，我们其实只考虑了普通的`object`和`array`两种数据类型，实际上所有的引用类型远远不止这两个，还有很多，下面我们先尝试获取对象准确的类型

#### 合理的判断引用类型

- 判断是否为引用类型，我们还需要考虑`function`和`null`两种特殊的数据类型：

```js
// 判断所有的对象类型
function isObject(target) {
  return (
    target !== null &&
    (typeof target === "object" || typeof target === "function")
  );
}
```

#### 获取数据类型

我们可以使用`toString`来获取准确的引用类型：

> 每一个引用类型都有`toString`方法，默认情况下，`toString()`方法被每个`Object`对象继承。如果此方法在自定义对象中未被覆盖，`toString()` 返回 `"[object type]"`，其中`type`是对象的类型。

注意，上面提到了如果此方法在自定义对象中未被覆盖，`toString`才会达到预想的效果，事实上，大部分引用类型比如`Array`、`Date`、`RegExp`等都重写了`toString`方法。

我们可以直接调用`Object` 原型上未被覆盖的 `toString()`方法，使用 `call` 来改变 `this` 指向来达到我们想要的效果。

```js
function getType(target) {
  return Object.prototype.toString.call(target);
}
```

下面我们抽离出一些常用的数据类型以便后面使用：

```js
// 可继续循环的
const objtTag = "[object Object]";
const arrTag = "[object Array]";
const setTag = "[object Set]";
const mapTag = "[object Map]";
const argTag = "[object Arguments]";

const numTag = "[object Number]";
const strTag = "[object String]";
const boolTag = "[object Boolean]";
const dateTag = "[object Date]";
const errorTag = "[object Error]";
const symTag = "[object Symbol]";
const regexpTag = "[object RegExp]";
const funcTag = "[object Function]";

const deepTag = [objtTag, arrTag, setTag, mapTag, argTag];
```

在上面的集中类型中，我们简单将他们分为两类：

- 可以继续遍历的类型
- 不可以继续遍历的类型
  我们分别为它们做不同的拷贝。

#### 可继续遍历的类型

这里我们只考虑这四种：`object`、`array`、`Map`、`Set`。

这几种类型还需要继续进行递归，我们首先需要获取它们的初始化数据，例如上面的[]和{}，我们可以通过拿到`constructor`的方式来通用的获取。

例如：`const target = {}`就是`const target = new Object()`的语法糖。另外这种方法还有一个好处：因为我们还使用了原对象的构造方法，所以它可以保留对象原型上的数据，如果直接使用普通的`{}`，那么原型必然是丢失了的。

```js
function createObjectOf(target) {
  const Fn = target.constructor;
  return new Fn();
}
```

我们改写 clone 函数，对可继续遍历的数据类型进行处理:

```js
const objtTag = "[object Object]";
const arrTag = "[object Array]";
const setTag = "[object Set]";
const mapTag = "[object Map]";
const argTag = "[object Arguments]";

function forEach(target, fn) {
  let index = -1;
  while (++index < target.length) {
    fn(target[index], index);
  }
}

function isObject(target) {
  return (
    target !== null &&
    (typeof target === "object" || typeof target === "function")
  );
}

function getType(target) {
  return Object.prototype.toString.call(target);
}

function createObjectOf(target) {
  const Fn = target.constructor;
  return new Fn();
}

function clone(target, map = new WeakMap()) {
  if (map.has(target)) {
    return map.get(target);
  }
  if (isObject(target)) {
    const type = getType(target);
    const cloneTarget = createObjectOf(target);
    map.set(target, cloneTarget);
    if (type == mapTag) {
      target.forEach(function (value, key) {
        cloneTarget.set(key, clone(value, map));
      });

      return cloneTarget;
    }

    if (type == setTag) {
      target.forEach(function (value) {
        cloneTarget.add(clone(value, map));
      });

      return cloneTarget;
    }

    const keys = Array.isArray(target) ? null : Object.keys(target);
    forEach(keys || target, function (value, key) {
      if (keys) {
        key = value;
      }
      cloneTarget[key] = clone(target[key], map);
    });

    return cloneTarget;
  } else {
    return target;
  }
}

// 测试用例
// const map = new Map();
// map.set("key", "value");
// map.set("ConardLi", "code秘密花园");

// const set = new Set();
// set.add("ConardLi");
// set.add("code秘密花园");

// const target = {
//   field1: 1,
//   field2: undefined,
//   field3: {
//     child: "child",
//   },
//   field4: [2, 4, 8],
//   empty: null,
//   map,
//   set,
// };

// target.target = target;
// const result = clone(target);
// console.log(result);
// console.log(target === result);
```

```js
// 测试用例
const target = {
  field1: 1,
  field2: undefined,
  field3: {
    child: "child",
  },
  field4: [2, 4, 8],
  empty: null,
  map,
  set,
};
```

#### 不可继续遍历的类型

其他剩余的类型我们把它们统一归类成不可处理的数据类型，我们依次进行处理：
`Bool`、`Number`、`String`、`String`、`Date、Error`这几种类型我们都可以直接用构造函数和原始数据创建一个新对象：

```js
function cloneOtherType(target, type) {
  const Fn = target.constructor;
  switch (type) {
    case numTag:
    case strTag:
    case boolTag:
    case dateTag:
    case errorTag:
      return Fn(target);
    case symTag:
      return cloneSymbol(target);
    case regexpTag:
      return cloneRegExp(target);
    case funcTag:
      return cloneFunc(target);
    default:
      return null;
  }
}
```

- 克隆 Symbol 类型：

```js
function cloneSymbol(target) {
  return Object(Symbol.prototype.valueOf.call(target));
}
```

- 克隆正则

```js
function cloneRegExp(target) {
  const flogReg = /\w*$/;
  const cloneTarget = target.constructor(
    target.source,
    flogReg.exec(target.toString())
  );
  cloneTarget.lastIndex = target.lastIndex;
  return cloneTarget;
}
```

- 克隆函数
  > 实际上克隆函数是没有实际应用场景的，两个对象使用一个在内存中处于同一个地址的函数也是没有任何问题的

```js
// 通过prototype来区分下箭头函数和普通函数，箭头函数是没有prototype的
// 直接使用eval和函数字符串来重新生成一个箭头函数
// 使用正则来处理普通函数
// 使用正则取出函数体和函数参数，然后使用new Function ([arg1[, arg2[, ...argN]],] functionBody)构造函数重新构造一个新的函数
function cloneFunc(target) {
  const paramRegExp = /(?<=\().+(?=\)\s*{)/;
  const bodyRegExp = /(?<={)(.|\n|\r)+(?=})/;
  const funcString = target.toString();
  const paramResult = paramRegExp.exec(funcString);
  const bodyResult = bodyRegExp.exec(funcString);
  if (target.prototype) {
    if (paramResult && bodyResult) {
      const param = paramResult[0].split(",");
      return new Function(...param, bodyResult[0]);
    } else if (!paramResult && bodyResult) {
      return new Function(bodyResult[0]);
    } else {
      return null;
    }
  } else {
    return eval(funcString);
  }
}
```

```js
// 测试用例
const map = new Map();
map.set("key", "value");
map.set("ConardLi", "code秘密花园");

const set = new Set();
set.add("ConardLi");
set.add("code秘密花园");

const target = {
  field1: 1,
  field2: undefined,
  field3: {
    child: "child",
  },
  field4: [2, 4, 8],
  empty: null,
  map,
  set,
  bool: new Boolean(true),
  num: new Number(2),
  str: new String(2),
  symbol: Object(Symbol(1)),
  date: new Date(),
  reg: /\d+/,
  error: new Error(),
  func1: () => {
    console.log("code秘密花园");
  },
  func2: function (a, b) {
    return a + b;
  },
};
```

### 结尾

```js
// 可继续循环的
const objtTag = "[object Object]";
const arrTag = "[object Array]";
const setTag = "[object Set]";
const mapTag = "[object Map]";
const argTag = "[object Arguments]";

const numTag = "[object Number]";
const strTag = "[object String]";
const boolTag = "[object Boolean]";
const dateTag = "[object Date]";
const errorTag = "[object Error]";
const symTag = "[object Symbol]";
const regexpTag = "[object RegExp]";
const funcTag = "[object Function]";

const deepTag = [objtTag, arrTag, setTag, mapTag, argTag];

function forEach(target, fn) {
  let index = -1;
  while (++index < target.length) {
    fn(target[index], index);
  }
}

function isObject(target) {
  return (
    target !== null &&
    (typeof target === "object" || typeof target === "function")
  );
}

function getType(target) {
  return Object.prototype.toString.call(target);
}

function createObjectOf(target) {
  const Fn = target.constructor;
  return new Fn();
}

function cloneSymbol(target) {
  return Object(Symbol.prototype.valueOf.call(target));
}

function cloneRegExp(target) {
  const flogReg = /\w*$/;
  const cloneTarget = target.constructor(
    target.source,
    flogReg.exec(target.toString())
  );
  cloneTarget.lastIndex = target.lastIndex;
  return cloneTarget;
}

function cloneFunc(target) {
  const paramRegExp = /(?<=\().+(?=\)\s*{)/;
  const bodyRegExp = /(?<={)(.|\n|\r)+(?=})/;
  const funcString = target.toString();
  const paramResult = paramRegExp.exec(funcString);
  const bodyResult = bodyRegExp.exec(funcString);
  if (target.prototype) {
    if (paramResult && bodyResult) {
      const param = paramResult[0].split(",");
      return new Function(...param, bodyResult[0]);
    } else if (!paramResult && bodyResult) {
      return new Function(bodyResult[0]);
    } else {
      return null;
    }
  } else {
    return eval(funcString);
  }
}

function cloneOtherType(target, type) {
  const Fn = target.constructor;
  switch (type) {
    case numTag:
    case strTag:
    case boolTag:
    case dateTag:
    case errorTag:
      return new Fn(target);
    case symTag:
      return cloneSymbol(target);
    case regexpTag:
      return cloneRegExp(target);
    case funcTag:
      return cloneFunc(target);
    default:
      return null;
  }
}

function clone(target, map = new WeakMap()) {
  if (!isObject(target)) {
    return target;
  }
  const type = getType(target);
  let cloneTarget;
  if (deepTag.includes(type)) {
    cloneTarget = createObjectOf(target);
  } else {
    return cloneOtherType(target, type);
  }

  if (map.has(target)) {
    return map.get(target);
  }

  map.set(target, cloneTarget);
  if (type == mapTag) {
    target.forEach(function (value, key) {
      cloneTarget.set(key, clone(value, map));
    });

    return cloneTarget;
  }

  if (type == setTag) {
    target.forEach(function (value) {
      cloneTarget.add(clone(value, map));
    });

    return cloneTarget;
  }

  const keys = type === arrTag ? null : Object.keys(target);
  forEach(keys || target, function (value, key) {
    if (keys) {
      key = value;
    }
    cloneTarget[key] = clone(target[key], map);
  });

  return cloneTarget;
}

// 测试用例
const map = new Map();
map.set("key", "value");
map.set("ConardLi", "code秘密花园");

const set = new Set();
set.add("ConardLi");
set.add("code秘密花园");

const target = {
  field1: 1,
  field2: undefined,
  field3: {
    child: "child",
  },
  field4: [2, 4, 8],
  empty: null,
  map,
  set,
  bool: new Boolean(true),
  num: new Number(2),
  str: new String(2),
  symbol: Object(Symbol(1)),
  date: new Date(),
  reg: /\d+/gi,
  error: new Error(),
  func1: () => {
    console.log("code秘密花园");
  },
  func2: function (a, b) {
    const sum = a + b;
    console.log(sum);
    return sum;
  },
};

target.target = target;
const result = clone(target);
console.log(result);
console.log(target === result);
result.func1();
result.func2(1, 2);
```
