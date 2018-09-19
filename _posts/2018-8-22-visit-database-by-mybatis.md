---
layout: post
title: '使用Mybatis访问数据库'
subtitle: ''
date: 2018-08-22
categories: SpringBoot
cover: ''
tags: SpringBoot 数据库 MyBatis Spring
---

# 介绍
Mybatis应该是最有名的数据库访问组件了,它的api有点像RESTfulapi的感觉,当然我是一个初学者,不可能说得非常细,只是最简单的使用

# 引入
```xml
        <!--引入mysql的连接器-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>


        <!--引入阿里巴巴的数据库连接池-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.29</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
        <!--引入mybatis-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.0</version>
        </dependency>
```

# 配置数据源
```Java
spring.datasource.url=jdbc:mysql://localhost:3306/demo_db
spring.datasource.username=root
spring.datasource.password=*********
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

# 实体类
实体类与数据库的一个表相对应
我的表是这样的
```Java
+-------+------+--------+----+
| name  | age  | sex    | id |
+-------+------+--------+----+
| tinuv |   20 | male   |  1 |
| lyy   |   19 | female |  2 |
+-------+------+--------+----+
```
与之对应的实体类是这样的
```Java
public class UserInfo {
    private String name;
    private int age;
    private String sex;
    private int id;
    // 省略getter和setter
}
```

# DAO层
这些api真的很像RESTfulapi,我只写一条,麻雀虽小五脏俱全,我只写一条语句,其他的都是类似
```Java
@Mapper
public interface UserInfoMapping {

    @Insert("insert into user_info values(#{name},#{age},#{sex},#{id})")
    int add(@Param("name") String name, @Param("age") int age, @Param("sex") String sex, @Param("id") int id);
}
```

# Service层
```Java
@Service
public class UserInfoService {

    @Autowired
    private UserInfoMapping userInfoMapping;

    public int add(String name,int age,String sex,int id) {
      return userInfoMapping.add(name,age,sex,id);
    }
}
```

# Controller层
```Java
@RestController
@RequestMapping("/userinfo")
public class UserInfoController {

    @Autowired
    UserInfoService userInfoService;


    @RequestMapping(value = "/add",method = RequestMethod.GET)
    public int add(){
        return userInfoService.add("yumm",20,"female",3);
    }
}
```

运行这个API后成功的插入了数据
```Java
+-------+------+--------+----+
| name  | age  | sex    | id |
+-------+------+--------+----+
| tinuv |   20 | male   |  1 |
| lyy   |   19 | female |  2 |
| yumm  |   20 | female |  3 |
+-------+------+--------+----+
```

# 总结
我本来是很想在测试框架中运行代码,但是不知道为什么就是报错,我还不怎么懂,总的来说使用Mybatis这个组件还是很容易的,但怎么把它玩出花来就是自己的本事了.