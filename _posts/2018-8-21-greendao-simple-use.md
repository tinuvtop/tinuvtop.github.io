---
layout: post
title: 'greenDAO的简单使用'
subtitle: ''
date: 2018-08-21
categories: Android后端框架
cover: ''
tags: Android 数据库
---
# 写在前面
今天想学习一下greenDAO,它是Android中最有名的ORM(对象关系映射)的一个库,这个库更像是一个后端框架,尤其是当我学习过Spring Boot后有这种感觉,它的很大作用就是简化了程序人员对数据库的操作

今天我使用greenDAO的最新版本3.2.2来使用,同时为了方便调试数据,我还使用另外一个库`Android-Debug-Database`这个库我使用1.0.4版本

# 引入依赖项
依赖项有两个,一个是`greenDAO`一个是`Android-Debug-Database`,尤其要注意的是`greenDAO`还需要引入插件.
一般我们都是在app模块(Module:app)下添加依赖,但greenDAO在project的build.gradle都要配置,在project的build.gradle是要引入插件,在app模块引入是为了引入这个包和配置greenDAO的一些属性.
这是官方的配置
## 在project下的build.gradle
```Java
// In your root build.gradle file:
buildscript {
    repositories {
        jcenter()
        mavenCentral() // add repository
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.1'
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' // add plugin
    }
}
```

## 在app模块下的build.gradle
```Java
// In your app projects build.gradle file:
apply plugin: 'com.android.application'
apply plugin: 'org.greenrobot.greendao' // apply plugin
 
dependencies {
    implementation 'org.greenrobot:greendao:3.2.2' // add library
}
```


## greenDAO的配置(在app模块下)
```Java
greendao{
    schemaVersion 2 // 数据库版本号
    daoPackage  'me.tinuv.greendaopractice.greendao'//greenDao 自动生成的代码保存的包名
    targetGenDir   'src/main/java' //自动生成的代码存储的路径，默认是 build/generated/source/greendao.
    generateTests false //true的时候自动生成测试单元
    targetGenDirTests 'src/androidTest/java' //测试单元的生成目录默认是
}
```

## 接下来引入`Android-Debug-Database`
```
debugImplementation 'com.amitshekhar.android:debug-db:1.0.4'
```
这个只在debug中依赖,在release中不依赖

## `Android-Debug-Database`的配置
主要是配置端口号
这个实在app模块配置
```Java
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
        debug {
            resValue("string", "PORT_NUMBER", "8080")// 配置Android-debug-database的端口
        }
    }
```

# 使用
## 定义实体类
首先定义一个实体类,并使用greenDAO的注解`@Entity`,同样还可以使用注解`@Id`,稍微学习过Spring Boot的我对这两个注解还算熟悉.
比如
```java
@Entity
public class MovieEntity {

    @Id
    private int id;

    private String title;

    private String rate;

    private String imageurl;
    
    // 省略getter/setter
}
```
之后build project,greenDAO的插件为为你自动生成一系列的代码,Entity会变成这样
```java
@Entity
public class MovieEntity {

    @Id
    private int id;

    private String title;

    private String rate;

    private String imageurl;

    @Generated(hash = 1955964426)
    public MovieEntity(int id, String title, String rate, String imageurl) {
        this.id = id;
        this.title = title;
        this.rate = rate;
        this.imageurl = imageurl;
    }

    @Generated(hash = 1226161063)
    public MovieEntity() {
    }
    
    // 省略getter和setter
}
```
还会为你自动生成几个类其中`DaoMaster`和`DaoSession`是公共的类,以Dao结尾的是Entity对应的类,如MovieEntity对应MovieDao.


## 初始化
一般在`Application`进行初始化.为了使用我们自定义的Application类还需要在manifests文件注册一下(在manifests的application标签设置name属性就可以).在这里初始化的好处是可以在全局中查询数据库
```xml
    <application
        android:name=".MyApplication"
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
在application的初始化一种固定写法
```java
public class MyApplication extends Application {

    private static DaoSession daoSession;

    @Override
    public void onCreate() {
        super.onCreate();
        initGreenDao();
    }

    private void initGreenDao(){
        DaoMaster.DevOpenHelper helper = new DaoMaster.DevOpenHelper(this,"test.db");
        SQLiteDatabase db = helper.getWritableDatabase();
        DaoMaster daoMaster = new DaoMaster(db);
        this.daoSession = daoMaster.newSession();
    }

    public static DaoSession getDaoSession(){
        return daoSession;
    }
}
```

# 使用Dao类查询数据库
为了简便这里直接在MainActivity中读写数据库,在实际使用中,视情况而定
## 插入一条数据
```java
        DaoSession daoSession = getDaoSession();
        MovieEntityDao movieEntityDao = daoSession.getMovieEntityDao();
        MovieEntity movieEntity = new MovieEntity();
        movieEntity.setImageurl("http://tinuv.me/yym.png");
        movieEntity.setRate("10");
        movieEntity.setTitle("photo");
        movieEntityDao.insert(movieEntity);
```

这里还有一个要注意的地方就是自动生成Id的时候Id的类型必需是`Long`类型,否则会出现转化错误


## 接下来要考虑的
在多线程的时候访问数据库要不要加锁处理,如多个线程同时调用inert方法会不会出现错误,需不需要`synchronized`处理,在获取DaoSession的时候需不需要使用单例模式.另外说一句`Android-debug-database`真的很好用.


