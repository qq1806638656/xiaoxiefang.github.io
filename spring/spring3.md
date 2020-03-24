# Spring管理数据库连接池
## 1.0 版本

		<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
			<property name="username" value="root" />
			<property name="password" value="root" />
			<property name="driverClassName" value="com.mysql.jdbc.Driver" />
			<property name="url" value="jdbc:mysql://localhost:3306/test" />
			<property name="initialSize" value="5" />
			<property name="maxActive" value="10" />
		</bean>

手动分割

	DataSource dataSource = (DataSource) applicationContext.getBean("dataSource");
## 2.0 Spring引入单独的jdbc.properties配置文件

### 2.1 创建jdbc.properties属性配置文件

username=root
password=root
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/test
initialSize=5
maxActive=10

### 2.2配置xml

PropertyPlaceholderConfigurer类专门用来加载properties属性配置文件

location属性设置你要加载的文件路径 
classpath: 表示从类路径下搜索

	<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">

		<property name="location" value="classpath:jdbc.properties" />
	</bean>
	
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
		<property name="username" value="${username}" />
		<property name="password" value="${password}" />
		<property name="driverClassName" value="${driverClassName}" />
		<property name="url" value="${url}" />
		<property name="initialSize" value="${initialSize}" />
		<property name="maxActive" value="${maxActive}" />
	</bean>

### 3.0 引用名称空间context

改变xml配置文件
	<!-- 标签用来代替PropertyPlaceholderConfigurer加载属性配置文件 -->
	<context:property-placeholder location="classpath:jdbc.properties"/>
	
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
		<property name="username" value="${user}" />
		<property name="password" value="${password}" />
		<property name="driverClassName" value="${driverClassName}" />
		<property name="url" value="${url}" />
		<property name="initialSize" value="${initialSize}" />
		<property name="maxActive" value="${maxActive}" />
	</bean>
