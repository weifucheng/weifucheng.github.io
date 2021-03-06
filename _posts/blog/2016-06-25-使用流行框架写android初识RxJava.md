---
layout: post
title:  "使用流行框架写android之初识RxJava"
categories: blog
---

## 使用流行框架写android之初识RxJava ##

### 为什么使用Rxjava ###
RxJava几乎是2015年最火的一款开源框架了。
这款框架解决的最大问题就是**异步**，最常见的就是在子线程中访问网络请求数据，然后在UI线程中刷新UI。如果使用同步来解决，UI线程便不能做其他事情，很容易造成界面的卡顿，之前异步的方法都是使用Handler，Asynctask这些（当然android中所有异步的底层实现绝对是handler）,但解决简单问题还好，遇到一些比较复杂的业务便会使代码逻辑变得混乱。使用RxJava无论多么复杂的逻辑，它都是那一套链式调用（一句话解决），去掉那一层层冗余的嵌套。

### 概念篇 ###
RxJava使用的是一种扩展的观察者模式：

RxJava 有四个基本概念：Observable (可观察者，即被观察者)、 Observer (观察者)、 subscribe (订阅)、事件。

Observable 和Observer 通过 subscribe() 方法实现订阅关系，从而 Observable 可以在需要的时候发出事件来通知Observer。

与传统观察者模式（类似给button注册OnClickListener的方式）不同， RxJava 的事件回调方法除了普通事件 onNext() （相当于 onClick() / onEvent()）之外，还定义了两个特殊的事件：onCompleted() 和 onError()。

onCompleted(): 事件队列完结。

onError(): 事件队列异常。

Node:一般observer采用它的扩展接口Subscriber

刚刚开始使用Rxjava懂了这么就够了，至于之后的一些什么from(),map(),flatMap(),filter(),你有需求的时候再去看，不然这个时候就会看懵你的。


### 代码实战篇 ###

使用Rxjava的一般套路

    Observable.create(new Observable.OnSubscribe<T>() {
            @Override
            public void call(final Subscriber<? super T> subscriber) {
                if(!subscriber.isUnsubscribed()){
                    //你的网络请求等费时操作
                }
            }
        }).subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
				.subscribe(new Subscriber<T>() {
            @Override
            public void onCompleted() {
					
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(T t) {
                //写你更新Ui的一些操作
            }
        });

一般情况下更简化为

     Observable.create(new Observable.OnSubscribe<In>() {
            @Override
            public void call(final Subscriber<? super In> subscriber) {
                if(!subscriber.isUnsubscribed()){
                    //你的网络请求等费时操作
                }
            }
        }).subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Action1<In>() {
                    @Override
                    public void call(In in) {
						//你的更新UI操作
                    }
                });

这种写法非常便于我们简化代码

这就是一个非常简单的RxJava的模式，至于你中间要加什么变化，你可以自己查看官方文档，总之功能异常强大，使用RxJava需要注意的是，不要在一个函数内写过多代码，要使用变化函数去链式调用。

RxJava最好配合Java8中的Lamada表达式，这样会使你的代码异常简洁

上述代码使用Lamada表达式后，会变成

    Observable.create(subscriber ->{网络请求等费时操作} )
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(o -> {更新UI操作});

够简洁不，这篇文章只是Rxjava的快速使用。

### 扩展阅读 ###
推荐几篇文章：



- [http://blog.csdn.net/lzyzsd/article/details/41833541](http://blog.csdn.net/lzyzsd/article/details/41833541)
这是个系列文章，教大家使用RxJava，基本把常用的RxJava变化全写了


- [http://gank.io/post/560e15be2dca930e00da1083#toc_8](http://gank.io/post/560e15be2dca930e00da1083#toc_8)
这篇是抛物线大神写的，内容比较透彻的分析了RxJava的实现原理

- 还有就是官方文档了
