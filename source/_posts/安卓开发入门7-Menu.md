---
title: 安卓开发入门7-Menu
tags:
  - Android
category:
  - Android
date: 2021-06-18 20:16:08
---

<!-- more -->

## Menu

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618201711.png" alt="image-20210618201711649" style="zoom: 25%;" />

## OptionMenu

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618202214.png" alt="image-20210618202214536" style="zoom:50%;" />

- 菜单也是一种资源，要放到res下，这里创建了一个menu目录来存放

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618202806.png" alt="image-20210618202806653" style="zoom:50%;" />

- 之后就可以在目录中新建菜单的配置文件了

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618202947.png" alt="image-20210618202947928" style="zoom:50%;" />

- Item是菜单项，menu是菜单，可以认为item必须在menu内部，而item也可以包含menu
- 这样就能构建出多级菜单，用Android Studio的可视化拖动构建也挺方便的。

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:title="保存"/>
    <item android:title="设置"/>
    <item android:title="更多" >
        <menu >
            <item android:title="1" />
            <item android:title="2" />
        </menu>
    </item>

</menu>
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618203228.png" alt="image-20210618203228458" style="zoom:50%;" />

- 在代码中加载菜单资源

```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    //将菜单资源加载到菜单
    getMenuInflater().inflate(R.menu.option,menu);
    return true;
}
```

- 一些常用属性

```xml
<item android:title="保存"
    app:showAsAction="always"/>
```

always 一直可见

never 不显示

ifRoom 有空间就显示

icon 图标

- 获取点击事件的方法都差不多,重写对应方法，获取ID,做些事情

```java
@Override
public boolean onOptionsItemSelected(@NonNull MenuItem item) {
    switch (item.getItemId()) {
        case R.id.set:
            Toast.makeText(this, "FUCK", Toast.LENGTH_SHORT);
            break;
        default:
            break;
    }
    return super.onOptionsItemSelected(item);
}
```

- finish()可以结束应用

## ContextMenu

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618205626.png" alt="image-20210618205626571" style="zoom: 25%;" />

- 如果说optionMenu的所有者是Activity，那ContextMenu的所有者就是普通的View

- contexMenu 需要注册到一个View上，创建和OptionMenu是完全一样的

```java
@Override
public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
    getMenuInflater().inflate(R.menu.context, menu);
    super.onCreateContextMenu(menu, v, menuInfo);
}
```

注册

```Java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    registerForContextMenu(findViewById(R.id.ctx_btn));
}
```

操作

```java
@Override
public boolean onContextItemSelected(@NonNull MenuItem item) {
    switch (item.getItemId()){
        case R.id.ctx_btn:
            //TODO 
            break;
    }
    return true;
}
```

## 上下文操作模式

- 首先先创建上下文操作模式
- 在create方法中设置menu

```java
ActionMode.Callback ac = new ActionMode.Callback() {
    @Override
    public boolean onCreateActionMode(ActionMode mode, Menu menu) {
      	getMenuInflater().inflate(R.menu.context, menu);
        return true;
    }

    @Override
    public boolean onPrepareActionMode(ActionMode mode, Menu menu) {
        return false;
    }

    @Override
    public boolean onActionItemClicked(ActionMode mode, MenuItem item) {
        return false;
    }

    @Override
    public void onDestroyActionMode(ActionMode mode) {
    
    }
};
```

- 设置View的长按事件为启动上下文操作模式

```java
findViewById(R.id.ctx_btn).setOnLongClickListener(new View.OnLongClickListener() {
    @Override
    public boolean onLongClick(View v) {
        startActionMode(ac);
        return false;
    }
});
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618215325.png" alt="image-20210618215325607" style="zoom:50%;" />

效果就是长按之后出现在顶部

## PopupMenu

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618215633.png" alt="image-20210618215633751" style="zoom:33%;" />

- inflater填充需要一个布局和一个menu对象，而popupMenu需要绑定一个view

```java
button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        //实例化对象
        PopupMenu popupMenu = new PopupMenu(MainActivity.this,button);
        //加载菜单资源:利用Menulnflater将Menu资源加载到PopupMenu.getMenu返回的menu中
        popupMenu.getMenuInflater().inflate(R.menu.context,popupMenu.getMenu());
        //为PopupMenu设置点击监听器
        popupMenu.setOnMenuItemClickListener(new PopupMenu.OnMenuItemClickListener() {
            @Override
            public boolean onMenuItemClick(MenuItem item) {
                return false;
            }
        });
        //显示
        popupMenu.show();


    }
});
```

## Menu创建方式

- 可以直接在Java源文件里写Menu布局

But why?

- 喜欢自虐的可以试试

## 注意

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618222017.png" alt="image-20210618222017795" style="zoom:50%;" />