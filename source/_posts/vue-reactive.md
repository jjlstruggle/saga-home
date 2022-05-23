---
title: Vue响应式原理浅析
category: vue
tag: [study, vue, js]
date: 2021-4-24 21:45:21
---

> 什么是响应式？

- 当数据变了,视图也会跟着改变，所以我们需要检测到数据的改变，这里就需要用到数据劫持

## Vue2 中的响应式原理

### 初识 **Object.defineProperty**()

> 在 Vue2 中使用了 Object 中的 defineProperty 方法来实现数据劫持
> `Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现 有属性，并返回此对象。 --MDN

> `Object.defineProperty(target,prop,descriptor)`

- 第一个参数为目标对象，第二个参数为目标对象的属性，第三个参数为要定义或修改的属性描述符。

来看一个简单的使用示例

```js
let obj = {};
let temp;
Object.defineProperty(obj, "a", {
  // writable:true, 是否可以更改,不可和get/set一起使用
  enumerable: true, // 是否可枚举
  configurable: true, // 是否可删除
  // value: 10  obj的a属性的值,不可和get/set一起使用
  get() {
    /* 
        	当读取a属性的时候执行的函数并返回该函数的返回值
        */
    return temp;
  },
  set(newVal) {
    // 当改变属性a的值时候执行的函数
    temp = newVal;
  },
});
```

这样当我们访问和修改这个对象的属性 a 的时候就可以监测到啦,此时当 a 的值发生改变时就可以 diff 和更改视图了(大概

我们先来把它简单的封装一下

```js
function defineReactive(target, key, value) {
  if (arguments.length == 2) {
    value = target[key];
  }
  Object.defineProperty(target, key, {
    enumerable: true,
    configurable: true,
    get() {
      console.log("get", value);
      return value;
    },
    set(newVal) {
      console.log("set", newVal);
      value = newVal;
    },
  });
}
```

封装完毕后就可以对对象的一个属性的操作进行捕获了,那么我们问题就来了,如果是对象里嵌套对象再嵌套对象,defineProperty 能监测的到吗？

```js
let obj = {
  a: {
    b: 123,
  },
};

defineReactive(obj, "a");
obj.a.b = 456;
// get {b:123}
```

当我们尝试修改 b 的值时发现并未调用 set，所以我们并没有完全监视到属性 a 的更改

### 观察者:**Observer **类与**Observe**函数

> 我们先来实现对普通对象的所有属性的监测

```js
// 一个工具函数，简单的操作属性的值和属性是否可枚举
function def(obj, key, value, enumerable) {
  Object.defineProperty(obj, key, {
    enumerable,
    value,
    writable: true,
    configurable: true,
  });
}

// 一个工具函数,用来检测对象是否被监测,如果被监测则直接返回，否则将其转化为被监测的对象
function observe(o) {
  if (typeof o != "object") return;
  let ob;
  if (typeof o.__ob__ !== undefined) {
    ob = o.__ob__;
  } else {
    ob = new Observer(o);
  }
  return ob;
}

class Observer {
  constructor(o) {
    def(o, "__ob__", this, false); // 使__ob__属性变为不可枚举
    this.walk(o);
  }
  // 通过这个函数遍历对象来实现监视所有属性
  walk(o) {
    for (let key in o) {
      defineReactive(o, key);
    }
  }
}
```

到这里我们就实现了监测对象的所有表层属性,接下来改写一下简单的改写一下 defineReactive 就可以实现递归监测了

```js
function defineReactive(target, key, value) {
  if (arguments.length == 2) {
    value = target[key];
  }
  observe(value); // 就加了这一行，就实现了深度监测
  Object.defineProperty(target, key, {
    enumerable: true,
    configurable: true,
    get() {
      return value;
    },
    set(newVal) {
      value = newVal;
      observe(newVal); // 防止修改的值也是个对象
    },
  });
}
```

但是这样还是只能检测到普通的对象，当我们修改数组的时候还是监测不到变化,所以下面来介绍 Vue2 是如何监测数组变化的

### **Object.create() && Object.setPrototypeOf()**

略!!!

### 重写数组 7 大方法数组

> 说是重写数组的 7 大方法,其实只是做比较简单的修改而已，怎么可能真的完全重写那么多方法涅

- push()
- pop()
- shift()
- unshift()
- splice()
- sort()
- reverse()

```js
let needChangeMethods = [
  "push",
  "pop",
  "shift",
  "unshift",
  "splice",
  "sort",
  "reverse",
];
let defaultPrototype = Array.prototype;
let methods = Object.create(defaultPrototype);
needChangeMethods.forEach((methodname) => {
  let original = defaultPrototype[methodname];
  def(
    defaultPrototype,
    methodname,
    function () {
      // original();
      const ob = this.__ob__;
      let inserted = [];
      switch (methodname) {
        case "push":
        case "unshift":
          inserted = arguments;
          break;
        case "splice":
          inserted = [...arguments].slice(2);
          break;
      }
      if (inserted) {
        ob.observeArray(inserted);
      }
      return original.apply(this, arguments);
    },
    false
  );
});
```

这里更改一下 Observer 类传入数组时候的处理方法

```js
class Observer {
  constructor(v) {
    def(v, "__ob__", this, false);
    if (Array.isArray(v)) {
      Object.setPrototypeOf(v, methods); // 如果是数组则覆盖它原本的方法
      this.observeArray(v);
    } else {
      this.walk(v);
    }
  }
  observeArray(a) {
    for (let i = 0, l = a.length; i < l; i++) {
      observe(a[i]);
    }
  }
  walk(v) {
    for (let key in v) {
      defineReactive(v, key);
    }
  }
}
```

### 收集依赖

> 在 vue2.x 中，使用到数据的组件视为依赖，在 getter 中收集依赖，在 setter 中触发依赖来实现响应式

##### **Dep**&&**Watcher**

```js
class Observer {
  constructor(v) {

    def(v, "__ob__", this, false);
    if (Array.isArray(v)) {
      Object.setPrototypeOf(v, methods); // 如果是数组则覆盖它原本的方法
      this.observeArray(v);
    } else {
      this.walk(v);
    }
  }
  observeArray(a) {
    for (let i = 0, l = a.length; i < l; i++) {
      observe(a[i]);
    }
  }
  walk(v) {
    for (let key in v) {
      defineReactive(v, key);
    }
  }
}

et uid = 0;

class Dep {
  constructor() {
    // 用于储存watcher实例
    this.subs = [];
    this.id = uid++;
  }
  addSub(sub) {
    this.subs.push(sub);
  }
  depend() {
    // 一个全局位置
    if (Dep.target) {
      this.addSub(Dep.target);
    }
  }
  notify() {
    // 浅克隆
    const subs = this.subs.slice();
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update();
    }
  }
}

let wid = 0;
class Watcher {
  constructor(target, expression, callback) {
    this.id = wid++;
    this.target = target;
    this.getter = parsePath(expression);
    this.callback = callback;
    this.value = this.get();
  }
  update() {}
  get() {
    // 进入依赖收集阶段
    Dep.target = this;
    const obj = this.target;
    var value;
    try {
      value = this.getter(obj);
    } finally {
      // 退出收集阶段
      Dep.target = null;
    }
    return value;
  }
  run() {
    this.getAndInvoke(this.callback);
  }
  getAndInvoke(cb) {
    const value = this.get();
    if (value !== this.value || typeof value === "object") {
      const oldValue = this.value;
      this.value = value;
      cb.call(this.target, value, oldValue);
    }
  }
}
```

此上响应式原理就结束啦,以下为完整代码

```js
function parsePath(str) {
  let segments = str.split(".");
  return (obj) => {
    for (let i = 0; i < segments.length; i++) {
      if (!obj) return;
      obj = obj[segments[i]];
    }
    return obj;
  };
}

function observe(o) {
  if (typeof o != "object") return;
  let ob;
  if (typeof o.__ob__ !== undefined) {
    ob = o.__ob__;
  } else {
    ob = new Observer(o);
  }
  return ob;
}

function def(obj, key, value, enumerable) {
  Object.defineProperty(obj, key, {
    enumerable,
    value,
    writable: true,
    configurable: true,
  });
}

function defineReactive(target, key, value) {
  const dep = new Dep();
  if (arguments.length == 2) {
    value = target[key];
  }
  let childOb = observe(value); // 就加了这一行，就实现了深度监测
  Object.defineProperty(target, key, {
    enumerable: true,
    configurable: true,
    get() {
      // 如果处于收集依赖阶段
      if (Dep.target) {
        dep.depend();
        if (childOb) {
          childOb.dep.depend();
        }
      }
      return value;
    },
    set(newVal) {
      value = newVal;
      childOb = observe(newVal); // 防止修改的值也是个对象
      dep.notify();
    },
  });
}

class Observer {
  constructor(v) {
    this.dep = new Dep();
    def(v, "__ob__", this, false);
    if (Array.isArray(v)) {
      Object.setPrototypeOf(v, methods); // 如果是数组则覆盖它原本的方法
      this.observeArray(v);
    } else {
      this.walk(v);
    }
  }
  observeArray(a) {
    for (let i = 0, l = a.length; i < l; i++) {
      observe(a[i]);
    }
  }
  walk(v) {
    for (let key in v) {
      defineReactive(v, key);
    }
  }
}

let needChangeMethods = [
  "push",
  "pop",
  "shift",
  "unshift",
  "splice",
  "sort",
  "reverse",
];
let defaultPrototype = Array.prototype;
let methods = Object.create(defaultPrototype);
needChangeMethods.forEach((methodname) => {
  let original = defaultPrototype[methodname];
  def(
    defaultPrototype,
    methodname,
    function () {
      // original();
      const ob = this.__ob__;
      let inserted = [];
      switch (methodname) {
        case "push":
        case "unshift":
          inserted = arguments;
          break;
        case "splice":
          inserted = [...arguments].slice(2);
          break;
      }
      if (inserted) {
        ob.observeArray(inserted);
      }
      ob.dep.notify();
      return original.apply(this, arguments);
    },
    false
  );
});

let uid = 0;

class Dep {
  constructor() {
    // 用于储存watcher实例
    this.subs = [];
    this.id = uid++;
  }
  addSub(sub) {
    this.subs.push(sub);
  }
  depend() {
    // 一个全局位置
    if (Dep.target) {
      this.addSub(Dep.target);
    }
  }
  notify() {
    // 浅克隆
    const subs = this.subs.slice();
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update();
    }
  }
}

let wid = 0;
class Watcher {
  constructor(target, expression, callback) {
    this.id = wid++;
    this.target = target;
    this.getter = parsePath(expression);
    this.callback = callback;
    this.value = this.get();
  }
  update() {}
  get() {
    // 进入依赖收集阶段
    Dep.target = this;
    const obj = this.target;
    var value;
    try {
      value = this.getter(obj);
    } finally {
      // 退出收集阶段
      Dep.target = null;
    }
    return value;
  }
  run() {
    this.getAndInvoke(this.callback);
  }
  getAndInvoke(cb) {
    const value = this.get();
    if (value !== this.value || typeof value === "object") {
      const oldValue = this.value;
      this.value = value;
      cb.call(this.target, value, oldValue);
    }
  }
}
```

## Vue3

> Vue3 使用了 ES6 中的新方法来实现数据劫持，那就是 Proxy 类
