## 一、Spring 核心概念

- IOC：**Inverse of Control**，控制反转。**对象的创建权力由程序反转给Spring框架。
- AOP：**面向切面编程**。在不修改目标对象的源代码情况下，增强IoC容器中Bean的功能。
- DI：**依赖注入**。在Spring框架负责创建Bean对象时，动态的将依赖对象注入到Bean组件中。
- spring容器：指的就是IoC容器。

## 二、Spring IOC原理分析

### 2.1、什么是IOC容器

所谓的Ioc容器就是指的Spring中Bean工厂里面的**Map存储结构**（存储了Bean的实例）。

### 2.2、Spring框架中的工厂有哪些

1. ApplicationContext接口
   - 实现了BeanFactory接口
   - 实现ApplicationContext接口的工厂，可以获取到容器中具体的Bean对象

2. BeanFactory（是Spring框架早期的创建Bean对象的工厂接口）
   - 实现BeanFactory接口的工厂也可以获取到Bean对象

> ApplicationContext和BeanFactory的区别
>
> - 创建Bean对象的时机不同:beanFactory采取**延迟加载**，第一次getBean时才会初始化Bean;ApplicationContext是加载完applicationContext.xml时，就创建具体的Bean对象的实例。（**只对BeanDefition中描述为是单例的bean，才进行饿汉式加载**）

**BeanFactory的结构体系图**

![1566746926985](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1566746926985.png)

![1566746937211](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1566746937211.png)

### 2.3、如何创建Web环境中的Ioc容器

#### 2.3.1、ApplicationContext接口常用实现类

ClassPathXmlApplicationContext：它是从**类的根路径**下加载配置文件	

FileSystemXmlApplicationContext：它是从**磁盘路径**上加载配置文件，配置文件可以在磁盘的任意位置。

AnnotationConfigApplicationContext：当我们使用**注解配置容器对象**时，需要使用此类来创建 spring 容器，它用来读取注解。

#### 2.3.2、Java应用中创建Ioc容器

ApplicationContext context = new ClassPathXmlApplicationContext(xml路径);

#### 2.3.3、 Web应用中创建Ioc容器

**源码流程如下：**

1. web服务器（tomcat）启动会加载web.xml（启动**ContextLoaderListener**监听器）

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml7272\wps1.jpg) 

2. 创建Web环境中的Spring容器

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml7272\wps2.jpg) 

3. ContextLoader类中创建Spring容器并初始化容器中的Bean实例

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml7272\wps3.jpg) 

4. configureAndRefreshWebApplicationContext方法中调用最终初始化Bean的**refresh**方法

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml7272\wps4.jpg)

> Web三类八种监听器

流程图如下

![1566747703549](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1566747703549.png)