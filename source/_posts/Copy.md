---
title: Copy
date: 2026-03-02 14:00:28
tags:
---
### 解锁浏览器禁止复制,浏览器控制台脚本
##  字节系
``` bash
javascript:(function(){var style=document.createElement('style');style.innerHTML='*{user-select:text!important;-webkit-user-select:text!important;}';document.head.appendChild(style);})();
```

``` bash
document.body.innerHTML = document.body.innerHTML;
```


