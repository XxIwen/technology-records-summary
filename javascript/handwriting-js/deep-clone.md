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

```js
// 可继续循环的
const objtTag = '[object Object]';
const arrTag = '[object Array]';
const setTag = '[object Set]';
const mapTag = '[object Map]';
const argTag = '[object Arguments]';

const numTag = '[object Number]';
const strTag = '[object String]';
const boolTag = '[object Boolean]';
const dateTag = '[object Date]';
const errorTag = '[object Error]';
const symTag = '[object Symbol]';
const regexpTag = '[object RegExp]';
const funcTag = '[object Function]';

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
    (typeof target === 'object' || typeof target === 'function')
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
      const param = paramResult[0].split(',');
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
map.set('key', 'value');
map.set('ConardLi', 'code秘密花园');

const set = new Set();
set.add('ConardLi');
set.add('code秘密花园');

const target = {
  field1: 1,
  field2: undefined,
  field3: {
    child: 'child',
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
    console.log('code秘密花园');
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
