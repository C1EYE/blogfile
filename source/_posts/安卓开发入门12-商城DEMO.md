---
title: 安卓开发入门12-商城DEMO
tags:
  - Android
category:
  - Android
index: true
info: true
date: 2021-07-07 17:23:49
---

<!-- more -->

> 经过一段时间的学习，现在来做一个小Demo

# 闪屏页

核心就这一句,延迟跳转主页面

```java
protected Handler mHandler = new Handler();

mHandler.postDelayed(new Runnable() {
            @Override
            public void run() {
                startActivity(new Intent(SplashActivity.this, MainActivity.class));
            }
        }, 2000);
```

