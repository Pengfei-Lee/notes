# MVC思想及Spring MVC

## MVC架构

参考文章：https://km.sankuai.com/page/173719871

- **Model（模型）** - 模型代表**一个存取数据的对象或 JAVA POJO**。它也可以带有逻辑，在数据变化时更新控制器。
- **View（视图）** - 视图代表模型包含的数据的可视化，**例如展示数据的前端HTML网页**。
- **Controller（控制器）** - 控制器作用于模型和视图上。它控制数据流向模型对象，并在数据变化时更新视图。它使视图与模型分离开，例如**Servlet**。

> 最简单的理解就是：模型就是一个java bean，代表数据库表中的一个记录，然后把该对象的行为放在控制器里面，而不是作为java bean的方法，而再通过视图类打印输出即可。所以控制器内部包含了一个模型对象，一个视图对象。
>
> 模型部分也可以进行逻辑处理的方法，控制器也可以实现，但控制器的主要功能应该是进行连接视图与模型。



**为什么M和V要分离？** 

> 同一个程序可以使用不同的表现形式，便于扩展。

![image-20210511120347836](https://tva1.sinaimg.cn/large/008i3skNly1gqeculxduaj30mq0uywh6.jpg)

## Spring MVC

### 1 Controller

SpringMVC有一个Servlet作为整个应用的前端控制器DispatcherServlet（org.springframework.web.servlet.DispatcherServlet），然后该控制器可以转发Web请求给次级控制器（org.springframework.web.servlet.mvc.Controller或org.springframework.web.method.HandlerMethod）

![image-20210511131706442](https://tva1.sinaimg.cn/large/008i3skNly1gqeeyxfqflj31160hemzh.jpg)

| 中文名       | 类名                                                         | 简介                                                         |
| :----------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 前端控制器   | org.springframework.web.servlet.**DispatcherServlet**        | SpringMVC的核心控制器，负责SpringMVC的流程管理。             |
| 处理器       | 处理器类型很多，最常用的是org.springframework.web.method.HandlerMethod | 最终执行单元，所有的业务逻辑与业务数据都在处理器中           |
| 处理器映射器 | org.springframework.web.servlet.HandlerMapping               | 将请求中的url映射到某个处理器的方法                          |
| 处理链       | org.springframework.web.servlet.HandlerExecutionChain        | 一个处理器+多个拦截器组成的执行编排                          |
| 处理器适配器 | org.springframework.web.servlet.HandlerAdapter               | 从请求中解析数据适配处理器并执行处理器的方法                 |
| 模型视图     | org.springframework.web.servlet.ModelAndView                 | 处理器的一种执行结果，包含一组模型和一个视图，现在已经很少使用了 |
| 视图解析器   | org.springframework.web.servlet.ViewResolver                 | 视图解析器，用于将视图名解析为视图                           |

###  2 SpringMVC中请求的生命周期

![image-20210511132251551](https://tva1.sinaimg.cn/large/008i3skNly1gqef4vl8ilj31mk0riwka.jpg)

> 以上生命周期图中DispatcherServlet总是返回视图给用户的意思现在并不准确了。目前更多的是DispatcherServlet返回数据给前端，前端渲染视图再给用户。**所以在如今的前后端分离的场景中，SpringMVC已经名存实亡，更多的是SpringMC。**

# Spring基本思想

## 1 IoC/ DI

​	IOC是一种思想，DI是一种实现。

- Spring 的 **IoC**（控制反转）思想：IoC 是指在程序开发中，实例的创建不再由调用者管理，而是由 Spring 容器创建。Spring 容器会负责控制程序之间的关系，而不是由程序代码直接控制，因此，控制权由程序代码转移到了 Spring 容器中，控制权发生了反转，这就是 Spring 的 IoC 思想。

- 依赖注入（Dependency Injection，DI）：就是IoC容器将beans.xml配置文件里面的属性值在创建实例对象时，注入给调用者。

  > 依赖注入分为两种方式。
  >
  > 1.setter注入：通过属性的setXxx方法设置属性值。
  >
  > 2.构造方法注入：通过带参构造函数初始化属性。
  >
  > 
  >
  > 其实就是将类的属性不直接指定，通过构造函数和setter来设置。

  ![image-20210506194919530](https://tva1.sinaimg.cn/large/008i3skNly1gq8y7gb0wjj31dg0dwtbf.jpg)

  > 原理：读取配置文件，获得类名之后，通过反射创建类的实例。

  ### 1.1 装配Spring Beans

  Spring中提供了三种装配方式
  
  1. 通过XML文件装配
  
     - 写一个POJO
     - 在resources里面创建beans.xml，在里面配置好对应POJO的属性值
     - 在main函数里（其它使用的任何地方），通过`new ClassPathXmlApplicationContext("beans.xml")`构造函数，读取配置文件，获得ioc 容器。
     - 后续通过获得的容器（基类是`ApplicationContext`）的`getBean`方法，获取指定name/id的类的已经注入的实例。
  
     
  
  2. 通过类和接口的注解装配
  
     - 写一个POJO, 在类名上使用`@Component`注解指定类的value（相当于xml中类的id/name），对属性使用`@Value`注解。
  
       > @Component 的衍生注解
       >
       > - dao【@Repository】
       > - Service 【@Service】
       > - controller【@Controller】
       >
       > 以上四个注解功能一样，都是代表将某个类注册到容器里，只是习惯性用在不同的业务对象上。
  
     - 写一个XXXConfig类，类中空着，仅使用`@ComponentScan`注解指定需要扫描装配的类或包即可。
  
       > 可以通过@ComponentScan中的 basePackageClasses和basePackages标签指定需要扫描装配的类。
  
     - 通过 `new AnnotationConfigApplicationContext(PersonConfig.class)`获取ioc容器。
  
     - 后续通过获得的容器（基类是`ApplicationContext`）的`getBean`方法，获取指定name/id的类的已经注入的实例。
  
     > 方式二，不能直接通过注解的@Value注入对象。
  
     
  
  3. 基于约定，通过隐式Bean的自动发现机制和自动装配原则
  
  > 优先使用方式3，若无法自动装配使用方式2，若使用第三方库时，无法在里面加注解，使用方式1。
  
  其它基本同方式二，只是在类成员变量为对象时，使用`@Autowired`注解，这样ioc容器将会自动识别这个对象所属的类，然后自动去注入这个类的对象。但正由于是自动识别类名，类名可能对应多个配置实例（多个配置文件中`类的name`），Spring IoC就不知道注入哪一个实例。所以还有`@Primary`和`@Qualifier`两个注解，来保证不会产生注入歧义。`@Primary`使这个实例优先被装配，`@Qualifier`可以指定类实例的`name`来进行装配。
  
  > 注意：自动注入不是通过类的`name`或`id`去装配，而是直接通过类名。
  
  ### 1.2 拓展的装配方式
  
  p- namespace：快捷的通关setter注入方式
  
  c-namespcae：通过构造器注入
  
  > 注意：这两种方式，不能直接使用，需要导入约束
  
  ### 1.3 自动装配
  
  - byname：通过匹配beanid与set方法后面的值一样
  
  - bytype: 通过匹配类名与set方法后面的值一样
  - 用autowired注解（是bytype找类名）
    - 如果通过xml配置，需要导入约束
    - 开启注解支持
    - 通过autowired注解注入对象，不用写setter方法。
  - 用Resource（jdk里的注解，先byname，没有对应的话，再bytype）
  
  ### 1.4 xml配置与注解的最佳实践
  
  - xml用来管理bean
  - 注解只用来做属性的设置

## 2  AOP

![image-20210508193931948](https://tva1.sinaimg.cn/large/008i3skNly1gqb95x2j0qj31ah0u01ah.jpg)

AOP 要达到的效果是，保证开发者不修改源代码的前提下，去为系统中的业务组件添加某种通用功能。

![image-20210506195421059](https://tva1.sinaimg.cn/large/008i3skNly1gq8ycoll1xj31ck0fi420.jpg)

AOP 领域中的特性术语：

- 通知（Advice）: AOP 框架中的**增强处理**。通知描述了切面何时执行以及如何执行增强处理。
- 连接点（join point）: 连接点表示应用执行过程中能够插入切面的一个点，这个点可以是方法的调用、异常的抛出。在 Spring AOP 中，连接点总是**方法的调用。**
- 切点（PointCut）: 可以插入增强处理的**连接点**。
- 切面（Aspect）: 切面是通知和切点的结合。
- 引入（Introduction）：引入允许我们向现有的类添加新的方法或者属性。
- 织入（Weaving）: 将**增强处理添加到目标对象**中，并创建一个被增强的对象，这个过程就是织入。

 Spring 中的 AOP 是通过**动态代理**实现的。

> Spring中要用接口类接受编织好的类。

使用aop需要导入的包

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.3.7</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

使用环绕时，需要显式调用切入点方法

```java
@Around("execution(* com.lipengfei.ServiceImpl.*(..))")
public void around(ProceedingJoinPoint joinPoint) throws Throwable {
  System.out.println("环绕前");
  Object proceed = joinPoint.proceed();
  System.out.println("环绕后");
}
```



具体使用参考：https://www.cnblogs.com/joy99/p/10941543.html

## 3 静态代理与动态代理

`Class`类是对所有`Class`对象（类对象）的抽象，而`Class`对象是对所有具有相同属性实例对象的抽象。我们常说的类，其实是类对象。

![image-20210508202314812](https://tva1.sinaimg.cn/large/008i3skNly1gqbafd8st6j31490u04d4.jpg)

- **静态代理**：代理类与被代理类实现同一个接口，将被代理类通过构造函数“扔”进代理类，只需要通过代理类来执行对应方法即可（代理类对应方法中，调用了传入被代理类的相应方法，并且在调用前后执行了一些操作）。

  **缺点**：针对每一个被代理类都要有相应的代理类

  ![image-20210508200215066](https://tva1.sinaimg.cn/large/008i3skNly1gqb9tiiptxj316e0kwdnb.jpg)

- **动态代理：**

  首先获得接口类的`Class`对象，然后再调用一个类（`InvocationHandler` ）对象（重写了invoke方法）的方法，间接调用被代理对象的方法和代理类增加的代码。

  具体使用方法参考：

  Java 动态代理作用是什么？ - bravo1988的回答 - 知乎 https://www.zhihu.com/question/20794107/answer/658139129

![image-20210508201543317](https://tva1.sinaimg.cn/large/008i3skNly1gqba7jl7c5j31b20ekk04.jpg)





![image-20210511112309019](https://tva1.sinaimg.cn/large/008i3skNly1gqeboe9u9rj312y0ouaj4.jpg)

> 其中目标类，是被代理的那个类，将会通过它获得它所实现的接口类，再根据这个接口实现一个新的代理类，代理类中将会调用目标类的方法。

- 根据代理Class的构造器创建对象时，需要传入InvocationHandler。**通过构造器传入一个引用，那么必然有个成员变量去接收。**代理对象的内部确实有个成员变量invocationHandler，而且代理对象的每个方法内部都会调用handler.invoke()

  ![image-20210511112645968](https://tva1.sinaimg.cn/large/008i3skNly1gqebs5culdj31bc0f4wl2.jpg)

> 其中 invoke方法需要我们自己重写，包括对目标对象（被代理对象）方法的调用

实现AOP的基本方式

```java
public class ProxyTest {
	public static void main(String[] args) throws Throwable {
		CalculatorImpl target = new CalculatorImpl();
                //传入目标对象
                //目的：1.根据它实现的接口生成代理对象 2.代理对象调用目标对象方法
		Calculator calculatorProxy = (Calculator) getProxy(target);
		calculatorProxy.add(1, 2);
		calculatorProxy.subtract(2, 1);
	}

	private static Object getProxy(final Object target) throws Exception {
		//参数1：随便找个类加载器给它， 参数2：目标对象实现的接口，让代理对象实现相同接口
		Class proxyClazz = Proxy.getProxyClass(target.getClass().getClassLoader(), target.getClass().getInterfaces());
		Constructor constructor = proxyClazz.getConstructor(InvocationHandler.class);
		Object proxy = constructor.newInstance(new InvocationHandler() {
			@Override
			public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
				System.out.println(method.getName() + "方法开始执行...");
				Object result = method.invoke(target, args);
				System.out.println(result);
				System.out.println(method.getName() + "方法执行结束...");
				return result;
			}
		});
		return proxy;
	}
}
```

更常用的简化方式

```java

public class ProxyTest {
	public static void main(String[] args) throws Throwable {
		CalculatorImpl target = new CalculatorImpl();
		Calculator calculatorProxy = (Calculator) getProxy(target);
		calculatorProxy.add(1, 2);
		calculatorProxy.subtract(2, 1);
	}

	private static Object getProxy(final Object target) throws Exception {
		Object proxy = Proxy.newProxyInstance(
				target.getClass().getClassLoader(),/*类加载器*/
				target.getClass().getInterfaces(),/*让代理对象和目标对象实现相同接口*/
				new InvocationHandler(){/*代理对象的方法最终都会被JVM导向它的invoke方法*/
					public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
						System.out.println(method.getName() + "方法开始执行...");
						Object result = method.invoke(target, args);
						System.out.println(result);
						System.out.println(method.getName() + "方法执行结束...");
						return result;
					}
				}
		);
		return proxy;
	}
}



```

## 4 bean的作用域

| Scope                                                        | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [singleton](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-singleton)（单例） | (**Default**) Scopes a single bean definition to a single object instance for each Spring IoC container. |
| [prototype](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-prototype)(原型) | Scopes a single bean definition to any number of object instances. |
| [request](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-request) | Scopes a single bean definition to the lifecycle of a single HTTP request. That is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [session](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-session) | Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [application](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-application) | Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [websocket](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#websocket-stomp-websocket-scope) | Scopes a single bean definition to the lifecycle of a `WebSocket`. Only valid in the context of a web-aware Spring `ApplicationContext`. |

> 单例和原型模式以外的在web中使用

# 学习资源

[使用SpringBoot创建initializr](https://start.spring.io/)

[spring快速入门博客](https://www.cnblogs.com/wmyskxz/p/8820371.html)

[spring官方文档](https://docs.spring.io/spring-framework/docs/current/reference/html/index.html)    