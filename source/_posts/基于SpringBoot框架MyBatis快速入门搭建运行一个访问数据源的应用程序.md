---
title: 基于SpringBoot框架MyBatis快速入门搭建运行一个访问数据源的应用程序
top: false
cover: false
toc: true
mathjax: true
date: 2021-11-05 15:12:33
password:
summary:
tags:  专业
categories: 语言(MyBatis)
---
### 一、环境配置
- Java环境：1.8版本
- 开发工具： Intellij IDEA

### 二 简单应用
* 1.创建项目
![IDEA中创建maven](https://i.loli.net/2021/11/05/TCJ7HIpRZlKh1Wm.png)
* 2.填写项目名称和项目存放路径，点击Finish:
![IDEA中写maven的项目名称和路径](https://i.loli.net/2021/11/05/T5kL4hXEjBIWxc2.png)
* 3.进入项目界面如下(因为本人已经建立好了，所以显示有对应文件)：
![初始化项目文件](https://i.loli.net/2021/11/05/y19N8WqOdIQMenx.png)
### 三、熟悉的HelloWorld
- 先在com.springboot下新建一个Package，命名为controller，再在controller下创建一个HelloWorldController类，如下：
```Java
package com.springboot.controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
/**
 * @author shajiu
 * @date 2021/11/5 13:48
 */
@RestController
public class HelloWorldController {
    @GetMapping("/hello")
    public String sayHelloWorld() {
        System.out.println("测试段!!!");
        return "HelloWorld";
    }
}
```
- 注释@RequestMapping(value = "/hello")是指定映射的URL，@RestController是将它标注的类中的所有方法返回的json格式的数据转化成视图view。
- 运行程序，在DemoApplication.java文件中，运行程序：
``` Java
package com.springboot;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
/**
 * @author shajiu
 * @date 2021/11/5 13:49
 */
@SpringBootApplication
@MapperScan("com.springboot.mapper")
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
 ```
 ![运行的结果](https://i.loli.net/2021/11/05/iAc9a57nFeNMWvr.png)
### 四、使用mybatis连接mysql数据库完成数据访问。
话不多说直接上代码，具体解释其对应代码中描述。 代码框架结构如下：
![Demo代码的框架结构图](https://i.loli.net/2021/11/05/q5X1Z2MQ6KCYxEl.png)
#####  1、在pom.xml文件中添加依赖，直接用如下替换之前的即可：
``` xml
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
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.4</version>
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
##### 2、修改后缀名为application.yml，直接用如下替换即可
``` yml
server:
  port: 1234
  servlet:
    context-path: /cn
spring:
  datasource:
    url: jdbc:oracle:thin:@192.168.168.105:1521:ZLORANEW
    username: devnew
    password: devpass
    driver-class-name: oracle.jdbc.OracleDriver
mybatis:
  mapper-locations:
    - classpath*:mapper/*.xml
 ```
##### 3、数据访问层
 - 3.1. UserPO类
 ```java
  package com.springboot.po;
  import java.io.Serializable;
  /**
   * @author shajiu
   * @date 2021/11/5 13:34
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
      public void setSex(String sex) {
          this.sex = sex;
      }
  }
 ```
 - 3.2. User类implements了一个Serializable类，该类的作用是序列化。
 - 3.2.1. UserMapper接口:
``` java
  package com.springboot.mapper;
  import org.apache.ibatis.annotations.Mapper;
  import org.springframework.stereotype.Repository;
  /**
   * @author shajiu
   * @date 2021/11/5 13:35
   */
  @Repository
  public interface UserMapper {
      int getAgeByUsername(String username);
  }
 ```
 - 3.2.2. UserMapper.xml配置文件：
 ``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--        mapper的namespace指定了该xml文件指向的Mapper接口，里面的sql语句接受传来的username数据进行数据访问。-->
<!--        语句的id="getAgeByUsername"则是对应mapper接口中的方法，resultType="int"指定本次数据访问的数据返回类型。-->
<mapper namespace="com.springboot.mapper.UserMapper">
    <resultMap id="ResultMap" type="com.springboot.po.UserPO">
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
        SELECT age FROM spring_boot_demo
    </select>
<!--    <select id="getNameByUsername" parameterType="com.springboot.po.UserPO" resultMap="ResultMap">-->
<!--        SELECT * from ${UserPO.username}-->
<!--    </select>-->
</mapper>
 ```
#####  4、业务逻辑层
  - 4.1、UserService类
 ```java
    package com.springboot.service;
   import javax.annotation.Resource;
    /**
     * @author shajiu
     * @date 2021/11/5 13:39
     */
    @Resource
    public interface UserService {
        int getAgeByUsername(String username);
    }
 ```
  - 4.2、UserServiceImpl类
    ``` java
    package com.springboot.service.impl;
    import com.springboot.mapper.UserMapper;
    import com.springboot.service.UserService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    /**
     * @author shajiu
     * @date 2021/11/5 13:47
     */
    @Service
    public class UserServiceImpl implements UserService {
        @Autowired
        //通过@Autowired注解获得自动注入的userMapper实现类，在重写的方法中进行调用，获得数据。
        UserMapper userMapper;
        @Override
        public int getAgeByUsername(String username) {
            return userMapper.getAgeByUsername(username);
        }
    }
     ```
#### 5、页面展示层
   - UserController类
    ```java
    package com.springboot.controller;
    import com.springboot.service.UserService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    /**
     * @author shajiu
     * @date 2021/11/5 13:48
     */
    @RestController
    public class UserController {
        @Autowired
        UserService userService;
        @RequestMapping("/user/age")
        public int getAgeOfUser(){
            return userService.getAgeByUsername("springbootdemo");
        }
    }
      ```
#### 6、运行程序
```java
      package com.springboot;
      import org.mybatis.spring.annotation.MapperScan;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      /**
       * @author shajiu
       * @date 2021/11/5 13:49
       */
      @SpringBootApplication
      @MapperScan("com.springboot.mapper")
      public class DemoApplication {
          public static void main(String[] args) {
              SpringApplication.run(DemoApplication.class, args);
          }
      }
  ```
#### 7、测试数据如下，spring_boot_demo：
![spring_boot_demo](https://i.loli.net/2021/11/05/ui3DtwlQp91vboc.png)
#### 8、若需源码，[请点击这里下载即可直接使用SpringBoot](https://github.com/Shajiu/JavaLearn/tree/main/SpringBoot)

 - 推荐一款好玩的东西
 [MyBatis Plus 教程](https://www.hxstrive.com/subject/mybatis_plus.htm?id=256)
