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

## Activity之间的数据传递

- 使用putExtra方法就好了

```java
Intent intent = new Intent(this, MyDialog.class);
intent.putExtra("ALG", 666);
```

- 接收方根据key来接受

```java
Intent intent = getIntent();
 if(intent!=null){
     intent.getStringExtra("ALG");
}
```

- 可以使用Bundle来存放不同类型的值

```java
Bundle bundle = new Bundle();
bundle.putString("ALG","ALG");
bundle.putInt("ALG",2);
```

- 想要在返回时传值
- 首先要在启动时使用

```java
startActivityForResult(intent,666,bundle);
```

- 返回时

```java
setResult(RESULT_OK，intent);
finish();
```

- 在这里处理，根据请求和返回值来判断是哪个Activity

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
}
```