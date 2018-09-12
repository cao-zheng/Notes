# spring

> Spring是开源的轻量级框架，不需要依赖额外的东西；
Spring是一站式框架，提供了3层的功能（web层、server层、dao层），每一层都提供了解决方案；
他解决的是业务逻辑层和其他各层的松耦合问题，因此它将面向接口的编程思想贯穿整个系统应用。

> ioc:控制反转。创建对象的方式反转了，从我们创建对象，反转给spring(程序)来创建。

> di:依赖注入。将必须的属性注入到对象当中。是为了实现ioc思想的必须条件。

![spring原理图](https://note.youdao.com/yws/api/personal/file/7D8D1D75B72844259EABEC4B321132AE?method=download&shareKey=382c0389ec8739a90d344a7db74ce4f3)

```
(1)使用spring导入如上图最基础的包，如
    spring-beans-4.2.4.RELEASE.jar
    spring-context-4.2.4.RELEASE.jar
    spring-core-4.2.4.RELEASE.jar
    spring-expression-4.2.4.RELEASE.jar
(2)日志包
    com.springsource.org.apache.log4j-1.2.15.jar
    com.springsource.org.apache.commons.logging-1.1.1.jar
(3)引用xsd约束文件
可参考https://www.ibm.com/developerworks/cn/xml/x-1008dubb/
打开ecilpse-->window-->Preferences-->XML-->XML Catalog-->Add
-->workspace或file system在xml中design设计模式中，对需要约束的标签如beans 
右键Edit namespace -->Select From Registered Namespaces选择最基础的xsi-->
Specity New Namespace-->Location Hint 中的Browse-->选中刚新建的约束文件
select Xml Catalog entry -->Namespace Name可复制Location Hint 中除开本xsd文件前面内容
```

### applicationContext&BeanFactory
1.BeanFactory

> spring的原始接口，针对原始接口的实现类功能较为单一。

> BeanFactory接口实现类的容器，特点每次在获得对象的时候才会创建对象

2.ApplicationContext

> ==每次容器启动时会创建容器中配置的所有对象==，并提供更多功能。

> 从类路径下加载配置文件：ApplicationContext context = new  ClassPathXmlApplicationContext("");

> 从硬盘绝对路径下加载配置文件：
ApplicationContext context = new FileSystemXmlApplicationContext("d:/XXX/YYY/XXX");

```
结论：在web开发中，使用applicationContext，
在资源匮乏的环境中使用BeanFactory
```
### spring配置


Bean元素:使用该元素描述需要Spring容器管理的对象

name属性:给被管理的对象取名字，获取对象时根据该名字获取对象.可以重复，可以使用特殊字符,后来的属性
	
class属性:被管理对象的完整类名.
	
id属性:与name属性一模一样.不可重复，不能使用特殊字符,早期的属性.	结论：尽量使用name属性

生命周期属性

init-method属性:指出初始化的方法

destory-method属性:指出销毁的方法

作用范围

scope属性:singleton(默认)创建容器时，立即创建单例对象           prototype每次获得对象时，才会创建对象并且每次都会创建新对象

分模块开发
 
`<import resource="com/it/model/ApplicationContext.xml"/>`

1.空参构造创建

```
	<bean name ="user" class="com.itcast.bean.User"></bean>
	
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
		User u = (User)context.getBean("user");
```
2.静态工厂
```
public class UserFactory {
//静态工厂
	public static User createUser(){
		User user = new User();
		user.setAge(2);
		user.setUsername("静态工厂");
		return user;
	}
}

<!-- 静态工厂 -->
<bean name="userstatic" class="com.it.factory.UserFactory" factory-method="createUser"/>

User user = (User)spring.getBean("userstatic");
```
3.实例工厂
```
public class UserFactory {
//静态工厂
	public User createUser(){
		User user = new User();
		user.setAge(2);
		user.setUsername("静态工厂");
		return user;
	}
}

<!-- 实例工厂 -->
<bean name="userFactory" class="com.it.factory.UserFactory"/>
<bean name="usernew" factory-bean="userFactory" factory-method="createUser2"></bean>

User user = (User)spring.getBean("usernew");
```
4.单例对象模式（spring容器默认）

singleton:单例对象.被标识为单例的对象在spring容器中只会存在一个实例
```
<bean name="user" class="com.it.model.User" scope="singleton"/>
```
5.多例模式

prototype:多例原型.被标识为多例的对象,每次再获得才会创建.每次创建都是新的对象.整合struts2时,ActionBean必须配置为多例的.
```
<bean name="user" class="com.it.model.User" scope="prototype"/>
```
6.生命周期属性

 init-method: 配置一个方法作为生命周期初始化方法.spring会在对象创建之后立即调用.
 
 destory-method
 配置一个方法作为生命周期的销毁方法.spring容器在关闭并销毁所有容器中的对象之前调用..
 ```
 public class User {
 	public void init(){
		System.out.println("初始化加载。。");
	}
	public void destroy(){
		System.out.println("销毁加载。。。");
	}
}

 <bean name="user" class="com.it.model.User" init-method="init" destroy-method="destroy"/>
 ```
 
 ### spring注入
 
 1.set方法注入
 ```
 <bean name="user" class="com.it.model.User" scope="singleton" init-method="init" destroy-method="destroy">
		<property name="username" value="spring注入"/>
		<property name="age" value="4"/>
		<property name="car" ref="car"/>
	</bean>
	<bean name="car" class="com.it.model.Car">
		<property name="name" value="奥迪"/>
		<property name="type" value="A8"/>
	</bean>
	
public class User {
	 private String username;
	 private Integer age; 
	 private Car car;
	 ...
}
public class Car {
	 private String name;
	 private String type;
	 ...
}
```
2.构造函数注入
```
<bean name="user" class="com.it.model.User" scope="singleton" init-method="init" destroy-method="destroy">
		<constructor-arg name="car" ref="car" index="0"/>
		<constructor-arg name="age" value="18" type="java.lang.Integer" index="1"/>
</bean>
<bean name="car" class="com.it.model.Car">
		<property name="name" value="奥迪"/>
		<property name="type" value="A8"/>
</bean>

...
...
public class User {
...
public User(Car car,Integer age){
		super();
		System.out.println("3有参构造："+car+"----"+age);
		this.age = age;
		this.car =car;
}
...
}

```
3.p名称空间注入
```
<bean name="pUser" class="com.it.model.User" p:username="p注入" p:age="4" p:car-ref="car"/>
```
4. spel注入
```
<bean name="spelUser" class="com.it.model.User">
	<property name="username" value="#{user.username}"/>
	<property name="age" value="#{pUser.age}"/>
	<property name="car" ref="car"/>
</bean>
```
5.复杂类型注入
```
public class Collection {
	private Object[] array;
	private List list;
	private Map map;
	private Properties properties;
	...
	...
}


<bean name="complexCollection" class="com.it.model.Collection">
	<!-- 数组array注入 -->
		<property name="array">
			<array>
				<value>数组1</value>
				<value>数组2</value>
				<ref bean="spelUser"/>
			</array>
		</property>
	<!-- list注入 -->
		<property name="list">
			<list>
				<value>list集合1</value>
				<value>list集合2</value>
				<ref bean="pUser"/>
			</list>
		</property>
	<!-- map注入 -->
		<property name="map">
			<map>
				<entry key="1" value="key1"/>
				<entry key="2" value="key2"/>
				<entry key="3" value="key3"/>
			</map>
		</property>
	<!-- properties注入 -->
		<property name="properties">
			<props>
				<prop key="drivClass">com.jdbc.mysql.Driver</prop>
				<prop key="userName">root</prop>
				<prop key="password">123456</prop>
			</props>
		</property>
	</bean>
```
6.Spring注解
```
<!-- 使用注解可以代替配置文件 指定扫描com.it.model下面所有类中的注解-->
<context:component-scan base-package="com.it.model"/>
	
	
@Component("annotation")//三个注解没有任何区别，Component为最初注解无任何层区别，Service为Service层注解，
			  Controller为web层注解，Repository为model层
@Service("annotation")//service层
@Controller("annotation")//action控制层
@Repository("annotation")//model层
@Scope(scopeName="singleton")//读取属性  singleton，prototype,request,session,global session作用域
public class Annotation {
    @Value("name注解注入")//不推荐使用，通过反射的field赋值，破坏了封装性
	private String name;
	private Integer age;
	@Autowired//自动装配,对象一多，比如car1,car2,car3 类型一致将无法选择具体注入哪一个对象。
	@Qualifier("car2")//使用@Qualifier注解告诉spring容器自动装配对应名称对象
	@Resource(name="car3")//手动注入，指定注入对应名称的对象 推荐使用(指名道姓)
	private Car car;
	...
	...
	@Value("1")//通过set方法赋值，推荐使用
	public void setAge(Integer age) {
		this.age = age;
	}
	@PostConstruct//在对象创建后调用
	public void init(){
		System.out.println("初始化方法annotation");
	}
	@PreDestroy//在销毁之前调用
	public void destroy(){
		System.out.println("销毁方法annotation");
	}
	
}



@Repository("car")
public class Car {
	private String name;
	private String type;
	...
	...
}
```
