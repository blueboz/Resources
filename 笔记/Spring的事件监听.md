# Spring 中的事件监听的实现

----------
这里我们不讨论事件监听的机制的原理，我们只讨论如何在项目中实现时间监听。
Spring的事件监听是基于观察者模式。设计开发中。如下类与接口是我们必须要使用的。

- **ApplicationContextAware**

    /**
     *实际开发中Action实现ApplicationContextAware就可以
     */
    public interface ApplicationContextAware extends Aware {
    	void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
    
    }


- **ApplicationContext**
- **ApplicationEvent**
- **ApplicationListener**
