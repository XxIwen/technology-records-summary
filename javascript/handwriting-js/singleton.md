## 单例模式
思路：
- 在合适的时候才创建对像，并且只创建唯一的一个。
- 创建对象和管理单例的职责被分布在两个不同的方法中，这两个方法组合起来才具有单例模式的威力。
- 使用闭包实现
```js
var Singleton = function (name) {
  this.name = name;
}

Singleton.prototype.getName = function () {
  console.log(this.name);
};

Singleton.getInstance = (function (name) {
  var instance;
  return function (name) {
    if (!instance) {
      instance = new Singleton(name);
    }

    return instance;
  };
})();

// var ins1 = Singleton.getInstance("instance1");
// var ins2 = Singleton.getInstance("instance2");
// ins1.getName();
// ins2.getName();
// console.log(ins1 === ins2);
```
