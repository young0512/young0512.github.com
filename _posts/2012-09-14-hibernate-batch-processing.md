---
layout: post
title: Hibernate批处理操作优化 (批量插入、更新与删除)
description: 
keywords: Hibernate, JDBC
category: Hibernate
tags: [Hibernate, JDBC]
comment: true
published: true
date: 2012-09-14 16:00:00
remark: Published by Cynthia Young
---


### 问题描述

1. 我开发的网站加了个新功能：需要在线上处理表数据的批量合并和更新，昨天下午发布上线，执行该功能后，服务器的load突然增高，变化曲线异常，SA教育了我一番，让我尽快处理，将CPU负载降低。

2. 工作所需，我经常要写些程序批量处理数据，每次执行几十万数据处理的时候，我机子的CPU都会飙高，而且数据处理速度会越来越慢。比如第一个1W条要5分钟，第二个1W条就要10分钟，要干其他事情的时候机子也卡的不行，只能等着处理完数据。

其实我一直认为是数据量太大，从来不认为是程序问题，所以一直没怎么关注过。这次问题浮上表面，所以要好好解决下！


### 产生原因

**主要原因：Hibernate的一级缓存影响。**

我们每次保存的东西都会保存在Session缓存中，这就是Hibernate的一级缓存，如果我们一直循环执行save等操作，缓存里东西会越来越多，速度也就越来越慢，服务器一直在循环处理，自然也会增加负载。

这本来就是Hibernate不擅长的地方，而且一级缓存不可以不用，如果我们要保存的数据量十分巨大，那么在程序中执行添加、更新方法时，Session对象自身开辟的一级缓存会不断消耗，直至OutOfMemoryError (内存溢出异常)。

这就需要我们管理好Hibernate的缓存，或者不使用Hibernate。


### 解决方案


**批量插入优化**

1、仍旧用Hibernate API来进行批处理，但在一定的量的时候，及时的清除缓存。

1）优化Hibernate，在配置文件中设置hibernate.jdbc.batch_size参数，来指定每次提交SQL的数量。  
配置hibernate.jdbc.batch_size参数的原因就是尽量少读数据库，hibernate.jdbc.batch_size参数值越大，读数据库的次数越少，速度越快。

{% highlight xml %}

<!--设置hibernate.jdbc.batch_size参数-->
<hibernate-configuration>
	<session-factory>
		.........
		<property name="hibernate.jdbc.batch_size">50</property>
		.........
	<session-factory>
<hibernate-configuration>

{% endhighlight %}

2）程序及时清除缓存，即每插入一定量的数据后及时把它们从内部缓存中清除掉，释放占用的内存。  
Session实现了异步write-behind，它允许Hibernate显式地写操作的批处理

示例代码：

{% highlight java %}

// 每处理50条清空缓存
session.save(myObject);
if (i/50 == 0) {
	session.flush();
	session.clear();
}

// 在我的项目中写法如下：
if (i/50 == 0) {
	this.getHibernateTemplate().flush();
	this.getHibernateTemplate().clear();
}

{% endhighlight %}


2、通过JDBC API来做批量插入，绕过Hibernate API。这个方法性能上是最好的，也是最快的。

示例代码：

{% highlight java %}

String insertSql = "insert into user(name,address) values(?,?)";
Session session = gethibernateTemplate().getSessionFactory().openSession();
Transaction tx = session.beginTransaction(); //使用Hibernate事务处理边界
Connection conn = session.connection(); 
PrepareStatement stmt = conn.prepareStatement(insertSql);

for(int i = 0; i++; i<10000) {
	stmt.setString(1, "testName");
	stmt.setString(2, "testAddress");
	stmt.addBatch();
	if (i % 100 == 0) {
		stmt.executeBatch();
		conn.commit();
	}
}

stmt.executeBatch();
conn.commit();
tx.commit();
session.close();

{% endhighlight %}

经测试，采用JDBC API来做批量处理，性能上比使用Hibernate API要高很多。

测试数据，待续。。。


**批量更新与删除优化**

待续。。。






















