---
title: 构建微服务-Spring boot 入门篇
top: false
cover: false
toc: true
mathjax: true
date: 2021-09-08 14:29:28
password:
summary:
tags: 专业
categories: 语言(SpringBoot)
---
## [构建微服务：Spring boot 入门篇](https://www.cnblogs.com/ityouknow/p/5662753.html)
>## com
  >>* +- example 
  >>>*  +- myproject
  >>>* +- Application.java      
  >>>*  |
  >>>* +- domain
  >>>* |  +- Customer.java
  >>>* |  +- CustomerRepository.java
  >>>* | 
  >>>* +- service
  >>>* |  +- CustomerService.java
  >>>* |
  >>>* +- controller
  >>>* |  +- CustomerController.java
  >>>* |
  >> 1. **Application.java** 建议放到跟目录下面,主要用于做一些框架配置
  >> 2. **domain**目录主要用于实体（Entity）与数据访问层（Repository）
  >> 3. **service** 层主要是业务类代码
  >> 4. **controller** 负责页面访问控制   
  >> 5. 采用默认配置可以省去很多配置，当然也可以根据自己的喜欢来进行更改,最后，启动Application main方法，至此一个java项目搭建好了！       