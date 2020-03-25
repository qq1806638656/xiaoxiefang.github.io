# AOP切面编程

面向切面编程指的是：程序是运行期间，动态地将某段代码插入到原来方法代码的某些位置中（前面、后面等）。这就叫面向切面编程

## 1 两种底层代理方式

### 1.1 使用jdk动态代理

优点：这种方式已经解决我们前面所有日记需要的问题。非常的灵活。而且可以方便的在后期进行维护和升级。  
缺点：当然使用jdk动态代理，需要有接口。如果没有接口。就无法使用jdk动态代理。  

### 1.2 使用cglib代理  

cglib代理可以在目标对象没有接口的情况下。实现代理。从而达到增强的效果。  
Cglib代理其实是在一个指定类的基本上，创建一个类去继承指定类的技术。从而实现增强的效果  

  
优点：在没有接口的情况下，同样可以实现代理的效果。  
缺点：同样需要自己编码实现代理全部过程。  
但是为了更好的整合Spring框架使用。所以我们需要学习一下Spring 的AOP 功能。也就是学习Spring提供的AOP功能。  
Spring AOP 底层就是使用的代理。而且不需要我们自己写。配置就可以了  

## 2 AOP编程的专业术语	
通知(Advice)  
通知就是增强的代码。比如前置增强的代码。后置增强的代码。异常增强代码。这些就叫通知  

切面(Aspect)  
切面就是包含有通知代码的类叫切面。  


横切关注点  
横切关注点，就是我们可以添加增强代码的位置。比如前置位置，后置位置，异常位置。和返回值位置。这些都叫横切关注点。

目标(Target)  
目标对象就是被关注的对象。或者被代理的对象。

代理(Proxy)  
为了拦截目标对象方法，而被创建出来的那个对象，就叫做代理对象。


连接点(Joinpoint)  
连接点指的是横切关注点和程序代码的连接，叫连接点。

切入点(pointcut)
切入点指的是用户真正处理的连接点，叫切入点。  
在Spring中切入点通过org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条件。

## 3 使用Spring实现AOP
步骤  
1.编写java类 注解@Aspect表示是切面类，注解方法@Before表示是前置方法
	


	@Component
	// @Aspect 表示我是一个切面类
	@Aspect
	public class LogUtils {
		/**
		 * @Before 表示前置通知<br/>
		 * 	value 需要写上切入点表达式
		 */
		@Before(value="execution(public int com.atguigu.pojo.Calculator.add(int, int))")
		public static void logBefore() {
			System.out.println("前置日记是：当前是【】操作，参数是：");
		}
	}

## 4 Spring的切入点表达式


切入点表达式，表示了当前通知对哪些方法生效。 一定要明白切入点的配置是在通知上面，而通知不一定只对一个方法或类起作用。通过配置表达式可能会对多个方法或类起作用

@PointCut切入点表达式语法格式是：	  

execution(访问权限 返回值类型 方法全限定名(参数类型列表))  

execution(public int com.atguigu.pojo.Calculator.add(int, int))    
参数详解  
public		————————	方法的访问权限  
int 		————————	返回值类型  
com.atguigu.pojo.Calculator.add		————————	 包名+类名+方法名  
(int, int)	————————	参数类型列表  

## 4.1限定符

### 1)匹配某全类名下，任意或多个方法。  

public int com.atguigu.pojo.Calculator.*(int, int)  
以上星的意思，是方法名任意  



### 2)在Spring中只有public权限能拦截到，访问权限可以省略（访问权限不能写*）。  

execution(public int com.atguigu.pojo.Calculator.add(int, int))  
省略之后是：  
execution(int com.atguigu.pojo.Calculator.add(int, int))  


### 3)匹配任意类型的返回值，可以使用 * 表示  

execution(public * com.atguigu.pojo.Calculator.add(int, int))  

表示任意的返回值类型都可以匹配  


### 4)匹配任意子包。
execution(public int com.atguigu.*.Calculator.add(int, int))
以上星表示
com.atguigu.任意子包（一层）


### 5)任意类型参数

execution(public int com.atguigu.pojo.Calculator.add(int, *))  
以上星表示第二个参数是任意参数 

## 5 通知

### 5.1 执行顺序
Spring通知的执行顺序是:  
正常情况：  
前置通知====>>>>后置通知=====>>>>返回值之后  
异常情况：  
前置通知====>>>>后置通知=====>>>>抛异常通知  

### 5.2 配置实列

@Before 表示前置通知  
@After 后置通知  
@AfterThrowing  异常通知
@AfterReturning是返回通知

	@Component
	// @Aspect 表示我是一个切面类
	@Aspect
	public class LogUtils {
		/**
		 * @Before 表示前置通知<br/>
		 *         value 需要写上切入点表达式
		 */
		@Before(value = "execution(public int com.atguigu.pojo.Calculator.*(int,int))")
		public static void logBefore() {
			System.out.println("前置日记是：当前是【】操作，参数是：");
		}
	
		/**
		 * @After 后置通知<br/>
		 */
		@After(value = "execution(public int com.atguigu.pojo.Calculator.*(int,int))")
		public static void logAfter() {
			System.out.println("后置日记是：当前是【】操作，参数是：");
		}
	
		/**
		 * @AfterThrowing异常通知<br/>
		 */
		@AfterThrowing(value = "execution(public int com.atguigu.pojo.Calculator.*(int,int))")
		public static void logAfterThrowing() {
			System.out.println("异常日记是：当前是【】操作，异常是：");
		}
	
		/**
		 * @AfterReturning 是返回通知
		 */
		@AfterReturning(value = "execution(public int com.atguigu.pojo.Calculator.*(int,int))")
		public static void logAfterReturning() {
			System.out.println("返回日记是：当前是【】操作，结果是：");
		}
	
	}
## 5 获取通知的一些信息

### 5.1获取连接点信息

JoinPoint 是连接点的信息。  
只需要在通知方法的参数中，加入一个JoinPoint参数。就可以获取到拦截方法的信息。  

注意：是org.aspectj.lang.JoinPoint这个类。  

	/**
	 * @Before 表示前置通知<br/>
	 *         value 需要写上切入点表达式
	 */
	@Before(value = "execution(public int com.atguigu.pojo.Calculator.*(int,int))")
	public static void logBefore(JoinPoint jp) {
		// jp.getSignature().getName() 获取方法名
		// jp.getArgs() 调用方法时的参数
		System.out.println("前置日记是：当前是【" + jp.getSignature().getName() + "】操作，参数是：" + Arrays.asList(jp.getArgs()));
	}

### 5.2 获取拦截方法的返回值和抛的异常信息

获取方法返回的值分为两个步骤：  
1、在返回值通知的方法中，追加一个参数 Object result  
2、然后在@AfterReturning注解中添加参数returning="参数名"  

	/**
	 * @AfterReturning 是返回通知
	 * 	1、在返回通知上添加一个Object result的参数<br/>
	 *  2、在返回通知注解上使用属性returning = "result"标明哪个参数接收返回值
	 */
	@AfterReturning(value = "execution(public int com.atguigu.pojo.Calculator.*(int,int))", returning = "result")
	public static void logAfterReturning(JoinPoint jp, Object result) {
		System.out.println("返回日记是：当前是【" + jp.getSignature().getName() + "】操作，结果是：" + result);
	}


获取方法抛出的异常分为两个步骤：  
1、在异常通知的方法中，追加一个参数Exception exception  
2、然后在@AfterThrowing 注解中添加参数 throwing="参数名" 

 
	/**
	 * @AfterThrowing异常通知<br/>
	 * 	1、在异常通知参数中添加一个Exception e的参数接收抛出的异常<br/>
	 *  2、在异常通知注解中使用属性throwing="参数名"
	 */
	@AfterThrowing(value = "execution(public int com.atguigu.pojo.Calculator.*(int,int))",throwing="e")
	public static void logAfterThrowing(JoinPoint jp,Exception e) {
		System.out.println("异常日记是：当前是【" + jp.getSignature().getName() + "】操作，异常是：" + e);
	}


## 6 Spring的环绕通知

ProceedingJoinPoint pjp 用于执行目标方法   

			执行目标方法
			result = pjp.proceed();
 
1、环绕通知使用@Around注解。  
2、环绕通知如果和其他通知同时执行。环绕通知会优先于其他通知之前执行。  
3、环绕通知一定要有返回值（环绕如果没有返回值。后面的其他通知就无法接收到目标方法执行的结果）。  
4、在环绕通知中。如果拦截异常。一定要往外抛。否则其他的异常通知是无法捕获到异常的。  

## 7 切入点表达式的复用

1、定义一个空的静态方法  
2、使用@Pointcut注解定义切入点表达式  
3、在需要复用切入点表达式的地方，使用方法调用代替  


	@Pointcut("execution(public int com.atguigu.pojo.Calculator.*(int,int))")
	public static void pointcut1() {}
	
	/**
	 * @Before 表示前置通知<br/>
	 *         value 需要写上切入点表达式
	 */
	@Before(value = "pointcut1()")
	public static void logBefore(JoinPoint jp) {
		// jp.getSignature().getName() 获取方法名
		// jp.getArgs() 调用方法时的参数
		System.out.println("前置日记是：当前是【" + jp.getSignature().getName() + "】操作，参数是：" + Arrays.asList(jp.getArgs()));
	}

## 8 多个通知的执行顺序 
当我们有多个切面，多个通知的时候：  
1、通知的执行顺序默认是由切面类的字母先后顺序决定。  
2、在切面类上使用@Order注解决定通知执行的顺序（值越小，越先执行）  


## 9 基于xml配置aop程序

步骤：  
1 配置目标对象  
2 配置切面类实例  
3 配置aop切面编程 <aop:config>
4-配置谁是切面类 aop:aspect
5 可以定义复用切入点表达式
6 配置通知  

	<!--1. 配置目标对象 -->
		<bean id="calculator" class="com.atguigu.pojo.Calculator"></bean>
		<!--2. 配置切面类实例 -->
		<bean id="logUtils" class="com.atguigu.pojo.utils.LogUtils"></bean>
		
		<!--3。 配置aop切面编程 -->
		<aop:config>
			<!-- 4. 配置切面类 ref="logUtils" 表示logUtils的id为切面类-->
			<aop:aspect id="aspect1" ref="logUtils">
				<!-- 5. 定义可复用的切入点表达式 -->
				<aop:pointcut expression="execution(public int com.atguigu.pojo.Calculator.*(..))" id="pointcut1"/>

				<!-- 6. 配置前置通知
						method 配置前置通知方法
						pointcut="execution(public int com.atguigu.pojo.Calculator.*(..))" 配置切入点表达式
				 -->
				<aop:before method="logBefore" 
					pointcut="execution(public int com.atguigu.pojo.Calculator.*(..))"/>
				<!-- 
					aop:after 配置后置通知
						method 配置后置通知方法
				 -->
				<aop:after method="logAfter" pointcut-ref="pointcut1"/>
				<!-- 配置异常通知 -->
				<aop:after-throwing method="logAfterThrowing" pointcut-ref="pointcut1" throwing="e"/>
				<!-- 配置返回通知 -->
				<aop:after-returning method="logAfterReturning" pointcut-ref="pointcut1" returning="result"/>
			</aop:aspect>
		</aop:config>
