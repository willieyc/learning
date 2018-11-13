##1、springboot启动源码分析

- @SpringBootConfiguration (里面使用@Configuration)
- @EnableAutoConfiguration
- @ComponentScan


#### @Configuration
Spring IoC容器的配置类，替换xml配置方式，基于JavaConfig的配置方式如下：

    @Configuration
	public class MockConfiguration{
    	//bean定义
	}

任何一个标注了@Configuration的Java类定义都是一个JavaConfig配置类

任何一个标注了@Bean的方法，其返回值将作为一个bean定义注册到Spring的IoC容器，方法名将默认成该bean定义的id

如果一个bean的定义依赖其他bean，则直接调用对应的JavaConfig类中依赖bean的创建方法，如下：

    @Configuration
	public class MockConfiguration{
 	    @Bean
 	    public MockService mockService(){
			return new MockServiceImpl(dependencyService());
		}
	
		@Bean
		public DependencyService dependencyService(){
			return new DependencyServiceImpl();
		}
	} 

####@ComponentScan
@ComponentScan这个注解在Sping中很重要，它对应xml配置中的元素，@ComponentScan的功能其实就是自动扫描并加载符合条件的组件（比如@Component和@Repository等）或者bean定义，最终将这些bean定义加载到IoC容器中。

**可以通过basePackages等属性来细粒度的定制@omponentScan自动扫描的范围，如果不指定，则默认Spring框架实现会从@ComponentScan所在类的package进行扫描。**

**注：所以SpringBoot的启动类最好是放在root package下，因为默认不指定basePackages。**

####@EnableAutoConfiguration
Spring框架提供很多以@Enable开头的Annotation定义，如@EnableScheduling，@EnableCachong，@EnableMBeanExport等，理念和方式其实一脉相承，简单概括一下就是，借助@Import的支持，收集和注册特定场景相关的bean定义。

- @EnableScheduling是通过@Import将Spring调度框架相关的bean定义都加载到IoC容器
- @EnableMBeanExport是通过@Import将JMX相关的bean定义加载到IoC容器
- 而@EnableAutoConfiguration也是借助@Import的帮助，将所有符合自动配置条件的bean定义加载到IoC容器

@EnableAutoConfiguration作为一个符合Annotation，其自身定义关键信息如下：

	@SuppressWarning("deprecation")
	@Target(ElementType.TYPE)
	@Retention(RetentionPolicy.RUNTIME)
	@Documented
	@Inherited
	@AutoConfigurationPackage
	@Import("EnableAutoConfigurationImportSelector.class")
	public @interface EnableAutoConfiguration{
		...
	}

其中，最关键的要属@Import("EnableAutoConfigurationImportSelector.class")，借助EnableAutoConfigurationImportSelector，@EnableAutoConfiguration可以帮助SpringBoot应用将所有符合条件的@Configuration配置都加载到当前SpringBoot创建并使用的IoC容器。就像一只“八爪鱼”一样。

借助于Spring框架原有的一个工具类：SpringFactoriesLoader的支持，@EnableAutoConfiguration可以智能的自动配置功效才得以大功告成！

![](https://github.com/willieyc/img_folder/blob/master/SpringBootImg/EnableAutoConfiguration.png)












