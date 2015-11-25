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

### 使用注解方式实现AOP（Aspect Orient Program） ###
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
		#		第一种，自定义
		#		第二种，内置事务增强类
		#	<aop:aspect></aop:aspect>通过指定切面类，用在自定义切面类上
		#	<aop:advise></aop:advise>通过指定增强事务代理类，用在事务上
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

	
