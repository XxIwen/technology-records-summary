## 手动实现 JSONP

### 原理

- 实例代码

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>JSONP 实例</title>
  </head>
  <body>
    <div id="divCustomers"></div>
    <script type="text/javascript">
      function callbackFunction(result, methodName) {
        var html = "<ul>";
        for (var i = 0; i < result.length; i++) {
          html += "<li>" + result[i] + "</li>";
        }
        html += "</ul>";
        document.getElementById("divCustomers").innerHTML = html;
      }
    </script>
    <script
      type="text/javascript"
      src="https://www.runoob.com/try/ajax/jsonp.php?jsoncallback=callbackFunction"
    ></script>
  </body>
</html>
```

- 实例代码中第一个`script`标签中，定义一个名为`callbackFunction`的全局函数
- 第二个`script`标签中，定义 jsonp 请求的数据；就是从`src`的地址加载一个有参的函数调用代码片段；
- 请求地址为：`https://www.runoob.com/try/ajax/jsonp.php?jsoncallback=callbackFunction` 返回值为`callbackFunction(["customername1","customername2"])`,预加载后为：

```js
<script
  type="text/javascript"
  src="https://www.runoob.com/try/ajax/jsonp.php?jsoncallback=callbackFunction"
>
  callbackFunction(["customername1","customername2"])
</script>
```

- 上述代码执行全局函数`callbackFunction`，参数为`["customername1","customername2"]`。所以如果定义了全局函数`callbackFunction`，就会立即调用执行。
- 可以在控制台中自定义参数调用函数`callbackFunction`，进行调试

### 实现

> 上面的代码为写死的 jsonp，接下来动态创建 jsonp

- 1.将传入的 data 数据转化为 url 字符串形式
- 2.处理 url 中的回调函数
- 3.创建一个 script 标签并插入到页面中
- 4.挂载回调函数

```js
// create jsonp function
(function (window, document) {
  // implement function to create jsonp
  const jsonp = function (url, data, callback) {
    let dataString = url.indexOf("?") == -1 ? "?" : "&";
    if (!!data) {
      for (let key in data) {
        dataString += key + "=" + data[key] + "&";
      }
    }

    // const funcName = 'my_json_cb_' + Math.random().toString().replace('.', '');
    // url += dataString + 'callback=' + funcName;
    const funcName = "callbackFunction";
    url += dataString + "jsoncallback=" + funcName;

    const scriptEl = document.createElement("script");
    scriptEl.src = url;
    window[funcName] = function (data) {
      callback(data);
      document.body.removeChild(scriptEl);
    };
    document.body.appendChild(scriptEl);
  };

  window.$jsonp = jsonp;
})(window, document);

// invoke jsonp function
(function (window, document) {
  function callbackFunction(result, methodName) {
    var html = "<ul>";
    for (var i = 0; i < result.length; i++) {
      html += "<li>" + result[i] + "</li>";
    }
    html += "</ul>";
    document.getElementById("divCustomers").innerHTML = html;
  }

  // invoke function
  const url = "https://www.runoob.com/try/ajax/jsonp.php";
  window.$jsonp(url, null, callbackFunction);
})(window, document);
```
[jsonp.html](./assets/jsonp.html)
