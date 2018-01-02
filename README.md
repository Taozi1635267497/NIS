## NIS
## 房产信息管理系统
- 项目基于Maven 多模块和Dubbo分布式搭建的Spring MVC的项目
- 主要分层用到Spring、Spring mvc处理业务逻辑
- 持久层使用hibernate对于数据的持久化
- Redis实现对于数据的缓存
***
## 环境要求
- Linux 环境下配置好Zookeeper和Dubbo也可Window下配置 本次测试基于CentOS 7.4版本（主要用于搭建服务器端）
- Eclipse 4.5
- JDK：版本1.6或更高版本 
- Maven：版本3.2或更高
- Tomcat：版本7或更高
- Mysql：5.7或更高
- Redis：3.X或者更高
- Dubbo  2.5.8或者更高
- Zookeeper 3.4.11或者更高
- Mysql 5.7
# Dubbo 配置
***
##  理解Dubbo的基本  官网:http://dubbo.io 
> Dubbo  是一个高性能，基于Java的RPC框架，由阿里巴巴开源。和许多RPC系统一样，Dubbo基于定义一个服务的思想，指定可以通过参数和返回类型远程调用的方法。在服务器端，服务器实现这个接口并运行一个dubbo服务器来处理客户端调用。在客户端，客户端有一个存根，提供与服务器相同的方法。

![](http://dubbo.io/images//dubbo-architecture.png)


## 数据库

##  项目POM配置(由于Maven依赖关系引入相关jar包自动引入相关的包)
``` XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>hu-book</groupId>
  <artifactId>hu-books</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>pom</packaging>
  <modules>
  	<module>books-pojo</module>
  	<module>books-api</module>
    <module>books-manage</module>
    <module>shopping</module>
  </modules>
  <dependencyManagement>
  	<dependencies>
  		<dependency>
  			<groupId>hu-book</groupId>
  			<artifactId>books-api</artifactId>
  			<version>0.0.1-SNAPSHOT</version>
  		</dependency>
  		<dependency>
  			<groupId>hu-book</groupId>
  			<artifactId>books-manage</artifactId>
  			<version>0.0.1-SNAPSHOT</version>
  			<type>war</type>
  		</dependency>
  		<dependency>
  			<groupId>hu-book</groupId>
  			<artifactId>books-pojo</artifactId>
  			<version>0.0.1-SNAPSHOT</version>
  		</dependency>
  		<dependency>
  			<groupId>hu-book</groupId>
  			<artifactId>shopping</artifactId>
  			<version>0.0.1-SNAPSHOT</version>
  			<type>war</type>
  		</dependency>
  		<!-- 框架依赖 -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.0.1</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>2.2</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
			<version>1.2</version>
		</dependency>
		
		<dependency>
		    <groupId>org.springframework.data</groupId>
		    <artifactId>spring-data-jpa</artifactId>
		    <version>1.11.4.RELEASE</version>
	    </dependency>	
	    <dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>4.3.9.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.hibernate</groupId>
			<artifactId>hibernate-core</artifactId>
			<version>4.3.6.Final</version>
		</dependency>
		<dependency>
			<groupId>org.hibernate</groupId>
			<artifactId>hibernate-entitymanager</artifactId>
			<version>4.3.6.Final</version>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.6</version>
		</dependency>
		<!-- 分布式 相关依赖 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>dubbo</artifactId>
			<version>2.5.8</version>
		</dependency>
		<dependency>
			<groupId>com.101tec</groupId>
			<artifactId>zkclient</artifactId>
			<version>0.10</version>
		</dependency>
		
		<!-- 分布式 相关依赖 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>dubbo</artifactId>
			<version>2.5.8</version>
		</dependency>
		<dependency>
			<groupId>com.101tec</groupId>
			<artifactId>zkclient</artifactId>
			<version>0.10</version>
		</dependency>
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-databind</artifactId>
			<version>2.8.6</version>
		</dependency>
  	</dependencies>
  </dependencyManagement>
</project>
```
### 项目 消费者Maven Pom配置代码
```XML
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>demo.dubbo</groupId>
	<artifactId>calcclient</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<dependencies>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>4.3.10.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>dubbo</artifactId>
			<version>2.5.8</version>
		</dependency>

		<dependency>
			<groupId>com.101tec</groupId>
			<artifactId>zkclient</artifactId>
			<version>0.10</version>
		</dependency>
	</dependencies>
</project>
```
### 项目 消费者 XML配置代码
```XML
 <?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

	<dubbo:application name="calcclient-consumer"></dubbo:application>
	<dubbo:registry address="zookeeper://10.0.13.188:2181"></dubbo:registry>
	
	<dubbo:reference id="calcService"  interface="demo.calc.CalcService"></dubbo:reference>

</beans>
  
``` 
### 项目 提供者XML配置代码
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

     <dubbo:application name="book-manage-provider"></dubbo:application>
     <dubbo:registry address="zookeeper://10.0.13.215:2181"></dubbo:registry>
     
     <dubbo:service interface="demo.api.bookapi" ref="bookservice"></dubbo:service>
     <dubbo:service interface="demo.api.bookapi" ref="typeservice"></dubbo:service>
     <dubbo:protocol name="dubbo" port="30000"></dubbo:protocol>

</beans>
```
### 项目 消费者请求类测试
```Java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestCalcClient {
	public static void main(String[] args) throws Exception {
		ApplicationContext context = new ClassPathXmlApplicationContext("/app-client.xml");
		CalcService calcservice =(CalcService)context.getBean("calcService"); //加载配置文件
		System.out.println(calcservice.add(100, 200));   //调用接口方法
		System.out.println(calcservice.multi(100, 200));
		System.in.read();
	}

}
```


