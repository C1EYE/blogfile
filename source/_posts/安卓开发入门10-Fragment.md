---
title: 安卓开发入门10-Fragment
tags:
  - Android
category:
  - Android
index: true
info: true
date: 2021-07-05 15:06:19
---

<!-- more -->

 # Fragment基本应用概念

> 和Activity差在哪呢?

[fragment](https://developer.android.com/guide/fragments)

## 设计思想

官方给出的示意图，可以看到同样的内容在平板和手机端展示效果不同，如果用Activity需要写三份，而Fragment可以拆分，一个Activity可以运行多个Fragment，而Fragment不能脱离Activity，可以根据情况组合使用。

- 所以Fragment可以用来解决不同分辨率的适配问题 

- 由于Fragement可以复用，可以进行模块化开发，对于复杂的Activity这很有帮助
- Activity是屏幕的主体，Fragment是Activity的组成成分
- Fragement同样具有生命周期

> Fragment表示应用界面中可重复使用的一部分。Fragment 定义和管理自己的布局，具有自己的生命周期，并且可以处理自己的输入事件。Fragment 不能独立存在，而是必须由 Activity 或另一个 Fragment 托管。Fragment 的视图层次结构会成为宿主的视图层次结构的一部分，或附加到宿主的视图层次结构。

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210705150842.png" alt="image-20210705150842554" style="zoom: 33%;" />

## Fragment生命周期

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210705151733.png" alt="image-20210705151733655" style="zoom: 50%;" />

## Fragment两种加载方式

- 静态加载：xml

写一个类继承Fragment

```java
public class ListFragment extends Fragment {
    @Override
    public void onAttach(Context context) {
        super.onAttach(context);
    }

    //创建视图
    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
      //在这里使用inflater填充完之后返回就可以了，也可以像下面一样做些别的事情
       View inflate = inflater.inflate(R.layout.fragment_list, container, false);
        TextView textView = inflate.findViewById(R.id.textView);
        textView.setText("ALG");
        return inflate;
    }

    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
    }

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }
}
```

在用到的Activity的布局里直接写就行

```xml
<fragment
        android:id="@+id/listFragment"
        android:layout_width="match_parent"
        android:name="com.example.fragementtest.ListFragment"
        android:layout_height="match_parent" />
```

- 动态加载

分三步，创建容器，创建fragment，将fragment放入容器中

```xml
 <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/detailContainer"
        android:orientation="horizontal"
        >

    </LinearLayout>
```

```java
 ListFragment fragment = new ListFragment();
//开启事务
 getSupportFragmentManager().beginTransaction()
   //这里还有remove之类的方法，很灵活
 .add(R.id.detailContainer, fragment).commit();
```

- 显然还是需要先在xml中预定好位置（container），但是可以根据情况加载，更灵活

# 传值

## Activity->Fragment

在Fragment类里写一个工厂方法

```java
 public static final String ALG = "ALG";
    private String o;

    public static ListFragment newInstance(String title){
        ListFragment listFragment = new ListFragment();
        Bundle bundle = new Bundle();
        bundle.putString(ALG, title);
        listFragment.setArguments(bundle);
        return listFragment;
    }
```

然后在创建时获取到bundle

```java
 @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Bundle arguments = getArguments();
        if(arguments!=null){
            o = (String) arguments.get(ALG);
        }
    }
```

就可以用了

```java
 //创建视图
    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View inflate = inflater.inflate(R.layout.fragment_list, container, false);
        TextView textView = inflate.findViewById(R.id.textView);
        textView.setText(o);
        return inflate;
    }
```

这样就可以在Activity里传值给Fragment了

```java
ListFragment fragment = ListFragment.newInstance("ALG");
        getSupportFragmentManager().beginTransaction()
                .add(R.id.detailContainer, fragment).commit();
```

## Fragment->Activity

> 没有太好的方法，一般使用回调

在Fragment类里创建回调接口

```java
 private OnTitleClickListener onTitleClickListener;

    public interface OnTitleClickListener {
        void onClick(String title);
    }
```

在onCreateView里获取到组件设置监听器

```java
 TextView textView = inflate.findViewById(R.id.textView);
        textView.setText(o);
        textView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (onTitleClickListener != null) {
                    onTitleClickListener.onClick("ALG");
                }
            }
        });
```

之后再创建Fragment的地方设置监听器就好，无论是匿名类还是继承

```java
public class MainActivity extends AppCompatActivity implements ListFragment.OnTitleClickListener{

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override
    public void onClick(String title) {
        setTitle(title);
    }
}
```

  