---
title: 选择第k个最大数
date: 2016-09-26 00:31:11
tags: 算法 数据结构
---

给定一组N个数，找出其中第k个最大数？
最直观的做法是对这N个数进行降序排序，然后输出第k个数即可。
另一种是构建一个临时数组存放取出k个数，进行降序排序,然后依次读取剩余的数，比较其与临时数组中的第k个数，若小则放弃，否则安装选择排序的思路对该数进行排序，最后将最后的一个数移除，使得数组始终保持在k个，则最终的结果即是临时数组的第k个数。
代码如下：
``` javascript
var input = [];
var max = 10;
for (var m = 0; m < max; m++) {
	input.push(Math.floor(Math.random() * max));
}
console.log('full input array: ' + input);
var temp = [];
var k = Math.floor(input.length / 2);
var i,j;
for (i = 0; i < k; i++) {
    temp.push(input.pop());
}
temp.sort(function(v1, v2) {return v2 - v1;});
console.log('Get the k elements and sort them: ' + temp);
console.log('Last some elemenets which will be inserted in temp: ' + input);
input.forEach(function(item) {
    if (item > temp[k - 1]) {
        //在这里使用temp[j]<item来做for循环的判定条件值得学习，使得代码更简洁
       for (j = k - 1; temp[j] < item; j--) 
       		temp[j+1] = temp[j];
       temp[j+1] = item; //重中之重，记住是插入到j+1的位置
       temp.pop();
       console.log('every time insert into a new element, the results is: ' + temp);
    }
});
console.log(temp);
console.log(temp[k - 1]);
```
上面代码中注释的地方是容易出错的地方，需要好好体会。

上面这两种方法在输入数据少的时候，效果还可以，但是当数据量超大了，就不能短时间内给出结果了，所以这两种方法都不算好的算法。
