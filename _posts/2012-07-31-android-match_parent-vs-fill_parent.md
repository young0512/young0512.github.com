---
layout: post
title: Android match_parent 和 fill_parent 区别
description: 
keywords: Android, match_parent, fill_parent
category: Android
tags: [Android, match_parent]
comment: true
published: true
date: 2012-07-31 12:30:00
remark: Published by Cynthia Young
---

match_parent 和 fill_parent 其实是一样的，只是有人觉得 match_parent 表达更准确些，所以从Android 2.2开始 fill_parent 改名为 match_parent。

Android Doc says: 

	FILL_PARENT means that the view wants to be as big as its parent, minus the parent's padding, if any. This value is deprecated starting in API Level 8 and replaced by MATCH_PARENT.

	MATCH_PARENT means that the view wants to be as big as its parent, minus the parent's padding, if any. Introduced in API Level 8.

android.view.ViewGroup 的静态嵌套类 LayoutParams 中的代码：

	public static final int FILL_PARENT = -1;

	public static final int MATCH_PARENT = -1;

所以，match_parent 和 fill_parent 的定义本质也是一样的，值均为 -1，只是换了个别名而已。

因此从 API Level 为 8 开始我们可以直接用 match_parent 来代替 fill_parent，若考虑低版本的情况(version 7 or below)，就需要用 fill_parent，否则会出现编译错误。




