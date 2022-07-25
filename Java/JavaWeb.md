# JavaWeb

## JDBC

### JDBC

#### 概念

* JDBC就是使用Java语言操作关系型数据库的一套API
* 全称：(Java DataBase Connectivity) Java数据库连接

#### 本质

* 官方（sun公司）定义的一套操作所有关系型数据库的规则，即接口
* 各个数据库厂商去实现这套接口，提供数据库驱动jar包
* 我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类

#### 好处

* 各数据库厂商使用相同的接口，Java代码不需要针对不同的数据库分别开发
* 可随时替换底层数据库，访问数据库的Java代码基本不变

#### 步骤

1. 注册驱动（5以后可以省略）

   ```java
   // MySQL5
   Class.forName("com.mysql.jdbc.Driver");
   
   // MySQL8
   Class.forName("com.mysql.cj.jdbc.Driver");
   ```

2. 获取连接

   ```java
   //MySQL5的url
   String url = "jdbc:mysql://hoseName:port/DBName;
   // MySQL8的url
   String url = "jdbc:mysql://hostName:port/DBName?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true";
   String user = "root";
   String password = "qcfdbj";
   Connection connection = DriverManager.getConnection(url, user, password);
   ```

3. 定义SQL语句

4. 获取执行SLQ对象

5. 执行SQL

6. 获取返回值

7. 释放资源

---

### PreparedStatement

#### PreparedStatement的作用

预编译SQL并执行SQL语句

1. 获取 `PreparedStatement` 对象

   ```java
   // SQL语句中的参数值，使用?占位符代替
   String sql = "select * from user where username = ? and password = ?";
   
   // 通过Connection对象获取并传入SQL语句
   PreparedStatement pstmt = conn.prepareStatemant(sql);
   ```

2. 设置参数值
   * PreparedStatement对象：`setXxx(参数1, 参数2) // 给?赋值`
   * Xxx：数据类型，如：`SetInt(参数1, 参数2)`
   * 参数：
     * 参数1：?的位置的编号，从1开始
     * 参数2：?的值

3. 执行SQL

   `executeUpdate();`或`executeQuery();`，不需要再传递SQL

#### PreparedStatement的原理

* PreparedStatement 好处
  1. 预编译SQL，性能更高
  2. 防止SQL注入：将特殊字符进行转义

* PreparedStatement 预编译功能开启：`useServerPreStmts = true`

---

### 数据库连接池

* 数据库连接池是一个容器，负责分配、管理数据库连接
* 他允许应用程序重复使用一个现有的数据库连接，而不是重新建立
* 释放空闲时间超过最大空闲时间的数据库连接请求来避免因为没有释放数据库连接而引起的数据库连接泄漏
* 好处
  * 资源重用
  * 提升系统响应速度
  * 避免数据库连接遗漏

![image-20220703141733675](asserts\image-20220703141733675.png)

* 标准接口：DataSource

  * 官方（sun）提供的数据库连接池标准接口，有第三方组织实现此接口

  * 功能：获取连接

    ```java
    Connection getConnection();
    ```

* 常见的数据库连接池
  * DBCP
  * C3P0
  * Druid

* Druid（德鲁伊）
  * Druid连接池是阿里巴巴开源的数据库连接池项目
  * 功能强大，性能优秀，是Java语言最好的数据库连接池之一

#### Druid 使用步骤

1. 导入jar包（非maven）
2. 定义配置文件
3. 加载配置文件
4. 获取数据库连接池对象
5. 获取连接

范例演示

```java
public class Druid {
    public static void main(String[] args) throws Exception {

        // 加载配置文件
        Properties prop = new Properties();
        prop.load(new FileInputStream("itcast/src/main/resources/druid.properties"));
        // 获取连接池对象
        DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);
        // 获取数据库连接
        Connection conn = dataSource.getConnection();

        System.out.println(conn);
    }
}
```

druid.properties配置

```properties
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/itheima?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
username=root
password=qcfdbj
#初始化连接数量
initialSize=5
#最大连接数
maxActive=10
#最大等待时间
maxWait=3000
```

## MyBatis

​	MyBatis是一款优秀的**持久层**框架，用于简化JDBC开发

* 持久层：
  * 负责将数据保存到数据库的那一层
  * JavaEE三层架构：表现层、业务层、持久层

* 框架：
  * 框架是一个半成品软件，是一套可重用的、通用的软件基础代码模型
  * 在框架的基础之上构建软件更加高效、规范、通用、可扩展

​	JDBC缺点：1. 硬编码    2. 操作繁琐
