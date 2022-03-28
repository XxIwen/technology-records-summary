## 变量提升和函数提升
```js
// 函数声明
function foo() {
  console.log('function declaration');
}

// 匿名函数表达式
var foo = function () {
  console.log('anonymous function expression');
};

// 具名函数表达式
var foo = function bar() {
  console.log('named function expression');
};
```

### 用例

- 情况一：

```js
console.log(a); // undefined
var a = 10;
(function a() {
  a = 20;
  console.log(a); // function a() {}
})();
console.log(a); // 10

// 预编译之后

var a;
console.log(a); // undefined
a = 10;
(function a() {
  // 这是一个立即执行的具名的函数表达式
  a = 20; // 具名的函数表达式内变量名和函数名相同，则该变量实质上是函数名；函数名变量可以理解为常量，不可改变。所以a = 20无效，在严格模式下会报错
  console.log(a); // function a() {}
})();
console.log(a); // 10
```

- 具名的函数表达式

```js
/**
 * 1. 具名的函数表达式（NFE）。在ECMAScript 标准中要求 NFE 实现两个特性1.只能在函数体内访问函数名变量。2.函数名变量可以理解为常量，不可改变。所以a = 20被忽* 略了，在严格模式下会报错
 * 2. 函数声明后，并且立即执行，就可以看成一个立即执行的具名的函数表达式
 *
 */
console.log(a); // undefined
var a = function a() {
  a = 20;
  console.log(a);
};
a(); // function a() {}
console.log(a); // function a() {}
```

- 情况二：

```js
a(); // 20
console.log(a); // 20
var a = 10;
function a() {
  a = 20;
  console.log(a);
}
console.log(a); // 10

// 预编译之后
var a;
a = function a() {
  a = 20;
  console.log(a);
};

a(); // 20
console.log(a); // 20
a = 10;

console.log(a); // 10
```

- 情况三：

```js
var a = 10;
a(); // a is not a function
function a() {
  a = 20;
  console.log(a);
}

// 预编译之后

var a;
a = function a() {
  a = 20;
  console.log(a);
};
a = 10;
a(); // a is not a function
```

- 情况四：函数声明遇到函数表达式时

```js
function hoistFunction() {
  foo(); // 2

  var foo = function () {
    console.log(1);
  };

  foo(); // 1

  function foo() {
    console.log(2);
  }

  foo(); // 1
}

hoistFunction();

// 预编译之后
function hoistFunction() {
  var foo;

  foo = function foo() {
    console.log(2);
  };

  foo(); // 2

  foo = function () {
    console.log(1);
  };

  foo(); // 1

  foo(); // 1
}

hoistFunction();
```

- 情况五：函数和变量重名时

```js
var foo = 3;

function hoistFunction() {
  console.log(foo); // function foo() {}

  foo = 5;

  console.log(foo); // 5

  function foo() {}
}

hoistFunction();
console.log(foo); // 3

// 预编译之后

var foo = 3;

function hoistFunction() {
  var foo;

  foo = function foo() {};

  console.log(foo); // function foo() {}

  foo = 5;

  console.log(foo); // 5
}

hoistFunction();
console.log(foo); // 3
```

### 参阅

- [JavaScript: 变量提升和函数提升](https://www.cnblogs.com/liuhe688/p/5891273.html)
