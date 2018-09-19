---
layout: post
title: 'ObjectBox Data Browser的使用'
subtitle: ''
date: 2018-08-23
categories: Android后端框架
cover: ''
tags: ObjectBox Android
---
# 介绍
ObjectBox Data Browser是一个object box数据库可视化的一个工具,在调试中非常有用,能够让你形成数据库的视觉模型,所以我觉得是非常有用的,但是为了使用这个工具也是折腾了我很久,一个原因是我对Gradle不熟,一个是我英语不够好,人家注意事项写在哪里,但我就是理解错误了.

# 引入
为了使用object box只要引入插件就可以了

在project的build.gradle中引入插件
```Java
buildscript {
    ext.objectboxVersion = '2.1.0'
    dependencies {
        classpath "io.objectbox:objectbox-gradle-plugin:$objectboxVersion"
    }
}
```
在app模块的bulid.gradle应用插件
```java
apply plugin: 'io.objectbox' // after applying Android plugin
```
为了使用这个工具还必须引入两个东西
```Java
debugImplementation "io.objectbox:objectbox-android-objectbrowser:2.1.0"
releaseImplementation "io.objectbox:objectbox-android:2.1.0"
```
这两个东西一个是在debug的时候也就是开发的时候引入,一个是正式发布时才应用.

直接这样引入时候造成重复文件冲突的
>Otherwise the build will fail with a duplicate files error (like Duplicate files copied in APK lib/armeabi-v7a/libobjectbox.so) because the ObjectBox plugin will add the objectbox-android library again.

解决办法是把apply plugin放在gradle.build的最后,这样build的时候会最后应用插件,就不会造成文件冲突了,就像这样
```Java
// 为了使用objectbox data browser
apply plugin: 'com.android.application'


android {
    compileSdkVersion 27
    defaultConfig {
        applicationId "me.tinuv.doubanclient"
        minSdkVersion 21
        targetSdkVersion 27
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:27.1.1'
    implementation 'com.android.support.constraint:constraint-layout:1.1.2'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'

    // https://mvnrepository.com/artifact/io.reactivex.rxjava2/rxjava
    // 引入RxJava2,版本:2.1.10
    implementation group: 'io.reactivex.rxjava2', name: 'rxjava', version: '2.1.10'

    // https://mvnrepository.com/artifact/io.reactivex.rxjava2/rxandroid
    // 引入RxAndroid 版本:2.0.1
    implementation group: 'io.reactivex.rxjava2', name: 'rxandroid', version: '2.0.1'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/retrofit
    // 引入retrofit2 版本:2.2.0
    implementation group: 'com.squareup.retrofit2', name: 'retrofit', version: '2.2.0'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/adapter-rxjava2
    // 引入adpter-rxjava2 版本:2.2.0
    implementation group: 'com.squareup.retrofit2', name: 'adapter-rxjava2', version: '2.2.0'

    // https://mvnrepository.com/artifact/com.squareup.okhttp3/okhttp
    // 引入okhttp3 版本:3.5.0
    implementation group: 'com.squareup.okhttp3', name: 'okhttp', version: '3.5.0'

    // https://mvnrepository.com/artifact/com.google.code.gson/gson
    // 引入json 版本:2.8.0
    implementation group: 'com.google.code.gson', name: 'gson', version: '2.8.0'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/converter-gson
    // 引入converter-gson 版本:2.2.0
    implementation group: 'com.squareup.retrofit2', name: 'converter-gson', version: '2.2.0'

    // 引入RecyclerView
    implementation 'com.android.support:recyclerview-v7:27.1.1'

    // 引入SmartrefreshLayout
    implementation 'com.scwang.smartrefresh:SmartRefreshLayout:1.1.0-alpha-12'

    debugImplementation "io.objectbox:objectbox-android-objectbrowser:2.1.0"
    releaseImplementation "io.objectbox:objectbox-android:2.1.0"

}
apply plugin: 'io.objectbox'
```
之前不使用objectbox data browser
```Java
apply plugin: 'com.android.application'
apply plugin: 'io.objectbox'

android {
    compileSdkVersion 27
    defaultConfig {
        applicationId "me.tinuv.doubanclient"
        minSdkVersion 21
        targetSdkVersion 27
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:27.1.1'
    implementation 'com.android.support.constraint:constraint-layout:1.1.2'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'

    // https://mvnrepository.com/artifact/io.reactivex.rxjava2/rxjava
    // 引入RxJava2,版本:2.1.10
    implementation group: 'io.reactivex.rxjava2', name: 'rxjava', version: '2.1.10'

    // https://mvnrepository.com/artifact/io.reactivex.rxjava2/rxandroid
    // 引入RxAndroid 版本:2.0.1
    implementation group: 'io.reactivex.rxjava2', name: 'rxandroid', version: '2.0.1'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/retrofit
    // 引入retrofit2 版本:2.2.0
    implementation group: 'com.squareup.retrofit2', name: 'retrofit', version: '2.2.0'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/adapter-rxjava2
    // 引入adpter-rxjava2 版本:2.2.0
    implementation group: 'com.squareup.retrofit2', name: 'adapter-rxjava2', version: '2.2.0'

    // https://mvnrepository.com/artifact/com.squareup.okhttp3/okhttp
    // 引入okhttp3 版本:3.5.0
    implementation group: 'com.squareup.okhttp3', name: 'okhttp', version: '3.5.0'

    // https://mvnrepository.com/artifact/com.google.code.gson/gson
    // 引入json 版本:2.8.0
    implementation group: 'com.google.code.gson', name: 'gson', version: '2.8.0'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/converter-gson
    // 引入converter-gson 版本:2.2.0
    implementation group: 'com.squareup.retrofit2', name: 'converter-gson', version: '2.2.0'

    // 引入RecyclerView
    implementation 'com.android.support:recyclerview-v7:27.1.1'

    // 引入SmartrefreshLayout
    implementation 'com.scwang.smartrefresh:SmartRefreshLayout:1.1.0-alpha-12'
}

```
# 使用objectbox data browser
之后就可以使用这个工具,官方推荐在Application或者它的子类初始化
```Java
    @Override
    public void onCreate() {
        super.onCreate();
        boxStore = MyObjectBox.builder().androidContext(MyApplication.this).build();
        if (BuildConfig.DEBUG) {
            boolean started = new AndroidObjectBrowser(boxStore).start(this);
            Log.i("ObjectBrowser", "Started: " + started + "   " + boxStore.getObjectBrowserPort());
        }
    }
```
默认只能在手机上的浏览器查看,如果想在电脑上查看数据库,可以使用这个命令
```Java
adb forward tcp:8090 tcp:8090
```
然后在电脑和手机输入地址就可以访问了