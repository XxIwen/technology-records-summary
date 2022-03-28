## 观察者模式

这就类似我们在微信平台订阅了公众号 , 当它有新的文章发表后，就会推送给我们所有订阅的人。

我们作为订阅者不必每次都去查看这个公众号有没有新文章发布，公众号作为发布者会在合适时间通知我们。

我们与公众号之间不再强耦合在一起。公众号不关心谁订阅了它， 不管你是男是女还是宠物狗，它只需要定时向所有订阅者发布消息即可。

### 观察者模式的优点

- 可以广泛应用于异步编程，它可以代替我们传统的回调函数
- 我们不需要关注对象在异步执行阶段的内部状态，我们只关心事件完成的时间点
- 取代对象之间硬编码通知机制，一个对象不必显式调用另一个对象的接口，而是松耦合的联系在一起

### Nodejs 的 EventEmitter

#### 基本使用

```js
var events = require("events");
var eventEmitter = new events.EventEmitter();

// 监听器 #1
var listener1 = function listener1() {
  console.log("监听器 listener1 执行。");
};

// 监听器 #2
var listener2 = function listener2() {
  console.log("监听器 listener2 执行。");
};

// 绑定 connection 事件，处理函数为 listener1
eventEmitter.addListener("connection", listener1);

// 绑定 connection 事件，调用一次，处理函数为 listener2
eventEmitter.once("connection", listener2);

// 处理 connection 事件
eventEmitter.emit("connection");

// 处理 connection 事件
eventEmitter.emit("connection");
```

### 手动实现 EventEmitter

```js
function EventEmitter() {
  this._maxListeners = 10;
  this._events = Object.create(null);
}

EventEmitter.prototype.addListener = function (type, listener, prepend) {
  if (this._events[type]) {
    if (!!prepend) {
      this._events[type].unshift(listener);
    } else {
      this._events[type].push(listener);
    }
  } else {
    this._events[type] = [listener];
  }
};

EventEmitter.prototype.removeListener = function (type, listener) {
  if (Array.isArray(this._events[type])) {
    if (!listener) {
      delete this._events[type];
    } else {
      this._events[type] = this._events[type].filter(
        (e) => e !== listener && e.origin !== listener
      );
    }
  }
};

EventEmitter.prototype.once = function (type, listener) {
  const only = (...args) => {
    listener.apply(this, args);
    this.removeListener(type, listener);
  };
  only.origin = listener;
  this.addListener(type, only);
};

EventEmitter.prototype.emit = function (type, ...args) {
  if (Array.isArray(this._events[type])) {
    this._events[type].forEach((fn) => {
      fn.apply(this, args);
    });
  }
};

EventEmitter.prototype.setMaxListener = function (count) {
  this._maxListeners = count;
};
```

- 测试代码：

```js
var emitter = new EventEmitter();

var onceListener = function (args) {
  console.log("我只能被执行一次", args, this);
};

var listener = function (args) {
  console.log("我是一个listener", args, this);
};

emitter.once("click", onceListener);
emitter.addListener("click", listener);

emitter.emit("click", "参数");
emitter.emit("click");

emitter.removeListener("click", listener);
emitter.emit("click");
```

### JavaScript 自定义事件

- `DOM`也提供了类似上面`EventEmitter`的`API`，基本使用：

```js
//1、创建事件
var myEvent = new Event("myEvent");

//2、注册事件监听器
elem.addEventListener("myEvent", function (e) {});

//3、触发事件
elem.dispatchEvent(myEvent);
```
