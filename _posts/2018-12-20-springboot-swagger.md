---
layout:     post
title:      springboot集成swagger
subtitle:   springboot项目笔记
date:       2018-12-20
author:     LJQ
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - springboot
    - springboot项目笔记
---

#厌倦手码API文档？试试swagger吧
>项目开发中，API 文档是很重要的一环，好的API文档编写习惯可以是前后端交互时节约大量时间，当API过多时，写API文档无疑是一件费时费力的事，**swagger**就是一款可以帮助各位快速、优雅生成API文档的一款框架。本文将基于**springboot**项目集成**swagger2**框架。
##项目结构
![2018-12-20-springboot-swagger-项目结构](https://upload-images.jianshu.io/upload_images/15504753-7eb2be6c54432ebb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##依赖导入
```
        <!--swagger2 start-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <!--  bootstrap风格swagger2 ui资源 -->
        <!--
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <artifactId>swagger-bootstrap-ui</artifactId>
            <version>1.8.6</version>
        </dependency>
        -->
        <!--  原生swagger2 ui资源 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
        <!--swagger2 end-->
```
##Controller层
```
@RestController
@RequestMapping("/test")
@Api(tags = "测试接口")
public class TestController {

    @PostMapping
    @ApiOperation(value = "post方式接口")
    @ApiResponses(value = {
            @ApiResponse( code = 200 ,message = "success",response = com.ljq.swagger_demo.vo.TestVo.class)
    })
    public TestVo test1(@RequestBody TestVo testVo){
        return testVo;
    }

    @GetMapping
    @ApiOperation(value = "get方式接口")
    @ApiImplicitParams(value = {
            @ApiImplicitParam(name = "name",value = "字符型",paramType = "query",dataType = "String"),
            @ApiImplicitParam(name = "amount",value = "浮点型",paramType = "query",dataType = "Float")
    })
    @ApiResponses(value = {
            @ApiResponse( code = 200 ,message = "success",response = com.ljq.swagger_demo.vo.TestVo.class)
    })
    public TestVo test2(@RequestParam("name")String name,
                        @RequestParam("amount")Float amount){
        return new TestVo(name,amount);
    }
}

```
##Vo层
```
@Data
@AllArgsConstructor
@NoArgsConstructor
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@ApiModel(description= "测试实体类")
public class TestVo {

    @ApiModelProperty(value = "字符型")
    private String name;

    @ApiModelProperty(value = "浮点型")
    private Float amount;

}
```
##Config类
```
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket apiDocket() {

        return new Docket(DocumentationType.SWAGGER_2)
            .apiInfo(apiInfo())
            .select()
            .apis(RequestHandlerSelectors.withClassAnnotation(RestController.class))
            .paths(PathSelectors.any())
            .build();
    }

    @Bean
    public UiConfiguration uiConfiguration() {

        return UiConfigurationBuilder.builder()
            .deepLinking(true)
            .defaultModelExpandDepth(1)
            .validatorUrl("")
            .displayOperationId(true)
            .displayRequestDuration(true)
            .tagsSorter(TagsSorter.of("release"))
            .showExtensions(true)
            .build();
    }

    private ApiInfo apiInfo() {

        return new ApiInfoBuilder()
            .title("在线api")
            .description("RESTful API")
            .version("1.0")
            .build();
    }
}
```
##访问API地址
>在浏览器输入```ip:端口号/swagger-ui.html```
####运行效果
![2018-12-20-springboot-swagger-运行效果](https://upload-images.jianshu.io/upload_images/15504753-81607d46fc7343df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##项目源码地址
[https://github.com/LiJinQ/SwaggerDemo.git](https://github.com/LiJinQ/SwaggerDemo.git)




