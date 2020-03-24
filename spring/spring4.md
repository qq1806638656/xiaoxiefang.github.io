# 注解功能
## 1.Spring配置bean的常用注解  

@Controller————配置控制器    
@Service————service业务层组件  
@Repository————持久层dao组件  
@Component————除了以上三种都可以用component  
@Scope————配置作用域（单例，多例）  


## 2.使用注解@Autowired自动装配  
@Autowired 注解 会自动的根据标注的对象类型在Spring容器中查找相对应的类。如果找到，就自动装配。  
使用@Autowired注解，不需要get/set方法  

### 2.1装配示例

	@Service
	public class BookService {
		/**
		 * @Autowired 实现自动注入<br/>
		 * 	1、先按类型查找并注入<br/>
		 */
		@Autowired
		private BookDao bookDao;
	
		@Override
		public String toString() {
			return "BookService [bookDao=" + bookDao + "]";
		}
	
	}
### 2.2 装配规则

	 * @Autowired 实现自动注入  
	 * 	1、先按类型查找并注入  
	 *  2、如果找到多个，就接着按属性名做为id继续查找并注入  

### 2.3 @Qualifier装配指定id的bean对象

根据成员变量名作为id还是找不到bean，可以使用@Qualifier注解明确指定目标bean的id  

	 * @Autowired 实现自动注入
	 * 	1、先按类型查找并注入
	 *  2、如果找到多个，就接着按属性名做为id继续查找并注入
	 *  3、如果找到多个。但是属性名做为id找不到，可以使用@Qualifier("bookDao")注解指定id查找并注入

手动分割

	@Autowired
	@Qualifier("bookDaoExt")
	private BookDao bookDao1;

### 2.4 @Autowired注解的required属性指定某个属性允许不被设置


	 * @Autowired 实现自动注入
	 * 	1、先按类型查找并注入
	 *  2、如果找到多个，就接着按属性名做为id继续查找并注入
	 *  3、如果找到多个。但是属性名做为id找不到，可以使用@Qualifier("bookDao")注解指定id查找并注入
	 *  4、可以通过修改@Autowired(required=false)允许字段值为null
	
手动分割

	@Autowired(required=false)
	@Qualifier("bookDaoExt1")
	private BookDao bookDao1;

### 2.5 @Autowired和@Qualifier在方法上的使用。
在方法的形参位置使用@Qualifier注解

	 * @Autowired 实现自动注入
	 * 		1、先按类型查找参数并调用方法传递
	 * 		2、如果找到多个，就接着按参数名做为id继续查找并注入
	 *		3、如果找到多个。但是参数名做为id找不到，可以使用@Qualifier("bookDao")注解指定id查找并调用
	 *     4、可以通过修改@Autowired(required=false)允许不调用此方法也不报错
手动分割

	@Autowired(required=false)
	public void setBookDao(@Qualifier("bookDaoExt1")BookDao abc) {
		System.out.println("BookDao进来啦 --->>> " + abc);
	}
