### 原型链继承

```js
function Parent() {}

function Child() {}

Child.prototype = new Parent();
```

### 借用构造函数继承

```js
function Parent() {}

function Child() {
  Parent.call(this);
}
```

### 组合继承

```js
function Parent() {}

function Child() {
  Parent.call(this);
}

Child.prototype = new Parent();
```

### 原型式继承

```js
function object(original) {
  function F() {}
  F.prototype = original;
  return new F();
}
```

### 寄生式继承

```js
function createObject(o) {
  var clone = object(o);
  o.sayName = function () {};
  return clone;
}
```

### 寄生组合式继承

```js
function Parent(name, age) {
  this.name = name;
}
Parent.prototype.sayName = function () {
  return this.name;
};

function Child(age, name) {
  Parent.call(this, name);
  this.age = age;
}

inherit(Child, Parent);

Child.prototype.sayAge = function () {
  return this.age;
};

function inherit(child, parent) {
  // first
  var prototype = object(parent.prototype);
  prototype.constructor = child;
  // second
  var prototype = Object.create(Parent.prototype, {
    constructor: {
      value: child,
      enumerable: false,
      writable: true,
      configurable: true,
    },
  });
  child.prototype = prototype;
}
```
