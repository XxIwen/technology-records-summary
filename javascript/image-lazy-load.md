## 图片懒加载

### 监听图片高度

- 图片，用一个其他属性存储真正的图片地址：

```html
<img
  src="loading.gif"
  data-src="https://cdn.pixabay.com/photo/2022/02/15/00/40/lemonade-7014122_1280.jpg"
  alt=""
/>
<img
  src="loading.gif"
  data-src="https://cdn.pixabay.com/photo/2022/03/06/05/30/clouds-7050884_1280.jpg"
  alt=""
/>
<img
  src="loading.gif"
  data-src="https://cdn.pixabay.com/photo/2022/02/28/15/28/sea-7039471_1280.jpg"
  alt=""
/>
<img
  src="loading.gif"
  data-src="https://cdn.pixabay.com/photo/2014/08/01/00/08/pier-407252_1280.jpg"
  alt=""
/>
<img
  src="loading.gif"
  data-src="https://cdn.pixabay.com/photo/2015/08/13/20/06/flower-887443_1280.jpg"
  alt=""
/>
<img
  src="loading.gif"
  data-src="https://cdn.pixabay.com/photo/2010/12/13/10/09/abstract-2384_1280.jpg"
  alt=""
/>
<img
  src="loading.gif"
  data-src="https://cdn.pixabay.com/photo/2015/10/24/11/09/drop-of-water-1004250_1280.jpg"
  alt=""
/>
```

- 通过图片`offsetTop`和`window`的`innerHeight`，`scrollTop`判断图片是否位于可视区域。

```js
// HTMLElement.offsetTop 为只读属性，它返回当前元素相对于其 offsetParent 元素的顶部内边距的距离
// window.innerHeight 浏览器窗口的视口（viewport）高度（以像素为单位）；如果有水平滚动条，也包括滚动条高度。
// document.documentElement.scrollTop || document.body.scrollTop; 滚动条距离顶部高度
// Element.clientHeight 这个属性是只读属性，对于没有定义CSS或者内联布局盒子的元素为0，否则，它是元素内部的高度(单位像素)，包含内边距，但不包括水平滚动条、边框和外边距。
// HTMLElement.offsetHeight 是一个只读属性，它返回该元素的像素高度，高度包含该元素的垂直内边距和边框，且是一个整数(包括元素的边框、内边距和元素的水平滚动条)。
const imgs = document.getElementsByTagName("img");
let n = 0;
const sleep = function (imgElement) {
  return new Promise(function (resolve, reject) {
    imgElement.onload = function (data) {
      resolve(data);
    };
    imgElement.onerror = function (error) {
      reject(error);
    };
  }).catch(function (e) {
    console.error(e);
  });
};
const throttle = function (handler, time) {
  let timer = null;
  return function (...args) {
    if (!timer) {
      timer = setTimeout(() => {
        timer = null;
        handler.apply(this, args);
      }, time);
    }
  };
};
const lazyLoad = async function () {
  const seeHeight = window.innerHeight;
  const scrollTop =
    document.documentElement.scrollTop || document.body.scrollTop;
  for (let i = n; i < imgs.length; i++) {
    const currentImg = imgs[i];
    if (imgs[i].offsetTop < seeHeight + scrollTop) {
      if (currentImg.getAttribute("src") == "loading.gif") {
        currentImg.src = currentImg.getAttribute("data-src");
      }
      await sleep(currentImg);
      n = i + 1;
    }
  }
};
lazyLoad();
window.addEventListener("scroll", throttle(lazyLoad, 200));
```
[DEMO 1](./assets/image-lazy-load_1.html)

### IntersectionObserver

> IntersectionObserver 接口 (从属于 Intersection Observer API) 提供了一种异步观察目标元素与其祖先元素或顶级文档视窗(viewport)交叉状态的方法。祖先元素与视窗(viewport)被称为根(root)。

- `Intersection Observer`可以不用监听`scroll`事件，做到元素一可见便调用回调，在回调里面我们来判断元素是否可见。

```js
// IntersectionObserver

// <div id="IMG"></div>
// <div id="IMG_FOOTER"></div>
const data = `
      https://cdn.pixabay.com/photo/2022/02/15/00/40/lemonade-7014122_1280.jpg,
      https://cdn.pixabay.com/photo/2022/03/06/05/30/clouds-7050884_1280.jpg,
      https://cdn.pixabay.com/photo/2022/02/28/15/28/sea-7039471_1280.jpg,
      https://cdn.pixabay.com/photo/2022/02/28/15/28/sea-7039471_1280.jpg,
      https://cdn.pixabay.com/photo/2015/08/13/20/06/flower-887443_1280.jpg,
      https://cdn.pixabay.com/photo/2010/12/13/10/09/abstract-2384_1280.jpg,
      https://cdn.pixabay.com/photo/2015/10/24/11/09/drop-of-water-1004250_1280.jpg
      `.split(",");
const imgArea = document.getElementById("IMG");
const imgFooter = document.getElementById("IMG_FOOTER");
const handler = new IntersectionObserver(function (entries) {
  entries.forEach(function (entry) {
    const imgElement = entry.target;
    if (data.length > 0 && entry.intersectionRatio > 0) {
      const imgElement = document.createElement("img");
      const seeHeight = window.innerHeight;
      const src = data.shift();
      imgElement.src = src;
      imgElement.height = seeHeight;
      imgArea.appendChild(imgElement);
    }
  });
});

handler.observe(imgFooter);
```
[DEMO 2](./assets/image-lazy-load_2.html)
[DEMO 3](./assets/image-lazy-load_3.html)
