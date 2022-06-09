---
title: 解决在map中使用async无法正确获取结果
category: js
tags:
  - study
  - js
keywords: 前端基础，js，promise,map
date: 2020-12-20 12:36:47
---

## 在map中使用async
> 当我们直接在数组中调用map方法并且使用async关键字修饰函数之后返回得到的全是pendding状态的promise,无法正确获取到返回的值
```js
let arr = ['a','b','c']

let data = arr.map(async(item,index)=>{
        const res = await Promise.resolve(arr[index])
        return res
	})

console.log(data)
```

得到的结果如下:

![](http://121.40.19.111:7002/cdn/image/20220610/1654792783428.webp)

在Promise中有一个all方法，可以将多个promise的结果一次性返回

```js
(async () => {
    let arr = ['a', 'b', 'c']
    let data = await Promise.all(arr.map(async (item, index) => {
        // 模拟异步请求得到的数据
        const res = await Promise.resolve(arr[index])
        return res
    }))
    console.log(data)
    // code here
})()
```

这样就这正常的拿到所有结果啦:

![](http://121.40.19.111:7002/cdn/image/20220610/1654794116791.webp)
