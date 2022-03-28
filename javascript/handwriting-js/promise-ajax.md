## 基于 Promise 的 ajax 封装

- 参数简陋封装

```js
function ajax(url, method = "get", param = {}) {
  return new Promise(function (resolve, reject) {
    const xhr = new XMLHttpRequest();
    const stringParam = getStringParam(param);
    if (method === "get" && !!stringParam) {
      url.indexOf("?") > -1 ? (url += stringParam) : (url += `?${stringParam}`);
    }

    xhr.open(method, url);
    xhr.onload(function () {
      const result = {
        status: xhr.status,
        statusText: xhr.statusText,
        headers: xhr.getAllResponseHeaders(),
        data: xhr.response || xhr.responseText,
      };

      if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
        resolve(result);
      } else {
        reject(result);
      }
    });
    // 设置请求头
    xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
    // 跨域携带cookie
    xhr.withCredentials = true;
    // 错误处理
    xhr.onerror = function () {
      reject(new TypeError("请求出错"));
    };

    xhr.timeout = function () {
      reject(new TypeError("请求超时"));
    };

    xhr.onabort = function () {
      reject(new TypeError("请求被终止"));
    };

    if (method === "post") {
      xhr.send(stringParam);
    } else {
      xhr.send();
    }
  });
}

function getStringParam() {
  let result = "";
  for (const key in param) {
    result += `${key}=${param[key]}&`;
  }
  return result;
}
```
