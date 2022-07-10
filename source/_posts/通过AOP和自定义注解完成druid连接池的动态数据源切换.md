---
title: 通过AOP和自定义注解完成druid连接池的动态数据源切换
top: false
cover: false
toc: true
mathjax: true
date: 2021-10-22 09:46:00
password:
summary:
tags: 专业
categories: 语言(Java)
---
通过AOP和自定义注解完成druid连接池的动态数据源切换

---
### 一、引入依赖的相关包pom.xml
```html
<?xml version="1.0" encoding="utf-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.9.RELEASE</version>
    <relativePath/>
    <!-- lookup parent from repository -->
  </parent>
  <groupId>com.rs.cloud.business</groupId>
  <artifactId>cache</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>cache</name>
  <description>Demo project for Spring Boot</description>
  <properties>
    <java.version>1.8</java.version>
  </properties>
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
    <!--druid-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid-spring-boot-starter</artifactId>
      <version>1.1.17</version>
    </dependency>
    <!--aop-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>1.3.2</version>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```
此外如需加入其他依赖包时，可以在pom.xml中的```<dependencies>xxx</dependencies>```中添加相应代码。
在Maven管理中点击Reload All Maven Projects,重启加载项目。
![IDE中刷新Maven项目](https://i.loli.net/2021/10/27/cHDpuL91vBwCqUh.jpg)

### 二、创建数据库
- #### 2.1. 主数据库（Oracle）
分别创建主数据库(spring_boot_demo)和辅数据库(other_data)
具体语法如下：
- ##### 2.1.1. 创建数据表
``` mysql
create table spring_boot_demo(
              id nvarchar2(10),
              username nvarchar2(39),
              password nvarchar2(20),
              age nvarchar2(11),
              sex nvarchar2(21))
 ```
- ##### 2.1.2. 数据表中插入数据
``` mysql
insert into sprin VALUES ('11','root','root','18','男')
 ```
- #### 2.2. 辅数据库（mysql）
辅数据库结构与主数据表结构一致，其实非务必一致。此处为了便于操作，与Oracle下部署数据库并且使表结构一致，方便形成数据对比。
- ##### 2.2.1. 创建数据表
``` mysql
CREATE TABLE IF NOT EXISTS `other_data`(
                `id` INT UNSIGNED AUTO_INCREMENT,
                `username` VARCHAR(100) NOT NULL,
                `password` VARCHAR(40) NOT NULL,
                `age` VARCHAR(100) NOT NULL,
                `sex` VARCHAR(100) NOT NULL,
                PRIMARY KEY ( `id` )
                )ENGINE=InnoDB DEFAULT CHARSET=utf8;
 ```
- ##### 2.2.2. 数据表中插入数据
``` mysql
insert into other_data VALUES ('11','root','root','18','男')
 ```
### 三、数据库连接配置文件信息
在application.yml中，修改数据库连接配置如下：
```yml
server:
  port: 9999
  servlet:
    context-path: /cn
spring:
  profiles: test
  application:
    name: spring-boot-demo
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      primary:
        url: jdbc:oracle:thin:@192.168.168.105:1521:ZLORANEW
        username: root
        password: root
        driverClassName: oracle.jdbc.OracleDriver
      second:
        url: jdbc:mysql://localhost:3306/test?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
        username: root
        password: root
        driverClassName: com.mysql.jdbc.Driver
  cloud:
    nacos:
      config:
        server-addr: 192.168.200.113:8848
#在application.yml中添加配置mapper.xml文件的路径：
mybatis:
  mapper-locations: classpath:mapper/*.xml   # 扫描路径
 ```
 ### 四、编写代码

- #### 4.1. 代码的整个结构
![整个代码结构说明](https://i.loli.net/2021/10/26/bNpErUD8ijvsAx2.png)
- #### 4.2. 数据库访问层
- ##### 4.2.1. 类UserPO.java
``` java
package com.rs.cloud.business.cache.po;
import java.io.Serializable;
/**
 * @author shajiu
 * @date 2021/10/22 10:16
 * 描述：User类implements了一个Serializable类，该类的作用是序列化。
 */
public class UserPO implements Serializable {
    /**
     * 用户ID
     */
    private int id;
    /**
     * 用户名
     */
    private String username;
    /**
     * 用户密码
     */
    private String password;
    /**
     * 年龄
     */
    private int age;
    /**
     * 性别
     */
    private String sex;
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public String getPassword() {
        return password;
    }
    public void setPassword(String password) {
        this.password = password;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public String getSex() {
        return sex;
    }
    public void setSex(String sex){this.sex=sex;}
}
 ```
 以上User类implements了一个Serializable类，此类的作用是序列化。
- ##### 4.2.2. 接口UserMapper.java
```java
package com.rs.cloud.business.cache.mapper;
import org.apache.ibatis.annotations.Param;
import org.springframework.stereotype.Repository;
import com.rs.cloud.business.cache.po.UserPO;
import java.util.List;
/**
 * @author shajiu
 * @date 2021/10/22 10:22
 * UserMapper接口  这里的函数名称跟xml中的一致
 */
@Repository  //@Repository注解是把这个接口的一个实现类交给spring管理
public interface UserMapper {
    int getAgeByUsername(@Param("username")String username);
    List<UserPO> getNameByUsername(@Param("UserPO")UserPO userpo);
}
 ```
- ##### 4.2.3. 配置文件 UserMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--        mapper的namespace指定了该xml文件指向的Mapper接口，里面的sql语句接受传来的username数据进行数据访问。-->
<!--        语句的id="getAgeByUsername"则是对应mapper接口中的方法，resultType="int"指定本次数据访问的数据返回类型。-->
<mapper namespace="com.rs.cloud.business.cache.mapper.UserMapper">
    <resultMap id="ResultMap" type="com.rs.cloud.business.cache.po.UserPO">
            <result column="ID" jdbcType="VARCHAR" property="id"/>
            <result column="USERNAME" jdbcType="VARCHAR" property="username"/>
            <result column="PASSWORD" jdbcType="VARCHAR" property="password" />
            <result column="AGE" jdbcType="VARCHAR" property="age"/>
            <result column="SEX" jdbcType="VARCHAR" property="sex"/>
    </resultMap>
    <sql id="Base_Column_List">
        ID,USERNAME,PASSWORD,AGE,SEX
    </sql>
    <select id="getAgeByUsername" resultType="int">
        SELECT age FROM spring_boot_demo  WHERE username='${username}'
    </select>
    <select id="getNameByUsername" parameterType="com.rs.cloud.business.cache.po.UserPO" resultMap="ResultMap">
        SELECT * from ${UserPO.username}
    </select>
</mapper>
 ```
详细说明已在脚本中注释，对应的在application.yml中添加配置mapper.xml文件的路径：
```yml
mybatis:
  mapper-locations: classpath:mapper/*.xml   # 扫描路径
 ```
- ##### 4.2.4. 在程序入口类中添加扫描CacheApplication.java
```java
@MapperScan("com.rs.cloud.business.cache.mapper")
 ```
- #### 4.3. 业务逻辑
代码结构如下：
![业务层结构](https://i.loli.net/2021/10/27/D1H9OsPzREqiavW.png)
- ##### 4.3.1. UserService.java类
``` java
package com.rs.cloud.business.cache.service;
import com.rs.cloud.business.cache.po.UserPO;
import org.apache.ibatis.annotations.Param;
import java.util.List;
/**
 * @author shajiu
 * @date 2021/10/22 10:33
 */
public interface UserService {
    int getAgeByUsername(String username);
    List<UserPO> getNameByUsername(UserPO userpo);
}
 ```
- ##### 4.3.2. UserServiceImpl.java类
```java
package com.rs.cloud.business.cache.service.impl;
import com.rs.cloud.business.cache.mapper.UserMapper;
import com.rs.cloud.business.cache.service.UserService;
import org.apache.ibatis.annotations.Param;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.rs.cloud.business.cache.po.UserPO;
import java.util.List;
/**
 * @author shajiu
 * @date 2021/10/22 10:35
 */
//在service接口的实现类中，要加上@Service注解，把实现类交给spring处理
@Service
public class UserServiceImpl implements UserService {
    //通过@Autowired注解获得自动注入的userMapper实现类，在重写的方法中进行调用，获得数据。
    @Autowired
    UserMapper userMapper;
    @Override
    public int getAgeByUsername(String username) {
        return userMapper.getAgeByUsername(username);
    }
    @Override
    public List<UserPO> getNameByUsername(UserPO userpo) {
        return userMapper.getNameByUsername(userpo);
    }
}
 ```
- #### 4.4. 页面展示层
-  ##### 4.4.1. UserController.java类
```java
package com.rs.cloud.business.cache.controller;
import com.rs.cloud.business.cache.annotation.DataSource;
import com.rs.cloud.business.cache.enums.DataSourceName;
import com.rs.cloud.business.cache.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import com.rs.cloud.business.cache.po.UserPO;
/**
 * @author shajiu
 * @date 2021/10/22 10:37
 * 按照配置的端口和映射URL，页面的路径应该是http://localhost:9999/cn/user/age
 * 结果与我们预存在数据库中的数据一致。函数名称跟xml中的一致
 * https://www.cnblogs.com/guobin-/p/13696900.html
 */
@RestController
public class UserController {
    @Autowired
    UserService userService;
    @RequestMapping("/user/age")
    //@DataSource(DataSourceName.SECOND)
    public int getAgeOfuser(){
        return userService.getAgeByUsername("沙九");
    }
    /**
     * 全部查询
     * @param userpo
     * @return
     */
    @PostMapping(value = "/createTable")
    //@DataSource(DataSourceName.SECOND)
    public Object getNameOfuser(@RequestBody UserPO userpo){
        String username=userpo.getUsername();
        System.out.printf("传入的参数:\t"+username+"\n");
        return userService.getNameByUsername(userpo);
    }
}
 ```
- #### 4.5. 枚举类DataSourceName.java
此类用于存储数据源名称，并且定义两个数据源名称分别为PRIMARY和SECOND
```Java
package com.rs.cloud.business.cache.enums;
/**
 * @author shajiu
 * @date 2021/10/21 13:30
 * 功能：枚举类DataSourceName便于切换
 * 此类用来存放数据源的名称，定义两个数据源名称分别为PRIMARY和SECOND。
 */
public enum DataSourceName {
    /**
     * 主数据源 spring_boot_demo
     */
    PRIMARY("PRIMARY"),
    /**
     * 副数据源other_data
     */
    SECOND("PRIMARY");
    private String dataSourceName;
    private DataSourceName(String dataSourceName) {
        this.dataSourceName = dataSourceName;
    }
    DataSourceName() {
    }
    public String getDataSourceName() {
        return this.dataSourceName;
    }
}
 ```
- #### 4.6. 配置类DynamicDataSourceConfig.Java
```Java
package com.rs.cloud.business.cache.config;
import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.spring.boot.autoconfigure.DruidDataSourceBuilder;
import com.rs.cloud.business.cache.bean.DynamicDataSource;
import com.rs.cloud.business.cache.enums.DataSourceName;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import java.util.HashMap;
import java.util.Map;
/**
 * @author shajiu
 * @date 2021/10/21 13:30
 */
@Configuration
public class DynamicDataSourceConfig {
    /**
     * 创建DataSource,将数据源配置从文件中读取
     */
    @Bean
    @ConfigurationProperties("spring.datasource.druid.primary")
    public DruidDataSource oneDataSource(){
        return DruidDataSourceBuilder.create().build();
    }
    @Bean
    @ConfigurationProperties("spring.datasource.druid.second")
    public DruidDataSource twoDataSource(){
        return DruidDataSourceBuilder.create().build();
    }
    /**
     * 将数据源放入到这个map中，注入到Ioc
     */
    @Bean
    @Primary
    public DynamicDataSource dataSource(DruidDataSource oneDataSource,DruidDataSource twoDataSource){
        Map<Object,Object> targetDataSources=new HashMap<>(2);
        targetDataSources.put(DataSourceName.PRIMARY.getDataSourceName(),oneDataSource);
        targetDataSources.put(DataSourceName.SECOND.getDataSourceName(),twoDataSource);
        return new DynamicDataSource(oneDataSource,targetDataSources);
    }
}
 ```
- #### 4.7. 动态数据源DynamicDataSource.Java
此类通过继承AbstractRoutingDataSource类，在构造函数中调用父类的方法，将配置类中放入map的数据源集合定为备选数据源，将传来的oneDataSource作为默认数据源。
```Java
package com.rs.cloud.business.cache.bean;
import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.jdbc.datasource.lookup.AbstractRoutingDataSource;
import java.util.Map;
/**
 * @author shajiu
 * @date 2021/10/21 13:29
 * 描述：通过继承AbstractRoutingDataSource类，在构造函数中调用父类的方法，将配置类
 *      中放入map的数据源集合定为备选数据源，将传来的oneDataSource作为默认数据源
 */
public class DynamicDataSource extends AbstractRoutingDataSource {
    private static final ThreadLocal<String> contextHolder=new ThreadLocal<>();
    /**
     * 配置DatsSource
     * 设置defaultTargetDataSource为主数据库
     * @param targetDataSources
     */
    public DynamicDataSource(DruidDataSource defaultTargetDataSource, Map<Object, Object> targetDataSources) {
        // 设置默认数据源
        super.setDefaultTargetDataSource(defaultTargetDataSource);
        // 设置备选的数据源集合
        super.setTargetDataSources(targetDataSources);
        super.afterPropertiesSet();
    }
    public static String getDataSource(){
        return contextHolder.get();
    }
    public static void setDataSource(String dataSource){
        contextHolder.set(dataSource);
    }
    public static void clearDataSource(){
        contextHolder.remove();
    }
    @Override
    protected Object determineCurrentLookupKey() {
        // 决定当前数据源对应的key
        return getDataSource();
    }
}
 ```
- #### 4.8. 自定义注释类DataSource.Java
```Java
package com.rs.cloud.business.cache.annotation;
import com.rs.cloud.business.cache.enums.DataSourceName;
import java.lang.annotation.*;
/**
 * @author shajiu
 * @date 2021/10/21 13:29
 * 描述：自定义注释类DataSource：
 */
@Documented                             //指定被标注的注解会包含在javadoc中
@Target(ElementType.METHOD)             //指定注释可能出现在Java程序中的语法位置（ElementType.METHOD则说明注解可能出现在方法上）
@Retention(RetentionPolicy.RUNTIME)     //指定注释的保留时间（RetentionPolicy.RUNTIME则是在java文件编译成class类时也依旧保存该注释）
public @interface DataSource {
    DataSourceName value() default DataSourceName.PRIMARY;
}
 ```
- #### 4.9.  切面类DataSourceAspect.Java
```Java
package com.rs.cloud.business.cache.aspect;
import com.rs.cloud.business.cache.annotation.DataSource;
import com.rs.cloud.business.cache.bean.DynamicDataSource;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.core.Ordered;
import org.springframework.stereotype.Component;
import java.lang.reflect.Method;
/**
 * @author shajiu
 * @date 2021/10/21 13:29
 * 描述：Spring框架有很多相同接口的实现类，提供了Ordered接口来处理相同接口实现类之间的优先级问题。
 * 通过环绕切面，对方法上的注释进行了检验，如果获取到有DataSource注释，则会进行数据源的切换，否则按默认数据源进行处理。
 */
@Aspect
@Component
public class DataSourceAspect implements Ordered {
    //初始化LoggerFactory对象 , CaseAutoPullController是日志所属的类
    private final static Logger log = LoggerFactory.getLogger(DataSourceAspect.class);
    /**
     * 切点：所有配置DataSource注解的方法
     */
    @Pointcut("@annotation(com.rs.cloud.business.cache.annotation.DataSource)")
    public void dataSourcePointCut() {
    }
    @Around(value = "dataSourcePointCut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        Object result;
        MethodSignature signature = (MethodSignature) point.getSignature();
        Method method = signature.getMethod();
        DataSource ds = method.getAnnotation(DataSource.class);
        /**
         * 判断DataSource的值
         * 获取当前方法应用的数据源
         */
        DynamicDataSource.setDataSource(ds.value().getDataSourceName());
        try {
            result = point.proceed();
        } finally {
            DynamicDataSource.clearDataSource();
        }
        return result;
    }
    @Override
    public int getOrder() {
        return 1;
    }
}
 ```
 Spring框架有很多相同接口的实现类，提供了Ordered接口来处理相同接口实现类之间的优先级问题。通过环绕切面，对方法上的注释进行了检验，如果获取到有DataSource注释，则会进行数据源的切换，否则按默认数据源进行处理。
- #### 4.10. 引入配置类CacheApplication.java
```Java
package com.rs.cloud.business.cache;
import com.rs.cloud.business.cache.config.DynamicDataSourceConfig;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.context.annotation.Import;
//  在程序入口类中添加扫描,添加该注解后，运行程序时会自动扫描指定路径中的mapper接口实现类。
//  @EnableAspectJAutoProxy用来开启AOP
@MapperScan("com.rs.cloud.business.cache.mapper")
@Import({DynamicDataSourceConfig.class})
@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
@EnableAspectJAutoProxy
public class CacheApplication {
	public static void main(String[] args) {
		SpringApplication.run(CacheApplication.class, args);
	}
}
 ```
 ### 五、测试阶段
- #### 5.1. 不使用注解
``` Java
package com.rs.cloud.business.cache.controller;
import com.rs.cloud.business.cache.annotation.DataSource;
import com.rs.cloud.business.cache.enums.DataSourceName;
import com.rs.cloud.business.cache.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import com.rs.cloud.business.cache.po.UserPO;
/**
 * @author shajiu
 * @date 2021/10/22 10:37
 * 按照配置的端口和映射URL，页面的路径应该是http://localhost:9999/cn/user/age
 * 结果与我们预存在数据库中的数据一致。函数名称跟xml中的一致
 */
@RestController
public class UserController {
    @Autowired
    UserService userService;
    @RequestMapping("/user/age")
    public int getAgeOfuser(){
        return userService.getAgeByUsername("shajiu");
    }
    /**
     * 全部查询
     * @param userpo
     * @return
     */
    @PostMapping(value = "/createTable")
    public Object getNameOfuser(@RequestBody UserPO userpo){
        String username=userpo.getUsername();
        System.out.printf("传入的参数:\t"+username+"\n");
        return userService.getNameByUsername(userpo);
    }
}
 ```
- #### 5.2. 访问链接：192.168.2.186:9999/cn/user/age
   以上结果返回“19”，以上结果从Oracle数据源的spring_boot_demo数据表中读取。
- #### 5.3. 数据表内容如下
``` mysql
  12	shajiu	root	19	男
 ```
- #### 5.4. 注解访问链接
在方法前添加注解@DataSource(DataSourceName.SECOND)，具体代码如下：
```Java
package com.rs.cloud.business.cache.controller;
import com.rs.cloud.business.cache.annotation.DataSource;
import com.rs.cloud.business.cache.enums.DataSourceName;
import com.rs.cloud.business.cache.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import com.rs.cloud.business.cache.po.UserPO;
/**
 * @author shajiu
 * @date 2021/10/22 10:37
 * 按照配置的端口和映射URL，页面的路径应该是http://localhost:9999/cn/user/age
 * 结果与我们预存在数据库中的数据一致。函数名称跟xml中的一致
 */
@RestController
public class UserController {
    @Autowired
    UserService userService;
    @RequestMapping("/user/age")
    @DataSource(DataSourceName.SECOND)
    public int getAgeOfuser(){
        return userService.getAgeByUsername("root");
    }
    /**
     * 全部查询
     * @param userpo
     * @return
     */
    @PostMapping(value = "/createTable")
    //@DataSource(DataSourceName.SECOND)
    public Object getNameOfuser(@RequestBody UserPO userpo){
        String username=userpo.getUsername();
        System.out.printf("传入的参数:\t"+username+"\n");
        return userService.getNameByUsername(userpo);
    }
}
 ```
以上结果返回“18”，以上结果从Mysql数据源的other_data数据表中读取。
