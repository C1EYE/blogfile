---
title: vue-template 踩坑总结
date: 2021-04-27 19:40:20
tags:
---

😅聪明人不犯二次错奥

<!-- more -->

## 全局变量

- 如果想要将内容抽取到全局变量里需要在项目目录下找到env开头文件比如.env.development

```js
    # 这是这个配置的名称，显然你可以有多个配置，每个配置单独一个文件，以.env开头之后接.xxx
    # 在package.json里修改使用的配置
    # "build:stage": "vue-cli-service build --mode development",
    ENV = 'development'
    # 全局变量
    VUE_APP_BASE_API = 'http://localhost:9000'
    VUE_APP_OSS_PATH = 'https://guli-c1eye.oss-cn-beijing.aliyuncs.com'
```

- 使用全局变量要注意

> 名字一定要对应啊！！！



```html
<template>
  <div class="home">
    <!-- template中使用环境变量 -->
     API: {{ api }}
  </div>
</template>

<script>
export default {
  name: "home",
  data() {
    return {
      api: process.env.VUE_APP_BASE_API
    };
  },
  mounted() {
    // js代码中使用环境变量
    console.log("BASE_URL: ", process.env.VUE_APP_BASE_API);
    console.log("VUE_APP_API: ", process.env.VUE_APP_OSS_PATH);
  }
};
</script>
    
```


