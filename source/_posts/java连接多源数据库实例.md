---
title: java连接多源数据库实例
top: false
cover: false
toc: true
mathjax: true
date: 2021-10-26 14:44:26
password:
summary:
tags: 专业
categories: 语言(java)
---
##### 使用Java连接多源数据库实例，并有相应的增、删、改、除等操作
- Java连接Oracle实例
```java
package com.rs.cloud.business.cache.test;
/**
 * @author shajiu
 * @date 2021/10/25 16:13
 */
import java.sql.*;
/**
 * 1:向数据库中添加数据
 *
 * @author biexiansheng
 */
public class ControllerOracle {
    public static void main(String[] args) {
        try {
            Class.forName("oracle.jdbc.OracleDriver");//加载数据库驱动
            System.out.println("加载数据库驱动成功");
            String url = "jdbc:oracle:thin:@192.168.168.105:1521:ZLORANEW";//声明数据库test的url
            String user = "root";//数据库的用户名
            String password = "root";//数据库的密码
            //建立数据库连接，获得连接对象conn(抛出异常即可)
            Connection conn = DriverManager.getConnection(url, user, password);
            System.out.println("连接数据库成功"+conn);
            Inquire inquire=new Inquire();
            /**
             * 调用增删改除操作接口如下
             * 分别传入conn和tableName
             */
            inquire.gitInquire(conn,"dev_tab_d");
            conn.close();
            System.out.println("关闭数据库成功");
        } catch (ClassNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }//
        catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
 ```
- Java连接Mysql实例
```java
package com.rs.cloud.business.cache.test;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
/**
 * @author shajiu
 * @date 2021/10/26 14:55
 */
public class ControllerMysql {
    public static void main(String[] args) {
        //MySql8.0以下版本-jdbc驱动名及数据库url
        final String jdbc_driver = "com.mysql.jdbc.Driver";
        final String db_url = "jdbc:mysql://localhost:3306/test?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC";
        final String user = "root";
        final String password = "root";
        Connection conn = null;
        try {
            //注册JDBC驱动
            Class.forName(jdbc_driver);
            //打开链接
            conn = DriverManager.getConnection(db_url, user, password);
            /**
             * 调用增删改除操作接口如下
             * 分别传入conn和tableName
             */
            Inquire inquire=new Inquire();
            inquire.gitInquire(conn,"sys_user");
            System.out.println("Mysql连接成功"+conn);
        } catch (ClassNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }//
        catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
 ```
- 查询接口(Oracle+Mysql)都通用
 ``` java
  package com.rs.cloud.business.cache.test;
  import java.sql.*;
  public class Inquire {
    public static String gitInquire(Connection conn, String tabname){
        try {
            // 执行的语句
            String sql = "select *from " + tabname;
            //创建该连接下的PreparedStatement对象
            PreparedStatement pstmt = conn.prepareStatement(sql);
            //执行查询语句，将数据保存到ResultSet对象中
            ResultSet rset = pstmt.executeQuery();
            ResultSetMetaData rsmd = rset.getMetaData();
            while (rset.next()) {
                for (int i = 1; i < rsmd.getColumnCount(); i++) {
                    ///获取指定列的列值
                    String columnValue = rset.getString(i);
                    System.out.println("获取指定列的列值:\t" + columnValue);
                    //获取指定列的列名
                    String colunmName = rsmd.getColumnName(i);
                    System.out.println("获取指定列的列名:\t" + colunmName);
                    //获得指定列的数据类型
                    int columnType = rsmd.getColumnType(i);
                    System.out.println("获得指定列的数据类型:\t" + columnType);
                    //获取指定列的数据类型名
                    String columnTypeName = rsmd.getColumnTypeName(i);
                    System.out.println("获取指定列的数据类型名:\t" + columnTypeName);
                    //所在的Catalog名字
                    String catalogName = rsmd.getColumnClassName(i);
                    System.out.println("所在的Catalog名字:\t" + catalogName);
                    //对应数据类型的类
                    String columnClassName = rsmd.getColumnClassName(i);
                    System.out.println("对应数据类型的类:\t" + columnClassName);
                    //在数据库中类型的最大字符个数
                    int columnDisplaySize = rsmd.getColumnDisplaySize(i);
                    System.out.println("在数据库中类型的最大字符个数:\t" + columnDisplaySize);
                    //默认的列的标题
                    String columnLabel = rsmd.getColumnLabel(i);
                    System.out.println("默认的列的标题:\t" + columnLabel);
                    //获得列的模式
                    String schemaName = rsmd.getSchemaName(i);
                    System.out.println("获得列的模式:\t" + schemaName);
                    //某列类型的精确度(类型的长度)
                    int precision = rsmd.getPrecision(i);
                    System.out.println("某列类型的精确度(类型的长度):\t" + precision);
                    //小数点后的位数
                    int scale = rsmd.getScale(i);
                    System.out.println("小数点后的位数:\t" + scale);
                    //获取某列对应的表名
                    String tableName = rsmd.getTableName(i);
                    System.out.println("获取某列对应的表名:\t" + tableName);
                    // 是否自动递增
                    boolean isAutoInctement = rsmd.isAutoIncrement(i);
                    System.out.println("是否自动递增:\t" + isAutoInctement);
                    //在数据库中是否为货币型
                    boolean isCurrency = rsmd.isCurrency(i);
                    System.out.println("在数据库中是否为货币型:\t" + isCurrency);
                    //是否为空
                    int isNullable = rsmd.isNullable(i);
                    System.out.println("是否为空:\t" + isNullable);
                    //是否为只读
                    boolean isReadOnly = rsmd.isReadOnly(i);
                    System.out.println("是否为只读:\t" + isReadOnly);
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return "查询完成";
    }
}
 ```
- 接口的实现
 ```MySql8
 String sql="待执行的语句";    // 例如 Alter table cxj drop (COM_CODE)
 Statement stmt= conn.createStatement();   //创建一个Statement对象
 stmt.executeUpdate(sql);       //执行sql语句
 ```

