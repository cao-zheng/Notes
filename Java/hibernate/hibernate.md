# hibernate

> hibernate是一款orm框架(object relation mapping对象关系映射)。hibernate属于4级：完全面向对象操作数据库。相关mybatis属于2级。dbutils属于1级

[原生SQL查询](#sql)




## hibernate框架搭建
1.导包

![link](https://note.youdao.com/yws/api/personal/file/9BD6E894CE9E4870A639AE086C263674?method=download&shareKey=1d9d55d57645b54d7fff43090c596267)
相关数据库驱动包
mysql-connector-java-5.1.7-bin.jar

2.准备实体类model

3.书写orm元数据(对象与表的映射文件)

- 导入约束
    
    hibernate-core-5.0.7.Final.jar包org.hibenrate下有相应dtd，xsd文件复制头即可

- 实体
```
public class Customer {
	private Long cust_id;
	private String cust_name;
	private String cust_source;
	private String cust_industry;
	private String cust_level;
	private String cust_linkman;
	...
	...
}
```

- orm元数据
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
<class name="Customer" table="cust_customer">
<id name="cust_id" column="cust_id">
	<generator class="native"/>
</id>
<property name="cust_name" column="cust_name"/>
</class>
</hibernate-mapping>
```

4.书写主配置文件

hibernate-release-5.0.7.Final.zip\hibernate-release-5.0.7.Final\project\etc\hibernate.properties样例

```
<!-- 数据库的驱动 -->
			<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
			<!-- 数据库的URL -->
			<property name="hibernate.connection.url">jdbc:mysql:///tz</property>
			<!-- 数据库连接用户名 -->
			<property name="hibernate.connection.username">root</property>
			<!-- 数据库连接密码 -->
			<property name="hibernate.connection.password">liangchen</property>
			<!-- 数据库方言 -->
			<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
			<!-- 数据库方言 
				不同数据库中，sql语句略有区别，制定方言可以让hibernate框架在生成sql语句时，针对数据库的方言进行生成
				所有的sql99标准：
				DDL 定义语言  库表的增删改查
				DCL 控制语言  事务 权限
				DML 操作语言  增删改查
			-->
		<!-- 
			#hibernate.show_sql true 
			#hibernate.format_sql true 
		-->
		
			<!-- 在控制台显示打印sql语句 -->
			<property name="hibernate.show_sql">true</property>
			<!-- hiberante把生成的sql进行格式化 -->
			<property name="hibernate.format_sql">true</property>

		<!--    自动     表结构          导出
			## auto schema export 
			#hibernate.hbm2ddl.auto create  自动建表，每次框架运行都会创建新的表，以前的表会被覆盖。
			表数据丢失（不建议开发环境和测试使用）
			#hibernate.hbm2ddl.auto create-drop 自动建表，每次框架运行结束都会将所有表删除。
			(开发环境中测试使用)
			#hibernate.hbm2ddl.auto update (推荐使用)自动生成表，如果已经存在不会再生成，如果表有变动，
			自动更新(不会删除任何数据)
			#hibernate.hbm2ddl.auto validate 校验，不自动生成表，每次启动会校验数据库中表是否正确。
		 -->

		<property name="hibernate.hbm2ddl.auto">update</property>
											  
		<mapping resource="com/itheima/domain/Customer.hbm.xml"/>
		
	</session-factory>
```

5.测试
```
/*主要1、SessionFactory用于负责保存和使用所以的配置。消耗内存资源非常大 2、SessionFactory属于现场安全的对象设计。
 * 结论：保证web想中，只创建一个SessionFactory.
 */
private static SessionFactory sessionFactory;
static{
	//创建空参构造配置
	Configuration config = new Configuration().configure();
	//创建session工厂
	sessionFactory = config.buildSessionFactory();
}
public static Session opensession(){
	return sessionFactory.openSession();//获得全新session
}
public static Session getCurrentSession(){
	return sessionFactory.getCurrentSession();//获得session =>获得与现场绑定的seesion
}
```

## session以及hibernate三种状态

![hibernate三种状态](https://note.youdao.com/yws/api/personal/file/28873528199F42EC9DBC3E888C94DCA2?method=download&shareKey=1f92abaa769934a13da85e3e1377e04f)

[可参考学习hibernate三种状态和一级缓存](https://www.cnblogs.com/whgk/p/6103038.html)

```
/*
hibernate中的对状态
	对象可以分为三种状态
	1、瞬时状态
	没有id没有与session进行关联
	2、持久化状态
	有id与session有关联
	3、游离|托管状态
	有id没有与sesion关联
*/
public class Demo {
//增
//save方法：其实不能理解成保存，理解成瞬时状态转换成持久状态的方法
@Test
public void fun1(){
	Configuration cf = new Configuration().configure();
	SessionFactory sessionFactory = cf.buildSessionFactory();
	Session session = sessionFactory.openSession();
	Transaction tx = session.beginTransaction();
	Customer c = new Customer();//没有与session之间做关联，瞬时状态
	c.setCust_name("1");
	c.setCust_level("1");
	session.save(c);//持久化状态 ，有id  ，有关联
	tx.commit();
	session.close();//游离|托管状态 ，有id， 没有关联
	sessionFactory.close();
}
//删
@Test
public void fun2(){
	Session session = HibernateUtils.opensession();
	Customer customer = session.get(Customer.class, "1");//持久化状态对象
	Transaction tx = session.beginTransaction();
	session.delete(customer);
	tx.commit();
	session.close();
}
//改
//持久化状态特点：持久化状态对象的任何变化都会自动同步到数据库中。
@Test
public void fun3(){
	Session session = HibernateUtils.opensession();
	Customer customer = session.get(Customer.class, "165eb7efffbcc927b1d225d450f8cd1f");//持久化状态对象
	Transaction tx = session.beginTransaction();//控制事务
	customer.setCust_level("2");
	tx.commit();//提交事务
	session.close();//游离|托管状态 ，有id， 没有关联
}
```
## 主键生成策略(7种)

increment:查询最大值，再加1.

identity:主键自增

sequence: oracle使用

hilo:hibernate自己实现自增算法


native:根据所选数据库，三选一

uuid:随机字符串

assigned:自然主键

[摘自starskyhu](https://www.cnblogs.com/hoobey/p/5508992.html)

1、assigned
主键由外部程序负责生成，在 save() 之前必须指定一个。Hibernate不负责维护主键生成。与Hibernate和底层数据库都无关，可以跨数据库。在存储对象前，必须要使用主键的setter方法给主键赋值，至于这个值怎么生成，完全由自己决定，这种方法应该尽量避免。

<id name="id" column="id">

<generator class="assigned" />

</id>

“ud”是自定义的策略名，人为起的名字，后面均用“ud”表示。

特点：可以跨数据库，人为控制主键生成，应尽量避免。。


2、increment
由Hibernate从数据库中取出主键的最大值（每个session只取1次），以该值为基础，每次增量为1，在内存中生成主键，不依赖于底层的数据库，因此可以跨数据库。

<id name="id" column="id">

<generator class="increment" />

</id>

Hibernate调用org.hibernate.id.IncrementGenerator类里面的generate()方法，使用select max(idColumnName) from tableName语句获取主键最大值。该方法被声明成了synchronized，所以在一个独立的Java虚拟机内部是没有问题的，然而，在多个JVM同时并发访问数据库select max时就可能取出相同的值，再insert就会发生Dumplicate entry的错误。所以只能有一个Hibernate应用进程访问数据库，否则就可能产生主键冲突，所以不适合多进程并发更新数据库，适合单一进程访问数据库，不能用于群集环境。

官方文档：只有在没有其他进程往同一张表中插入数据时才能使用，在集群下不要使用。

特点：跨数据库，不适合多进程并发更新数据库，适合单一进程访问数据库，不能用于群集环境。

................


## hibernate 一级缓存和快照

> hibernate中的一级缓存是为了提高数据库操作效率，提高效率手段1:提高查询效率。提高效率手段2:减少不必要的修改语句发送

![hibernate一级缓存](https://note.youdao.com/yws/api/personal/file/D4890D25EC844A5DB67E858BAC08E1CB?method=download&shareKey=baae317900b2203c6e1afaae1f589f7a)

```
@Test
//持久化状态对象其实就是放入session缓存中的对象
public void fun4(){
	//1、获得session
	Session session = HibernateUtils.opensession();
	//2、控制事务
	Transaction tx = session.beginTransaction();
	//3、执行操作
	Customer c1 = new Customer();//瞬时状态    		         没有id，没有session关联
	c1.setCust_id("1");//托管|游离        		      	         有id，无session关联
	session.update(c1);//持久化 c1被放入session缓存当中       有id，有session关联
	
	Customer c2 = session.get(Customer.class,"1");//不会打印sql，会先从session缓存中取数据

	//4、提交事务、关闭资源
	tx.commit();// hibernate对比缓存中的对象和快照，如果有变化就会同步到数据库。 
	//以上过程中快照为空(没有从数据库取，无快照)，缓存和快照不一致,输出update
	session.close();//游离|托管状态 ，有id， 没有关联

	
}
```

## hibernate中的事务

> 事务特性a 原子性c 一致性i 隔离性d 持久性

> 事务并发问题      脏读1，不可重复读2，幻读3

> 事务的隔离级别    读未提交：123，读已提交：23，可重复读(mysql)：3，串行化：没有问题。

```
<!-- 指定Hibernate操作数据库时的隔离级别 
#hibernate.connection.isolation 1|2|4|8  0000
0001  1   读未提交
0010  2   读已提交
0100  4  可重复读
1000  8  串行化
-->
<property name="hibernate.connection.isolation">4</property>
```

- 业务开始之前打开事务，业务执行之后提交事务，执行过程中出现，回滚事务

- 在dao层操作数据库需要用到session对象，在service控制事务也是使用session对象完成，
我们要确保dao层和service层使用的是同一个session对象 

- 在Hibernate中，确保使用同一个session的问题,只需要调用get.CurrnetSession()方法即可获得与当前现场绑定的session对象,调用getCurrentSession方法必须配合主配置中的一段配置
```
//事务
Transaction tx = session.beginTransaction();
...
tx.commit();
```

```
<!-- 指定session与当前线程绑定 -->
<property name="hibernate.current_session_context_class">thread</property>
```

## HQL查询 ：Hibernate query language 
独家查询语言：面向对象的查询语言
```
	Session session = HibernateUtils.opensession();
	//setFirstResult 是起始数据，setMaxResults是查询显示的数据。
	//如果放在分页程序里边 ，
	//setFirstResult的值应该是 (当前页面-1)X每页条数，表示从第几条记录开始，
	//setMaxResults 就是每页的条数了，是查询的条数，和记录的总条数不一样，set之前的list.size()才是总条数
	List<Customer> customerList= session.createQuery("from com.it.model.Customer customer "
	+"where customer.cust_id=:cust_id")
	.setParameter("cust_id", "165eb7efffbcc927b1d225d450f8cd1f")
	.setFirstResult(0).setMaxResults(1)
	.list();
	System.out.println(customerList.get(0).toString());
```

## Criteria查询
Hibernate自创的无语面向对象查询
```
	//条件查询
	// >  gt
	// >=  ge
	//<    lt
	//<=   le
	//==   eq
	//!=   en
	//in    in
	//between and  between
	//like   like
	//not null  
	// is null     isNull
	// is not null  isNotNull
	//OR  or
	//and and


	Session session = HibernateUtils.opensession();
	Criteria criteria = session.createCriteria(Customer.class);
	//设置查询的聚合函数 =>总行数
	criteria.setProjection(Projections.rowCount());
	//添加查询的参数
	criteria.add(Restrictions.eq("cust_id", "165eb7efffbcc927b1d225d450f8cd1f"));
	//设置分页信息limit ?,?
	criteria.setFirstResult(0);
	criteria.setMaxResults(1);
	Long count =(Long)criteria.uniqueResult();
	System.out.println(count);
```

## 原生SQL查询
<span id="sql"></span>

```
Session session = HibernateUtils.opensession();
@SuppressWarnings("unchecked")
List<Customer> customerList = session.createSQLQuery("select * from cust_customer").addEntity(Customer.class).list();
System.out.println(customerList.get(0));
//addEntity只适用于存在Customer实体的，如果customer是个视图无对应实体，只是一个JavaBean,可通过一下方法
@SuppressWarnings("unchecked")
List<Customer> customerList1 =session.createSQLQuery("select cust_id cust_id,cust_name cust_name,"
		+ "cust_source cust_source,cust_industry cust_industry,cust_level cust_level,"
		+ "cust_linkman cust_linkman,cust_phone cust_phone,cust_moblie cust_moblie "
		+ "from cust_customer where cust_id=?")
		.addScalar("cust_id")
		.addScalar("cust_name")
		.addScalar("cust_source")
		.addScalar("cust_industry")
		.addScalar("cust_level")
		.addScalar("cust_linkman")
		.addScalar("cust_phone")
		.addScalar("cust_moblie")
		.setResultTransformer(Transformers.aliasToBean(Customer.class))
		.setParameter(0, "165eb7efffbcc927b1d225d450f8cd1f").list();
//Hibernate就会自动将查出来的三列内容组装到VO对象中去,addScalar方法就是将实体和结果对应的,
//可提高性能,名称都需要重新定义为小写的(需set方法属性一致),在Oracle中查出来的列都默认为大写的;
System.out.println(customerList1.get(0));
```