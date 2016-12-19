---
title: DIY一个自己的jQuery1
date: 2016-09-15 15:11:50
tags: jquery,javascript
---

jQuery在调用的时候并没有让我们去实例化一个jquery对象实例，然后再调用其自定义的方法，而是可以直接使用诸如`jQuery`或着更常见的`$()`方法。下面我们自己探索下它是如何实现的。

```
var $div = $('div');

(function() {
  var _jQuery = window.jQuery,
      _$ = window.$;

  var version = '0.0.1',
      jQuery = function(selector) {
        console.log(document.querySelector(selector));
      };
  jQuery.prototype = {
    jquery: version,
    constructor: jQuery
  };

  window.$ = window.jQuery = function(selector) {
    return new jQuery(selector); // 在接口函数中定义了方法，而调用接口返回的是new初始化的实例
  };
})();

```

