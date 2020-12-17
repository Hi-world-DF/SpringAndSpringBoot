# SpringBoot&MyBatis Dynamic Sql

项目目录：

![image-20201217153341524](/Users/test/Library/Application Support/typora-user-images/image-20201217153341524.png)

Pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.facemagic</groupId>
    <artifactId>backend_practice</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>backend_practice</name>
    <description>Practice project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <spring-boot.version>2.3.7.RELEASE</spring-boot.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.mybatis.dynamic-sql/mybatis-dynamic-sql -->
        <dependency>
            <groupId>org.mybatis.dynamic-sql</groupId>
            <artifactId>mybatis-dynamic-sql</artifactId>
            <version>1.2.1</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.0</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.3.7</version>
        </dependency>

        <!--连接驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.11</version>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>RELEASE</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <finalName>${project.artifactId}-${project.version}</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.0</version>
                <configuration>
                    <!--mybatis的代码生成器的配置文件-->
                    <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
                    <!--允许覆盖生成的文件-->
                    <overwrite>true</overwrite>
                    <!--将当前pom的依赖项添加到生成器的类路径中-->
                    <includeCompileDependencies>true</includeCompileDependencies>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```





application.properties

```properties
# 应用名称
spring.application.name=backend_practice

# 应用服务 WEB 访问端口
server.port=8090

# MySQL数据源配置
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/Test
spring.datasource.username=root
spring.datasource.password=xgd@df1996
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

#mybatis别名
mybatis.type.aliases.package=com.facemagic.backend.dao
```

generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>

    <!--classPathEntry:数据库的JDBC驱动,换成你自己的驱动位置 可选 -->
    <classPathEntry location="${user.home}/.m2/repository/mysql/mysql-connector-java/8.0.21/mysql-connector-java-8.0.21.jar"/>

    <context id="backendTables" defaultModelType="conditional" targetRuntime="MyBatis3DynamicSql">
        <!-- 数据库关键字冲突,自动处理 -->
        <property name="autoDelimitKeywords" value="true"/>
        <!-- 用反引号`包裹,默认是双引号" -->
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>
        <plugin type="org.mybatis.generator.plugins.SerializablePlugin" />
        <plugin type="org.mybatis.generator.plugins.EqualsHashCodePlugin" />
        <plugin type="org.mybatis.generator.plugins.ToStringPlugin" />
        <plugin type="org.mybatis.generator.plugins.FluentBuilderMethodsPlugin" />

        <commentGenerator>
            <!-- 添加数据库注释到生成的代码 -->
            <property name="addRemarkComments" value="true" />
            <!-- 不生成日期 -->
            <property name="suppressDate" value="true"/>
        </commentGenerator>

        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/Test"
                        userId="root"
                        password="xgd@df1996">
            <!--MySQL 不支持 schema 或者 catalog 所以需要添加这个，作用就是只生成指定数据库的 -->
            <!--参考 : http://www.mybatis.org/generator/usage/mysql.html -->
            <property name="nullCatalogMeansCurrent" value="true" />
        </jdbcConnection>

        <javaTypeResolver >
            <property name="forceBigDecimals" value="true" />
            <!-- time field use LocalDate etc -->
            <property name="useJSR310Types" value="true" />
        </javaTypeResolver>

        <javaModelGenerator targetPackage="com.facemagic.backend.entity" targetProject="src/main/java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>

        <!-- 需要生成XML时才有效 -->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>

        <javaClientGenerator type="ANNOTATEDMAPPER" targetPackage="com.facemagic.backend.dao" targetProject="src/main/java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>

        <table schema="test" tableName="test" >
            <property name="useActualColumnNames" value="false"/>
            <generatedKey column="ID" sqlStatement="MySql" identity="true" />
        </table>
    </context>

</generatorConfiguration>
```

BackendPracticeApplication.java

```java
package com.facemagic.backend;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
import org.springframework.context.annotation.ComponentScan;

/**
 * Application
 *
 * @author jianwen
 * @version 1.0, 2020/12/17
 * @since 1.0.0
 */
@ComponentScan(basePackages = "com.facemagic.backend")
@SpringBootApplication
public class BackendPracticeApplication extends SpringBootServletInitializer {

	public static void main(String[] args) {
		SpringApplication.run(BackendPracticeApplication.class, args);
	}
}
```

BackendPracticeApplicationTests.java

```java
package com.facemagic.backend;


import com.facemagic.backend.dao.TestDynamicSqlSupport;
import com.facemagic.backend.dao.TestMapper;
import com.facemagic.backend.service.UserService;
import org.junit.jupiter.api.Test;

import org.mybatis.dynamic.sql.delete.render.DeleteStatementProvider;
import org.mybatis.dynamic.sql.insert.render.InsertStatementProvider;
import org.mybatis.dynamic.sql.render.RenderingStrategy;
import org.mybatis.dynamic.sql.select.render.SelectStatementProvider;
import org.mybatis.dynamic.sql.update.render.UpdateStatementProvider;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;



import java.time.LocalDate;
import java.util.List;
import java.util.Optional;

import static org.mybatis.dynamic.sql.SqlBuilder.*;

@SpringBootTest
class BackendPracticeApplicationTests {

    @Autowired
    TestMapper testMapper;

    @Autowired
    UserService userService;

    TestDynamicSqlSupport.Test testTable = new TestDynamicSqlSupport.Test();

    com.facemagic.backend.entity.Test test = new com.facemagic.backend.entity.Test();

    @Test
    void contextLoads() {
        userService.getUserById();
    }

    @Test
    public void testInsert(){

        test.setId(1);
        test.setName("user2");
        test.setPassword("123456");
        test.setSubmissionDate(LocalDate.now());

        InsertStatementProvider<com.facemagic.backend.entity.Test> insertStatement = insert(test)
                .into(testTable)
                .map(testTable.id).toProperty("id")
                .map(testTable.name).toProperty("name")
                .map(testTable.password).toProperty("password")
                .map(testTable.submissionDate).toProperty("submissionDate")
                .build()
                .render(RenderingStrategy.MYBATIS3);

        int rows = testMapper.insert(insertStatement);
        System.out.println(rows);
    }

    @Test
    public void testUpdate(){

        UpdateStatementProvider updateStatement = update(testTable)
                .set(testTable.id).equalTo(10)
                .set(testTable.name).equalTo("xiao")
                .where(testTable.id,isEqualTo(2))
                .build()
                .render(RenderingStrategy.MYBATIS3);

        int rows = testMapper.update(updateStatement);
        System.out.println(rows);
    }

    @Test
    public void testDelete(){

        DeleteStatementProvider deleteStatement = deleteFrom(testTable)
                .where(testTable.password,isNull())
                .build()
                .render(RenderingStrategy.MYBATIS3);

        int rows = testMapper.delete(deleteStatement);
        System.out.println(rows);
    }

    @Test
    public void testDeleteByPrimaryKey(){

        int rows = testMapper.deleteByPrimaryKey(10);
        System.out.println("影响行数："+rows);
    }

    @Test
    public void testCount(){

        SelectStatementProvider selectStatement = select(testTable.id,testTable.name,testTable.password,testTable.submissionDate)
                .from(testTable)
                .where(testTable.id,isEqualTo(2))
                .build()
                .render(RenderingStrategy.MYBATIS3);

        System.out.println(testMapper.count(selectStatement));
    }

    @Test
    public void testSelectMany(){

        SelectStatementProvider selectStatement = select(testTable.id,testTable.name,testTable.password,testTable.submissionDate)
                .from(testTable)
                .where(testTable.id,isIn(1,2,3,4,5))
                .build()
                .render(RenderingStrategy.MYBATIS3);

        List<com.facemagic.backend.entity.Test> rows = testMapper.selectMany(selectStatement);
        System.out.println(rows);
    }

}
```