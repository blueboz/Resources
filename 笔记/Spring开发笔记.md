# Spring开发笔记 #
## Spring入门 ##
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
Spring的配置文件的名称可以是beans.xml，也可以是applicationContext.xml
![这里写图片描述](http://img.blog.csdn.net/20151124221828497)

### 使用注解方式实现(@Annotation) ###

	
下面三种往IOC容器中注入Bean对象的方法

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

line
	
	public void main