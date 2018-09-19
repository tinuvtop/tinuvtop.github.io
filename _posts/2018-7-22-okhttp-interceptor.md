---
layout: post
title: 'okhttp拦截器'
subtitle: ''
date: 2018-07-22
categories: Java
cover: ''
tags: Java okhttp 拦截器
---
#### 介绍
拦截器可以实现网络监听,网络请求,响应重写和请求失败重试的功能.

本例使用的软件版本:
Java:1.8.0_181
okhttp:okhttp3 3.4.1


#### 实现网络监听功能
实现网络监听功能就是可以监听请求的信息和响应信息,并做相应的处理,在这里举个例子,打印请求和响应的头部信息
```java
public class Main {
    public static void main(String args[]) throws IOException {

        OkHttpClient client = new OkHttpClient.Builder()
                .addInterceptor(chain ->{
                    Response response = null;
                    Request request = chain.request();
                    response = chain.proceed(request);
                    System.out.println("request headers\n"+request.headers().toString());
                    System.out.println("---------");
                    System.out.println("response headers\n"+response.headers().toString());
                    return response;})
                .build();

        Request request = new Request.Builder()
                .url("http://api.douban.com/v2/movie/subject/1764796")
                .build();

        client.newCall(request).execute();
    }
}
```

#### 重写请求
这个重写包括了请求头和请求体,比如重写post请求的参数
```java
public class Main {
    public static void main(String args[]) throws IOException {

        OkHttpClient client = new OkHttpClient.Builder()
                .addInterceptor(chain -> {
                    Response response = null;
                    Request request = chain.request();
                    System.out.println("original resquest headers");
                    System.out.println("request headers\n" + request.headers().toString());
                    System.out.println("after add params request headers");
                    Request pRequest = request
                            .newBuilder()
                            .header("Content-Encoding", "gzip")
                            .build();
                    System.out.println("request headers\n" + pRequest.headers().toString());
                    response = chain.proceed(pRequest);
                    System.out.println("---------");
                    System.out.println("response headers\n" + response.headers().toString());
                    return response;
                })
                .build();

        Request request = new Request.Builder()
                .url("http://api.douban.com/v2/movie/subject/1764796")
                .build();

        client.newCall(request).execute();
    }
}
```
可以看到利用原来的request构造了一个新的request对象,并在里面添加了一个请求头部的参数


#### 重写响应
比如说重写响应的头信息
```java
public class Main {
    public static void main(String args[]) throws IOException {
        OkHttpClient client = new OkHttpClient.Builder()
                .addInterceptor(chain -> {
                    Response response = null;
                    Request request = chain.request();
                    response = chain.proceed(request);
                    System.out.println("original response header:\n"+response.headers().toString());
                    Response pResponse = response.newBuilder()
                            .header("Cache-Control", "max-age=60")
                            .build();
                    System.out.println("after add a header:\n"+pResponse.headers().toString());
                    return pResponse;
                })
                .build();

        Request request = new Request.Builder()
                .url("http://api.douban.com/v2/movie/subject/1764796")
                .build();
        client.newCall(request).execute();
    }
}
```


#### 注意
okhttp有两个拦截器,一个是application interceptor,一个是network interceptor,从名称我们可以看出侧重点,一个是从应用的层面拦截,这个拦截忽略了请求的一些中间结果,比如被重定向,重定向必须进行多次请求,当然也会返回多次请求响应,但是如果使用的是application interceptor只会拦截第一次请求和最后一次响应结果,也就是最终结果,如果使用的network interceptor拦截这个需要重定向的请求,他会把所有中间的请求和响应统统拦截,因为它侧重于对网络的拦截.


#### 关于Request.Build类和Response.Build类的header和addheader
官方(文档)的解释是:
```
addHeader(String name, String value)
Adds a header with name and value.

header(String name, String value)
Sets the header named name to value.
```
一个是添加一个叫name的值为value
一个是设置名字为name的值为value

