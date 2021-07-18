---
title: 安卓开发入门13-Service AIDL
tags:
  - Android
category:
  - Android
index: true
info: true
date: 2021-07-15 14:40:34
---

<!-- more -->

> 四大组件之一，很像是没有界面的Activity

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210715160205.png" alt="image-20210715144201966" style="zoom: 25%;" />

# Service

1. 四大组件之一
2. 用于在后台处理耗时操作
3. 不受Activity生命周期影响

> 诸如音乐播放，文件下载之类的后台耗时操作

## 生命周期

![image-20210715144954121](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210715160205.png)

## 操作

### 启动服务

```java
//启动 服务创建 服务启动
Intent intent = new Intent(this, MyService.class);
                startService(intent);
//停止 服务销毁
                stopService(intent);
```

多次启动服务操作的还是同一个，而且activity销毁服务依然存在，它们的生命周期不同。

### 绑定服务

绑定服务不会启动服务，而且会随着绑定的activity销毁而销毁。

- 主要用来对service任务进行进度监控

```java
Intent intent = new Intent(this, MyService.class);
//conn 连接服务和客户端的桥梁
  ServiceConnection conn; = new ServiceConnection() {
                    @Override
    //正常情况
   public void onServiceConnected(ComponentName name, IBinder service) {
			//这里可以进行监控操作 每次解绑和绑定都会调用
     
   }

    //连接断开
   @Override
   public void onServiceDisconnected(ComponentName name) {

   }
 };
//创建绑定 第三个参数表示没有自动创建
bindService(intent, conn, BIND_AUTO_CREATE);
//解绑销毁
unbindService(conn);
```

**注意**：

- start之后绑定的服务必须先解绑才能停止
- 解绑只调用一次unbind，再次绑定不会调用bind，但是会调用connection对象里的connected方法

### 实现IBinder

> 这可接口要实现的太多，我们一般用Binder实现类

```java
//Service类要求实现此方法，binder会传给connection对象
 @Override
    public IBinder onBind(Intent intent) {
        return new MyBinder();
    }
//自定义Binder

```

# AIDL

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210715160310.png" alt="image-20210715160310298" style="zoom:50%;" />

## 远程操作服务

由于不同module下不能直接获取到对方的类，所以要在xml配置中起别名

```xml
 <service
          android:name=".Service.MyService"
          android:enabled="true"
          android:exported="true">
     <intent-filter>
       <action android:name="com.app.myservice" />
     </intent-filter>
</service>
```

然后使用intent启动或停止服务

```java
 Intent intent = new Intent("com.app.myservice");
                startService(intent);
//停止也一样
```

绑定也是一样的，都需要在intent指定

安卓5.0之后代码如下

![image-20210715164133834](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210715164133.png)

## 使用AIDL

- 创建AIDL文件

首先在service所在module中创建AIDL文件

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210715165839.png" alt="image-20210715165839648" style="zoom:50%;" />

```java

interface IMyAidlInterface {
    /**
     * Demonstrates some basic types that you can use as parameters
     * and return values in AIDL.
     */
    void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,
            double aDouble, String aString);

    //定义自己的接口
    void showProgress();
}

```

build之后会自动生成java文件

现在service返回生成的类，在这里实现方法即可被远程调用

```java
 @Override
    public IBinder onBind(Intent intent) {
        return new IMyAidlInterface.Stub() {
            @Override
            public void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat, double aDouble, String aString) throws RemoteException {
                
            }

            @Override
            public void showProgress() throws RemoteException {

            }
        };
    }
```

- 自身调用

```java
ServiceConnection conn = new ServiceConnection() {
  @Override
  public void onServiceConnected(ComponentName name, IBinder service) {
    IMyAidlInterface iMyAidlInterface = IMyAidlInterface.Stub.asInterface(service);
    iMyAidlInterface.showProgress();
  }

  @Override
  public void onServiceDisconnected(ComponentName name) {

  }
                }
```

- 远程调用

创建一份一样的AIDL文件正常用即可，核心在于生成的文件

```java
ServiceConnection conn = new ServiceConnection() {
      @Override
      public void onServiceConnected(ComponentName name, IBinder service) {
        IMyAidlInterface iMyAidlInterface = 					      					                                                                            IMyAidlInterface.Stub.asInterface(service);
        iMyAidlInterface.showProgress();
      }

      @Override
      public void onServiceDisconnected(ComponentName name) {

      }
                    }
```

