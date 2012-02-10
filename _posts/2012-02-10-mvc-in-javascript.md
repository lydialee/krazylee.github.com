---
layout: post
title: "MVC in Javascript"
category: 
tags: []
---
{% include JB/setup %}

##Background
  一个Javascript web application的业务逻辑大致分这么几个部分：
- 从remote server上获取数据
- 事件和页面DOM的绑定
- 从页面元素变化的自动渲染render
- routes路由变化的监控


当数据模型较少且单一的情况下，客户端的代码可以比较轻松的解决这几个目的。
而当数据模型较多,且页面结构复杂的时候，客户端代码则需要遵循一定的表现层模式([presentation patterns](http://www.infoq.com/cn/articles/rationalizing-presentation-tier)).
其目的就是各部分代码逻辑的解藕，表现形式就是类似models,controllers,views,routes的目录结构的划分.


##Concepts
更准确的说应该是MVVM(Model-View-ViewModel)才对。
因为严格说来并没有controller的存在，反倒更多的是routes的设置。
这也是为什么backbone在后来的版本中把Controller的名字改成了Route的原因。

###to be continued...


