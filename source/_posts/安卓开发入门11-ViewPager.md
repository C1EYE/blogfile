---
title: 安卓开发入门11-ViewPager
tags:
  - Android
category:
  - Android
index: true
info: true
date: 2021-07-05 17:01:03
---

<!-- more -->

# ViewPager应用场景

- 引导界面，多图片查看

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210705170235.png" alt="image-20210705170235856" style="zoom:25%;" />

- 多Tab页面，App导航

- 广告播放

> 来自support包,在新版已经变成androidx包下了

## 一个Demo

```java
public class MainActivity extends AppCompatActivity {

    private ViewPager view_pager;
    private int[] mLayoutIDs = {
            R.layout.view_first,
            R.layout.view_second
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
        final ArrayList<View> views = new ArrayList<View>();
        for (int i = 0; i < mLayoutIDs.length; i++) {
            views.add(getLayoutInflater().inflate(mLayoutIDs[i], null));
        }
      //需要重写四个方法来实现
        view_pager.setAdapter(new PagerAdapter() {
            @Override
            public int getCount() {
                return mLayoutIDs.length;
            }

            @Override
            public boolean isViewFromObject(@NonNull View view, @NonNull Object object) {
                return false;
            }

            @NonNull
            @Override
            public Object instantiateItem(@NonNull ViewGroup container, int position) {
                View child = views.get(position);
                container.addView(child);
                return child;
            }

            @Override
            public void destroyItem(@NonNull ViewGroup container, int position, @NonNull Object object) {
                container.removeView(views.get(position));
            }
        });
    }

    private void initView() {
        view_pager = (ViewPager) findViewById(R.id.view_pager);

    }
}

```

- 可以在viewpage里放图像组件，达到轮播图的效果

- 通过监听器可以实现底部的导航效果

> 做法就是在布局里添加一个容器，然后根据页面数动态添加点，在监听器里监听变化改变图片

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210705201810.png" alt="image-20210705201810074" style="zoom:33%;" />

```java
view_pager.setOnPageChangeListener(new ViewPager.OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

            }

            @Override
            public void onPageSelected(int position) {

            }

            @Override
            public void onPageScrollStateChanged(int state) {

            }
        });
```

# Fragment配合VIewPager

实现一个适配器就行，很容易

```java
  view_pager.setAdapter(new FragmentPagerAdapter(getSupportFragmentManager()) {
            @Override
            public Fragment getItem(int position) {
                return null;
            }

            @Override
            public int getCount() {
                return 0;
            }
        });
```

传值在Fragment传值里介绍过，写一个工场方法

## 实现底部导航

