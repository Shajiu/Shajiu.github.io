---
title: mysql的基本语法格式汇总
top: false
cover: false
toc: true
mathjax: true
date: 2021-11-26 13:39:45
password:
summary:
tags: 专业
categories: 语言(mysql)
---
#### Mysql 的基本语法格式汇总
##### 1、Mysql 先判断是否存在此表，如果不存在则创建此表:字段、字段类型、字段长度、字段描述、表描述、表编码、多列唯一索引。
``` Mysql
CREATE TABLE
IF
	NOT EXISTS WEWWWW (
		id INT ( 11 ) COMMENT '用户id',
		INDEX ( id ),
		FIE_NAME VARCHAR ( 25 ) COMMENT '字段名称',
		FIE_DES VARCHAR ( 25 ) COMMENT '字段描述',
		FIE_TYPE VARCHAR ( 25 ) COMMENT '字段类型',
		FIE_LEN INT ( 100 ) COMMENT '字段长度',
		IND_FLAG VARCHAR ( 25 ) COMMENT '是否为索引',
		TAB_NAME VARCHAR ( 25 ) COMMENT '表名',
		UNIQUE INDEX ( TAB_NAME, FIE_NAME, IND_FLAG ),
	TAB_DES VARCHAR ( 11 ) COMMENT '表描述'
	) COMMENT = '测试表头信息' CHARSET = utf8
  ```
##### 2、Mysql 查询表格。
 ``` Mysql
SELECT * FROM DEV_TAB_D
  ```

##### 3、Mysql 删除表格。
``` Mysql
 DROP TABLE EDM_COM
 ```

 ##### 4、Mysql插入数据。
 ``` Mysql
 INSERT INTO DEV_TAB_H ( id, TAB_NAME,TAB_DES,FIE_NAME,FIE_DES,FIE_TYPE,FIE_LEN,IND_FLAG)
                       VALUES
                       ( '4', 'EDM_COM','产品物料清单格','field4','任务数量4','INT',25,'Y');
 ```
##### 5、获取所有表结构TABLES。
``` Mysql
SELECT  *  FROM information_schema.TABLES WHERE  TABLE_SCHEMA='数据库名';
 ```
