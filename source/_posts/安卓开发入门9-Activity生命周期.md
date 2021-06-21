---
title: 安卓开发入门9-Activity生命周期
tags:
  - Android
category:
  - Android
date: 2021-06-19 15:37:05
---

<!-- more -->

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210619153911.png" alt="image-20210619153911443" style="zoom: 25%;" />

## 创建时1->2->3

完成这三就可以看到界面并交互了

## 销毁时4->5->6

6之后就真没了，但是5之后还可以7

- 页面被activity遮挡时会pause，完全被覆盖时会stop
- dialog并不会导致pause
- pause之后可以resume，stop之后可以restart

## 其他一些

- 销毁之前

```
@Override
public void onSaveInstanceState(@NonNull Bundle outState, @NonNull PersistableBundle outPersistentState) {
    super.onSaveInstanceState(outState, outPersistentState);
}
```