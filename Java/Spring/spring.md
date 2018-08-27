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
(3)   
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

