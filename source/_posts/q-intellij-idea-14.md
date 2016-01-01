---
title: "Question Shredder: Intellij IDEA\_14 Tomcat Console Output Messy Code"
tags:
  - IDE
  - Question
categories:
  - Develop
date: 2015-08-04 15:04:48
---

## Q.description
Tomcat 7 控制台输出日志乱码

## Q.resolvent
Intellij IDEA 中设置 Web Server 的虚拟机编码，这样 IDE 的编码（控制台编码受IDE影响）和 Web Server 输出的编码一致，问题解决。

<!-- more -->

![屏幕快照 2015-08-04 14.59.16](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/08/屏幕快照-2015-08-04-14.59.16-632x252.png)

启动 Web Server之后再分析 JVM 的参数可以看到 -Dfile.encoding=UTF-8。

> 遇见性更新
