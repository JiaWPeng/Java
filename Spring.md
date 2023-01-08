# 进阶第29天（Spring）

### Spring Framework 系统架构

- Core Contatiner：核心容器（Beans、Core、Context、SpEL）
  - 代码解耦
- Aspects：AOP思想实现
- AOP：面向切面 编程
  - Aspects：AOP思想实现
    - 功能增强
- Web：Web开发
  - 集成各种优秀的框架
  - 便捷的事务管理
- Data Intergration：数据集成
- Data Access：数据访问
- Test：单元测试与集成测试
  - 方便进行单元测试

#### IoC（Inversion ofControl）控制反转

​	**失去了一个new的权利，需要对象时找容器要(主动创建---被动获取)**

- 对象的创建控制权有程序转移到外部；使用对象时，由**主动new产生**对象转换为**由外部提供**对象，此过程中对象创建控制权由程序转移到外部
- **IoC容器**负责**对象的创建、初始化等**一些列工作，被创建或被管理的对象在IoC容器中统称为**Bean**
- **是一个对象容器  ===》Spring容器（ioc容器），Spring使用配置形式实现了这个容器**

##### DI依赖注入

在容器中建立bean与bean之间的依赖关系的整个过程，称为依赖注入

​	**由容器将依赖关系，注入到对应的属性中**

​	**DI容器在构建你得对象的同时，会完成其属性的赋值**

##### 目标：充分解耦

1. 使用IoC容器创建bean（IoC）
2. 在IoC容器内将有依赖关系的bean进行关系绑定（DI）

##### 实现IoC和DI的步骤

###### IoC

1. **导入坐标**

   ```xml
   <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-context</artifactId>
         <version>5.2.10.RELEASE</version>
       </dependency>
     </dependencies>
   ```

2. **创建Spring配置文件作为Bean容器管理对象**

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <!--
           IoC
           添加需要Spring管理的Bean
           class：需要管理的类全类名
           id：外部该对象可以根据此值获取
       -->
       <bean class="com.itheima.dao.impl.BookDaoImpl" id="bookDao"></bean>
   
       <bean class="com.itheima.service.impl.BookServiceImpl" id="bookService">
           <!--
               DI注入依赖
               name；本类的成员变量名（底层调用set方法）
               ref：引用类型的指向
               value：基本类型的值
           -->
           <property name="bookDao" ref="bookDao"/>
           <property name="i" value="1"/>
   
       </bean>
   </beans>
   ```

   **注意事项：bean定义时id属性在同一个上下文中(IOC容器中)不能重复**

3. **初始化IOC容器（Spring核心容器/Spring容器），通过容器获取Bean对象**

   ```java
      public static void main(String[] args) {
           //1.创建IoC容器对象，加载spring核心配置文件
           ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
           //2 从IOC容器中获取Bean对象(BookService对象)
           BookService bookService= (BookService)ctx.getBean("bookService");
           //3 调用Bean对象(BookService对象)的方法
           bookService.save();
       }
   ```

   

| **类别**   | **描述**                                                     |
| ---------- | ------------------------------------------------------------ |
| 名称       | bean                                                         |
| 类型       | 标签                                                         |
| 所属       | beans标签                                                    |
| 功能       | 定义Spring核心容器管理的对象                                 |
| 格式       | <beans>         <bean/>       <bean></bean>     </beans>     |
| 属性  列表 | id：bean的id，**使用容器可以通过id值获取对应的bean**，在一个容器中id值唯一  class：bean的类型，即配置的bean的全路径类名 |
| 范例       | <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>  <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl"></bean> |

**别名**

| **类别** | **描述**                                                     |
| -------- | ------------------------------------------------------------ |
| 名称     | name                                                         |
| 类型     | 属性                                                         |
| 所属     | bean标签                                                     |
| 功能     | 定义bean的别名，可定义多个，使用逗号(,)分号(;)空格( )分隔    |
| 范例     | <bean id="bookDao" name="dao bookDaoImpl" class="com.itheima.dao.impl.BookDaoImpl"/>  <bean name="service,bookServiceImpl" class="com.itheima.service.impl.BookServiceImpl"/> |

```java
注意事项：
//获取bean无论是通过id还是name获取，如果无法获取到，将抛出异常NoSuchBeanDefinitionException
NoSuchBeanDefinitionException: No bean named 'bookServiceImpl' available
```

**单例or多例**

| **类别** | **描述**                                                     |
| -------- | ------------------------------------------------------------ |
| 名称     | scope                                                        |
| 类型     | 属性                                                         |
| 所属     | bean标签                                                     |
| 功能     | 定义bean的作用范围，可选范围如下  singleton：单例（默认）  prototype：非单例 |
| 范例     | <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl" scope="prototype" /> |

**为什么bean默认为单例？**

- 适合交给容器进行管理的bean

​			表现层对象

​			业务层对象

​			数据层对象

​			工具对象

- 不适合交给容器进行管理的bean

​			封装实体的域对象

##### 实例化bean的四种方式 

Spring管理对象的方式，通过对象的无参构造反射构建出来的对象

默认情况下：**Bean的创建是基于反射，调用无参构造产生的**

- **构造方法（常用）**

  - BookDaoImpl实现类

  ```java
  public class BookDaoImpl implements BookDao {
      public BookDaoImpl() {
          System.out.println("book dao constructor is running ....");
      }
      public void save() {
          System.out.println("book dao save ...");
      }
  }
  ```

  - applicationContext.xml配置

  ```xml
  <!--方式一：构造方法实例化bean-->
  <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
  ```

  - AppForInstanceBook测试类

  ```java
  public class AppForInstanceBook {
      public static void main(String[] args) {
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
  
          BookDao bookDao = (BookDao) ctx.getBean("bookDao");
  
          bookDao.save();
      }
  }
  ```

- **静态工厂**

  - OrderDao接口和OrderDaoImpl实现类

  ```java
  public interface OrderDao {
      public void save();
  }
  public class OrderDaoImpl implements OrderDao {
      public void save() {
          System.out.println("order dao save ...");
      }
  }
  ```

  - OrderDaoFatory工厂类

  ```java
  //静态工厂创建对象
  public class OrderDaoFactory {
      public static OrderDao getOrderDao(){
          System.out.println("factory setup....");
          return new OrderDaoImpl();
      }
  }
  ```

  - applicationContext.xml配置

  ```xml
  <!--方式二：使用静态工厂实例化bean-->
  <bean id="orderDao" class="com.itheima.factory.OrderDaoFactory" factory-method="getOrderDao"/>
  ```

  ![image-20210729195248948](F:/Program Files/feiq/Recv Files/spring_day01/01_讲义/md/assets/image-20210729195248948.png)

  - AppForInstanceOrder测试类

  ```java
  public class AppForInstanceOrder {
      public static void main(String[] args) {
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
  
          OrderDao orderDao = (OrderDao) ctx.getBean("orderDao");
  
          orderDao.save();
      }
  }
  ```

- **实例工厂**

  - UserDao接口和UserDaoImpl实现类

  ```java
  public interface UserDao {
      public void save();
  }
  public class UserDaoImpl implements UserDao {
      public void save() {
          System.out.println("user dao save ...");
      }
  }
  ```

  - UserDaoFactory工厂类

  ```java
  //实例工厂创建对象
  public class UserDaoFactory {
      public UserDao getUserDao(){
          return new UserDaoImpl();
      }
  }
  ```

  - applicationContext.xml配置

  ```xml
  <!--方式三：使用实例工厂实例化bean-->
  <bean id="userFactory" class="com.itheima.factory.UserDaoFactory"/>
  
  <bean id="userDao" factory-method="getUserDao" factory-bean="userFactory"/>
  ```

  ![image-20210729200203249](F:/Program Files/feiq/Recv Files/spring_day01/01_讲义/md/assets/image-20210729200203249.png)

  - AppForInstanceUser测试类

  ```java
  public class AppForInstanceUser {
      public static void main(String[] args) {
          //        //创建实例工厂对象
          //        UserDaoFactory userDaoFactory = new UserDaoFactory();
          //        //通过实例工厂对象创建对象
          //        UserDao userDao = userDaoFactory.getUserDao();
          //        userDao.save();
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          UserDao userDao = (UserDao) ctx.getBean("userDao");
          userDao.save();
      }
  }
  ```

- **FactoryBean**

  - 定义UserDaoFactoryBean实现FactoryBean\<UserDao>

  > UserDaoFactoryBean中实例化什么类型的对象泛型就是该类型。

  ```java
  //FactoryBean创建对象
  public class UserDaoFactoryBean implements FactoryBean<UserDao> {
      //代替原始实例工厂中创建对象的方法
      public UserDao getObject() throws Exception {
          return new UserDaoImpl();
      }
  
      public Class<?> getObjectType() {
          return UserDao.class;
      }
  }
  ```

  - applicationContext.xml配置

  ```xml
  <!--方式四：使用FactoryBean实例化bean-->
  <bean id="userDao" class="com.itheima.factory.UserDaoFactoryBean"/>
  ```

  > 使用之前的AppForInstanceUser测试类去运行看结果就行了。注意配置文件中id="userDao"是否重复。

- **概括**

```xml
	  1:Bean的创建,是基于反射,调用空参构造产生的.  只要掌握这个,能说出来.
	  2:静态工厂交给spring管理,静态工厂的对象也交给spring管理.  
        <bean id="a对象" class="工厂位置" factory-method="creata方法名" ></bean>
      3:实例工厂交给spring管理,实例工厂要先教给spring.对象也交给spring 
        <bean id="实例工厂id" class="实例工厂位置"></bean>
		<bean id="a对象" factory-bean="实例工厂id" factory-method="creata方法名"></bean>	  
      4:按照流水线的实例工厂 implements BeanFactory<>
	    <bean id="a对象" class="工厂地址"></bean>
```



##### Bean的生命周期

- 初始化容器
  1. 创建对象
  2. 执行构造方法
  3. 执行属性注入
  4. 执行bean初始化方法
- 使用bean
  1. 执行业务操作
- 关闭/销毁容器
  1. 执行bean销毁方法

**bean随着容器的产生而产生，随着容器的销毁而销毁**



##### 依赖注入的两种方式

- set方式

  ```xml
  <property name="属性名" value/ref="值">  
      value:基本类型
      ref:引用类型
  ```

  

- 构造方式

  ```xml
  <constructor-arg name="属性名" value/ref="值">  
      value:基本类型
      ref:引用类型
  ```

**依赖注入方式选择**

1. 强制依赖使用构造器进行，使用setter注入有概率不进行注入导致null对象出现

   ```xml
   <bean id="bookService" class="com.jwpeng.service.impl.BookServiceImpl" scope="prototype">
           <constructor-arg name="bookDao" ref="bookDao"/>
       </bean>
       <bean id="bookDao">
       </bean>
   <!-- class没有指向实体类 -->
   ```
   
   
   
2. 可选依赖使用setter注入进行，灵活性强

3. Spring框架倡导使用构造器，第三方框架内部大多数采用构造器注入的形式进行数据初始化，相对严谨

4. 如果有必要可以两者同时使用，使用构造器注入完成强制依赖的注入，使用setter注入完成可选依赖的注入

5. 实际开发过程中还要根据实际情况分析，如果受控对象没有提供setter方法就必须使用构造器注入

6. **==自己开发的模块推荐使用setter注入==**

###### 依赖自动装配

配置中使用bean标签**autowire属性**设置自动装配的类型

```xml
<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl" autowire="byType"/>
```

**依赖自动装配特征**

1. 自动装配用于引用类型依赖注入，不能对简单类型进行操作
2. 使用按类型装配时（byType）必须保障容器中相同类型的bean唯一，推荐使用
3. 使用按名称装配时（byName）必须保障容器中具有指定名称的bean，因变量名与配置耦合，不推荐使用
4. 自动装配优先级低于setter注入与构造器注入，同时出现时自动装配配置失效

##### 集合注入

###### 注入数组类型数据

```xml
<property name="数组的属性名">
	<array>
    	<value>100</value>
        ...
    </array>
</property>
```

###### 注入List类型数据

```xml
<property name="List类型的属性名">
	<list>
    	<value>值1</value>
        ...
    </list>
</property>
```



###### 注入Set类型数据

```xml
<property name="Set类型的属性名">
	<set>
    	<value>值1</value>
        ...
    </set>
</property>
```



###### 注入Map类型数据

```xml
<property name="Map类型的属性名">
	<map>
    	<entry key="键" value="值"/>
        ...
    </map>
</property>
```



###### 注入Properties类型数据

```xml
<property name="properties">
    <props>
        <prop key="country">china</prop>
        <prop key="province">henan</prop>
        <prop key="city">kaifeng</prop>
    </props>
</property>
```

**说明：property标签表示setter方式注入，构造方式注入constructor-arg标签内部也可以写\<array>、\<list>、\<set>、\<map>、\<props>标签**



#### 绑定Druid连接池的配置信息

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" >
        <property name="username" value="root"/>
        <property name="password" value="a15932780581"/>
        <property name="url" value="jdbc:mysql://127.0.0.1:3306/spring?useSSL=false"/>
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
    </bean>
```

#### 加载Properties文件

```xml
<!-- 加载Properties文件绑定数据库 -->
    <context:property-placeholder location="db.properties"/>

<bean id="druid" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
        <property name="url" value="${url}"/>
        <property name="driverClassName" value="${driver}"/>
    </bean>

```

- 不加载系统属性：

  ```xml
  <context:property-placeholder location="jdbc.properties" system-properties-mode="NEVER"/>
  
  ```

- 加载多个Properties文件

  ```xml
  <context:property-placeholder location="jdbc.properties,msg.properties"/>
  
  ```

- 加载所有Properties文件

  ```xml
  <context:property-placeholder location="*.properties"/>
  
  ```

- 加载Properties文件标准格式

  ```xml
  <context:property-placeholder location="classpath:*.properties"/>
  
  ```

- 从类路径或jar包中搜索并加载Properties文件

  ```xml
  <context:property-placeholder location="classpath*:*.properties"/>
  
  ```

#### 创建容器

1. 类路径加载配置文件

   ```java
   ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
   
   ```

2. 文件路径加载配置文件

   ```java
   ApplicationContext ctx = new FileSystemXmlApplicationContext("D:\\applicationContext.xml");
   
   ```

3. 加载多个配置文件

   ```java
   ApplicationContext ctx = new ClassPathXmlApplicationContext("bean1.xml", "bean2.xml");
   ```

#### 获取bean

1. 使用bean名称获取

   ```java
   BookDao bookDao = (BookDao) ctx.getBean("bookDao");
   ```

2. 使用bean名称获取并指定类型

   ```java
   BookDao bookDao = ctx.getBean("bookDao", BookDao.class);
   ```

3. 使用bean类型获取

   ```java
   BookDao bookDao = ctx.getBean(BookDao.class);
   ```

#### BeanFactory初始化

- 类路径加载配置文件

  ```java
  Resource resources = new ClassPathResource("applicationContext.xml");
  BeanFactory bf = new XmlBeanFactory(resources);
  BookDao bookDao = bf.getBean("bookDao", BookDao.class);
  bookDao.save();
  
  ```

- BeanFactory创建完毕后，所有的bean均**为延迟加载**

#### 容器相关

- BeanFactory是IoC容器的顶层接口，初始化BeanFactory对象时，加载的bean延迟加载**，节约资源**
- ApplicationContext接口是Spring容器的核心接口，初始化时bean立即加载，**后期响应快**
- ApplicationContext接口提供基础的bean操作相关方法，通过其他接口扩展其功能
- ApplicationContext接口常用初始化类
  - ClassPathXmlApplicationContext
  - FileSystemXmlApplicationContext

#### bean相关

| bean 的属性    | 含义                                   |
| -------------- | -------------------------------------- |
| id             | bean的Id                               |
| name           | bean的别名                             |
| class          | bean类型，静态工厂类，FactoryBean类    |
| scope          | 控制bean的单例和多例（实例数量）       |
| init-method    | 生命周期初始化方法                     |
| destroy-method | 生命周期销毁方法                       |
| autowire       | 自动装配类型                           |
| factory-method | bean工厂方法，应用于静态工厂或实例工厂 |
| factory-bean   | 实例工厂bean                           |
| lazy-init      | 控制bean延迟加载                       |

#### 依赖注入相关

| 注入标签及属性                                               | 含义               |
| ------------------------------------------------------------ | ------------------ |
| <constructor-arg name="bookDao" ref="bookDao"/>              | 构造器注入引用类型 |
| <constructor-arg name="msg" value="WARN"/>                   | 构造器注入基本类型 |
| <constructor-arg type="java.lang.String" index="3" value="WARN"/> | 类型匹配与索引匹配 |
| <property name="bookDao" ref="bookDao"/>                     | set注入引用类型    |
| <property name="msg" value="WARN"/>                          | set注入基本类型    |
| <property name="names"><br/>     <list><br/>       <value>itcast</value><br/>       <ref bean="dataSource"/><br/>     </list><br/>   </property> | set注入集合类型    |
| <list><br/>       <value>itcast</value><br/>       <ref bean="dataSource"/><br/>     </list><br/> | list集合           |
| <value>itcast</value>                                        | 集合注入简单类型   |
| <ref bean="dataSource"/>                                     | 集合注入引用类型   |

#### 注解开发定义bean

- 使用@Component定义bean

  ```java
  @Component("bookDao")
  public class BookDaoImpl implements BookDao {}
  
  @Component//默认类名开头小写bookServiceImpl
  public class BookServiceImpl implements BookService {}
  
  ```

- 核心配置文集中通过组件扫描加载bean

  ```xml
  <context:component-scan base-package="com.itheima"/>
  
  ```

Spring提供@Component注解的三个衍生注解（可以替代Component）

- @Controller：用于表现层bean定义

- @Service：用于业务层bean定义

- @Repository（@Mapper）：用于数据层bean定义

  ```java
  @Repository("bookDao")
  public class BookDaoImpl implements BookDao {}
  @Service 
  public class BookServiceImpl implements BookService {}
  
  ```

#### 纯注解开发

Spring3.0开启了纯注解开发模式，使用Java类替代配置文件，开启了Spring快速开发赛道

Java类代替Spring核心配置文件

```java
@Configuration
@ComponentScan("com.itheima")
public class SpringConfig {}

<context:component-scan base-package="com.itheima"/>

//@Configuration注解用于设定当前类为配置类
//@ComponentScan注解用于设定扫描路径，此注解只能添加一次，多个数据请用数组格式
@ComponentScan({com.itheima.service","com.itheima.dao"})

```

读取Spring核心配置文件初始化容器对象**切换为读取Java配置类初始化容器对象**

```java
//加载配置文件初始化容器
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
//加载配置类初始化容器
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);

```

##### **bean作用范围**

使用@Scope定义作用范围

```
@Repository
@Scope("singleton")
public class BookDaoImpl implements BookDao {}

```

##### **bean生命周期**

使用@PostConstruct、@PreDestroy定义bean生命周期

```java
@Repository
@Scope("singleton")
public class BookDaoImpl implements BookDao {    	public BookDaoImpl() {        	System.out.println("book dao constructor ...");    	}    
	@PostConstruct//设置bean的初始化方法，构造之后执行
	public void init(){        	
        System.out.println("book init ...");
	}    
	@PreDestroy//设置bean的销毁方法，bean销毁之前执行
	public void destroy(){        	
        System.out.println("book destory ...");    
	}
}

```

##### 依赖注入

###### **@Autowired**

使用@Autowired注解开启自动装配模式（按类型）

```java
@Service
public class BookServiceImpl implements BookService {
	@Autowired
	private BookDao bookDao;
	public void setBookDao(BookDao bookDao) {
		this.bookDao = bookDao;
	}
	public void save() {
		System.out.println("book service save ...");
		bookDao.save();
	}
}

```

**注意**：

- 自动装配**基于反射设计创建**对象并**暴力反射对应属性**为私有属性初始化数据，因此无需提供setter方法

- 自动装配**建议使用无参构造方法创建对象**（默认），如果不提供对应构造方法，**请提供唯一的构造方法**

###### @Qualifier

使用@Qualifier注解开启指定名称装配bean

```java
@Service
public class BookServiceImpl implements BookService {
	@Autowired
	@Qualifier("bookDao")
	private BookDao bookDao;
}

```

**注意**：@Qualifier注解无法单独使用，必须配合@Autowired注解使用

###### @Value

使用@Value实现简单类型注入

```java
@Repository("bookDao")
public class BookDaoImpl implements BookDao {
	@Value("100")
	private String connectionNum;
}

```

###### @PropertySource

使用@PropertySource注解加载properties文件

```java
@Configuration
@ComponentScan("com.itheima")
@PropertySource("classpath:jdbc.properties")//classpath是类路径(resouces)
public class SpringConfig {}


@Repository("bookDao")
public class BookDaoImpl implements BookDao {
	@Value("${username}")
	private String connectionNum;
}
```

**注意**：路径仅支持单一文件配置，多文件请使用数组格式配置，不允许使用通配符*

##### 第三方bean管理

创建方法（@Bean）来返回一个bean对象交（@Import）给Spring管理

###### @Bean

使用@Bean配置第三方bean

```java
@Configuration
public class SpringConfig {
	@Bean//默认名是方法名
	public DataSource dataSource(){
		DruidDataSource ds = new DruidDataSource();
		ds.setDriverClassName("com.mysql.jdbc.Driver");
		ds.setUrl("jdbc:mysql://localhost:3306/spring_db");
		ds.setUsername("root");
		ds.setPassword("root");
		return ds;
	}
}

```

使用独立的配置类管理第三方bean

```java
public class JdbcConfig {
	@Bean
	public DataSource dataSource(){
	DruidDataSource ds = new DruidDataSource();
		ds.setDriverClassName("com.mysql.jdbc.Driver");
		ds.setUrl("jdbc:mysql://localhost:3306/spring_db");
		ds.setUsername("root");
		ds.setPassword("root");
		return ds;
	}
}

```

将独立的配置类加入核心配置

1. 导入式

   ```java
   public class JdbcConfig {
   	@Bean
   	public DataSource dataSource(){
   		DruidDataSource ds = new DruidDataSource();
   		//相关配置
           return ds;
       }
   }
   
   使用@Import注解手动加入配置类到核心配置，此注解只能添加一次，多个数据请用数组格式
   @Configuration
   @Import(JdbcConfig.class)
   public class SpringConfig {}
   ```

2. 扫描式

   ```java
   @Configuration
   public class JdbcConfig {
   	@Bean
   	public DataSource dataSource(){
   		DruidDataSource ds = new DruidDataSource();
   		//相关配置
           return ds;
       }
   }
   
   使用@ComponentScan注解扫描配置类所在的包，加载对应的配置类信息
   @Configuration
   @ComponentScan({"com.itheima.config","com.itheima.service","com.itheima.dao"})
   public class SpringConfig {}
   ```

##### 第三方bean依赖注入

- 简单类型

  ```java
  public class JdbcConfig {   
  	@Value("com.mysql.jdbc.Driver")
  	private String driver;
  	@Value("jdbc:mysql://localhost:3306/spring_db")
  	private String url;
  	@Value("root")
  	private String userName;
  	@Value("root")
  	private String password;
  	@Bean
  	public DataSource dataSource(){
  		DruidDataSource ds = new DruidDataSource();
  		ds.setDriverClassName(driver);
  		ds.setUrl(url);
  		ds.setUsername(userName);
  		ds.setPassword(userName);
  		return ds;
  	}
  }
  
  ```

- 引用类型

  ```java
  @Bean
  public DataSource dataSource(BookService bookService){
  	System.out.println(bookService);
  	DruidDataSource ds = new DruidDataSource();
  	//属性设置
  	return ds;
  }
  
  引用类型注入只需要为bean定义方法设置形参即可，容器会根据类型自动装配对象
  
  ```

XML配置对比注解配置

| **功能**       | **XML**配置                                                  | **注解**                                                     |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 定义bean       | bean标签  lid属性  lclass属性                                | @Component  @Controller  **@Service**  @Repository  **@ComponentScan** |
| 设置依赖注入   | setter注入(set方法)  引用/简单  构造器注入(构造方法)  引用/简单  自动装配 | **@Autowired**  @Qualifier  @Value                           |
| 配置第三方bean | bean标签  静态工厂、实例工厂、FactoryBean                    | **@Bean**                                                    |
| 作用范围       | scope属性                                                    | @Scope                                                       |
| 生命周期       | 标准接口  init-method  destroy-method                        | @PostConstructor  @PreDestroy                                |

#### Spring整合MyBatis

##### 整合MyBatis步骤

1. 创建DataSource配置类
   1. 创建bean对象（创建一个方法返回值为DataSource）
   2. 在Spring配置类中导入properties文件读取JDBC四大连接属性
   3. 在DataSource类中添加四个成员属性使用@Value注入数据
   4.    在方法中设置四大属性信息
2. 创建MyBatis配置类
   1. 创建SqlSessionFactoryBean的bean对象（返回值为SqlSessionFactoryBean）
      1. 设置别名属性（setTypeAliasesPackage）
      2. 设置数据源（setDataSource）
   2. 创建MappScannerConfigurer的bean对象（返回值为MappScannerConfigurer）
      1. 设置映射类（setBasePackage）
3. 使用@Import导入配置类到Spring核心类中

```xml
<configuration>
	<properties resource="jdbc.properties"></properties>  <!-- 初始化属性数据 -->
	<typeAliases>
		<package name="com.itheima.domain"/>			  <!-- 初始化类型别名 -->
	</typeAliases>
	<environments default="mysql">						  <!-- 初始化dataSource -->
	<environment id="mysql">
		<transactionManager type="JDBC"></transactionManager>
		<dataSource type="POOLED">
			<property name="driver" value="${jdbc.driver}"></property>
			<property name="url" value="${jdbc.url}"></property>
			<property name="username" value="${jdbc.username}"></property>
			<property name="password" value="${jdbc.password}"></property>
		</dataSource>
	</environment>
	</environments>
	<mappers>
		<package name="com.itheima.dao"></package>		  	<!-- 初始化映射配置 -->
	</mappers>
</configuration>

```

```java
<typeAliases>
		<package name="com.itheima.domain"/>			  <!-- 初始化类型别名 -->
</typeAliases>

@Bean
public SqlSessionFactoryBean sqlSessionFactory(@Autowired DataSource dataSource){//@Autowired可以省略 会自动去Spring中去找对应的bean
	SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
	
    //<!-- 初始化类型别名 --> 
    ssfb.setTypeAliasesPackage("com.itheima.domain");
    //<!-- 初始化dataSource --> 
	ssfb.setDataSource(dataSource);
	return ssfb;
}

```

```java
<mappers>
		<package name="com.itheima.dao"></package>		  	<!-- 初始化映射配置 -->
</mappers>
@Bean
public MapperScannerConfigurer mapperScannerConfigurer(){
	MapperScannerConfigurer msc = new MapperScannerConfigurer();
    //<!-- 初始化映射配置 -->
	msc.setBasePackage("com.itheima.dao");
	return msc;
}

```

#### Spring整合Junit

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
@ContextConfiguration(locations = "xml")
public class BookServiceTest {
	@Autowired
	private BookService bookService;
	@Test
	public void testSave(){
		bookService.save();
	}
}
```



#### AOP思想

两个看起来没有直接关系的代码，在运行的时候可以织入到一起

AOP(Aspect Oriented Programming)面向切面编程，一种编程范式，指导开发者如何组织程序结构

OOP(Object Oriented Programming)面向对象编程

作用：在**不惊动原始设**计的基础上为**其进行功能增强**

Spring理念：无入侵式/无侵入式

#### 核心概念

把业务代码和逻辑代码进行抽取

- 连接点（JoinPoint）：程序执行过程中的任意位置，粒度为执行方法、抛出异常、设置变量等
  - 在SpringAOP中，**理解为方法的执行**

- 切入点（**@Pointcut**）：匹配连接点的式子
  - 在SpringAOP中，一个切入点可以只**描述一个具体方法，也可以匹配多个方法**
    - 一个具体方法：com.itheima.dao包下的BookDao接口中的无形参无返回值的save方法
    - 匹配多个方法：所有的save方法，所有的get开头的方法，所有以Dao结尾的接口中的任意方法，所有带有一个参数的方法

- 通知（Advice）：在切入点处执行的操作，也就是共性功能**（增强代码）**
  - 在SpringAOP中，功能最终以方法的形式呈现

- 通知类：定义通知的类**（通知方法所在的类）**

- 切面（**@Aspect**）：描述通知与切入点的对应关系**（配置切面）**

@Before：在切入方法之前运行

```java
//获取切入方法参数
@Before("pt()")
public void before(JoinPoint jp) {
Object[] args = jp.getArgs();
System.out.println(Arrays.toString(args));
}
```

@After：在切入方法之后运行（**不管有没有异常都会执行**）

@AfterReturning：正常执行完后的通知

```java
//获取切入方法返回值
@AfterReturning(value = "pt()",returning = "ret")
public void afterReturning(String ret) {
System.out.println("afterReturning advice ..."+ret);
}
```

@AfterThrowing：异常后执行的通知

```java
//获取切入方法异常
@AfterThrowing(value = "pt()",throwing = "t")
public void afterThrowing(Throwable t) {    System.out.println("afterThrowing advice ..."+ t);
}

```

**@Around**：环绕通知

```java
@Around("pt()")
public Object aroud(ProceedingJoinPoint pjp){//参数代表正在执行的切入点
	Object returnValue =null;
	try{
		//@Before
		System.out.println("前置通知，我一勺三花淡奶")：
		//原有的业务代码切入点的方法执行
		returnValue = pjp.proceed();//
		method.invoke()原始业务代码的执行
		//正常执行后通知
		System.oUt.println("正常执行后通知..喝完没有住院")；
	}catch (Throwable e){
		//执行后异常通知@AfterThrowing
		System.out.println("异常后通知，，住院了.，.")；
	}finally
		//@QAfter后置通知
		System.out.println("后置通知，倒沫子了")：
	  }
	return returnValve;
}
```

  

```java
//signature权限定类名方法名 包名+类名+方法名
Signature signature pjp.getsignature();
String className = signature.getDeclaringTypeName();//获取的类名
String methodName = signature.getName();//方法名
```



#### AOP工作流程

1. Spring容器启动，加载bean
2. 启动AOP，加载所有切入点
3. bean工作的过程中，AOP内部监听机制一直监听配置的切入点是否运行
4. 当切入点对应的对象（**目标对象**）执行对应的切入点方法时，创建目标对象的**代理对象**
5. 动态将通知内容（**增强代码**）织入到代理对象对应的方法中
6. 最终由代理对象完成最终工作

**JDK的动态代理底层需要接口，所以我们被增强的目标类，一定是有接口实现的**

#### 切入点表达式

标准格式：动作关键字（访问修饰符 返回值 包名.类/接口名.方法名（参数）异常名）

动作关键字：描述切入点的行为动作，例如execution表示执行到指定切入点

访问修饰符：public，private等，可以省略

返回值

包名

类/接口名

方法名

参数

异常名：方法定义中抛出指定异常，可以省略

```java
@Pointcut("execution(修饰符 返回值 包名.类/接口名.方法名(参数))")
@Pointcut("execution(* com.jwpeng.service.*Service.*(..))")//绑定切入点

重点掌握：
* com.jwpeng.serivce.impl.*.*(..)	
* com.jwpeng.serivce..*.*(..)

```

*不能用于方法参数上，代表所有

..代表目录折叠

##### 常用技巧

1.所有代码按照标准规范开发，否则以下技巧全部失效

2.描述切入点通常描述到接口，而不描述到实现类

3.访问控制修饰符针对接口开发均采用public描述

4.返回值类型对于增删改类使用精准类型加速匹配，对于查询类使用*通配快速描述

5.包名书写尽量不使用..匹配，效率过低，常用*做单个包描述匹配，或精准匹配

6.接口名/类名书写名称与模块相关的采用匹配，例如UserService书写成Service，绑定业务层接口名

7.方法名书写以动词进行精准匹配，名词采用匹配，例如getById书写成getBy,selectAll书写成selectAll

8.参数规则较为复杂，根据业务方法灵活调整

9.通常不使用异常作为匹配规则

#### 获取切入点方法的参数

- 获取切入点方法的参数
  - JoinPoint：适用于前置、后置、返回后、抛出异常后通知
  - ProceedJointPoint：适用于环绕通知

- 获取切入点方法返回值
  - 返回后通知
  - 环绕通知

- 获取切入点方法运行异常信息
  - 抛出异常后通知
  - 环绕通知

#### AOP注解总结

**@Aspect：表示一个切面类**

**@Pointcut：绑定切入点/切入方法**

**@Before：切入方法执行前执行（可以获取参数）**

**@After：切入方法执行完成后执行**

**@AfterReturning：切入方法执行完成后执行（可以获取返回值）**

**@AfterThrowing：切入方法执行异常后执行（可以获取异常）**

**@Around：环绕可以顶前面四个**

**@EnableAspectJAutoProxy：开启切面类扫描**



#### Spring事务管理

事务作用：在数据层保障一系列的数据库操作同成功同失败

Spring事务作用：在数据层或业务层保障一系列的数据库操作同成功同失败

添加Spring事务管理：**@Transactional**

**用在方法上优先级高于类和接口**

注意：

1. Spring注解式事务通常添加在业务层接口中而不会添加到业务层实现类中，降低耦合
2. 注解式事务可以添加到业务方法上表示当前方法开启事务，也可以添加到接口上表示当前接口所有方法开启事务

Spring开启事务驱动：**@EnableTransactionManagement**

**底层就是AOP策略**

```java
@Bean
public DataSourceTransactionManager dataSourceTransactionManager(DataSource dataSource){
    DataSourceTransactionManager dstm = new DataSourceTransactionManager();
    dstm.setDataSource(dataSource);
    return dstm;
}
```



##### 事务角色

**事务管理员**：发起事务方，在Spring中通常指代业务层开启事务的方法

**事务协调员**：加入事务方，在Spring中通常指代数据层方法，也可以是业务层方法

##### 事务配置

| **属性**               | **作用**                     | **示例**                                    |
| ---------------------- | ---------------------------- | :------------------------------------------ |
| readOnly               | 设置是否为只读事务           | readOnly=true 只读事务                      |
| timeout                | 设置事务超时时间             | timeout  = -1（永不超时）                   |
| **rollbackFor**        | 设置事务回滚异常（class）    | rollbackFor =  {NullPointException.class}   |
| rollbackForClassName   | 设置事务回滚异常（String）   | 同上格式为字符串                            |
| noRollbackFor          | 设置事务不回滚异常（class）  | noRollbackFor =  {NullPointException.class} |
| noRollbackForClassName | 设置事务不回滚异常（String） | 同上格式为字符串                            |
| propagation            | 设置事务传播行为             | ……                                          |

##### 事务传播行为

事务协调员对事务管理员所携带事务的处理态度

在**TransactionDefinition**中定义事务传播行为.

| **传播属性**     | **事务管理员**                                               | **事务协调员** |
| ---------------- | ------------------------------------------------------------ | -------------- |
| REQUIRED（默认） | 开启T                                                        | 加入T          |
|                  | 无                                                           | 新建T2         |
| **REQUIRES_NEW** | 开启T                                                        | 新建T2         |
|                  | 无                                                           | 新建T2         |
| SUPPORTS         | 开启T                                                        | 加入T          |
|                  | 无                                                           | 无             |
| NOT_SUPPORTED    | 开启T                                                        | 无             |
|                  | 无                                                           | 无             |
| MANDATORY        | 开启T                                                        | 加入T          |
|                  | 无                                                           | ERROR          |
| NEVER            | 开启T                                                        | ERROR          |
|                  | 无                                                           | 无             |
| NESTED           | 设置savePoint,一旦事务回滚，事务将回滚到savePoint处，交由客户响应提交/回滚 |                |



#### SpringMVC

SpringMVC技术与Servlet技术功能等同，均属于web层开发技术

DispatcherServlet核心控制器

##### 使用步骤

1. 使用SpringMVC技术需要先导入SpringMVC坐标与Servlet坐标

   ```xml
   <dependency>
   	<groupId>javax.servlet</groupId>
   	<artifactId>javax.servlet-api</artifactId>
   	<version>3.1.0</version>
   	<scope>provided</scope>
   </dependency>
   <dependency>
   	<groupId>org.springframework</groupId>
   	<artifactId>spring-webmvc</artifactId>
   	<version>5.2.10.RELEASE</version>
   </dependency>
   ```

2. 创建SpringMVC控制器类（等同于Servlet功能）

   ```java
   @Controller
   public class UserController {
   @RequestMapping("/save")
   @ResponseBody
   	public String save(String name){
   		System.out.println("user save ..."+name);
   		return "{'info':'springmvc'}";
   	}
   }
   ```

3. 初始化SpringMVC环境（同Spring环境），设定SpringMVC加载对应的bean

   ```java
   @Configuration
   @ComponentScan("com.itheima.controller")
   public class SpringMvcConfig {}
   ```

4. 初始化Servlet容器，加载SpringMVC环境，并设置SpringMVC技术处理的请求

   ```java
   public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
       //创建Web容器
       protected WebApplicationContext createServletApplicationContext() {
           AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
           ctx.register(SpringMvcConfig.class);
           return ctx;
       }
       //拦截后分发
       protected String[] getServletMappings() {
           return new String[]{"/"};
       }
       //存放除了Web层对象的容器
       protected WebApplicationContext createRootApplicationContext() {
           return null;
       }
   }
   ```

##### MVC的注解

1. **@Controller**：是@Component的小弟，设定SpringMVC的核心控制器bean，定义在类上

2. **@RequestMapping**：设置当前控制器方法请求访问路径，定义在方法上，默认值value：请求访问路径（**定义在类上代表分层访问该路径下方法的RequestMapping**）

   ```java
   @Controller
   @RequestMapping("/user")
   public class UserController {
   @RequestMapping("/save")
   @ResponseBody
   public String save(){
   System.out.println("user save ...");
   return "{'module':'user save'}";
   }
   }
   
   
   //请求参数与形参名需要一致
   @RequestMapping("/commonParamDifferentName")
   @ResponseBody
   public String commonParamDifferentName(@RequestParam("name")String userName , int age){
   System.out.println("普通参数传递 userName ==> "+userName);
   System.out.println("普通参数传递 age ==> "+age);
   return "{'module':'common param different name'}";
   }
   
   
   // PoJo参数 请求参数与形参对象属性需要一致
   @RequestMapping("/pojoParam")
   @ResponseBody
   public String pojoParam(User user){
       System.out.println("pojo参数传递 user ==> "+user);
       return "{'module':'pojo param'}";
   }
   ```

   

3. **@ResponseBody**：设置当前控制器方法响应内容为当前返回值，无需解析，**定义在方法上**，**不加此注解表示此方法跳转的是页面**

   ```java
   //响应页面
   @RequestMapping("/toPage")
   public String toPage(){
       return "page.jsp";
   }
   
   //响应文本数据
   @RequestMapping("/toText")
   @ResponseBody
   public String toText(){
       return "response text";
   }
   
   //响应json数据（对象转json）
   @RequestMapping("/toJsonPOJO")
   @ResponseBody
   public User toJsonPOJO(){
       User user = new User();
       user.setName("赵云");
       user.setAge(41);
       return user;
   }
   
   //响应json数据（对象集合转json数组）
   @RequestMapping("/toJsonList")
   @ResponseBody
   public List<User> toJsonList(){
       User user1 = new User();
       user1.setName("赵云");
       user1.setAge(41);
       User user2 = new User();
       user2.setName("master 赵云");
       user2.setAge(40);
       List<User> userList = new ArrayList<User>();
       userList.add(user1);
       userList.add(user2);
       return userList;
   }
   
   
   
   ```

   

4. **@EnableWebMvc**：开启SpringMVC多项辅助功能，定义在类上

   

5. **@RequestBody**：将请求中请求体所包含的数据传递给请求参数，此注解一个处理器方法只能使用一次，**定义在**SpringMVC控制器方法**形参**定义前面

   ```java
   @RequestMapping("/listPojoParamForJson")
   @ResponseBody
   public String listPojoParamForJson(@RequestBody List<User> list){
   	System.out.println("list pojo(json)参数传递 list ==> "+list);
   	return "{'module':'list pojo for json param'}";
   }
   ```

   

6. @**DateTimeFormat**：设定日期时间型数据格式，定义在SpringMVC控制器方法形参前面

   ```java
   @RequestMapping("/dataParam")
   @ResponseBody
   public String dataParam(Date date,
                           @DateTimeFormat(pattern = "yyyy-MM-dd") Date date1,
                           @DateTimeFormat(pattern = "yyyy/MM/dd HH:mm:ss")Date date2){
       System.out.println("参数传递 date ==> "+date);
       System.out.println("参数传递 date(yyyy-MM-dd) ==> "+date1);
       System.out.println("参数传递 date(yyyy-MM-dd HH:mm:ss) ==> "+date2);
       return "{'module':'data param'}";
   }
   
   ```

7. @**PathVariable**：绑定路径参数与处理器方法形参间的关系，要求路径参数名与形参名一一对应，定义在形式参数前面

   ```java
   @RequestMapping(value = "/users/{id}" ,method = RequestMethod.DELETE)
   @ResponseBody
   public String delete(@PathVariable Integer id){
       System.out.println("user delete..." + id);
       return "{'module':'user delete'}";
   }
   ```

8. **@RestController**：设置当前控制器类为RESTful风格，等同于@Controller与@ResponseBody两个注解组合功能，定义在类的上方

   ```java
   @RestController
   public class BookController {}
   
   ```

9. **@GetMapping  @PostMapping @PutMapping @DeleteMapping**：设置当前控制器方法请求访问路径与请求动作，每种对应一个请求动作，例如@GetMapping对应GET请求，定义在方法上

   ```java
   @GetMapping("/{id}")
   public String getById(@PathVariable Integer id){
       System.out.println("book getById..."+id);
       return "{'module':'book getById'}";
   }
   
   ```

10. @**RestControllerAdvice**：为Rest风格开发的控制器类做增强，定义在增强类上

   ```java
   @RestControllerAdvice
   public class ProjectExceptionAdvice {}
   ```

11. @**ExceptionHandler：**设置指定异常的处理方案，功能等同于控制器方法，出现异常后终止原始控制器执行，并转入当前方法执行，定义在处理异常的方法上方

**@RequestBody @RequestParam @PathVariable区别**

- @RequestParam用于接收url地址传参或表单传参
- @RequestBody用于接收json数据
- @PathVariable用于接收路径参数，使用**{参数名称}**描述路径参数

**应用**

- 后期开发中，发送请求参数超过1个时，以json格式为主，@RequestBody应用较广
- 如果发送非json格式数据，选用@RequestParam接收请求参数
- 采用RESTful进行开发，当参数数量较少时，例如1个，可以采用@PathVariable接收请求路径变量，通常用于传递id值



- **AbstractDispatcherServletInitializer类**是SpringMVC提供的快速初始化Web3.0容器的抽象类

- **AbstractDispatcherServletInitializer**提供三个接口方法供用户实现
  - **ncreateServletApplicationContext()方法**，创建Servlet容器时，加载SpringMVC对应的bean并放入WebApplicationContext对象范围中，而WebApplicationContext的作用范围为ServletContext范围，即整个web容器范围
  - **ncreateRootApplicationContext()方法**，如果创建Servlet容器时需要加载非SpringMVC对应的bean，使用当前方法进行，使用方式同createServletApplicationContext()

##### 工作流程分析

- 启动服务器初始化过程
  1. 服务器启动，执行ServletContainersInitConfig类，初始化web容器
  2. 执行createServletApplicationContext方法，创建了WebApplicationContext对象
  3. 加载SpringMvcConfig
  4. 执行@ComponentScan加载对应的bean
  5. 加载UserController，每个@RequestMapping的名称对应一个具体的方法
  6. 执行getServletMappings方法，定义所有的请求都通过SpringMVC

- 单次请求过程
  1. 发送请求localhost/save
  2. web容器发现所有请求都经过SpringMVC，将请求交给SpringMVC处理
  3. 解析请求路径/save
  4. 由/save匹配执行对应的方法save(）
  5. 执行save()
  6. 检测到有@ResponseBody直接将save()方法的返回值作为响应求体返回给请求方

**Controller加载控制与业务bean加载控制**

- Spring控制的bean
  - 业务bean（Service）
  - 功能bean（DataSource等）

- SpringMVC相关bean加载控制
  - SpringMVC加载的bean对应的包均在com.itheima.controller包内

- Spring相关bean加载控制
  - 方式一：Spring加载的bean设定扫描范围为com.itheima，排除掉controller包内的bean
  - 方式二：Spring加载的bean设定扫描范围为精准范围，例如service包、dao包等
  - 方式三：不区分Spring与SpringMVC的环境，加载到同一个环境中

```java
@Configuration
@ComponentScan(value = "com.itheima",
			excludeFilters = @ComponentScan.Filter(        
			type = FilterType.ANNOTATION,
            classes = Controller.class
            )
)
public class SpringConfig {}

//属性
excludeFilters：排除扫描路径中加载的bean，需要指定类别（type）与具体项（classes）
includeFilters：加载指定的bean，需要指定类别（type）与具体项（classes）


```



#### 接收请求中的Json数据

1. 添加json数据转换相关坐标

   ```xml
   <dependency>
   	<groupId>com.fasterxml.jackson.core</groupId>
   	<artifactId>jackson-databind</artifactId>
   	<version>2.9.0</version>
   </dependency>
   
   ```

2. 设置发送json数据（请求body中添加json数据）

3. 开启自动转换json数据的支持

   ```java
   @Configuration
   @ComponentScan("com.itheima.controller")
   @EnableWebMvc//开启Json自动转换
   public class SpringMvcConfig {}
   
   
   //@EnableWebMvc注解功能强大，该注解整合了多个功能，此处仅使用其中一部分功能，即json数据进行自动类型转换
   
   ```

4. 设置接收json数据

   ```java
   @RequestMapping("/listParamForJson")
   @ResponseBody
   public String listParamForJson(@RequestBody List<String> likes){
   	System.out.println("list common(json)参数传递 list ==> "+likes);
   	return "{'module':'list common for json param'}";
   }
   
   
   //@RequestBody 把请求体中的Json数据取出，自动转换为后面的类型
   ```


##### 底层数据转换

```java
其内部依赖Converter接口
public interface Converter<S, T> {
    @Nullable
    T convert(S var1);
}

请求参数年龄数据(String→Integer)
json数据转对象(json→pojp)
日期格式转换(String→Date)
```

### REST风格

**REST**（Representational State Transfer），表现形式状态转换，**隐藏资源的访问行为，无法通过地址得知对资源是何种操作**

```http
传统风格资源描述形式
	http://localhost/user/getById?id=1
	http://localhost/user/saveUser
REST风格描述形式
	http://localhost/user/1
	http://localhost/user
```

写的路径相似，但是请求方式不一样，那对资源的操作就不一样；

**REST核心：**通过请求方式代替做的动作

1. GET（查询）
2. POST（新增/保存）
3. PUT（修改/更新）
4. DELETE（删除）

#### RESTful入门案例

```java
//设置清除参数
@RequestMapping(value = "/users/{id}" ,method = RequestMethod.DELETE)
@ResponseBody
public String delete(@PathVariable("id") Integer ida){
    System.out.println("user delete..." + ida;
    return "{'module':'user delete'}";
}

```

**@PathVariable**：定义在形参前，但是**参数名要和url中的占位符值一致**（不一致时服务器报空指针异常，页面报500）才可以获取到请求体的Value，可以优化性能，当value省略时（参数名和请求体中的Key值一致时）正常方法；（不一致报NumberFormatException: For input string 需要的Key名，页面报400）

**@RestController**：

**@GetMapping**  

**@PostMapping** 

**@PutMapping**

**@DeleteMapping**

### SSM整合

- Spring：管理bean
  - SpringConfig：扫描非web层的所有bean
- MyBatis
  - MyBatisConfig：MyBatis整合到Spring上，两个核心对象交给Spring管理（SqlSessionFactoryBean和MapperScannerConfigurer）
  - JdbcConfig：读取四大信息，生产连接池对象@Bean交给Spring管理
  - jdbc.properties：设置数据源的四大信息
- SpringMvc
  - ServletConfig：web项目启动时加载的一个配置，指的是web项目启动后关于SpringMvc前端控制内容
    - 产生对应的Spring容器
      - 加载springMvc的配置类
      - 加载Spring的配置类
    - 设置前端控制器拦截路径（ / 、  *.do  、 / *）
    - 编码过滤器
  - SpringMvcConfig：管理web层bean

#### Spring整合MyBatis

#### Spring整合SpringMVC

#### 异常处理

**所有异常交给web（controller）层处理**

出现异常现象的常见位置与常见诱因如下：

- 框架内部抛出的异常：因使用不合规导致
- 数据层抛出的异常：因外部服务器故障导致（例如：服务器访问超时）
- 业务层抛出的异常：因业务逻辑书写错误导致（例如：遍历业务书写操作，导致索引异常等）
- 表现层抛出的异常：因数据收集、校验等规则导致（例如：不匹配的数据类型间导致异常）
- 工具类抛出的异常：因工具类书写不严谨不够健壮导致（例如：必要释放的连接长期未释放等）

**所有的异常均抛出到表现层进行处理**

```java
//集中的、统一的处理项目中出现的异常
@RestControllerAdvice
public class ProjectExceptionAdvice {
    @ExceptionHandler(Exception.class)
    public Result doException(Exception ex){
        return new Result(666,null);
    }
}

```

@**RestControllerAdvice**

@**ExceptionHandler**

##### 项目异常分类

- 业务异常（BusinessException）
  - 规范的用户行为产生的异常
  - 不规范的用户行为操作产生的异常
- 系统异常（SystemException）
  - 项目运行过程中可预计且无法避免的异常
- 其他异常（Exception）
  - 编程人员未预期到的异常

#### 拦截器

拦截器（Interceptor）是一种动态拦截方法调用的机制，在SpringMVC中动态拦截控制器方法的执行

在指定的方法调用前后执行预先设定的代码

阻止原始方法的执行

总结：增强，原理是AOP思想

##### **拦截器和过滤器的区别**

**归属不同**：Filter属于Servlet技术，Interceptor属于SpringMVC技术

**拦截内容不同**：Filter对所有访问进行增强（可以拦截任意资源），Interceptor仅针对SpringMVC的访问进行增强（只能拦截对控制器方法的访问）

##### **入门案例**

1. ```java
   //声明拦截器的bean，并实现HandlerInterceptor接口（注意：扫描加载bean）
   @Component
   public class ProjectInterceptor implements HandlerInterceptor {
       //原始方法调用前执行
       //返回值 来决定 拦截是否放行  true放行 false拦截
       public boolean preHandle(..) throws Exception {
           System.out.println("preHandle...");
           return true;
       }
       //原始方法调用后执行
       //正常执行后 执行 如果遇到异常不执行
       public void postHandle(..) throws Exception {
           System.out.println("postHandle...");
       }
       //原始方法调用后执行
       //后置通知 不管有没有异常都会执行
       public void afterCompletion(..) throws Exception {
           System.out.println("afterCompletion...");    }
   }
   
   ```

2. ```java
   //定义配置类，继承WebMvcConfigurationSupport，实现addInterceptor方法（注意：扫描加载配置）
   @Configuration
   public class SpringMvcSupport extends WebMvcConfigurationSupport {
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           ...
       }
   }
   
   
   ```

3. ```java
   //添加拦截器并设定拦截的访问路径，路径可以通过可变参数设置多个
   @Configuration
   public class SpringMvcSupport extends WebMvcConfigurationSupport {
       @Autowired
       private ProjectInterceptor projectInterceptor;
       
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(projectInterceptor).addPathPatterns("/books");
       }
   }
   
   ```

4. ```java
   //使用标准接口WebMvcConfigurer简化开发
   @Configuration
   @ComponentScan("com.itheima.controller")
   @EnableWebMvc
   public class SpringMvcConfig implements WebMvcConfigurer{
       @Autowired
       private ProjectInterceptor projectInterceptor;
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(projectInterceptor).addPathPatterns("/books", "/books/*");
       }
   }
   
   ```

##### **拦截器执行流程**

preHandle =》return true =》controller =》postHandle =》afterCompletion

preHandle =》return false结束

##### 拦截器参数

###### 前置处理

```java
//参数request:请求对象
//参数response:响应对象
//参数handler:被调用的处理器对象，本质上是一个方法对象，对反射技术中的Method对象进行了再包装

//返回值为false，被拦截的处理器将不执行

public boolean preHandle(HttpServletRequest request, 
                         HttpServletResponse response, 
                         Object handler) throws Exception {
    System.out.println("preHandle...");
    return true;
}

```

###### 后置处理

```java
//modelAndView:如果处理器执行完成具有返回结果，可以读取到对应数据与页面信息，并进行调整

public void postHandle(HttpServletRequest request, 
                       HttpServletResponse response, 
                       Object handler, 
                       ModelAndView modelAndView) throws Exception {
    System.out.println("postHandle...");
}

```

###### 处理完成后

```java
//ex:如果处理器执行过程中出现异常对象，可以针对异常情况进行单独处理 

public void afterCompletion(HttpServletRequest request, 
                            HttpServletResponse response, 
                            Object handler, 
                            Exception ex) throws Exception {
    System.out.println("afterCompletion...");
}

```

##### 多拦截器执行顺序

- 当配置多个拦截器时，形成拦截器链
- 拦截器链的运行顺序参照拦截器添加顺序为准
- 当拦截器中出现对原始处理器的拦截，后面的拦截器均终止运行
- 当拦截器运行中断，仅运行配置在前面的拦截器的afterCompletion操作

###### 拦截器链的运行顺序

preHandle：与配置顺序相同，必定运行

postHandle：与配置顺序相反，可能不运行

afterCompletion：与配置顺序相反，可能不运行

### Spring Boot 快速构建项目

约定优于配置

提供了一种快速使用Spring的方式

#### 特点

1. **自动配置**

   Spring Boot的自动配置是一个运行时（更准确地说，是应用程序启动时）的过程，考虑了众多因素，才决定Spring配置应该用哪个，不该用哪个。该过程是SpringBoot自动完成的。

2. **起步依赖**

   起步依赖本质上是一个Maven项目对象模型（Project Object Model，POM），定义了对其他库的传递依赖，这些东西加在一起即支持某项功能。

   简单的说，起步依赖就是将具备某种功能的坐标打包到一起，并提供一些默认的功能。

3. **辅助功能**

   提供了一些大型项目中常见的非功能性特性，如嵌入式服务器、安全、指标，健康检测、外部配置等。

**Spring Boot 并不是对 Spring 功能上的增强，而是提供了一种快速使用 Spring 的方式。**

#### 快速入门

SpringBoot在创建项目时，使用jar的打包方式。

SpringBoot的引导类，是项目入口，运行main方法就可以启动项目。

使用SpringBoot和Spring构建的项目，业务代码编写方式完全一样。

##### 使用maven搭建SpringBoot工程，定义HelloController.hello()方法，返回”Hello SpringBoot!”

1. 在当前项目，创建模块，Maven项目spring-boot-01

2. 在pom.xml，导入SpringBoot起步依赖

   ```xml
   <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.7.5</version>
           <relativePath/> 
   </parent>
   
   <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   ```

3. 编写SpringBoot启动类

   ```java
   @SpringBootApplication
   public class Day07SpringBootApplication {
   
       public static void main(String[] args) {
           SpringApplication.run(Day07SpringBootApplication.class, args);
       }
   
   }
   ```

4. 定义Controller：和启动类平级创建

   ```java
   @RestController
   public class HelloController {
       @RequestMapping("/hello")
       public String hello(){
           return "Hello SpringBootONE";
       }
   }
   ```

5. 启动测试

##### 使用Spring Initializr(启动器依赖器)创建SpringBoot工程

1. 使用Spring Initializr 构建工程
2. 配置项目信息
3. 选择起步依赖
4. 编写Controller
5. 启动测试

##### 起步依赖原理分析

在spring-boot-starter-parent中定义了各种技术的版本信息，组合了一套最优搭配的技术版本。

在各种starter中，定义了完成该功能需要的坐标合集。

我们的工程继承parent，引入starter后，通过依赖传递，就可以简单方便获得需要的jar包，并且不会存在版本冲突等问题。

#### Spring Boot配置

##### 配置文件分类

1. properties：

   ```
   server.port=8080
   server.address=127.0.0.1
   ```

2. yml：

   ```
   server:
     port: 8080
     address: 127.0.0.1
   ```

3. xml:

   ```xml
   <server>
       <port>8080</port>
       <address>127.0.0.1</address>
   </server>
   ```

SpringBoot提供了2种配置文件类型：properteis和yml/yaml

默认配置文件名称：application

在同一级目录下优先级为：properties > yml > yaml

###### yml/yaml基本语法

1. **大小写敏感**
2. **数据值**前边**必须有空格**，作为分隔符
3. 使用**缩进表示层级**关系
4. 缩进时**不允许使用Tab键**，只**允许使用空格**（各个系统 Tab对应的 空格数目可能不同，导致层次混乱）。
5. 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
6. 表示注释，从这个字符一直到行尾，都会被解析器忽略。

**数据格式**

```
普通语法
personName: zhangsan
对象：键值对集合
person:
    name: haohao
    age: 31
    addr: beijing
#或者（了解即可）
person: {name: haohao,age: 31,addr: beijing}}  
数组：一组按次序排列的值
address:
  - beijing
  - shanghai
# 行内写法
address: [beijing,shanghai]

```

##### 读取配置文件内容（注入Bean）

@Value：使用${Key}：**只能读取字符串接收的内容**

@ConfigurationProperties：**必须提供Set方法**

```java
@Component
public class Person{
    @Value("${person.name}")
    private String name;
    @Value("${person.age}")
	private String age;
    @Value("${person.addr[1]}")
	private String addr;
}

@Data
@Component
@ConfigurationProperties(prefix "person")
public class Person{
    private String name;
	private String age;
	private String addr;
}

//编辑yml文件有提示
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-configuration-processor</artifactId>
	<optional>true</optional>
</dependency>
```

 自动提示配置信息 

##### profile

profile功能就是来进行动态配置切换的。

```yaml
#通过active指定选用配置环境
spring:
  profiles:
    active: pro
    
    spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/spring_boot?useSSL=false
    username: root
    password: a15932780581

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.jwpeng.springbootmybatis.entity

```

**profile配置方式**

- profile文件方式
- yml配置方式
- Yml多文档方式

**profile激活方式**

- 配置文件：spring.profiles.active=dey
- 虚拟机参数：在VM options指定：-Dspring.profiles..active=dev
- 命令行参数：java-jar xxx.jar-spring-profiles.active=dev

```
配置文件：再配置文件中配置：spring.profiles.active=dey
虚拟机参数（了解）：在VM options指定：-Dspring.profiles..active=dev
命令行参数（了解）：java-jar xxx.jar-spring-profiles.active=dev
```

#### SpringBoot整合MyBatis

1. 搭建SpringBoot工程，名为spring-boot-mybatis
2. 引入mybatis起步依赖，添加mysql驱动
3. 编写DataSource和MyBatis相关配置
4. 定义表和实体类
5. 编写dao和mapper文件/纯注解开发
6. 测试

**使用接口注入Service方便底层AOP操作**

#### SpringBoot整合Junit

1. 引入starter-test起步依赖

2. 编写测试类

3. 添加测试相关注解

   - @RunWith(SpringRunner.class)

      (2.3.0及更高版本的SpringBoot无需指定该配置)

   - @SpringBootTest

   - 编写测试方法，在方法上加@Test

     ```java
     //@RunWith(SpringRunner.class) // 2.3.0及更高版本的SpringBoot无需指定该配置
     @SpringBootTest
     public class UserServiceImplTest {
     
         @Autowired
         private UserServiceImpl userService;
         @Test
         public void findAll() {
             List<User> users = userService.findAll();
             System.out.println(users);
         }
     }
     ```

**Assert断言操作**

Assert.notNull(对象，"描述信息")；断言这个对象不是空的，如果是空的抛异常展示描述信息

#### SpringBoot整合RestTemplate

完成远程调用

1. 创建SpringBoot工程，名为spring-boot-restemplate

2. 编写配置类，创建RestTemplate对象

   ```java
   @Configuration
   public class BeanConfig {
   
       @Bean
       public RestTemplate createRestTemplate(){
           return new RestTemplate();
       }
   }
   ```

3. 编写TestController类，远程访问另一个moudle中的处理器

   ```java
   @RestController
   @RequestMapping("/test")
   public class TestController {
   
       @Autowired
       RestTemplate restTemplate;
   	
   	@GetMapping("/callFindAll")
   	public List callFindAll(){
   
   		List returnObj = restTemplate.postForObject("http://localhost:8080/findAllUser",null, List.class);
   		return returnObj;
   	}
   }
   ```

4. 修改配置文件，端口改为9008

5. 访问测试 （先启动spring-boot-mybatis工程，再启动当前工程）

方法介绍

| 方法名        | 方法含义       | 参数列表                                                     | 返回值 |
| ------------- | -------------- | ------------------------------------------------------------ | ------ |
| put           | 访问put请求    | 参1：url地址（绝对路径） 参2：请求体 参3：参数（可变参数类型） | void   |
| delete        | 访问delete请求 | 参1：url地址（绝对路径） 参2：请求体 参3：参数（可变参数类型） | void   |
| getForObject  | 访问get请求    | 参1：url地址（绝对路径） 参2：参数（可变参数类型）           |        |
| postForObject | 访问post请求   | 参1：url地址（绝对路径） 参2：参数（可变参数类型）           |        |

备注：`getForObject()`比`getForEntity()`多包含了将HTTP转成POJO的功能，但是`getForObject`没有处理`response`的能力。因为它拿到手的就是成型的`pojo`。省略了很多`response`的信息。

### MyBatisPlus

```xml
<!--需要手动导入-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.0</version>
</dependency>
```

### 

```yml
# datasource
spring:
  datasource:
    url: jdbc:mysql:///mp?useSSL=false&useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
mybatis-plus: # 控制台打印执行日志
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    # 映射 _下划线命名 驼峰是命名之间关闭自动转换  
    # MybatisPlus默认为true 默认按实体类的成员属性如userName会映射数据表的user_name 改为false则会映射数据表的userName
  	map-underscore-to-camel-case: false
```

备注：mybatis-plus默认的mapper映射文件目录是mapper/**/*.xml。

**MetaObjectHander：**元数据对象处理器，指在数据添加到数据库之前，想操作哪些字段，可以在这里尽情操作

```
步骤：
1.确定 哪些字段需要进行自动填充
2.确定这些字段什么时候填充
3.在元数据处理器中：确定什么时候指定字段自动填充什么
```

编写实体类

```java
@TableName("tb_user")//当前类和数据库 tb_user对应
@Data //加上setget方法
public class User {
     @TableId(type = IdType.AUTO)//id采取数据库自增策略
     private Long id;// 自增生成策略
                     // 如果不告诉mp 这是自增
                    // 它会利用雪花算法 生成一个19位不重复的id

     // 如果 java 驼峰 数据库是  下划线 怎么对应上呢?  resultMap 或者 起别名解决,,,
     // 通过一个注解来解决 实体类与表字段不一致问题
     @TableField("user_name")
     private String userName;

     private String password;
     private String name;
     private int age;
     private String email;
}

```

编写mapper

```java
@SpringBootApplication
@MapperScan("com.itheima.mp.mapper")
public class MpApplication {

    public static void main(String[] args) {
        SpringApplication.run(MpApplication.class, args);
    }

}

```

在启动类上添加@MapperScan

```java
@SpringBootApplication
@MapperScan("com.itheima.mp.mapper")
public class MpApplication {

    public static void main(String[] args) {
        SpringApplication.run(MpApplication.class, args);
    }

}

```



#### CRUD

1. 添加操作：int insert(T entity);

   ```java
   @TableName("tb_user")//指定表名
   @Data
   public class User{
   @TableId(type = IdType.AUTO)
   private Long id;
   @TableField("user_name")
   //指症字段
   private string userName;
   private String password;
   private string name;
   private Integer age;
   private String email;
   @TableField(exist=false)//忽略字段
   private String info;
   }
   
   1)表及字段
   //@TableName("tb_user"):
   指定表名
   //@TableField("user-name")指定字段映射关系
   实体类的属性名和数据库的字段名自动映射：
   ·名称一样
   数据库字段使用_分则，实体类属性名使用驼峰名称
   否贝博更使用TableField("user_name“)指定味射关系
   2)忽略某个字段的查和括入
   //@TableField(exist false)
   3)设置id姓成策略：AUT0数库自赠
   /*
   @TableId(type IdType.AUTO)
   mybatisplus如果不设置1D生成策略。
   默认生成策略是雪花算法，会生成一申不重复的19位的数宇
   */
   ```

2. 删除操作

   ```java
   //根据entity条件，删除记录
   int delete(@Param(Constants.WRAPPER)Wrapper<T>wrapper);
   //删除（根据ID批量删除）
   int deleteBatchIds(@Param(Constants.COLLECTION)Collection<?extends Serializable>idList);
   //根据ID删除
   int deleteById(Serializable id);
   //根据columnMap条件，删除记录
   int deleteByMap(@Param(Constants.COLUMN_MAP)Map<string,object>columnMap);
   ```

3. 修改操作

   ```java
   //根据whereEntity条件，更新记录
   int update(@Param(Constants.ENTITY)T entity,@Param(Constants.WRAPPER)Wrapper<T>updateWrapper)
   //根据ID修改
   int updateById(@Param(Constants.ENTITY)T entity);
   ```

4. 查询操作

   ```java
   //根据ID查询
   T selectById(Serializable id);
   //根据entity条件，查询一条记录
   T selectone(@Param(Constants.WRAPPER)Wrapper<T>queryWrapper);
   //查询（根据ID批量查询）
   List<T>selectBatchIds(@Param(Constants.COLLECTION)Collection<?extends Serializable>idList);
   //根据entity条件，查询全记录
   List<T>selectList(@Param(Constants.WRAPPER)Wrapper<T>querywrapper);
   //查间（根据columnMap条件）
   List<T>selectByMap(@Param(Constants.COLUMN _MAP)Map<String,Object>columnMap);
   //根据Wrapper条件，查询全部记录
   List<Map<String,Object>>selectMaps(@Param(Constants.WRAPPER)Wrapper<T>querywrapper);
   //根据Wrapper条件，查询全部记录。注意：只返回第一个字段的值
   List<object>selectobjs(@Param(Constants.WRAPPER)Wrapper<T>querywrapper);
   //根据entity条件，查询全部记录（并顶》
   IPage<T>selectPage(IPage<T>page,@Param(Constants.WRAPPER)Wrapper<T>querywrapper);
   //根据Wrapper条件，查询全部记录（并页）
   IPage<Map<string,Object>>selectMapsPage(IPage<T>page,@Param(Constants.WRAPPER)Wrapper<T>que
   //根据Wrapper条件，查询总记录数
   Integer selectCount(@Param(Constants.WRAPPER)Wrapper<T>querywrapper);
   ```

5. 分页查询

   ```java
   /*
   在启动类添加一个MyBatisPlus的拦截器
   */
   @Bean
   public MybatisPlusInterceptor mybatisPlusInterceptor(){
          MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
          mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
          return mybatisPlusInterceptor;
   }
   
   /*
   测试
   */
    	@Test
       public void testSelectPage(){
           // 分页查询
           //  需要两个已知条件  当前页码
           int current = 2;//当前页码
           int size = 2; //每页显示条数
   
           // 根据 limit语句  count语句
           // 查询出来   当前页的数据,和总条数  (算出总页数)
   
           // 现在  我们作为使用者 用写sql语句吗?
           // mp的思路   IPage  Page 代表着一个 分页对象
           // 属性里面 包含  当前页码 每页显示条数 当前记录数据  总条数
           //            current1  pagesize2  records3    total4
           //       1,2 是已知   3 4 求的
           IPage<User> page = new Page<>(current,size);//封装进去已知条件
   
           // 我们是不是想要让mp 根据我的已知条件 求出 3 4 两个内容
           userMapper.selectPage(page,null);
                                        // 除了分页以外条件
           // 底层会执行两个sql语句  查询总条数  查询分页数据
           //  并且会完成 数据的赋值操作....
           // page对象里面 就有了 总条数 有了当前页数据 有了总页数
           // 说白了 mp底层会通过这个方法 给我们 page对象进行了增强
           //  因为 mp底层采用的拦截器的形式 对page对象进行增强!!
           //  如果想要实现功能 需要在启动类  配置一个 mp的 拦截器对象
           // 查看当前页的数据
           List<User> records = page.getRecords();
           // 查看总条数
           long total = page.getTotal();
           //查看总页数
           long pages = page.getPages();
   
           System.out.println("当前页数据:"+records);
           System.out.println("总条数:"+total);
           System.out.println("总页数:"+pages);
       }
   ```

   

##### 小结：

**@TableName("表名")**：实体类对应的数据库表

**@TableId(type = IdType.AUTO)**：id采取数据库自增策略；不加默认按照雪花算法产生19位不重复id

**@TableField("字段名")**：解决数据库表和类中属性不一致问题；@TableField(exist=false)：**忽略字段**



#### 条件构造器

就是WHERE后面的条件

wrapper可以简单理解代表的是WHERE**后面的条件**

支持链式编程

- QueryWrapper WHERE后面的条件
  - LambdaQueryWrapper
- UpdateWrapper UPDATE SET 的内容
  - LambdaUpdateWrapper

##### 基本比较查询

- eq( ) : 等于 =
- ne( ) : 不等于 < >
- gt( ) : 大于 >
- ge( ) : 大于等于 >=
- lt( ) : 小于 <
- le( ) : 小于等于 <=
- between ( ) : BETWEEN 值1 AND 值2
- notBetween ( ) : NOT BETWEEN 值1 AND 值2
- in( ) : in
- notIn( ) ：not in

```java
@Test
public void testwrapper1(){
//1.创建查询条件构建器
QueryWr apper<User>wrapper new QueryWr apper<>(;
/2.设置条件
wrapper.eq("user_name","lisi")
.1t("age",23)
.in("name","李四"，"王五")；
//select from tb_user where user_name =and age and name in (?,?
List<User>users userMapper.selectList(wrapper);
System.out.println(users);
```

##### 逻辑查询

or( ) ：让紧接着下一个方法用or连接 

```java
@Test
public void testwrapper2(){
//1.创建查询条件构建器
Querywr apper<User>wrapper new Querywr apper<>(;
//2.设置条件
wrapper.eq("user_name","lisi")
.or()
.1t("age",23)
.in("name","李四"，"王五")：
/*
select from tb_user where user_name =or age and name in (?,?
*/
List<User>users userMapper.selectList(wrapper);
System.out.println(users);
```

##### 模糊查询

- like
- notLike
- likeLeft
- likeRight

```java
/*
模糊查询
*/
@Test
public void testwrapper3({
//1.创建查询条件构建器
QueryWr apper<user>wrapper new QueryWr apper<>);
//2.设置条件
wr apper.likeLeft("user_name","zhang");
/*
SELECT id,user_name,password,name,age,email
from tb_user
where user_name like
%zhang
*/
List<User>users userMapper.selectList (wrapper);
System.out.println(users);
```

##### 排序查询

- orderBy
- orderByAsc
- orderByDesc

```java
@Test
public void testwrapper4(){
//1.创建查询条件构建器
QueryWrapper<User>wrapper new QueryWr apper<>(;
//2.设置条件
wrapper.eq("user_name","lisi")
.or(0
.1t("age",23)
.in("name","李四"，"王五")
//.orderBy(true,true,"age")
orderByDesc("age");
/*
select from tb_user where user_name or age and name in (??order
by age asc
*/
List<User>users userMapper.selectList(wrapper);
System.out.println(users);
```

##### select

指定需要查询的字段

```java
@Test
public void testwrapper5(){
//1.创建查询条件构建器
Querywr apper<User>wrapper new Querywr apper<>();
//2.设置条件
wr apper.eq("user_name","lisi")
.or(0
.1t("age",23)
.in("name","李四"，"王五")
//.orderBy(true,true,"age")
.orderByDesc("age")
.select("id","user_name");
/*
select id,user_name from tb_user where user_name or age and name in
(7,?order by age asc
*/
List<User>users userMapper.selectList (wrapper);
System.out.println(users);
```

##### LambdaQueryWrapper

消除代码中的硬编码

硬编码就是这个内容是程序员拼出来的字符串，**存在写错风险**

```java
@Test
public void testwrapper7(){
Querywrapper<User>wrapper new Querywr apper<>();
/*
wrapper.eq("user_name","lisi");
User user1 userMapper.selectone(wrapper);
System.out.println(user1);
*/
LambdaQuerywrapper<user> 1wapper = new LambdaQuerywrapper ()
1wapper.eq(User::getUserName,"lisi");//User::getName
User user2 userMapper.selectone(1Wapper);
System.out.println(user2);
```

##### 删除条件构造器（尽量避免使用）

```java
//根据entity条件，删除记录
int delete(@Param(Constants.WRAPPER)Wrapper<T>wrapper);
//删除（根据D批量除）
int deleteBatchIds(@Param(Constants.COLLECTION)Collection<?extends Serializable>idList);
//根据ID删除
int deleteById(Serializable id);
//根据columnMap条件，除记录
int deleteByMap(@Param(Constants.COLUMN_MAP)Map<string,Object>columnMap);

@Test
public void testwrapper8(){
QueryWrapper<User>wrapper = new QueryWrapper<>();
wrapper.eq("user_name","bbb");
userMapper.delete(wrapper);
```

##### 修改条件构造器（尽量避免使用）

```java
//根据entity条件，除记录
int delete(@Param(Constants.WRAPPER)Wrapper<T>wrapper);
//删除（根据D批量除）
int deleteBatchIds(@Param(Constants.COLLECTION)Collection<?extends Serializable>idList);
//根据ID除
int deleteById(Serializable id);
//根据columnMap条件，除记录
int deleteByMap(@Param(Constants.COLUMN MAP)Map<string,Object>columnMap);

//根据whereEntity条件，更新记录
int update(@Param(Constants.ENTITY)T entity,@Param(Constants.WRAPPER)Wrapper<T>updatewrapper)
//根据ID修改
int updateById(@Param(Constants.ENTITY)T entity);

@Test
public void testwrapper9(){
Updatewrapper<User>wr apper new Updatewr apper<();
//条件
wrapper.eq("user_name","lisi")
.set("pas5word","22222");
//update tb_user set password where user_name
userMapper.update(null,wr apper);
}
@Test
public void testwrapper10({
Updatewrapper<User>wrapper new Updatewr apper<();
//条件
wrapper.eq("user_name","lisi");
//update tb_user set password ?age where user_name
User user new User();
user.setPassword("3333");
user.setAge(33);
userMapper.update(user,wrapper);
```

#### Mybatis-Plus Service封装

可以继承它提供的接口和实现类，使得编码更加高效。

1. 定义接口继承IService
2. 定义实现类继承ServiceImpl<Mapper,Entity>，实现定义的接口

```java
//接口
public interface _UserService extends IService<User>{
//实现类封装
@Service
public class _UserServiceImpl extends ServiceImpl<UserMapper,User>implements
_UserService {
```



### Spring Cloud 微服务