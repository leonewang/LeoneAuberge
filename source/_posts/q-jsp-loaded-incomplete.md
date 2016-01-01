---
title: "Question Shredder: JSP Analysis To HTML Incomplete"
tags:
  - JSP
  - Question
categories:
  - Develop
date: 2015-11-09 15:20:53
---
<!-- more -->
[![2015-11-09 16.20.12](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/11/屏幕快照-2015-11-09-16.20.12-1020x647.png)](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/11/屏幕快照-2015-11-09-16.20.12.png)

## 问题描述
jsp 解析后 **html页面加载不完整**，把前面的代码删一点后面的就跟着多加载一点

## 环境
`Tomcat7` + `MySQL`

## 直接原因
jsp页面中调用的java代码出现了异常，`java.lang.ArrayIndexOutOfBoundsException`，**数组下标越界错误**，未考虑到某些因素导致数组初始值大小错误。

## 归纳其他原因

* jsp 缓存大小问题，在 jsp 页面中，默认是 autoFlush="true"，也就是
  ``` html
  <%@ page contentType="text/html;charset=UTF-8" autoFlush="true"%>;
  ```
  但通常情况下只用写成
  ``` html
  <%@ page contentType="text/html;charset=UTF-8"%>
  ```
  就默认为 `autoFlush="true"`。）但也可能出现错误，这个时候很可能会报 `java.io.IOException: Error: JSP Buffer overflow` 的错误，表示 **jsp 缓存大小** 不够，所以就必须设置增大 jsp 的缓存大小，也就是如 `buffer="300kb"` ，完整的如下：

  ``` html
  <%@ page contentType="text/html;charset=UTF-8" autoFlush="false" buffer="300kb"%>
  ```

  **另外要注意** 的是：buffer 的值设置的如果不够大，那么在显示数据的时候，由于没有足够的缓存会抛出一些异常，比如 `NullPointer` 错误等，这个时候并不是你程序出了错，只要将 buffer 值再设置得大一些到不再出错即可。如果还有 `outOfMemery` 异常，那么就需要调整 JVM 的内存大小了。

*   空指针异常。
