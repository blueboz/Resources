#### c# Spring开发笔记 #
## Spring入门 ##

### Spring与web结合方法 ###
#### 首先导入Spring所需的核心依赖jar包 ####
 
![这里写图片描述](http://img.blog.csdn.net/20151124220601830)

其中Spring-Core中有如下jar包。是Spring的核心依赖

	commons-logging-1.1.1.jar
	spring-beans-3.2.5.RELEASE.jar
	spring-context-3.2.5.RELEASE.jar
	spring-core-3.2.5.RELEASE.jar
	spring-expression-3.2.5.RELEASE.jar
Spring-Aop用于事务的切入点等事务需求

Spring-Orm是用于Hibernate与Spring结合的如HibernatenateTemplate，OpenSessionInViewFilter等。

Spring-Tx是事务操作。

Spring-Web是用于与Struts框架结合。

#### 创建配置文件 ####
Spring的配置文件的名称可以是beans.xml，也可以是applicationContext.xml,在这里我选择beans.xml
![这里写图片描述](http://img.blog.csdn.net/20151124221828497)

下面这是必要引入的DTD约束

	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:context="http://www.springframework.org/schema/context"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xmlns:tx="http://www.springframework.org/schema/tx"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://www.springframework.org/schema/beans 
		    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
		    http://www.springframework.org/schema/context 
		    http://www.springframework.org/schema/context/spring-context-3.0.xsd
		    http://www.springframework.org/schema/tx 
		    http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
		    http://www.springframework.org/schema/aop 
		    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">
有了beans.xml后，我们便可以在里面配置各种bean
#### 在web.xml中配置 ####
	<!-- web容器启动的时候，自动加载spring容器（监听器） -->
	<context-param>
		<param-name>
			contextConfigLocation
		</param-name>

		<param-value>
			classpath:beans.xml
		</param-value>
	</context-param>
	
	<!--监听器-->
	<listener>
		<listener-class>
			org.springframework.web.context.ContextLoaderListener
		</listener-class>
	</listener>

这里一定要配置。对于web程序启动时，自动执行该监听器，创建IOC容器，并通过DI注入对象。
从而监听访问的页面。对页面要求创建对象提供支持 

当然	测试时候如果用实现ApplicationContext接口的类读取ClassPathXmlApplicationContext读取的话。是不走监听器的。                    


## AOP编程 ##
描述切面的常用术语 有通知(advice),切点（pointcut）。

遗憾的是，大多数描述AOP功能的术语并不直观

**通知(Advice)**  
&emsp;&emsp;在AOP术语中，切面的工作被叫为通知。  
&emsp;&emsp;通知定义了切面是什么时候以及何时使用。除了描述切面要完成的工作。通知还解决了何时执行这个问题。它因该应用在某个方法执行之前，之后。还是抛出异常的时候。  
&emsp;&emsp;Spring切面可以应用5种类型的通知。

* Before			在方法被调用之前调用
* After				在方法完成之后调用通知
* After-returning	在方法成功返回之后
* After-throwing		在方法抛出异常之后调用
* Around			环绕通知

**连接点（Joinpoint）**  
&emsp;&emsp;连接点是应用执行过程中能够插入切面的一个点。这个点可以是调用方法时、抛出异常时，甚至修改一个字段时候。切面代码可以利用这些点切入到应用的正常执行过程中。并添加新的行为。

**切点（Pointcut）**  
&emsp;&emsp;切点有助于缩小切面所通知连接点的范围。如果通知定义了切面‘什么’和‘何时’，那么切点就定义了‘何处’。切点会匹配通知所要植入的一个或者多个连接点。我们通常使用明确的类和方法名来指定这些切点，或利用正则表达式定义匹配的类和方法名称模式来指定这些切点。

**切面（Aspect）**  
&emsp;&emsp;切面是通知和切点的结合。通知和切点共同定义了关于切面的全部内容。它是什么，何处，何时完成其功能。

**引入（Introduction）**  
引入允许我们向现有的类添加方法和属性。

**织入（Waving）**  
织入是将切面应用到目标对象来创建新的代理对象的过程。切面在指定的连接点被植入到目标对象中。在目标对象的盛明周期里有多个点可以进行织入。

* 编译期
* 类加载器
* 运行时期	Spring AOP使用这种方式。

&emsp;&emsp;至此，我们知道了连接点是程序执行过程中能够应用通知的所有点。  
&emsp;&emsp;切点定义了通知被应用的所在位置（在那些连接点）。  
&emsp;&emsp;其中关键的概念是切点定义了那些连接点会得到通知。

&emsp;&emsp;Spring只支持方法连接点，因为其是基于动态代理的。这个与其他一些AOP框架是不同的，例如AspectJ和Jboss，除了方法切点，还提供了字段和构造器的切点。我们也无法在Bean创建时候通知应用。

**Spring借助AspectJ的切点表达式语言来定义Spring切面**
&emsp;&emsp;在Spring中尝试使用AspectJ其他指示器时，只有execution指示器是唯一的执行匹配，而其它的指示器都是限制匹配的。

**编写切点**
![这里写图片描述](http://img.blog.csdn.net/20151126111536739)

我们匹配Instrument的play()方法。方法表达式以*号开头。标识我们不关心方法返回值的类型。

    execution(* com.springinaction.springido1.Instrument.play(..))
假设我们需要配置切点仅匹配com.springinaction.springido1包。在此场景下，可以使用within()指示器来匹配。  

**使用within()指示器限制切点范围*
  
	within(com.springinaction.springido1)
**使用bean()指示器**

	bean(eddie)	限定了Bean的ID伟eddid
### 在Xml中声明切面 ###	

&emsp;&emsp;关于Spring AOP配置元素，第一个需要注意的事项是大多数的AOP配置元素必须在<aop:config>元素的山下问内使用。这条规则有好几种例外场景，但是把Bean声明为一个切面时，我们总是从<aop:config>元素开始配置的。  
&emsp;&emsp;在<aop:config>元素里面，我们声明了一个或者多个通知器、切面或者切点。在<aop:aspect>元素声明了一个简单的切面。ref元素引用了一个POJO Bean，改Bean实现了切面的功能。  
&emsp;&emsp;所有的通知元素中，pointcut属性定义了通知所应用的切点。pointcut属性的值是使用AspectJ切点表达式语法所定义的切点。  
&emsp;&emsp;你也许注意到所有通知的pointcut属性的值都是一样的，这是因为所有的通知都是应用到相同的切点上面。这似乎违反了DRY原则。为了避免重复定义切点，可以使用<aop:pointcut>元素定义一个命名的切点。  
**关于环绕通知的优势**  
&emsp;&esmp;假设我们需要记录目标方法的执行时间，这时候，需要前置通知和后置通知。在前置通知中记录一个开始时间，在后置通知中记录一个结束时间，但是这样的话，我们必须在一个成员变量中保存开始时间。因为切面类是单例的，如果像这样保存状态，会引发线程安全问题。  
&emsp;&emsp;这一点环绕通知相比前面的更加有优势。 
&emsp;&emsp;声明环绕通知，<aop:around>  
**如何给通知传递参数**  
	
	<aop:config>
		<aop:aspect ref="magician">
			<aop:pointcut id="thinking" expression="execution(* com.springinaction.springidol.Thinker.thinkOfSomething(String)) and args(thoughts)"/>
			<aop:before pointcut-ref="thinking" method="interceptThougths" arg-names="thoughts"/>
		</aop:aspect>
	</aop:config>

<aop:before>的arg-names属性。切点标识了Thinker的thinkOfSomething()方法，指定了String参数，然后再args参数中标识了将thoughts作为参数。
		
### 通过切面引入新功能 ###
&emsp;&esmp;一些编程语言，可以不用直接修改对象或类的定义，就能够为类或者对象增加新的方法。但是Java并不能。但是我们可以利用Spring的ＡＯＰ实现。
　　
我们要给Ａ类引入Contestant接口
	
	package com.springinaction.springidol;

	public interface Contestant{
		void receiveAward();
	}

假设我们可以访问A接口的所有实现，并能偶修改它来实现Contestant。但是从设计的角度，这个并不严谨。而且我们也无法修改所有A类的实现，尤其是在使用第三方实现的时候，可能不会有他们的源代码。  
值得庆幸的是 ，借助AOP，我们可以非侵入式的改变现有的实现。为了搞定它，我们使用<aop:declare-parent>标签

	<aop:aspect>
		<aop:declare-parents types-matching="cn.bluboz.A+" implement-interface="com.springinaction.springidol.Contestant" default-impl="com.springinaction.springidol.ContestantImpl"/>
	</aop:aspect>

<aop:declare-parents>声明了此切面所通知的Bean在它的对象层次结构中拥有新的父类型。具体到本例子中。类型匹配到A接口的那些Bean会实现Contestant接口。有implement-interface指定。最后Contestant的接口的实现是由default-impl指定的。但是也可以使用delegate-ref来指定。如下
	<!--要求Spring容器中有一个ID为这个的Bean-->
	<bean id="contestantImpl" class="com.springinaction.springidol.ContestantImpl">
	</bean>

	<aop:aspect>
		<aop:declare-parents types-matching="cn.bluboz.A+" implement-interface="com.springinaction.springidol.Contestant" default-impl="contestantImpl"/>
	</aop:aspect>

### 注解切面 ###
&emsp:&emsp;使用注解来创建切面是AspectJ
5所引入的关键特性。通过少量注解把任意类转变为切面。这种特性通常称为@AspectJ。通过AspectJ，我们可以在不需要任何额外的类或者Bean声明就能级那个它转化为一个切面。

不过，即使你通过注解把一个类声明为注解类的时候，最后还有一件事要做，就是让Spring将你的类作为一个切面。我们需要在Spring上下文声明一个自动代理Bean，该Bean知道如何把@Aspect注解所标注的Bean转化为代理通知。为此，Spring自带了AnnotationAwareAspectJAutoProxyCreator的自动创建代理创建类。我们可以在Spring上下文中把AnnotationAwareAspectJAutoProxyCreator注册为一个Bean。注入。但是我们需要敲一大堆的文字。为此Spring为了简化我们的操作，提供了一个自定义的配置元素，
<aop:aspectj-autoproxy/>  

同样值得一提的是，<aop:aspect>元素和@AspectJ注解都是把一个POJO转化为一个切面的有效方式。但是<aop:aspect>有一个优势是不需要实现切面功能的源码，通过@AspectJ我们必须标注类和方法，它需要有源码。而<aop:aspect>可以引用任意一个Bean  
**传递参数给所标注的通知**
	
	//声明参数化的切点  
	@Poingcut("execution(* com.blueboz.test(String))&&args(params)")
	
	@Before("methodname(params)")
	
标注引入

	<aop:declare-parents>的注解是@DeclareParents	
	其使用方式与标签没有什么区别。
	@DeclareParents(
		value="com.springinactin.springidol.A+"
		defaultImpl="GracesContestant.class
	)	


1.开启注解扫描,注解扫描功能是一个共有的功能，所以存在于context命名空间里面
    
	<context:component-scan base-package="cn.itcast.e_aop_anno">
    </context:component-scan>

2.设置aop注解方式 JDK代理还是Cglib


	<aop:aspectj-autoproxy proxy-target-class="false">
	</aop:aspectj-autoproxy>
	使用jdk动态代理织入增强

	<aop:aspectj-autoproxy proxy-target-class="true">
	</aop:aspectj-autoproxy>
	使用CGLib动态代理技术织入增强。

**如果目标类没有声明接口,则spring将自动使用CGLib动态代理。优先JDK代理**
3.使用注解

	@Aspect							指定一个类为切面类		
	@Pointcut("execution(* cn.itcast.e_aop_anno.*.*(..))")  指定切入点表达式
	
	@Before("pointCut_()")				前置通知: 目标方法之前执行
	@After("pointCut_()")					后置通知：目标方法之后执行（始终执行）
	@AfterReturning("pointCut_()")		    返回后通知： 执行方法结束前执行(异常不执行)
	@AfterThrowing("pointCut_()")			异常通知:  出现异常时候执行
	@Around("pointCut_()")				环绕通知： 环绕目标方法执行
4.切面类

	@Component	//将App类的实例对象加入IOC容器
	@Aspect  	//指定当前类为切面类
	public class Aop {

		// 指定切入点表达式： 拦截哪些方法； 即为哪些类生成代理对象
		@Pointcut("execution(* cn.itcast.e_aop_anno.*.*(..))")
		public void pointCut_(){
		}
		
		// 前置通知 : 在执行目标方法之前执行
		@Before("pointCut_()")
		public void begin(){
			System.out.println("开始事务/异常");
		}
		
		// 后置/最终通知：在执行目标方法之后执行  【无论是否出现异常最终都会执行】
		@After("pointCut_()")
		public void after(){
			System.out.println("提交事务/关闭");
		}
		
		// 返回后通知： 在调用目标方法结束后执行 【出现异常不执行】
		@AfterReturning("pointCut_()")
		public void afterReturning() {
			System.out.println("afterReturning()");
		}
		
		// 异常通知： 当目标方法执行异常时候执行此关注点代码
		@AfterThrowing("pointCut_()")
		public void afterThrowing(){
			System.out.println("afterThrowing()");
		}
		
		// 环绕通知：环绕目标方式执行
		@Around("pointCut_()")
		public void around(ProceedingJoinPoint pjp) throws Throwable{
			System.out.println("环绕前....");
			pjp.proceed();  // 执行目标方法
			System.out.println("环绕后....");
		}
	
	}

### XML实现AOP编程 ###

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xmlns:p="http://www.springframework.org/schema/p"
	    xmlns:context="http://www.springframework.org/schema/context"
	    xmlns:aop="http://www.springframework.org/schema/aop"
	    xsi:schemaLocation="
	        http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/context
	        http://www.springframework.org/schema/context/spring-context.xsd
	        http://www.springframework.org/schema/aop
	        http://www.springframework.org/schema/aop/spring-aop.xsd">
		
		<!-- dao 实例 -->
		<bean id="userDao" class="cn.itcast.f_aop_xml.UserDao"></bean>
		<bean id="orderDao" class="cn.itcast.f_aop_xml.OrderDao"></bean>
		
		<!-- 切面类 -->
		<bean id="aop" class="cn.itcast.f_aop_xml.Aop"></bean>
		<!— 
		####################################################
	    #<aop:config>
		#	一共有2种方式指定切面类
		#		第一种，自定义切面类
		#		第二种，内置事务增强类
		#	<aop:aspect></aop:aspect>通过指定切面类，用在自定义切面类上
		#	<aop:advise></aop:advise>通过指定增强事务代理类，用在事务上，通知
		#</aop:config>
		####################################################
		-->
首先介绍的是自定义切面类

		<!-- Aop配置 -->
		<aop:config>

			<!-- 定义一个切入点表达式： 拦截哪些方法 -->
			<aop:pointcut expression="execution(* cn.itcast.f_aop_xml.*.*(..))" id="pt"/>

			<!-- 切面 -->
			<aop:aspect ref="aop">
				<!-- 环绕通知 -->
				<aop:around method="around" pointcut-ref="pt"/>
				<!-- 前置通知： 在目标方法调用前执行 -->
				<aop:before method="begin" pointcut-ref="pt"/>
				<!-- 后置通知： -->
				<aop:after method="after" pointcut-ref="pt"/>
				<!-- 返回后通知 -->
				<aop:after-returning method="afterReturning" pointcut-ref="pt"/>
				<!-- 异常通知 -->
				<aop:after-throwing method="afterThrowing" pointcut-ref="pt"/>
			</aop:aspect>

		</aop:config>
	</beans>  
其次是
	
	//配置通知
	<tx:advice id="txAdvice" transaction-manager="txManager">
		<tx:attributes>
			<tx:method name="save*" read-only="false"/>
			<tx:method name="update*" read-only="false"/>
			<tx:method name="delete*" read-only="false"/>
			<tx:method name="*" read-only="true"/>
		</tx:attributes>
	</tx:advice> 
	
	<aop:config>
		<aop:pointcut expression="* cn.blueboz.elec.*.*(..)" id="pt"/>
		<aop:advisor advice-ref="txAdivce"/>
	</aop:config>


### IOC容器中注入Action Service Dao（使用注解方式） ###
下面三种往IOC容器中注入Bean对象的方法
	//所有都可以用下面这个往IOC注入	
	@Component

	//在Action中的配置
	@Controller("elecMenuAction")
	@Scope(value="prototype")
	
	//上面相当于在applicationContext.xml文件中配置如下	
	<bean id="elecMenuAction"
		  class="cn.blueboz.elec.web.action.ElecMenuAction" 
		  scope="prototype">
	</bean>
**在上面有一点要记住，scope="prototype"**　

	//在Service中的配置
	@Service(IElecTextService.SERVICE_NAME)
	@Transactional(readOnly=true)
　

	//在Dao持久层的配置
	@Repository(IElecTextDao.SERVICE_NAME
　

	//从IOC容器中根据name查找对应名称的bean实例对象
	@Resource(name=IElecTextDao.SERVICE_NAME)

	
