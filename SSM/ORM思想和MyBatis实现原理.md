## 一、ORM思想

​	目前的市场上，通过Java语言连接并操作数据库的技术或者方式以及有很多，例如：JDBC、MyBatis、Hibernate等等。其中JDBC是Java的原生API，支持连接并操作各种关系型数据库。

​	JDBC作为Java原生API，有优点也有缺点，缺点主要是：

1. 编码繁琐，效率低
2. 数据库连接的创建和释放比较重复，也造成了系统资源的浪费
3. 大量硬编码，缺乏灵活性，不利于后期维护
4. 参数的复制和数据的封装全是手动进行的

那么了解这些缺点以后，我们回顾一下以前是怎么使用JDBC来连接数据库，操作数据库的。

```java
 public List<Book> findAll(){
     Connection connection = null;
     PreparedStatement pre = null;
     ResultSet result = null;
     List<Book> bookList = null;
     
   	try {
 		//加载数据库驱动
		Class.forName("com.mysql.jdbc.Driver");
        //通过驱动管理类获取数据库链接
  		connection=
      	DriverManager.getConnection("jdbc:mysql://localhost:3306/test", "root", "123");
         //定义 sql 语句 ?表示占位符
         String sql = "select * from t_book where author = ?";
         //获取预处理 statement
         preparedStatement = connection.prepareStatement(sql);
         //设置参数，第一个参数为 sql 语句中参数的序号（从 1 开始），第二个参数为设置的参数值
         preparedStatement.setString(1, "张三"); 
        //向数据库发出 sql 执行查询，查询出结果集
         resultSet = preparedStatement.executeQuery();
         //遍历查询结果集
         bookList = new ArrayList<>();
         while(resultSet.next()){
             Book book=new Book();
             book.setId(resultSet.getInt("id"));
             book.setName(resultSet.getString("bname"));
             book.setAuthor(resultSet.getString("author"));
             book.setPrice(resultSet.getDouble("price"));
             bookList.add(book);
         }
         return bookList;
         } catch (Exception e) {
        	 e.printStackTrace();
        	 return null;
         }finally{
             //释放资源
             if(resultSet!=null){
         		try {
         			resultSet.close();
         		} catch (SQLException e) {
        			 e.printStackTrace();
         		}
         	}
         	  if(preparedStatement!=null){
         		  try {
         				preparedStatement.close();
         		} catch (SQLException e) {
        			 e.printStackTrace();
         		}
               if(connection != null){
                   try {
         				connection.close();
         		} catch (SQLException e) {
        			 e.printStackTrace();
         		}
               }
           }
    }
```

​	总的流程就是：

- 加载数据库驱动
- 获取数据库连接
- 准备sql语句和获得PreparedStatement对象
- 执行查询，获得结果集
- 遍历结果集
- 关闭数据库连接

很多程序员其实都亲自尝试去对JDBC进行封装和优化，设计并编写过一些API，其中设计的思想是不一样的，主要分为两大类：

1. 对JDBC进行API层的抽取和封装，以及功能的增强，经典代表是DbUtils.
2. 借助面向对象的思想，以对象的方式操作数据库，无需编写sql语句，典型代表就是ORM。ORM(Object Relational Mapping)吸收了面向对象的思想，把对 sql 的操作 转换为对象的操作，从而让程序员使用起来更加方便和易于接受。这种转换是通过对象和表之间的元数据映射实现的，这是实现 ORM 的关键，如下图所示

![1567041896982](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567041896982.png)

由于类和表之间以及属性和字段之间建立起了映射关系，所以，通过 sql 对表的操作就 可以转换为对象的操作，程序员从此无需编写 sql 语句，由框架根据映射关系自动生成，这 就是 ORM 思想。

## 二、MyBatis的ORM实现原理

​	为了探究MyBatis的ORM实现原理，我们接下来通过一个案例，使用debug的方式来查看这其中运行的过程。

### 2.1、案例结构

![1567059815288](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567059815288.png)

### 2.2、entity实体类

**DeptEmp**

```java
package com.choi.mybatis.entity;

//实体类
public class DeptEmp {

    private String dname;  //部门名
    private Integer total; //员工总数

    public String getDname() {
        return dname;
    }

    public void setDname(String dname) {
        this.dname = dname;
    }

    public Integer getTotal() {
        return total;
    }

    public void setTotal(Integer total) {
        this.total = total;
    }

    @Override
    public String toString() {
        return "DeptEmp{" +
                "dname='" + dname + '\'' +
                ", total=" + total +
                '}';
    }
}
```

### 2.3、mapper接口和xml文件

**DeptEmpMapper.java**

```java
package com.choi.mybatis.mapper;

import com.choi.mybatis.entity.DeptEmp;

import java.util.List;

public interface DeptEmpMapper {

    List<DeptEmp> getEmpTotalByDept();

}

```

**DeptEmpMapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--Mapper映射配置文件-->
<mapper namespace="com.choi.mybatis.mapper.DeptEmpMapper">

    <!--自定义一个resultMap配置实体类和结果集之间的映射关系-->
    <resultMap id="dept_emp_result_map" type="deptEmp">
        <result property="dname" column="dname"/>
        <result property="total" column="total"/>
    </resultMap>

    <select id="getEmpTotalByDept" resultMap="dept_emp_result_map">
        SELECT dname, COUNT(*) AS total  FROM  dept, emp WHERE emp.`dept_id` = dept.`did` GROUP BY dname
    </select>

</mapper>


```

### 2.4、MyBatis配置文件

**SqlMapConfig.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!-- 设置别名-->
    <typeAliases>
        <package name="com.choi.mybatis.entity"/>
    </typeAliases>

    <environments default="MySQLDevelopment">
        <environment id="MySQLDevelopment">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/study?characterEncoding=utf-8"/>
                <property name="username" value="root"/>
                <property name="password" value="6680124"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <package name="com.choi.mybatis.mapper"/>
    </mappers>

</configuration>
```

### 2.5、测试类

**DeptEmpTest.java**

```java
import com.choi.mybatis.entity.DeptEmp;
import com.choi.mybatis.mapper.DeptEmpMapper;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.InputStream;
import java.util.List;

public class DeptEmpTest {

    @Test
    public void testGetEmpTotalByDept() throws  Exception{

        //1. 加载核心配置文件
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder=new SqlSessionFactoryBuilder();
        InputStream inputStream= Resources.getResourceAsStream("SqlMapConfig.xml");

        //2. 解析核心配置文件并创建SqlSessionFactory对象
        SqlSessionFactory sqlSessionFactory=sqlSessionFactoryBuilder.build(inputStream);

        //3. 创建核心对象
        SqlSession sqlSession=sqlSessionFactory.openSession();

        //4. 得到Mapper代理对象
        DeptEmpMapper deptEmpMapper=sqlSession.getMapper(DeptEmpMapper.class);

        //5. 调用自定义的方法实现查询功能
        List<DeptEmp> list= deptEmpMapper.getEmpTotalByDept();
        for(DeptEmp de:list){
            System.out.println(de);
        }

        //6. 关闭sqlSession
        sqlSession.close();
    }

}
```

### 2.6、pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.choi.mybatis</groupId>
    <artifactId>mybatisDemo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.0</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.36</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>

    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
        </resources>
    </build>


</project>
```

**注意：**pom文件中必须引入这个<build>标签中的内容，否则mapper.xml文件将不会被编译，导致项目启动的时候找不到对应的xml文件。如下图所示

![1567060430392](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567060430392.png)

### 2.7、运行结果

![1567060489712](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567060489712.png)

### 2.8、MyBatis的ORM实现原理

接下来，我们使用debug的方式来运行，看一下这其中运行的原理

1. 解析MyBatis核心配置文件并创建出一个SqlSessionFactory对象

```java
       //1. 加载核心配置文件
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder=new SqlSessionFactoryBuilder();
        InputStream inputStream= Resources.getResourceAsStream("SqlMapConfig.xml");

        //2. 解析核心配置文件并创建SqlSessionFactory对象
        SqlSessionFactory sqlSessionFactory=sqlSessionFactoryBuilder.build(inputStream);
```

![1567060796807](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567060796807.png)

![1567060820318](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567060820318.png)

![1567060848280](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567060848280.png)

通过断点调试，在第69行的时候创建了一个xml解析器对象，并在第70行对MyBatis核心配置文件进行了解析，拿到了数据库连接数据和银蛇配置文件中的数据（包括我们编写的sql语句和自定义的resultMap），如下所示

![1567060909529](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567060909529.png)

![1567060958760](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567060958760.png)

![1567061127290](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567061127290.png)

在88行代码中创建了DefaultSqlSessionFactory对象，并把上图中展示的解析数据传给它，继续跟踪

![1567061414570](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567061414570.png)

2. 调用sqlSessionFactory的openSession方法创建sqlSession

```java
       //3. 创建核心对象
        SqlSession sqlSession=sqlSessionFactory.openSession();

```

![1567061561480](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567061561480.png)

![1567061649128](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567061649128.png)

图中71行-75行代码，主要是通过读取Configuration对象中的数据分别创建了Environment对象，事务对象，Executor对象，最终直接new了一个DefaultSqlSession对象（**SqlSession接口的实现类**），该对象是MyBatis对象的核心对象。

3. 调用getMapper方法获取代理对象

```java
        //4. 得到Mapper代理对象
        DeptEmpMapper deptEmpMapper=sqlSession.getMapper(DeptEmpMapper.class);	

```

![1567061876944](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567061876944.png)

![1567061897152](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567061897152.png)

![1567061934964](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567061934964.png)

通过第34行代码中的代理工厂最终把我们自定义的Mapper接口的代理对象创建出来

4. 调用代理对象中的方法执行查询

```java
        //5. 调用自定义的方法实现查询功能
        List<DeptEmp> list= deptEmpMapper.getEmpTotalByDept();

```

![1567062083503](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062083503.png)

当我们调用代理对象的getEmpTotalByDept方法时，框架内部会调用MapperProxy的invoke方法，我们可以观察这个方法的三个参数值：

![1567062221669](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062221669.png)

在invoke方法的内部，调用了execute方法执行查询

![1567062320958](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062320958.png)

在execute方法内部先进行增删查改的判断，本次案例是查询，并且可能查出多条记录，所以走的是60行代码，进入该行代码

![1567062404178](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062404178.png)

第124行代码是判断是否进行分页查询，本案例中不需要，所以执行的是else中的代码

![1567062493428](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062493428.png)

继续跟踪，发现123行代码从Configuration对象中，获取了MappedStatement对象，该对象存储的是映射配置文件中的所有信息，如下图所示

![1567062521717](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062521717.png)

![1567062609002](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062609002.png)

继续往下跟踪

![1567062659557](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062659557.png)

![1567062706545](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062706545.png)

![1567062780695](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062780695.png)

最后发现，在134行代码中调用了queryFromDataBase方法最终执行了sql语句查询，并将查询结构按照我们自定义的resultMap进行封装，如下图所示

![1567062906043](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1567062906043.png)

总结：MyBatis工作原理

1. 读取核心配置文件并返回InputStream流对象。
2. 根据InputStream流对象解析出Configuration对象，然后创建SqlSessionFactory工厂对象
3. 根据一系列属性从SqlSessionFactory工厂中创建SqlSession
4. 从SqlSession中调用Executor执行数据库操作&&生成具体SQL指令
5. 对执行结果进行二次封装
6. 提交与事务

扩展阅读

[https://zhuanlan.zhihu.com/p/59792101]()

