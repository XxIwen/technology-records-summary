```js
// 判断Object的prototype是否在a的原型链上
a instanceof Object;

// 实现
function myInstanceof(target, origin) {
  const proto = target.__proto__;
  if (proto) {
    if (origin.prototype === proto) {
      return true;
    } else {
      myInstanceof(proto, origin);
    }
  } else {
    return false;
  }
}
```
