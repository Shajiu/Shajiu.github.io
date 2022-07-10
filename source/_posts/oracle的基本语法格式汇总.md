---
title: Oracle的基本语法格式汇总
top: false
cover: false
toc: true
mathjax: true
date: 2021-11-16 15:54:00
password:
summary:
tags: 专业
categories:  语言(oracle)
---
最近由于工作原因不得不接触关于不同数据库的操作语法，为此本人将相关语法进行汇总，便于后续工作中使用以及他人学习，有需要的直接复制粘贴即可使用。

##### 基于Oracle的语法
- Oracle查询
```Oracle
 SELECT * FROM 数据表 WHERE 关联字段='具体的值'    --例如：SELECT * FROM DEV_DBS WHERE USR_NAME='dev'
 ```

- Oracle 删除数据表
``` Oracle
    DROP TABLE 表名  -- 例如   DROP TABLE PAC_ORDER
 ```


- Oracle创建数据表
``` Oracle
CREATE TABLE 表名 ( 字段 类型 (大小 ), 字段 类型 ( 大小 ) )  --例如:CREATE TABLE TABLE_TEST ( ORD_QTY NUMBER ( 20 ), DEP_CODE nvarchar2 ( 5 ) )
 ```

- Oracle添加字段注解
``` Oracle
COMMENT ON COLUMN 表名.字段 IS 注释'  --例如: COMMENT ON COLUMN TABLE_TEST.ORD_QTY IS '测试'
 ```
- Oracle添加表的描述
```Oracle
COMMENT ON TABLE 表名 IS '表描述'   --例如: COMMENT ON TABLE TABLE_TEST IS '个人信息'
 ```
- Oracle添加唯一索引
```Oracle
CREATE UNIQUE INDEX 设定的索引名 ON 表名 ( 字段1,字段2)  --例如: CREATE UNIQUE INDEX IDX_PROCESSVERSION_PROCID ON TABLE_TEST ( ORD_QTY,DEP_CODE)
 ```
-Oracle 判断是否存在数据表
```Oracle
SELECT COUNT(*) FROM  USER_TABLES WHERE TABLE_NAME =UPPER('表名')   --例如:
SELECT COUNT(*) FROM  USER_TABLES WHERE TABLE_NAME =UPPER('PAC_ORDER')
 ```
- Oracle删除表中的字段
``` Oracle
ALTER TABLE 表名 DROP (字段)  --例如: ALTER TABLE PAC_ORDER DROP (PON)
```
- Oracle 修改某个字段的值
```Oracle
UPDATE 表名 SET 待修改字段 = '具体的值' WHERE 条件字段='值'  //UPDATE DEV_DBS SET NAME = 'test' WHERE USR_NAME='dev'
```
- 新增字段并赋予默认值
``` Oracle
ALTER TABLE   表名
ADD (字段 类型(长度) default '默认值' not null) //例如：alter table DBS_TYPE
add (NAME varchar2(30) default '2' not null);
```
- Oracle修改某个字段的值
``` Oracle
Alter table 表名 rename column 原字段名称 to 新字段名称  // 例如：alter table cxj rename column DEP_NAME to NEW_DEP_NAME  将数据表cxj中字典DEP_NAME更改为NEW_DEP_NAME
```

- 更新Oracle中的字段属性
``` Oracle
Alter table 表名 modify (字段 字段类型(长度)); //例如：Alter table cxj modify (DEP_NAME NVARCHAR2(5));
```
- 获取Oracle数据表中字段的属性值
``` Oracle
select 列名,类型,长度,DATA_PRECISION ,DATA_SCALE from all_tab_columns  
where table_name=upper('表名') AND COLUMN_NAME='字段名称' //  select COLUMN_NAME,data_type,data_length,DATA_PRECISION ,DATA_SCALE from all_tab_columns  
where table_name=upper('SHA') AND COLUMN_NAME='DEP_CODE'
 ```