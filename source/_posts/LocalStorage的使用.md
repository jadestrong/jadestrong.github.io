---
title: LocalStorage的使用
date: 2016-09-17 11:48:29
tags: localStorage,wrap 
---

首先，了解一下localStorage的信息，它是Web Storage API的一部分，另一部分是sessionStorage。在使用上，这两者的API的是一样的，不过localStorage是可以用于离线存储数据，而sessionStorage当浏览器关闭时，数据会丢失。

- storage.getItem(key): 根据给定的key，返回存储的数据；
- storage.removeItem(key): 移除匹配key的数据；
- storage.set(key, value): 使用给定键值存储响应的给定值；
- storage.clear(): 清空storage的内容。

除了使用上面的接口函数，还可以直接使用诸如`storage.key = value;`等快捷方式来存储数据。
另外，localStorage和sessionStorage合计有10M空间可用。

然后，它们的存储的都是字符串，所以我们不论存储和取出的都是字符串格式，当我们想得到保存的对象时并不理想，所以可以使用`JSON.parse()和JSON.stringify()`来对要存储的以及要取出的数据进行预处理。

如：
```
export default storage => ({
    get(k) {
        try {
            return JSON.parse(storage.getItem(k));
        } catch (e) {
            return null;
        }
    },
    set(k, v) {
        storage.set(k, JSON.stringify(v));
    }
});
```
