---
title: 安卓开发入门3-UI布局
date: 2021-06-17 00:10:20
tags: 
- Android
category:
- Android
---

<!-- more -->

## 布局，清单文件

### Activity

- 继承自Activity的可视化界面，当然版本更迭可能包装好几层了

- 在进入页面时就会执行onCreate方法
- 布局信息来自R类里面的属性
- 通过R.类别名.资源名去操作对应的资源

> 关于R类
>
> Android在打包时，通过AAPT工具，对主工程和引入的依赖里的所有资源文件进行编译压缩，并会对res/里的资源文件如drawable、layout、values等生成唯一的id，同时生成R.java文件，保存所有的id值，以及生成resource.arsc文件，建立id对应资源的值(如string)或文件路径(如png)的关系表。

[R](https://blog.csdn.net/qq_15827013/article/details/103717702)

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}
```

### 布局文件

- 格式是activity_***.xml（只是个规范，并不强制）
- 每个activity对应一个布局文件

- 在布局文件中设置组件和布局的属性,下面是一个例子

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    android:background="#ff0000">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/fuck_you"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</LinearLayout>
```

![image-20210617114131403](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617114131.png)

![image-20210617114156694](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617114156.png)

> 关于属性的详细请看

[文档](https://developer.android.com/guide/topics/ui/declaring-layout?hl=zh-cn)

### 清单文件

- 在根目录下AndroidManifest.xml
- 项目全局配置
- 每个activity都需要在这里配置，当然大部分时候ide自动配置就行
- 在<intent-filter> 里设置为主页面和启动页面

```xml
<activity android:name=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

## 常用布局

一些常用的布局介绍

- 通用属性

![image-20210617160652018](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617160652.png)

Match_parent:和父容器一致，也就是说尽可能填满父容器

wrap_content:包裹内容，大小取决于内部组件大小，但是不会超过屏幕

dp像素 字体用sp

### 线性布局

- 使用orientation设置排列方向

```xml
 <LinearLayout
       android:layout_width="match_parent"
       android:layout_height="wrap_content"
       android:orientation="horizontal">
       <TextView
           android:layout_width="0dp"
           android:layout_height="wrap_content"
           android:text="@string/fuck_you"
           android:layout_weight="1"
           android:background="#ff0000"/>
       <TextView
           android:layout_width="0dp"
           android:layout_height="wrap_content"
           android:text="@string/fuck_you"
           android:layout_weight="2"
           android:background="#00ff00"/>

   </LinearLayout>
```



![image-20210617161659464](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617161659.png)

- 这里新增了两个个重要概念

#### 权重

- 使用layout_weight属性设置权重

- 对于处于同一容器的组件设置水平或垂直方向的权重可以按比例分配组件空间
- 按权重分配的组件需要将对应的layout_width/height设为0dp
- 设置权重将会最后绘制

#### 重力

- 使用layout_gravity属性设置重力
- 直观理解就是对于当前组件所在容器的重力方向进行设置
- 组件就会向重力的方向对齐

例子

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">
   <LinearLayout
       android:layout_width="match_parent"
       android:layout_height="60dp"
       android:orientation="horizontal"
       android:background="#333333">
        <TextView
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:text="< 小P"
            android:textSize="26sp"
            android:layout_gravity="center_vertical"
            android:paddingLeft="15sp"
            android:layout_weight="1"/>
       <ImageView
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:src="@mipmap/ic_launcher_round"/>
   </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal"
        android:layout_weight="1">
    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:background="#cccccc"
        android:orientation="horizontal">
        <ImageView
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:src="@mipmap/ic_launcher_round"
            android:layout_weight="1"
            />
        <EditText
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:layout_weight="3"
            android:text="@string/fuck_you"/>
    </LinearLayout>
</LinearLayout>
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617182507.png" alt="image-20210617182507676" style="zoom: 25%;" />

### 相对布局

相对布局的每个组件需要参照物，比如父容器，其他组件

![image-20210617182304206](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617182304.png)

![image-20210617183405386](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617183405.png

![image-20210617183436974](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617183437.png)

- 通过id属性加id

例子：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="match_parent">
    <TextView
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:id="@+id/center"
        android:textSize="30dp"
        android:text="center"
        android:background="#00ff00"
        android:layout_centerInParent="true"/>
    <TextView
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_alignParentRight="true"
        android:layout_alignParentBottom="true"
        android:text="rightbottom"
        android:textSize="30dp"
        android:background="#00ff00"/>
    <TextView
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_above="@+id/center"
        android:background="#0000ff"
        android:layout_toLeftOf="@+id/center"/>

</RelativeLayout>
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617183316.png" alt="image-20210617183316346" style="zoom: 33%;" />

- 小案例

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="2"
        android:background="#ff0000"
        android:orientation="horizontal">

        <RelativeLayout
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="2"
            android:background="#ffffff">

            <androidx.appcompat.widget.AppCompatImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@mipmap/ic_launcher_round" />
        </RelativeLayout>

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:background="#ff00ff"
            android:orientation="vertical">

            <RelativeLayout
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight="1"
                android:background="#00ff00">

                <androidx.appcompat.widget.AppCompatImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:src="@mipmap/ic_launcher_round" />
            </RelativeLayout>

            <RelativeLayout
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight="1"
                android:background="#0000ff">

                <androidx.appcompat.widget.AppCompatImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:src="@mipmap/ic_launcher_round" />
            </RelativeLayout>
        </LinearLayout>
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:background="#00ff00"
        android:orientation="horizontal">

        <RelativeLayout
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:background="#00ffff">

            <androidx.appcompat.widget.AppCompatImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@mipmap/ic_launcher_round" />
        </RelativeLayout>

        <RelativeLayout
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:background="#0000ff">

            <androidx.appcompat.widget.AppCompatImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@mipmap/ic_launcher_round" />
        </RelativeLayout>

        <RelativeLayout
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:background="#00ff00">

            <androidx.appcompat.widget.AppCompatImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@mipmap/ic_launcher_round" />
        </RelativeLayout>
    </LinearLayout>
</LinearLayout>
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617190014.png" alt="image-20210617190013943" style="zoom:25%;" />

## 表格布局

```xml
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="match_parent">
    <TableRow>
        <Button/>    
    </TableRow>
    <TableRow>
        <Button/>
    </TableRow>
    
</TableLayout>
```

行和列显而易见

```xml
android:stretchColumns="0,1,2"
```

- 可以伸展某一列

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617215303.png" alt="image-20210617215303700" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617215341.png" alt="image-20210617215341523" style="zoom:50%;" />

## 帧布局

感觉没啥用，先放着

## 网格布局

直接定义行和列数

- 可以跨行列

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210617215456.png" alt="image-20210617215456884" style="zoom:50%;" />

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content" android:layout_height="wrap_content"
    android:rowCount="2"
    android:columnCount="2">
    <Button android:layout_columnSpan="2"
        android:layout_gravity="fill"
        />
    <Button/>
    <Button/>

</GridLayout>
```

