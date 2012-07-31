---
layout: post
title: 防止Android代码被反编译
description: 
keywords: Android, Decompiler
category: Android
tags: [Android, Decompiler]
comment: true
published: true
date: 2012-07-30 21:00:00
remark: Published by Cynthia Young
---

作为Android应用开发者，自己辛辛苦苦开发的应用可以被别人很轻易的就反编译出来，一些不想公开的逻辑被别人识破，更有不道德的人，拿着自己的代码重新打包发布。
代码拿去还算是小事，万一某些重要的数据被盗用，万一被坏人植入一些恶意代码，然后重新发布盗用用户信息，后果真是不堪设想。

利用SDK编写的代码，生成的apk，很容易就可以反编译了。原本加密apk是一种最好的方式，但由于apk是Android虚拟机加载的，它有一定的规范，加密apk后Dalvik无法识别apk了。
所以完全避免是不可能的，总有人能够破解你的代码。

目前能够采取的方式就是提高被反编译后，获取代码的难度。

以下是提高反编译难度的几种方式：

**1. 关键代码使用jni调用本地代码**  
可以将一些重要的代码逻辑写入.so文件，使用jni方式调用。  

优点：用c或c++编写，相对于class文件，so相对比较难于反编译。  
缺点：很多搞java的程序员不太熟悉如何写c或c++代码，同时本地代码很难调试。出错容易导致整个虚拟机死掉，用户感受不好。 

**2. 混淆java代码**  
混淆是不改变代码逻辑的情况下，增加无用代码，或者重命名，使反编译后的源代码难于看懂。  

优点：网上开源的java代码混淆工具较多，而且从SDK2.3开始android-sdk\tools\下面多了一个proguard文件夹，通过proguard混淆代码。  
缺点：代码虽然变得晦涩难懂，但是代码的逻辑，重要的数据还是能够被轻易的获取的。  




















