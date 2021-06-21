---
title: 安卓开发入门6-Activity
tags:
  - Android
category:
  - Android
date: 2021-06-18 18:38:12
---



安卓四大件

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618183903.png" alt="image-20210618183903559" style="zoom:50%;" />

<!-- more -->

## Activity基础知识

- 我们所看到的所有界面都是Activity
- 用户直接交互的组件

> 每个Activity都需要在AndroidManifest.xml里注册

```xml
<application
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    <activity android:name=".MainActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />

            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>
</application>
```

- 默认打开的是MAIN

> 布局和数据组成Activity

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618190012.png" alt="image-20210618190012686" style="zoom:33%;" />

- 布局是可以动态设置的，为了开发方便，layout会被索引到R类中
- 通过组件ID获取组件设置各种事件监听和属性

```Java
EditText name_text = (EditText) findViewById(R.id.name_text);
```

## Activity之间的跳转

- 使用intent,参数分别是从哪里到哪里

```java
Intent intent = new Intent(this, newA.class);
startActivity(intent);
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618191526.png" alt="image-20210618191526309" style="zoom:50%;" />

### 四种启动模式

安卓使用栈来维护Activity之间的关系，每开始一个新的Activity就会入栈，对于新的Activity如何处理有四种方式，在AndroidManifest.xml中配置。

- stanard（默认）

这种模式是默认的模式，每打开一个新的Activity就会向栈中入栈，并不关心是否已经有相同的Activity在栈中

 <img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618191817.png" alt="image-20210618191817243" style="zoom: 33%;" />

在AndroidManifest.xml中不配置或者如下配置就是这种模式

```xml
 <activity android:name=".MainActivity"
            android:launchMode="standard">
 </activity>
```

> 要注意的是，并非只有一个栈，而谁启动了Activity，Activity就会加入谁所在的栈中

- singleTop

顶部只有一个，也就是说如果相同的Activity在栈顶直接就会使用而不是新建

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618192349.png" alt="image-20210618192349772" style="zoom: 25%;" />

- singleTask

如果栈中有要打开的Activity直接使用，而且会将这个Activity之后入栈的Activity全部出栈

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618200508.png" alt="image-20210618200507994" style="zoom: 25%;" />

- singleInstance

会另外创建一个栈，这种模式下activity会单独占用一个栈，即使已经出现在其他栈中

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618200637.png" alt="image-20210618200637363" style="zoom:25%;" />

在代码中可以设置启动模式以及操作栈

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210618201003.png" alt="image-20210618201003095" style="zoom:50%;" />

> 实际开发中具体用到哪种启动模式完全取决于需求

