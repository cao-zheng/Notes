# spring

> Spring是开源的轻量级框架，轻量级的意思就是不需要依赖额外的东西；
Spring是一站式框架，提供了3层的功能（web层、server层、dao层），每一层都提供了解决方案；
他解决的是业务逻辑层和其他各层的松耦合问题，因此它将面向接口的编程思想贯穿整个系统应用。

![spring原理图](https://note.youdao.com/yws/api/personal/file/7D8D1D75B72844259EABEC4B321132AE?method=download&shareKey=382c0389ec8739a90d344a7db74ce4f3)

```
(1)使用spring导入如上图最基础的包，如
    spring-beans-4.2.4.RELEASE.jar
    spring-context-4.2.4.RELEASE.jar
    spring-core-4.2.4.RELEASE.jar
    spring-expression-4.2.4.RELEASE.jar
(2)日志包
    com.springsource.org.apache.log4j-1.2.15.jar
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

> 每次容器启动时会创建容器中配置的所有对象，并提供更多功能。

> 从类路径下加载配置文件：ApplicationContext context = new  ClassPathXmlApplicationContext("");

> 从硬盘绝对路径下加载配置文件：
ApplicationContext context = new FileSystemXmlApplicationContext("d:/XXX/YYY/XXX");

```
结论：在web开发中，使用applicationContext，
在资源匮乏的环境中使用BeanFactory
```
ApplicationContext.xml中代码片段如下
```
<!-- 将User对象交给spring容器管理 -->
<!-- Bean元素:使用该元素描述需要Spring容器管理的对象
	name属性:给被管理的对象取名字，获取对象时根据该名字获取对象.可以重复，可以使用特殊字符.
	class属性:被管理对象的完整类名.
	id属性:与name属性一模一样.名称不可重复，不能使用特殊字符.
	结论：尽量使用name属性
	
 -->
	<bean name ="user" class="com.itcast.bean.User"></bean>
```
class类中代码片段如下
```
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
		User u = (User)context.getBean("user");
```