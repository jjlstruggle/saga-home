---
title: 简易使用IntersectionObserver API
category: js
tags:
  - study
  - js
aimg: /medias/article/1.webp
keywords: IntersectionObserver，IntersectionObserver API，IntersectionObserver简易使用
abbrlink: 11826
date: 2022-05-23 23:15:00
---

## IntersectionObserver

> 用于观察一个元素是否在与根元素交叉（在根元素内部）
> 用途：无限滚动、图片懒加载、控制动画/视频执行（性能优化）

- 一般我们要监测某个元素是否进入了视口，是通过监听 scroll 等事件，调用目标元素的 getBoundingClientRect()方法，得到对于视口左上角的位置，再去判断是否在视口之内，缺点是由于 scroll 事件频发，计算量很大，容易造成性能问题，而这个 api 由浏览器提供的原生方法，可以自动观察元素是否可见

## 构造器与方法

> IntersectionObserver()

```javascript
const io = new IntersectionObserver(callback(entries){
    do something here
}, options);
io.observe(DOM);
```

> observe(element)

- 使监测器监测某个节点，可多次调用

> unobserve(element)

- 使监测器取消监测某个节点，可多次调用

> takeRecords()

- 获取当前监测的数组

> disconnect()

- 终止所有监测

## 简易使用

### 使用 IntersectionObserver 进行图片懒加载

> 实现十分简便，只需几行代码，而且性能方面有很大提升

- 这里提供一个非常简单的 demo，在 react vue 等框架中的实现思路也是差不多的

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      #app {
        width: 100vw;
        height: 100vh;
        overflow: hidden auto;
      }
      img {
        width: 640px;
        height: 270px;
        margin: 60px auto;
        display: block;
      }
    </style>
  </head>
  <body>
    <div id="app">
      <img src="./lazy.jpg" alt="" />
      <img src="./lazy.jpg" alt="" />
      <img src="./lazy.jpg" alt="" />
      <img src="./lazy.jpg" alt="" />
      <img src="./lazy.jpg" alt="" />
      <img src="./lazy.jpg" alt="" />
    </div>
    <script src="./demo.js"></script>
  </body>
</html>
```

```javascript
/**
 * intersectionObserver实现图片懒加载
 */

const srcs = [
  "https://pic4.zhimg.com/v2-af38328d9d111c3b43cfb3075e8178eb_r.jpg",
  "https://pic4.zhimg.com/v2-af38328d9d111c3b43cfb3075e8178eb_r.jpg",
  "https://pic4.zhimg.com/v2-af38328d9d111c3b43cfb3075e8178eb_r.jpg",
  "https://pic4.zhimg.com/v2-af38328d9d111c3b43cfb3075e8178eb_r.jpg",
  "https://pic4.zhimg.com/v2-af38328d9d111c3b43cfb3075e8178eb_r.jpg",
  "https://pic4.zhimg.com/v2-af38328d9d111c3b43cfb3075e8178eb_r.jpg",
];

const imgs = [...document.querySelectorAll("img")];

const intersectionObserver = new IntersectionObserver(function (entries) {
  entries.forEach(({ target, isIntersecting }, index) => {
    // 解构出target和isIntersecting,target是触发的元素,isIntersecting是是否交叉
    isIntersecting &&
      (target.src = srcs[index]) &&
      intersectionObserver.unobserve(target);
  });
});

imgs.forEach((img) => intersectionObserver.observe(img));
```

### 使用 IntersectionObserver 实现无限滚动

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      #app {
        height: 600px;
        width: 400px;
        background-color: #1890ff;
        margin: auto;
        overflow: hidden auto;
      }
      .list {
        width: 200px;
        height: 60px;
        background-color: aquamarine;
        margin: auto;
        text-align: center;
      }
    </style>
  </head>
  <body>
    <div id="app"></div>
    <script src="./demo2.js"></script>
  </body>
</html>
```

```javascript
/**
 * 无线滚动
 */
let arr = [],
  i = (cur = 0);
const app = document.querySelector("#app");

// 模拟一万条数据
while (i++ < 10000) {
  arr.push(i);
}

const io = new IntersectionObserver(
  function (entries) {
    entries.forEach((en, index) => {
      console.log(en);
      const { target, isIntersecting } = en;
      isIntersecting && load() && io.unobserve(target);
    });
  },
  { root: app }
);

const load = () => {
  let j = 0;
  while (j < 10 && arr.length) {
    console.log(j);
    let r = arr.shift();
    let el = document.createElement("div");
    el.className = "list";
    el.innerText = r;
    app.appendChild(el);
    if (j == 9) {
      console.log("ob");
      io.observe(el);
    }
    j++;
  }
};

load();
```

> 示例比较粗糙，太丑勿喷，总之就是一个非常好用的 api，支持度也就 ie 不太行 😂

## 参考链接 🔗

[MDN: IntersectionObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserver/IntersectionObserver)
