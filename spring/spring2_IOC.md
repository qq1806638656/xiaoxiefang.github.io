
# IOC详讲

IOC 全称指的是 Inverse Of Control 控制反转。  
把创建bean的权力给spring，spring加载xml配置文件以后创建bean。再java程序需要的时候给与。默认只创建一个.  
## 示例

java代码  

	ApplicationContext applicationContext=new ClassPathXmlApplicationContext("applicationContxet.xml");
	Person person = (Person) applicationContext.getBean("hello_test1");
	System.out.println(person);

xml配置文件

	<bean id="hello_test1" class="hello.bean.Person">
		<property name="name" value="阿飞"/>
		<property name="age" value="18"></property>
	</bean>
注意事项  

1.config文件夹使用source folder.  
&emsp;  folder和source folder的区别  
&emsp;  folder就是普通的文件夹，跟Windows中的文件夹没有区别  
&emsp;  source folder是一种特别的文件夹，source folder下面的.java文件都会被编译，编译后的文件（.class）会被放在我们设置的某个文件夹下面；
&emsp;  Source folder下面的非.java文件都会被copy一份（如.xml文件），放在设置的文件夹下面。  

2.获取配置文件的两种方式  
 &emsp;  FileSystemXmlApplicationContext 相对路径  
 &emsp;  ClassPathXmlApplicationContext  绝对路径  

3.创建bean的时间  
 &emsp;   再new ClassPathXmlApplicationContex的时候，并且只会创建一个

## 1.1 获取bean

### 1.1.1 根据id获取

	示例就是根据id获取

### 1.1.2 根据类型获取  

	java
	
	Person person = (Person) applicationContext.getBean(Person.class);  
	System.out.println(person);

注意事项  
&emsp; 有多个同Person.class类型实现的时候,会报错

## 1.2 通过构造方法配置bean

### 1.2.1 通过构造方法参数名注入值
constructor-arg:  
&emsp; constructor 构造方法，arg 参数  

	xml配置 java 通过getBean（id）调用
	<bean id="test4" class="study.bean.Person">
		<constructor-arg name="name" value="小法"></constructor-arg>
		<constructor-arg name="age" value="16"></constructor-arg>
	</bean>
注意事项：  
&emsp;  一般创建bean都是通过set方法创建，这种配置方法使用构造方法  
&emsp;  使用构造函数依赖注入时，Spring保证所有一个对象所有依赖的对象先实例化后，才实例化这个对象。（没有他们就没有我原则）  
&emsp;使用set方法依赖注入时，Spring首先实例化对象，然后才实例化所有依赖的对象。

### 1.2.2 index属性指定参数的位置  

	xml配置
	<bean id="test5" class="study.bean.Person">
		<constructor-arg index="0" value="猪"></constructor-arg>
		<constructor-arg index="1" value="16"></constructor-arg>
	</bean>
	
注意事项  
&emsp; index从0开始计算

### 1.2.3 根据参数类型注入

	<bean id="test6" class="study.bean.Person">
		<constructor-arg index="0" type="String" value="狗"></constructor-arg>
		<constructor-arg index="1" type="int" value="16"></constructor-arg>
	</bean>
注意事项  
&emsp; 使用类型区分重载的构造函数有一点需要特别注意  
&emsp; 如果代码中的类型是Integer , 那么type类型是 java.lang.Integer    
&emsp; 如果代码中的类型是int , 那么type类型是int  


### 1.2.4 null值的使用

	xml配置
	<bean id="test7" class="study.bean.Person">
		<property name="age"  value="14"></property>
		<property name="name">
			<null/>
		</property>
	</bean>

## 1.3 子对象的配置


### 1.3.1 子对象的赋值-ref

ref代表引用  

	xml配置
	
	<bean id="car1" class="study.bean.Car">
		<property name="carName"  value="宝马"></property>
		<property name="nuo"  value="京88888"></property>
	</bean>
	
	<bean id="test8" class="study.bean.NewPerpson">
		<property name="age"  value="14"></property>
		<property name="name"  value="开宝马的我"></property>
		
		<property name="car"  ref="car1"></property>
	
	</bean>

### 1.3.2 内部Bean的使用

再一个property中再配置一个bean  

	xml配置
	<bean id="test9" class="study.bean.NewPerpson">
		<property name="age"  value="14"></property>
		<property name="name"  value="开奔驰的我"></property>
		
		<property name="car">
			<bean id="car2" class="study.bean.Car">		
				<property name="carName"  value="奔驰"></property>
				<property name="nuo"  value="京66666"></property>
			</bean>
		</property>
	
	</bean>

### 1.3.3 级联属性赋值

级联属性的赋值一定记住要先有值，因为property是通过set方法赋值，如果一开始没配置car的值那么car就是null如果再配置car.carName就会报错  
另外ref是地址引用，如果修改了值，那么被引用的值也会被修改

	<bean id="test10" class="study.bean.NewPerpson">
		<property name="age"  value="14"></property>
		<property name="name"  value="开大众的我"></property>
		<property name="car" ref="car1"></property>
		<property name="car.carName" value="大众"></property>		
	</bean>

## 1.4 集合元素的bean配置

### 1.4.1 List属性的赋值
	xml配置
	<bean id="test11" class="study.bean.NewPerpson">
		<property name="age"  value="14"></property>
		<property name="name"  value="开大众的我"></property>
		<property name="list">
			<list>
				<value>string</value>
				<value>12</value>
				<bean id="test11_2" class="study.bean.NewPerpson">
					<property name="age"  value="14"></property>
					<property name="name"  value="开大众的我"></property>
					<property name="car" ref="car1"></property>
					<property name="car.carName" value="大众"></property>		
				</bean>
			</list>	 
		</property>
	</bean>
	
	注解
	<value>12</value>：12也是string类型
	list=[string, 12, NewPerpson [name=开大众的我, age=14, 
	car=Car [carName=大众, nuo=京88888], list=null]]]
### 1.4.2 Map属性的赋值

	xml配置
	<property name="map">
			<map>
				<!-- entry 表示map中有每一项 -->
				<entry key="aaa" value="aaaValue" />
				<entry key="bbb" value="bbbValue" />
				<entry key="ccc" value="cccValue" />
				<entry key="d" value-ref="car1">
			</map>
		</property>
		
### 1.4.3 Properties属性的赋值
	
	xml配置
		<property name="props">
			<props>
				<prop key="username">root</prop>
				<prop key="password">root</prop>
				<prop key="drivers">com.mysql.jdbc.Driver</prop>
				<prop key="url">jdbc:mysql://localhost:3306/spring</prop>
			</props>
		</property>
		
Properties（Java.util.Properties），该类主要用于读取Java的配置文件，不同的编程语言有自己所支持的配置文件，配置文件中很多变量是经常改变的，为了方便用户的配置，能让用户够脱离程序本身去修改相关的变量设置。就像在Java中，其配置文件常为.properties文件，是以键值对的形式进行参数配置的。

## 1.5 利用java代码配置bean

### 1.5.1 静态工厂方法创建Bean

简单步骤
&emsp; 	1.创建静态方法，并用set或构造方法复制  
&emsp; 	2.配置Bean，再application.xml被加载的时候bean会执行相应代码
	

	java代码
	public static Person createPerson() {
			Person person = new Person();
			person.setId(17);
			person.setName("这是静态工厂方法");
			return person;
		}
		
手动分割  

	xml配置
	<!-- 使用静态工厂方法
		class 是工厂 的全类名
		factory-method 这是工厂方法
	 -->	
	<bean id="person17" class="com.atguigu.pojo.factory.PersonFactory"
		factory-method="createPerson" />

### 1.5.2 工厂实例方法创建Bean


简单步骤
&emsp; 	1.创建静态方法，并用set或构造方法复制  
&emsp; 	2.配置Bean
&emsp;&emsp;      第一步：先使用bean标签配置一个bean对象
&emsp;&emsp;	  第二步:使用bean标签配置factory-bean和factory-method方法，factory-bean表示使用哪一个工厂对象的实现，factory-method表示调用对象的哪一个方法去获取对象实例

java代码
	public class PersonFactory {
		public Person getPerson() {
			Person person = new Person();
			person.setId(18);
			person.setName("这是工厂实例方法");
			return person;
		}
	}
手动分割  

	xml配置

	<!-- 工厂实例方法分两步：
		第一步：先使用bean标签配置一个bean对象
		第二步:使用bean标签配置factory-bean和factory-method方法
	 -->

	1
	<bean id="personFactory" class="com.atguigu.pojo.factory.PersonFactory" />


	<!-- 
		factory-bean表示使用哪一个工厂对象的实现
		factory-method表示调用对象的哪一个方法去获取对象实例
	 -->
	2
	<bean id="person18" factory-bean="personFactory" factory-method="getPerson">

### 1.5.3 FactoryBean接口方式创建对象

简单步骤
&emsp; 	1.实现FactoryBean
&emsp; 	2.配置Bean，再applicationContext.xml被加载的时候bean会执行相应代码

	public class PersonFacotryBean implements FactoryBean<Person> {
		/**
		 * 创建bean对象的方法
		 */
		@Override
		public Person getObject() throws Exception {
			return new Person(17, "这是FactoryBean接口创建的bean", null);
		}
		/**
		 * 获取bean的具体类型的方法
		 */
		@Override
		public Class<?> getObjectType() {
			return Person.class;
		}
		/**
		 * 判断是否是单例的方法
		 */
		@Override
		public boolean isSingleton() {
			return true;
		}

	}

### 1.5.4 继承Bean配置

通过继承实现bean配置信息的重用

	xml配置
	<bean id="parent" class="com.atguigu.pojo.Person">
		<property name="id" value="100" />
		<property name="name" value="我是父亲" />
		<property name="list">
			<list>
				<value>list1</value>
				<value>list2</value>
				<value>list3</value>
			</list>
		</property>
	</bean>
	<!-- parent属性设置你要继承哪个id的配置 -->
	<bean id="p18" class="com.atguigu.pojo.Person" parent="parent">
		<property name="name" value="我是子"></property>
	</bean>

### 1.5.5 abstract抽象Bean

abstract的bean配置，就是为了让其他的bean做继承使用。而不能实例化

	<!-- 
	abstract="true"表示这个bean，不能被实例化，而只能被继承使用
	 -->
	<bean id="parent" class="com.atguigu.pojo.Person" abstract="true">
		<property name="id" value="100" />
		<property name="name" value="我是父亲" />
		<property name="list">
			<list>
				<value>list1</value>
				<value>list2</value>
				<value>list3</value>
			</list>
		</property>
	</bean>
	
## 1.6名称空间

### 1.6.1 p
p名称空间，是一种通过简短属性方式调用setXxx方法赋值属性的技术

	xml配置
	<bean id="person06" class="com.atguigu.pojo.Person" p:id="6" 
	p:name="第6个人" p:age="18" p:phone="18600001111" />

### .6.2 uitl

untl配置list可以被所有的bean引用

	xml配置
	<!-- 定义一个list集合 -->
	<util:list id="list1">
		<value>string1</value>
		<value>string2</value>
		<value>string3</value>
	</util:list>
	
	<bean id="person15" class="com.atguigu.pojo.Person">
		<property name="id" value="10" />
		<property name="name" value="perosn10" />
		<property name="age" value="18" />
		<property name="phone" value="0101001" />
		<!-- list对象 ref表示引用 -->
		<property name="phones" ref="list1" />
	</bean>

## 1.7 bean的生命周期

对于单例的bean，生命周期有11个步骤： 

&emsp; 1.instantiate bean对象实例化，bean对象实例化，是在加载配置文件的时候实例的。即，我们启动spring容器的时候，加载配置文件，此时就实例化bean了。 

&emsp; 2.populate properties 封装属性 

&emsp; 3.如果Bean实现BeanNameAware， 执行 setBeanName 

&emsp; 4.如果Bean实现BeanFactoryAware 或者 ApplicationContextAware，设置工厂 setBeanFactory 或者上下文对象 setApplicationContext 

&emsp; 5.如果存在类实现 BeanPostProcessor（后处理Bean） ，执行postProcessBeforeInitialization（此点常常用来增强bean） 
&emsp; 6.如果Bean实现InitializingBean 执行 afterPropertiesSet  

&emsp; 7.调用<bean init-method="init"> 指定初始化方法 init  
	
&emsp; 8.如果存在类实现 BeanPostProcessor（后处理Bean） ，执行postProcessAfterInitialization（此点常常用来增强bean） 
&emsp; 9.执行业务处理 

&emsp; 10.如果Bean实现 DisposableBean 执行 destroy 

&emsp; 11.调用<bean destroy-method="customerDestroy"> 指定销毁方法

### 1.7.1 设置依赖
	
	先创建bc 再创建a
	<!-- 默认情况下。bean对象创建的顺序，是从上到下
			depends-on 可以设定依赖
	 -->
	<bean id="a" class="com.atguigu.pojo.A" depends-on="b,c"/>
	<bean id="b" class="com.atguigu.pojo.B" />
	<bean id="c" class="com.atguigu.pojo.C" />
### 1.7.2 自动注入

配置bean的时候，autowire用于寻找其他bean的值来自动注入

	autowire属性说明
		default 和 no 表示不自动查找并注入（你不赋值，它就null）
		byName 	是指通过属性名做为id来查找bean对象，并注入
					1、找到就注入
					2、找不到就为null
		byType  是指按属性的类型进行查找并注入
					1、找到一个就注入
					2、找到多个就报错
					3、没有找到就为null
		constructor 是指按构造器参数进行查找并注入。
					1、先按照构造器参数类型进行查找并注入
					2、如果按类型查找到多个，接着按参数名做为id继续查找并注入。
					3、按id查找不到，就不赋值。
手动分割

	<bean id="p19" class="com.atguigu.pojo.Person" autowire="constructor">
		<property name="name" value="p19" />
	</bean>


### 1.7.3 Bean的单例和多例
	<!-- 
	scope 属性设置对象的域
		singleton	表示单例（默认）
				1、Spring容器在创建的时候，就会创建Bean对象
				2、每次调用getBean都返回spring容器中的唯一一个对象

		prototype	表示多例
				1、多例在Spring容器被创建的时候，不会跟着一起被创建。
				2、每次调用getBean都会创建一个新对象返回

		request		在一次请求中，多次调用getBean方法都是返回同一个实例。
				
				底层大概的实现原理
				getBean("p20"); 

				Object bean = request.getAttribute("p20");
				if (bean == null) {
					bean = new 对象();
					request.setAttribute("p20",bean);
				}
				return bean;

		session		在一个会话中，多次调用getBean方法都是返回同一个实例。
		
				底层大概的实现原理
				getBean("p20"); 

				Object bean = session.getAttribute("p20");
				if (bean == null) {
					bean = new 对象();
					session.setAttribute("p20",bean);
				}
				return bean;
 -->
	<bean id="p20" class="com.atguigu.pojo.Person" scope="singleton">
		<property name="name" value="p20" />
	</bean>
## 1.8bean生命周期API

### 1.8.1创建带有生命周期方法的bean

	<!-- 
		init-method配置初始化方法(bean对象创建之后)
		destroy-method配置销毁方法（在spring容器关闭的时候,只对单例有效）
	 -->
	<bean id="p21" class="com.atguigu.pojo.Person" init-method="init" destroy-method="destroy" scope="singleton">
		<property name="name" value="p21"/>
	</bean>
	
### 1.8.2后置处理器BeanPostProcessor

后置处理器，可以在bean对象的初始化方法前/后，做一些工作。  
后置处理器对所有的bean都起作用

后置处理器使用步骤：
&emsp;&emsp; 1、编写一个类去实现BeanPostProcessor接口
&emsp;&emsp; 2、到Spring容器配置文件中配置

	java
	public class MyBeanPostProcessor implements BeanPostProcessor {
		/**
		 * 初始化方法之后调用
		 */
		@Override
		public Object postProcessAfterInitialization(Object bean, String id) throws BeansException {
			System.out.println("初始化方法之后。正在初始化的对象bean->" + bean + ",正在初始化对象的id值->" + id);

			if ("p21".equals(id)) {
				Person person = (Person) bean;
				person.setName("这是我给的值");
			}

			return bean;
		}

		/**
		 * 初始化方法之前调用
		 */
		@Override
		public Object postProcessBeforeInitialization(Object bean, String id) throws BeansException {
			System.out.println("初始化方法之前。正在初始化的对象bean->" + bean + ",正在初始化对象的id值->" + id);
			return bean;
		}

	}
手动分割

	<bean id="p21" class="com.atguigu.pojo.Person" init-method="init" destroy-method="destroy" scope="singleton">
		<property name="name" value="p21"/>
	</bean>
	<!-- 配置自定义的后置处理器 -->
	<bean class="com.atguigu.postprocessor.MyBeanPostProcessor" />
