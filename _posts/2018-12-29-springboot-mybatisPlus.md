---
layout:     post
title:      springboot集成mybatis-plus
subtitle:   springboot项目笔记
date:       2018-12-29
author:     LJQ
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - springboot
    - springboot项目笔记
---

# MyBatis-Plus，让开发更有效率
>MyBatis-Plus（简称 MP）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

## 数据库结构与测试数据

MyBatis-Plus在自动生成代码时，会将数据库的注释同时生成到实体类中
```
SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for t_class
-- ----------------------------
DROP TABLE IF EXISTS `t_class`;
CREATE TABLE `t_class` (
  `class_id` varchar(50) NOT NULL COMMENT '班级主键',
  `class_name` varchar(50) DEFAULT NULL COMMENT '班级名称',
  PRIMARY KEY (`class_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of t_class
-- ----------------------------
INSERT INTO `t_class` VALUES ('1', '计算机科学与技术');
INSERT INTO `t_class` VALUES ('2', '软件工程');

-- ----------------------------
-- Table structure for t_user
-- ----------------------------
DROP TABLE IF EXISTS `t_user`;
CREATE TABLE `t_user` (
  `id` varchar(255) NOT NULL COMMENT '主键ID',
  `name` varchar(30) DEFAULT NULL COMMENT '姓名',
  `age` int(11) DEFAULT NULL COMMENT '年龄',
  `email` varchar(50) DEFAULT NULL COMMENT '邮箱',
  `class_id` varchar(255) DEFAULT NULL COMMENT '班级外键',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of t_user
-- ----------------------------
INSERT INTO `t_user` VALUES ('1', 'Jone', '18', 'test1@baomidou.com', '1');
INSERT INTO `t_user` VALUES ('2', 'Jack', '20', 'test2@baomidou.com', '1');
INSERT INTO `t_user` VALUES ('3', 'Tom', '28', 'test3@baomidou.com', '1');
INSERT INTO `t_user` VALUES ('4', 'Sandy', '21', 'test4@baomidou.com', '2');
INSERT INTO `t_user` VALUES ('5', 'Billie', '24', 'test5@baomidou.com', '2');
SET FOREIGN_KEY_CHECKS=1;

```
## 依赖导入
```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <!-- MyBatis-Plus -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.0.5</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.0</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>io.projectreactor</groupId>
            <artifactId>reactor-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```
## 配置文件 

```application.yml```


```
spring:
  datasource:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql:///mp_demo?useUnicode=true&characterEncoding=utf-8&useSSL=false&zeroDateTimeBehavior=convertToNull&serverTimezone=UTC
      username: root
      password: 123456
mybatis-plus:
  mapper-locations: classpath:mapper/*.xml
  #实体扫描，多个package用逗号或者分号分隔
  typeAliasesPackage: com.ljq.mp_demo.entity
  global-config:
    db-config:
      id-type: id-worker-str
      #字段策略 0:"忽略判断",1:"非 NULL 判断"),2:"非空判断"
      field-strategy: not-empty
  configuration:
    map-underscore-to-camel-case: true
    cache-enabled: false
```

>MyBatis-Plus为我们提供了自动生成mapper.xml文件、pojo实体类、dao层、service层的方法，可以将其放在test文件下

```
public class CodeGenerator {

    /**
     * mysql数据库，生成dao,service,controller
     * @param
     */
    @Test
    public void main() {
        
        String srcPath = System.getProperty("user.dir") + "/src/main/java";//项目源码绝对路径
        
        String username = "root";
        String password = "123456";
        String url = "jdbc:mysql:///mp_demo?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT";
        
        String[] tablePrefixs = new String[] {"t_"};//表前缀
        
        AutoGenerator mpg = new AutoGenerator();
        // 选择 freemarker 引擎，默认 Veloctiy
        // mpg.setTemplateEngine(new FreemarkerTemplateEngine());

        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        gc.setOutputDir(srcPath);
        gc.setFileOverride(true);
        gc.setActiveRecord(true);// 不需要ActiveRecord特性的请改为false
        gc.setEnableCache(false);// XML 二级缓存
        gc.setBaseResultMap(true);// XML ResultMap
        gc.setBaseColumnList(false);// XML columList
        // .setKotlin(true) 是否生成 kotlin 代码
        gc.setAuthor("自动生成作者信息");

        // 自定义文件命名，注意 %s 会自动填充表实体属性！
        gc.setMapperName("%sMapper");
        gc.setXmlName("%sMapper");
        gc.setServiceName("%sService");
        gc.setServiceImplName("%sServiceImpl");
        gc.setControllerName("%sController");
        mpg.setGlobalConfig(gc);

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setDbType(DbType.MYSQL);
        dsc.setTypeConvert(new MySqlTypeConvert());
        dsc.setDriverName("com.mysql.jdbc.Driver");
        dsc.setUsername(username);
        dsc.setPassword(password);
        dsc.setUrl(url);
        mpg.setDataSource(dsc);

        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        // strategy.setCapitalMode(true);// 全局大写命名 ORACLE 注意
        strategy.setTablePrefix(tablePrefixs);// 此处可以修改为您的表前缀
        strategy.setNaming(NamingStrategy.underline_to_camel);// 表名生成策略
        // strategy.setInclude(new String[] { "user" }); // 需要生成的表,不写就是全部
        // strategy.setExclude(new String[]{"test"}); // 排除生成的表
        // 自定义实体父类
        // strategy.setSuperEntityClass("com.baomidou.demo.TestEntity");
        // 自定义实体，公共字段
        // strategy.setSuperEntityColumns(new String[] { "test_id", "age" });
        // 自定义 mapper 父类
        // strategy.setSuperMapperClass("com.baomidou.demo.TestMapper");
        // 自定义 service 父类
        // strategy.setSuperServiceClass("com.baomidou.demo.TestService");
        // 自定义 service 实现类父类
        // strategy.setSuperServiceImplClass("com.baomidou.demo.TestServiceImpl");
        // 自定义 controller 父类
        // strategy.setSuperControllerClass("com.baomidou.demo.TestController");
        // 【实体】是否生成字段常量（默认 false）
        // public static final String ID = "test_id";
        // strategy.setEntityColumnConstant(true);
        // 【实体】是否为构建者模型（默认 false）
        // public User setName(String name) {this.name = name; return this;}
        strategy.setEntityBuilderModel(true);
        //使用lombok注解
        strategy.setEntityLombokModel(true);
        mpg.setStrategy(strategy);

        // 包配置
        PackageConfig pc = new PackageConfig();
        pc.setParent("com");//父包名
        pc.setModuleName("ljq.mp_demo");//模块名，该模块是下列所有包名的父包
        pc.setController("web.controller");
        pc.setService("service");
        pc.setServiceImpl("service.impl");
        pc.setMapper("dao");
        mpg.setPackageInfo(pc);

        // 注入自定义配置，可以在 VM 中使用 cfg.abc 【可无】
        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {

            }
        };

        // 自定义模板配置，可以 copy 源码 mybatis-plus/src/main/resources/templates 下面内容修改，
        // 放置自己项目的 src/main/resources/templates 目录下, 默认名称一下可以不配置，也可以自定义模板名称
        TemplateConfig tc = new TemplateConfig();
        
        tc.setController(null);//不生成controller
        // tc.setEntity("...");
        // tc.setMapper("...");
        //tc.setMapper(null);
        tc.setXml(null);
        //tc.setService(null);
        //tc.setServiceImpl(null);
        // 如上任何一个模块如果设置 空 OR Null 将不生成该模块。
        // 调整 xml 生成目录演示
        //此路径为项目在计算机的实际路径
        String resourcesPath  = "F:\\mp_demo\\src\\main\\resources";
        List<FileOutConfig> focList = new ArrayList<>();
        focList.add(new FileOutConfig("/templates/mapper.xml.vm") {
            @Override
            public String outputFile(TableInfo tableInfo) {
                 return resourcesPath+"/mapper/" + tableInfo.getEntityName() + "Mapper.xml";

            }
        });
        cfg.setFileOutConfigList(focList);
        mpg.setCfg(cfg);

        mpg.setTemplate(tc);

        // 执行生成
        mpg.execute();
    }
}
```
### 自动生成前
![生成前.jpg](https://upload-images.jianshu.io/upload_images/15504753-20a808067139ae2b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 自动生成后
![生成后.png](https://upload-images.jianshu.io/upload_images/15504753-235a9f3975c11a34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### entity示例
```
/**
 * <p>
 * 
 * </p>
 *
 * @author 自动生成作者信息
 * @since 2018-12-29
 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_user")
public class User extends Model<User> {

    private static final long serialVersionUID = 1L;

    /**
     * 主键ID
     */
    private String id;

    /**
     * 姓名
     */
    private String name;

    /**
     * 年龄
     */
    private Integer age;

    /**
     * 邮箱
     */
    private String email;

    /**
     * 班级外键
     */
    private String classId;


    @Override
    protected Serializable pkVal() {
        return this.id;
    }

}
```
自动生成主键需要在主键相应属性添加注解
```
@TableId(value = "id", type = IdType.UUID)
```
## CRUD操作
```
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {

    @Override
    public void CrudDemo() {
        //用户信息
        User user = new User();
        user.setName("TomCat");
        user.setAge(11);
        user.setClassId("1");
        user.setEmail("666@666.com");
        //新增用户信息
        this.save(user);
        //修改用户信息
        this.updateById(user);
        //新增或修改
        this.saveOrUpdate(user);
        //删除
        /*
        sql:
        delete t_user tu where tu.id = 1
         */
        QueryWrapper<User> removeQueryWrapper = new QueryWrapper<>();
        removeQueryWrapper.eq("id",1);
        this.remove(removeQueryWrapper);
        //查找
        /*
        sql:
        select tu.* from t_user tu where tu.id = 1
         */
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("id",1);
        List<User> users = this.list(queryWrapper);
        /*
        sqk:
        select tu.name,tu.age from t_user tu where tu.name like '%L%'
        order by tu.id desc
         */
        QueryWrapper<User> queryWrapper2 = new QueryWrapper<>();
        queryWrapper2.select("name","age")
                .like("name","L")
                .orderByDesc("id");
    }
}
```


[MyBatis-Plus官方地址](https://mp.baomidou.com/)