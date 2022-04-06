---
title: debug反爬
categories:
    - 爬虫
tags:
    - Python
    - 爬虫
---

#### 关于解决(function anonymous() {debugger})的问题

##### 1. 实现原理

如何实现无限debugger呢？实现无限debugger就是不断的打断你，页面跳转到source页面，阻止你看内容。
写一个不断调用debugger即可。
```javascript

(function() {var a = new Date(); debugger; return new Date() - a > 100;}())

```

##### 2. 问题解决


