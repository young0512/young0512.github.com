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
Session实现了异步write-behind，它允许Hibernate显式地写操作的批处理。

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
Session session = getHibernateTemplate().getSessionFactory().openSession();
Connection conn = session.connection();
PrepareStatement stmt = conn.prepareStatement(insertSql);

// 方式1：自动提交
conn.setAutoCommit(true);
for(int i = 0; i++; i<10000) {
	stmt.setString(1, "testName");
	stmt.setString(2, "testAddress");
	stmt.execute();
}

// 方式2：批量提交
conn.setAutoCommit(false);
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

// 关闭session
session.close();

{% endhighlight %}

附测试数据：

{% highlight java %}

// 测试方法：循环插入10000条数据，拆分10页，每页1000条。

// 直接Hibernate的save()方法，不做任何处理。
page 0 process time : 5925
page 1 process time : 6722
page 2 process time : 8019
page 3 process time : 9456
page 4 process time : 10263
page 5 process time : 11511
page 6 process time : 12988
page 7 process time : 13969
page 8 process time : 15196
page 9 process time : 16820

// Hibernate的save()方法，但每1个清除缓存。
page 0 process time : 10257
page 1 process time : 10709
page 2 process time : 11223
page 3 process time : 10595
page 4 process time : 10990
page 5 process time : 10222
page 6 process time : 10453
page 7 process time : 10196
page 8 process time : 9645
page 9 process time : 10295

// Hibernate的save()方法，但每50个清除缓存。
page 0 process time : 5848
page 1 process time : 5480
page 2 process time : 5739
page 3 process time : 5960
page 4 process time : 6287
page 5 process time : 5947
page 6 process time : 7012
page 7 process time : 6235
page 8 process time : 6063
page 9 process time : 6055

// JDBC的auto commit 方式
page 0 process time : 840
page 1 process time : 800
page 2 process time : 800
page 3 process time : 847
page 4 process time : 806
page 5 process time : 829
page 6 process time : 1042
page 7 process time : 893
page 8 process time : 857
page 9 process time : 854

// JDBC的batch方式，每50个commit
page 0 process time : 827
page 1 process time : 801
page 2 process time : 918
page 3 process time : 828
page 4 process time : 856
page 5 process time : 831
page 6 process time : 815
page 7 process time : 842
page 8 process time : 817
page 9 process time : 937

{% endhighlight %}


经测试：

1）若直接使用Hibernate，处理同样数据的时间会递增，甚至成倍增加，而且在测试过程中CPU使用率一直在70%上下。

2）若在使用Hibernate中每save一次都清空缓存的话，虽然时间不会递增，但处理速度很慢。在本例中采用每50个清空一次缓存较为合适，实际应用视情况而定。
一定量的时候清空缓存，虽然速度上没有提升，但会比较稳定，不会随着时间陡增，而且测试中CPU使用率也维持在20%上下，可以挽救一点性能损失，使系统相对稳定。

3）若使用JDBC API，不论auto commit方式还是batch方式，相比Hibernate在性能上都有近10倍的提升。不过在数据量较大的时候，推荐使用batch方式。


**批量更新与删除优化**

Hibernate2中，对于批量更新/删除操作，都是先将符合要求的数据查出来，然后再做更新/删除操作。这样一来会占用大量内存，而且海量数据处理的时候性能很低。

而Hibernate3对批量更新/删除提供了支持，能够直接执行批量更新或批量删除语句，无需把被更新或删除的对象先加载到内存中，类似于JDBC的批量更新/删除操作。

不过对于循环处理数据更新和删除场景，建议还是使用JDBC，方法同上：批量插入的方法2。




