---
layout: post
title: Android 混淆文件 proguard.cfg 详解
description: 
keywords: Android, proguard
category: Android
tags: [Android, proguard]
comment: true
published: true
date: 2012-07-30 22:00:00
remark: Published by Cynthia Young
---

为了提高被反编译取代码的难度，Google提供一个java代码混淆的工具：proguard，通过proguard，别人即使反编译你的apk包，也只会看到一些让人很难看懂的代码，从而达到保护代码的作用。

从SDK2.3开始我们可以看到在Android-sdk-windows\tools\下面多了一个proguard文件夹，我们只需要在工程的"default.properties"中添加这样一句话“proguard.config=proguard.cfg”，打包签名后的.apk就是混淆的。

一般情况下，只需求增加上面的这一句话就OK了，但是有时候我们需要更复杂的混淆代码，或者混淆代码时影响到了第三方库的调用，这就需要我们对proguard.cfg文件进行一些详细的配置了。

`
Android 混淆文件proguard.cfg详解:  
-injars  Androidtest.jar【jar包所在地址】  
-outjars  out【输出地址】  
-libraryjars    'D:\Android-sdk-windows\platforms\android-9\android.jar' 【引用的库的jar，用于解析injars所指定的jar类】  
-optimizationpasses 5  
-dontusemixedcaseclassnames 【混淆时不会产生形形色色的类名 】  
-dontskipnonpubliclibraryclasses 【指定不去忽略非公共的库类。 】  
-dontpreverify 【不预校验】  
-verbose  
-optimizations !code/simplification/arithmetic,!field/*,!class/merging/* 【优化】  
-keep public class * extends Android.app.Activity　　【不进行混淆保持原样】  
-keep public class * extends Android.app.Application  
-keep public class * extends Android.app.Service  
-keep public class * extends Android.content.BroadcastReceiver  
-keep public class * extends Android.content.ContentProvider  
-keep public class * extends Android.app.backup.BackupAgentHelper  
-keep public class * extends Android.preference.Preference  
-keep public class com.Android.vending.licensing.ILicensingService  
-keep public abstract interface com.asqw.Android.Listener{  
public protected <methods>;  【所有方法不进行混淆】  
}  
-keep public class com.asqw.Android{  
public void Start(java.lang.String); 【对该方法不进行混淆】  
}  
-keepclasseswithmembernames class * { 【保护指定的类和类的成员的名称，如果所有指定的类成员出席（在压缩步骤之后）】  
native <methods>;  
}  
-keepclasseswithmembers class * { 【保护指定的类和类的成员，但条件是所有指定的类和类成员是要存在。】  
public <init>(Android.content.Context, android.util.AttributeSet);  
}  
-keepclasseswithmembers class * {  
public <init>(Android.content.Context, android.util.AttributeSet, int);  
}  
-keepclassmembers class * extends Android.app.Activity {【保护指定类的成员，如果此类受到保护他们会保护的更好 】  
public void *(Android.view.View);  
}  
-keepclassmembers enum * {  
public static **[] values();  
public static ** valueOf(java.lang.String);  
}  
-keep class * implements Android.os.Parcelable {【保护指定的类文件和类的成员】  
public static final Android.os.Parcelable$Creator *;  
}  
`