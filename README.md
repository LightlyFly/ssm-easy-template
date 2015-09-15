# ssm-easy-template

## 介绍
**Ssm-Easy-Template** 是一个J2Ee项目快速开发脚手架，集成了最常用的框架,适用于`Restfull` 架构风格`Web Service`接口开发。

####组成
#####主要框架
* **Spring**: 不多说，貌似J2Ee离不开他了。
* **Springmvc**: 与Spring无缝集成，简单灵活，支持Restful风格。
* **Mybatis** :个人认为比Hibernate好控制，毕竟是自己写的Sql语句。
* **Shiro**: Apache的权限管理框架，扩展性好，使用简单，个人认为比`Spring-Security`框架容易入手。
* **tomcat连接池** : 稳定，性能好。
* **sf4j** ：支持多种日志系统，使用的是log4j。

#####工具框架
* **Spring-Test** :包括了常用单元测试、集成测试、Web测试，`Src/Test/Java`下有几个简单的测试类。使用测试框架的好处就是节省时间，无需启动Server就能测试程序。
* **Mybatis-Pagehelper** :Mybatis的分页排序插件，由国人开发，用起来非常方便，[Mybatis-Pagehelperp][2]项目主页。
* **Mybatis通用Mapper3** 也是有上面作者开发，极其方便的使用Mybatis单表的增删改查，如果是单表操作，基本不用写Mapper文件,[Mybatis通用Mapper3][3]项目主页。
* **Spring-Mail**： 可修改`/src/main/resource/mail-config.properties`配置文件，这个配置文件配置的是主邮箱。
* **commons fileupload**:`spring mvc`中集成了`appache-commons-fileupload`上传组件。上传处理更便捷。


####开发工具
#####IDE
Eclipse确实强大，但 [Intellij Idea][1] 更智能，强烈推荐 **Idea**
#####依赖管理工具
`Maven`可能更容易上手，但我更喜欢`Gradle`的简洁

## 使用
###下载
`Download Zip`或者`git clone`
``` shell
	git clone https://github.com/ichenkaihua/ssm-easy-template.git
```
###导入
建议使用IDEA，eclipse也没问题

###快速开始
导入`doc/ssm-easy-template.sql`到Mysql
```shell
#进入项目目录
cd ssm-easy-template/
# jetty启动项目
./gradlew  jettyStart
# 获取所有用户 
curl http://localhost:8080/users

# 其他操作...

#关闭jetty
./gradlew jettyStop
```

###详细修改

####利用mybatis-generator(MBG)生成`model/mapper/mapper.xml`文件
Mybatis考虑到手写XML文件的繁琐，因此开发了MBG工具，通用Mapper这个项目再次简化了mybatis的生成代码数量。ssm-easy-template把mybatis generator放在`build.gradle`中，封装成一个`Gradle Task`。

 * **修改generator配置文件**: 打开`gradle.propertis`文件,修改数据库信息，默认如下
```shell
#mybatis generator properties
driverClass=com.mysql.jdbc.Driver
connectionURL=jdbc:mysql://localhost:3306/ssm_easy_template
userId=root
password=root
#生成的model类所在包
modelPackage=com.github.ichenkaihua.model
#生成的mapper接口类所在包
mapperPackage=com.github.ichenkaihua.mapper
#生成的mapper xml文件所在包，默认存储在resources目录下
sqlMapperPackage=mybatis_mapper

```
* **执行 mybatisGenerate task** :
```shell
./gradlew mybatisGenerate
```

####修改项目配置文件
在`src/main/resources`目录下，有下列文件：
```shell
#MBG 配置文件
generatorConfig.xml
#项目运行的数据库配置
jdbc-mysql.properties
#log4j配置
log4j.properties
#mail 
mail-config.properties
#spring Application root Context
spring-config.xml
# spirng-mail
spring-mail.xml
#springMVC context
spring-mvc-config.xml
#springMVC shiro
spring-mvc-shiro.xml
#spring-mybatis
spring-mybatis.xml
#spring-shiro
spring-shiro.xml
#spring-tomcatl-pool 连接池
spring-tomcat-pool.xml
#连接池配置
tomcat-pool-config.properties
```
**ssm-easy-template把项目配置分离，便于后期扩展或替换组件。根据项目需要更改配置文件**


#### 实现项目逻辑

为了方便后期扩展与重用，ssm-easy-template封装出`BaseService<Mapper<M>,M>`,通常service继承`BaseService<Mapper<M>,M>`，单表操作的逻辑不需要再实现，BaseService实现的接口如下:
```java
package com.github.ichenkaihua.service;

import org.apache.ibatis.session.RowBounds;
import org.springframework.beans.factory.annotation.Autowired;
import tk.mybatis.mapper.common.Mapper;

import java.util.List;

/**
 * Created by chenkaihua on 15-9-15.
 */
public class BaseService<Mapp extends Mapper<M>,M> implements  Mapper<M>  {


    @Autowired
  protected   Mapp mapper;

    @Override
    public int deleteByExample(Object example) {
        return mapper.deleteByExample(example);
    }

    @Override
    public int deleteByPrimaryKey(Object key) {
        return mapper.deleteByPrimaryKey(key);
    }

    @Override
    public int delete(M record) {
        return mapper.delete(record);
    }

    @Override
    public int insert(M record) {
        return mapper.insert(record);
    }

    @Override
    public int insertSelective(M record) {
        return mapper.insertSelective(record);
    }

    @Override
    public List<M> selectByExample(Object example) {
        return mapper.selectByExample(example);
    }

    @Override
    public List<M> selectByExampleAndRowBounds(Object example, RowBounds rowBounds) {
        return mapper.selectByExampleAndRowBounds(example,rowBounds);
    }

    @Override
    public M selectByPrimaryKey(Object key) {
        return mapper.selectByPrimaryKey(key);
    }

    @Override
    public int selectCountByExample(Object example) {
        return mapper.selectCountByExample(example);
    }

    @Override
    public int selectCount(M record) {
        return mapper.selectCount(record);
    }

    @Override
    public List<M> select(M record) {
        return mapper.select(record);
    }

    @Override
    public M selectOne(M record) {
        return mapper.selectOne(record);
    }

    @Override
    public List<M> selectByRowBounds(M record, RowBounds rowBounds) {
        return mapper.selectByRowBounds(record,rowBounds);
    }

    @Override
    public int updateByExample(M record, Object example) {
        return mapper.updateByExample(record,example);
    }

    @Override
    public int updateByExampleSelective(M record, Object example) {
        return mapper.updateByExampleSelective(record,example);
    }

    @Override
    public int updateByPrimaryKey(M record) {
        return mapper.updateByPrimaryKey(record);
    }

    @Override
    public int updateByPrimaryKeySelective(M record) {
        return mapper.updateByPrimaryKeySelective(record);
    }
}

```


###部署
`Tomcat`或者`Jetty`都行，只需要配置 IDEA的 `run config`
```shell
#jetty启动项目，http://locaohost:8080/
./gradlew  jettyStart

#关闭jetty
./gradlew jettyStop

```
## 关于我
**博客链接**:[陈开华的博客][4]

**Email**: admin@chenkaihua.com

[1]:https://www.jetbrains.com/idea/
[2]: https://github.com/pagehelper/Mybatis-PageHelper
[3]:https://github.com/abel533/Mapper
[4]:http://www.chenkaihua.com
