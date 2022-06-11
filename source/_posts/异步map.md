---
title: map中使用async异步函数
category: js
tags:
  - study
  - js
keywords: '前端基础，js，promise,map'
abbrlink: 57699
date: 2020-12-20 12:36:47
updated: 2020-12-24 19:27:02
---

## 在map中使用async
> 当我们直接在数组中调用map方法并且使用async关键字修饰函数之后返回得到的全是promise,无法正常使用返回的值
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

* 在Promise中有一个all方法，可以将多个promise的结果一次性返回，它的参数为一个数组，数组里每一个元素都是Promise，它会在所有的Promise都结束时(无论成功还是失败)一次返回，返回值为每一个promise的结果的数组

```js
// 结合IIFA
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
