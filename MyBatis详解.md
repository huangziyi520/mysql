# MyBatis详解

快速入门

1 依赖

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mosgirl</groupId>
  <artifactId>maven_day01_one</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>maven_day01_one Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <dependencies>

<!--    &lt;!&ndash; 配置spirng&ndash;&gt;-->
<!--    <dependency>-->
<!--      <groupId>org.springframework</groupId>-->
<!--      <artifactId>spring-context</artifactId>-->
<!--      <version>5.1.9.RELEASE</version>-->
<!--    </dependency>-->
<!--    &lt;!&ndash;  配置DispatcherServlet需要  &ndash;&gt;-->
<!--    <dependency>-->
<!--      <groupId>javax.servlet</groupId>-->
<!--      <artifactId>servlet-api</artifactId>-->
<!--      <version>2.5</version>-->
<!--      <scope>provided</scope>-->
<!--    </dependency>-->
<!--    <dependency>-->
<!--      <groupId>org.springframework</groupId>-->
<!--      <artifactId>spring-web</artifactId>-->
<!--      <version>5.1.9.RELEASE</version>-->
<!--    </dependency>-->
<!--    <dependency>-->
<!--      <groupId>org.springframework</groupId>-->
<!--      <artifactId>spring-webmvc</artifactId>-->
<!--      <version>5.1.9.RELEASE</version>-->
<!--    </dependency>-->

<!-- mybatis依赖 -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.2</version>
    </dependency>
<!-- mysql相关驱动 -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.17</version>
    </dependency>
<!-- 测试工具依赖 -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
    </dependency>
<!-- 日志打印需要的相关依赖 -->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>

  </dependencies>

  <build>
    <finalName>maven_day01_one</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```

目录结构：

![1583886400371](E:\Typora_storage\images\1583886400371.png)

IUserDao.java

```java
package cn.mosgirl.dao;

import cn.mosgirl.domain.User;

import java.util.List;

/**
 * @author hzc
 * @date 2020/3/10 23:21
 */
public interface IUserDao {

    /*查询所有的方法*/
    List<User> findAll();

}

```

User.java

```java
package cn.mosgirl.domain;


import java.io.Serializable;
import java.util.Date;

/**
 * @author hzc
 * @date 2020/3/10 22:53
 */
public class User implements Serializable {

    private Integer id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}

```

同时在数据库中jdbc:mysql://localhost:3306/mybatis_test总创建一张表user，其各个字段的取名和user.java的类相对应

配置文件

sqlMap.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <environments default="mysql">
        <environment id="mysql">
            <!-- 配置事务 -->
            <transactionManager type="JDBC"></transactionManager>
            <!-- 配置数据源 -->
            <dataSource type="POOLED">
                <!-- 配置连接数据库的四个基本参数 -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis_test?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="huangziyi520"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!-- 配置映射文件 -->
        <mapper resource="cn/mosgirl/dao/IUserDao.xml"></mapper>
    </mappers>
</configuration>
```

IUser.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.mosgirl.dao.IUserDao">
    <select id="findAll" resultType="cn.mosgirl.domain.User">
        select * from user
    </select>
</mapper>
```

测试：

```java
package cn.mosgirl;


import cn.mosgirl.dao.IUserDao;
import cn.mosgirl.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.apache.log4j.Logger;
import java.io.IOException;
import java.io.InputStream;
import java.util.List;



/**
 * @author hzc
 * @date 2020/3/11 0:02
 */
public class UserDaoTest {

    private static Logger logger = Logger.getLogger(UserDaoTest.class);
    public static void main(String[] args) throws IOException {
        /*1 加载核心配置文件*/
        InputStream inputStream = Resources.getResourceAsStream("sqlMap.xml");
        /*2 构建SqlSessionFactory工厂*/
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        /*3 打开SqlSession*/
        SqlSession sqlSession = sessionFactory.openSession();
        /*4 获取接口的代理对象*/
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        /*5 执行查询的方法*/
        List<User> list = userDao.findAll();
        for(User user:list){
            logger.info(user.toString());
        }
        /*释放资源*/
        sqlSession.close();
        inputStream.close();
    }
}

```

## 第一章  使用mybatis

使用mybatis的步骤:

```
1 导入mybatis包和mysql-connetor包
2 配合核心配置文件mybatis-config.xml
3 创建映射文件XxxMapper.xml [其中，xxx是接口的名称]
4 创建mapper接口,实现两个绑定
5 创建会话对象SqlSession,操作数据库
6 测试
```



### 1 实体类属性名和数据库字段名不一致

```xml
<mapper namespace="cn.mosgirl.dao.IUserDao">
    <!--用来配置实体类中的属性名和数据库中列名的对应关系-->
    <resultMap id="myMap" type="org.mosgirl.domain.User">
    	<!--配置主键之间的对应关系-->
    	<id property="user_id" column="id"></id>
    	<!--配置其他普通字段的对应关系-->
    	<result property="user_username" column="username"></result>
    	<result property="birthday" colu mn="username"></result>
    	<result property="user_sex" colu mn="sex"></result>
    	<result property="user_address" colu mn="address"></result>
    </resultMap>
    <select id="findAll" resultType="cn.mosgirl.domain.User">
        select * from user
    </select>
</mapper>
```

### 2 properties,typeAliases和package标签的作用

在sqlMapConfig.xml文件中引用类路径下的文件:

```properties
<properties resource="jdbcConfig.properties">
</properties>
<!--在映射文件里面写User这个类的时候就不需要全路径名,只需要写别名user就行-->
<typeAliases>
	<typeAlias type="org.westos.domain.User" alias="user"></typeAlias>
</typeAliases>
<!---->
<mappers>
	<package name="org.westos.domain">	
</mappers>
...便可以在这里引用properties中的参数
```

### 3 连接池

相当于一个容器,会提前初始化一定数量的连接对象,以便重复使用

### 4 动态sql语句[foreach语句]

```xml
<mapper namespace="cn.mosgirl.dao.IUserDao">
    <select id="findAll" resultType="cn.mosgirl.domain.User">
        select * from user where 1=1
    </select>
    <if test="sex!='' and sex!=null">
    	and sex=#{sex}
    </if>
     <if test="username!='' and username!=null">
    	and username=#{username}
    </if>
</mapper>
```

### 5 抽取重复的代码片段

```
<mapper namespace="cn.mosgirl.dao.IUserDao">
    <!--可以抽取的重复sql代码片段-->
    <sql id="msql">
    	select * from user<!--后面的分号最好不要写-->
    </sql>
    <select id="findAll" resultType="cn.mosgirl.domain.User">
        <!--select * from user where 1=1-->
        <!--在此处引用sql语句-->
        <include refide="mysql"></include>
    </select>
    <if test="sex!='' and sex!=null">
    	and sex=#{sex}
    </if>
     <if test="username!='' and username!=null">
    	and username=#{username}
    </if>
</mapper>
```

## 第二章 mybatis原理讲解

```
mybatis是orm框架，orm框架也就是把字段映射为对象的属性
```

jdbc的标准操作：

```
1 导入JDBC驱动包
2 通过DriverManager注册驱动
3 创建连接
4 创建Statement
5 CRUD
6 操作结果集
7 关闭连接
```

jdbc的标准操作：

```java
package cn.mosgirl.multipartState;

import java.sql.*;

/**
 * @author hzc
 * @date 2020/6/4 10:25
 */
public class Test {

    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("类加载有问题，无法成功加载com.mysql.cj.jdbc.Driver");
            e.printStackTrace();
        }
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;
        try {
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/test");
            statement = connection.createStatement();
            resultSet = statement.executeQuery("select * from user");
            while (resultSet.next()) {
                int id = resultSet.getByte(1);
                String name = resultSet.getString(2);
//                再将id和name这两个值封装到对象中
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (resultSet != null) {
                try {
                    resultSet.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (statement != null) {
                try {
                    resultSet.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (connection != null) {
                try {
                    resultSet.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}
```

FactoryBean.java的讲解

```java
package cn.mosgirl.multipartState;


import org.springframework.beans.factory.FactoryBean;
import org.springframework.stereotype.Component;

/**
 * @author hzc
 * @date 2020/6/7 18:49
 */
@Component
public class MyFactoryBean implements FactoryBean {
    @Override
    public Object getObject() throws Exception {
//        spring容器会将这个方法返回的对象注入到容器中
        return null;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }
}

```

Appconfig.java

```java
配置dataSource和SqlSessionFactoryBean这两个类
@Bean
public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) throws Exception{
	SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
	factoryBean.setDataSource(dataSource);
	return factoryBean;
}
@Bean
public DataSource dataSource(){
	DriverManagerDataSource driverManagerDataSource = new DriverManagerDataSource();
	driverManagerDataSource.setDrvierClassName("com.mysql.jdbc.Driver");
	dri verManagerDataSource.serUsername("root");
	driverManagerDataSource.setPassword("root");
	driverManagerDataSource.setUrl("jdbc:mysql://localhost:3306/test?userUnicode=true");
	return driverManagerDataSource;
}
```

![1593159097987](E:\Typora_storage\images\1593159097987.png)

## 第三章 spring-mybatis

### 采用xml的形式使用mybatis

dao层

cn.mosgirl.mybatis.TestInteface.class

```java
package cn.mosgirl.mybatis;

import cn.mosgirl.mybatis.pojo.User;
import org.apache.ibatis.annotations.Select;

import java.util.List;

/**
 * @author hzc
 * @date 2020/6/14 21:39
 */
public interface TestInteface {
    List<User> findAll();
}

```

SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="mysql">
        <environment id="mysql">
            <!-- 配置事务 -->
            <transactionManager type="JDBC"></transactionManager>
            <!-- 配置数据源 -->
            <dataSource type="POOLED">
                <!-- 配置连接数据库的四个基本参数 -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="huangzichao520"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!-- 配置映射文件 -->
        <mapper resource="cn/mosgirl/mybatis/TestInteface.xml"></mapper>
        <!--        <mapper class="cn.mosgirl.mybatis.TestInteface"></mapper>-->
    </mappers>


</configuration>
```

cn/mosgirl/mybatis/TestInteface.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.mosgirl.mybatis.TestInteface">
    <select id="findAll" resultType="cn.mosgirl.mybatis.pojo.User">
        select * from user
    </select>
</mapper>
```

### 采用注解的形式使用mybatis

cn.mosgirl.mybatis.TestInteface.class

```java
package cn.mosgirl.mybatis;

import cn.mosgirl.mybatis.pojo.User;
import org.apache.ibatis.annotations.Select;

import java.util.List;

/**
 * @author hzc
 * @date 2020/6/14 21:39
 */
public interface TestInteface {

    @Select("select * from user")
    List<User> findAll();
}

```

SqlMapConfig.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>

<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="mysql">
        <environment id="mysql">
            <!-- 配置事务 -->
            <transactionManager type="JDBC"></transactionManager>
            <!-- 配置数据源 -->
            <dataSource type="POOLED">
                <!-- 配置连接数据库的四个基本参数 -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="huangzichao520"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!-- 配置映射文件 -->
<!--        <mapper resource="cn/mosgirl/mybatis/TestInteface.xml"></mapper>-->
                <mapper class="cn.mosgirl.mybatis.TestInteface"></mapper>
    </mappers>


</configuration>
```

### spring与mybatis整合

#### 采用注解的方式

cn.mosgirl.dao.UserMapper.class

```java
package cn.mosgirl.dao;

import cn.mosgirl.mybatis.pojo.User;
import org.apache.ibatis.annotations.Select;

import java.util.List;

/**
 * @author hzc
 * @date 2020/7/4 16:26
 */
public interface UserMapper {
    @Select("select * from user")
    List<User> findAll();
}

```

cn.mosgirl.mybatis.pojo.User.class

```java
package cn.mosgirl.mybatis.pojo;

/**
 * @author hzc
 * @date 2020/6/14 21:39
 */
public class User {
    private Integer id;
    private String name;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}
```

web.xml

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app xmlns="http://java.sun.com/dtd/web-app_2_3.dtd">

    <display-name>myWebAppName</display-name>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

springMVC.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <mvc:default-servlet-handler/>
    <!-- 注解驱动 -->
    <mvc:annotation-driven conversion-service="conversionService"/>
    <!--    配置ConversionService -->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <!--  自定义数据类型转换器，这个类同时需要放入到spring的容器中 -->
                <bean class="cn.mosgirl.converters.UserConverter"></bean>
            </set>
        </property>
    </bean>
    <context:component-scan base-package="cn.mosgirl.controller"/>

    <!-- 配置视图解析器:把handler方法返回值解析为实际的物理视图 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
<!--    配置文件上传所需要的bean  -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <property name="defaultEncoding" value="UTF-8"></property>
        <property name="maxUploadSize" value="5242880"></property>
    </bean>
</beans>
```

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd http://mybatis.org/schema/mybatis-spring http://mybatis.org/schema/mybatis-spring.xsd">
    <context:component-scan base-package="cn.mosgirl.service"/>
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="configLocation" value="classpath:mybatis/SqlMapConfig.xml"></property>
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <!-- 配置连接数据库的四个基本参数 -->
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/test?serverTimezone=UTC"/>
        <property name="username" value="root"/>
        <property name="password" value="huangzichao520"/>
    </bean>
    <--使用这个扫描注解或者其下面的方式[共三种方式皆可以起到扫描dao类的作用]--!>
	<mybatis-spring:scan base-package="cn.mosgirl.dao"></mybatis-spring:scan>
    <!--    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">-->
    <!--        <property name="basePackage" value="cn.mosgirl.dao"/>-->
    <!--    </bean>-->
    <!--    <bean class="org.mybatis.spring.mapper.MapperFactoryBean">-->
    <!--        <property name="mapperInterface" value="cn.mosgirl.dao.UserMapper"/>-->
    <!--        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>-->
    <!--    </bean>-->
    
</beans>
```

mybatis/SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
</configuration>
```

#### 采用xml的方式

相对与注解方式的改动，主要是需要指定mapper.xml文件的位置,指定的方式主要有三种[同时将dao层的mapper类的方法上的注解去掉]:

一：在SqlMapConfig.xml文件中

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <mappers>
           <mapper resource="cn.mosgirl.dao/UserMapper.xml"></mapper>
    </mappers>


</configuration>
```

二:在applicationContext.xml文件中的SqlSessionFactoryBean.class，添加mapperLocations属性

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd http://mybatis.org/schema/mybatis-spring http://mybatis.org/schema/mybatis-spring.xsd">
    <context:component-scan base-package="cn.mosgirl.service"/>
    <!--    <context:annotation-config/>-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="configLocation" value="classpath:mybatis/SqlMapConfig.xml"></property>
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <!-- 配置连接数据库的四个基本参数 -->
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/test?serverTimezone=UTC"/>
        <property name="username" value="root"/>
        <property name="password" value="huangzichao520"/>
    </bean>
</beans>
```

完整代码如下:

cn.mosgirl.dao.UserMapper.class

```java
package cn.mosgirl.dao;

import cn.mosgirl.mybatis.pojo.User;

import java.util.List;

/**
 * @author hzc
 * @date 2020/7/4 16:26
 */
public interface UserMapper {
//    @Select("select * from user")
    List<User> findAll();
}

```

cn.mosgirl.mybatis.pojo.User.class

```java
package cn.mosgirl.mybatis.pojo;

/**
 * @author hzc
 * @date 2020/6/14 21:39
 */
public class User {
    private Integer id;
    private String name;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}
```

cn.mosgirl.dao/UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.mosgirl.dao.UserMapper">
    <select id="findAll" resultType="cn.mosgirl.mybatis.pojo.User">
        select * from user
    </select>
<!--    cn.mosgirl.dao.UserMapper-->
</mapper>
```

mybatis/SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <mappers>
                <mapper resource="cn.mosgirl.dao/UserMapper.xml"></mapper>
    </mappers>


</configuration>
```

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd http://mybatis.org/schema/mybatis-spring http://mybatis.org/schema/mybatis-spring.xsd">
    <context:component-scan base-package="cn.mosgirl.service"/>
    <!--    <context:annotation-config/>-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="configLocation" value="classpath:mybatis/SqlMapConfig.xml"></property>
        <property name="dataSource" ref="dataSource"></property>
<!--        <property name="mapperLocations" value="classpath:cn.mosgirl.dao/*.xml"></property>-->
        <!--                <property name="mapperLocations" value="classpath:cn/mosgirl/mybatis/UserMapper.xml"></property>-->
        <!--                <property name="mapperLocations" value="classpath:cn.mosgirl.dao/UserMapper.xml"></property>-->
    </bean>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <!-- 配置连接数据库的四个基本参数 -->
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/test?serverTimezone=UTC"/>
        <property name="username" value="root"/>
        <property name="password" value="huangzichao520"/>
    </bean>
    <mybatis-spring:scan base-package="cn.mosgirl.dao"></mybatis-spring:scan>
    <!--    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">-->
    <!--        <property name="basePackage" value="cn.mosgirl.dao"/>-->
    <!--    </bean>-->
    <!--    <bean class="org.mybatis.spring.mapper.MapperFactoryBean">-->
    <!--        <property name="mapperInterface" value="cn.mosgirl.dao.UserMapper"/>-->
    <!--        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>-->
    <!--    </bean>-->
    
</beans>
```

springMVC.xml

```xml
	<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <mvc:default-servlet-handler/>
    <!-- 注解驱动 -->
    <mvc:annotation-driven conversion-service="conversionService"/>
    <!--    配置ConversionService -->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <!--  自定义数据类型转换器，这个类同时需要放入到spring的容器中 -->
                <bean class="cn.mosgirl.converters.UserConverter"></bean>
            </set>
        </property>
    </bean>
    <context:component-scan base-package="cn.mosgirl.controller"/>

    <!-- 配置视图解析器:把handler方法返回值解析为实际的物理视图 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
<!--    配置文件上传所需要的bean  -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <property name="defaultEncoding" value="UTF-8"></property>
        <property name="maxUploadSize" value="5242880"></property>
    </bean>
</beans>
```

