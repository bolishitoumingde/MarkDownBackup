## MyBatis

​	MyBatis是一款优秀的**持久层**框架，用于简化JDBC开发

* 持久层：
  * 负责将数据保存到数据库的那一层
  * JavaEE三层架构：表现层、业务层、持久层

* 框架：
  * 框架是一个半成品软件，是一套可重用的、通用的软件基础代码模型
  * 在框架的基础之上构建软件更加高效、规范、通用、可扩展

​	JDBC缺点：1. 硬编码    2. 操作繁琐





## MyBatis配置

### JaveWeb

* 新建配置文件 mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd" >
<!--配置mybatis环境-->
<configuration>
    <!--定义实体类位置-->
    <typeAliases>
        <package name="bl.javaweb.entity"/>
    </typeAliases>
    <!--配置连接使用的相关参数default为默认使用的环境  development:开发环境  product:生产环境-->
    <environments default="development">
        <!--开发环境-->
        <environment id="development">
            <!--事务管理类型：指定事务管理的方式 JDBC-->
            <transactionManager type="JDBC"/>
            <!--数据库连接相关配置，动态获取config.properties文件里的内容-->
            <!--数据源类型：POOLED 表示支持JDBC数据源连接池 UNPOOLED 表示不支持数据源连接池 JNDI 表示支持外部数据源连接池-->
            <dataSource type="POOLED">
                <!--此处使用的是MySQL数据库，使用Oracle数据库时需要修改，仔细检查各项参数是否正确，里面配置了时区、编码方式、SSL，用以防止中文查询乱码，导致查询结果为null及SSL警告等问题-->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url"
                          value="jdbc:mysql://localhost:3306/dbName?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC&amp;useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
        <!--生产环境-->
        <environment id="product">
			<!--格式与开发环境相同-->
        </environment>
    </environments>

    <!--注册mapper配置文件（mapper文件路径配置）注意：映射配置文件位置要和映射器位置一样，如：映射器在com.mycode.dao里，
                那么配置文件就应该在resources的com/mycode/dao目录下，
                否则会报Could not find resource com.mycode.dao.UserMapper.xml类似错误
                -->
    <mappers>
        <!--下面编写mapper映射文件↓↓↓↓↓ 参考格式：<mapper resource="dao/UserMapper.xml"/> -->
        <!--<mapper resource="bl/javaweb/mapper/UserMapper.xml"/>-->
        
        <!--mapper代理加载映射文件方式如下-->
        <package name="bl.javaweb.mapper"/>
    </mappers>

</configuration>
```

* 获取 sqlSessionFactory 对象（抽取工具类）

​		新建 util 名为 SqlSessionFactoryUtils

```java
package bl.javaweb.util;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;


public class SqlSessionFactoryUtils {
    // 创建静态成员，只修改一次
    private static SqlSessionFactory sqlSessionFactory;

    // 静态代码块，该方法只执行一次
    static {
        try {
            // 获取mybatis配置
            String resource = "mybatis-config.xml";
            // 获取字节输入流
            InputStream inputStream = Resources.getResourceAsStream(resource);
            // 修改 SqlSessionFactory 对象
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * 公有方法返回 SqlSessionFactory 对象
     *
     * @return
     */
    public static SqlSessionFactory getSqlSessionFactory() {
        return sqlSessionFactory;
    }
}

```

* 编写 Xxxmapper.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<!--
      mapper为映射的根节点，用来管理DAO接口
      namespace指定DAO接口的完整类名，表示mapper配置文件管理哪个DAO接口(包.接口名)
      mybatis会依据这个接口动态创建一个实现类去实现这个接口，而这个实现类是一个Mapper对象
   -->
<mapper namespace="包名.类名">
    <!--例：bl.javaweb.mapper.UserMapper-->

    <!--
          id = "接口中的方法名"
          parameterType = "接口中传入方法的参数类型"
          resultType = "返回实体类对象：包.类名"  处理结果集 自动封装
          注意:sql语句后不要出现";"号
              查询：select标签
              增加：insert标签
              修改：update标签
              删除：delete标签
      -->

</mapper>
```



