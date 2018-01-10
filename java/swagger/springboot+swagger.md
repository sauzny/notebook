# springboot+swagger

在spring boot的启动类上增加注释`@EnableSwagger2`，启动swagger

访问页面：http://your_path/swagger-ui.html

以下为依赖文件，和controller的举例

## 一、jar

```
"io.springfox:springfox-swagger2:2.7.0",
"io.springfox:springfox-swagger-ui:2.7.0",
```

## 二、config

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;

@Configuration
public class Swagger2 {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("你的controller所在包路径"))
                .paths(PathSelectors.any())
                .build();
    }
    
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("swagger的api文档")
                .description("项目地址：xxxxxxxxxxxxxxxxx")
                .termsOfServiceUrl("xxxxxxxxxxxxxxxxx")
                .version("1.0")
                .build();
    }
}

```

## 三、controller

```java
import java.util.Arrays;
import java.util.List;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;


import cn.gbase.bi.HardCode;
import cn.gbase.bi.web.vo.AjaxDoneResult;
import cn.gbase.bi.web.vo.Swagger;
import cn.gbase.bi.web.vo.SwaggerContent;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiImplicitParam;
import io.swagger.annotations.ApiImplicitParams;
import io.swagger.annotations.ApiOperation;
import io.swagger.annotations.ApiResponse;
import io.swagger.annotations.ApiResponses;

@Api("SwaggerController相关api")
@RestController
@RequestMapping(value="/swaggers")
public class SwaggerController {

    private static final Logger logger = LoggerFactory.getLogger(SwaggerController.class);
    
    // 查询所有
    @ApiOperation(value="查询所有", notes="没有参数，列出所有的模型的meta信息")
    @ApiResponses({@ApiResponse(code = 400, message = "请求参数没填好"), @ApiResponse(code = 404, message = "请求路径没有或页面跳转路径不对")})
    @ResponseBody
    @RequestMapping(value="", method={RequestMethod.GET})
    public List<Swagger> swaggers() {
        return HardCode.swaggerList();
    }
    
    // 查询 指定 id ，返回多条记录
    @ApiOperation(value="获取模型的meta信息", notes="根据url的id数组来获取模型的meta信息")
    @ApiResponses({@ApiResponse(code = 400, message = "请求参数没填好"), @ApiResponse(code = 404, message = "请求路径没有或页面跳转路径不对")})
    @ApiImplicitParam(name = "ids", value = "模型id数组", required = true, dataType = "array", paramType = "path")
    @ResponseBody
    @RequestMapping(value="/{ids}", method={RequestMethod.GET})
    public List<Swagger> swaggers(@PathVariable String[] ids) {
        logger.debug(Arrays.asList(ids).toString());
        return HardCode.swaggerList();
    }
    
    // 查询 指定id 返回一条
    @ApiOperation(value="获取模型的meta信息", notes="根据url的id来获取模型的meta信息")
    @ApiResponses({@ApiResponse(code = 400, message = "请求参数没填好"), @ApiResponse(code = 404, message = "请求路径没有或页面跳转路径不对")})
    @ApiImplicitParam(name = "id", value = "模型id", required = true, dataType = "string", paramType = "path")
    @ResponseBody
    @RequestMapping(value="/{id}", method={RequestMethod.GET})
    public Swagger swagger(@PathVariable String id) {
        logger.debug(id);
        return HardCode.swagger(id);
    }

    // 查询 指定id 中的内容
    @ApiOperation(value="获取模型的内容", notes="根据url的id来获取模型的内容")
    @ApiResponses({@ApiResponse(code = 400, message = "请求参数没填好"), @ApiResponse(code = 404, message = "请求路径没有或页面跳转路径不对")})
    @ApiImplicitParam(name = "id", value = "模型id", required = true, dataType = "string", paramType = "path")
    @ResponseBody
    @RequestMapping(value="/{id}/content", method={RequestMethod.GET})
    public SwaggerContent swaggerContent(@PathVariable String id) {
        logger.debug(id);
        return HardCode.swaggerContent(id);
    }
    
    // 删除
    @ApiOperation(value="删除模型", notes="根据url的id来删除模型")
    @ApiResponses({
        @ApiResponse(code = 200, message = "Successful — 请求已完成"),
        @ApiResponse(code = 400, message = "请求中有语法问题，或不能满足请求"),
        @ApiResponse(code = 401, message = "未授权客户机访问数据"),
        @ApiResponse(code = 404, message = "服务器找不到给定的资源；文档不存在"),
        @ApiResponse(code = 500, message = "服务器不能完成请求")})
    @ApiImplicitParam(name = "id", value = "模型id", required = true, dataType = "string", paramType = "path")
    @ResponseBody
    @RequestMapping(value="/{id}", method={RequestMethod.DELETE})
    public AjaxDoneResult swaggerDelete(@PathVariable String id){
        logger.debug(id);
        return AjaxDoneResult.success();
    }

    // 修改
    @ApiOperation(value="修改模型", notes="根据url的id来修改模型")
    @ApiResponses({@ApiResponse(code = 400, message = "请求参数没填好"), @ApiResponse(code = 404, message = "请求路径没有或页面跳转路径不对")})
    @ApiImplicitParams({
        @ApiImplicitParam(name = "id", value = "模型id", required = true, dataType = "string", paramType = "path"),
        @ApiImplicitParam(name = "swagger", value = "模型", required = true, dataType = "Swagger")
    })
    @ResponseBody
    @RequestMapping(value="/{id}", method={RequestMethod.PUT})
    public AjaxDoneResult swaggerPut(@PathVariable String id, @RequestBody Swagger swagger){
        logger.debug(id);
        logger.debug(swagger.toString());
        return AjaxDoneResult.success();
    }
    
    // 增加
    @ApiOperation(value="增加模型", notes="增加模型")
    @ApiResponses({@ApiResponse(code = 400, message = "请求参数没填好"), @ApiResponse(code = 404, message = "请求路径没有或页面跳转路径不对")})
    @ApiImplicitParams({
        @ApiImplicitParam(name = "swagger", value = "模型", required = true, dataType = "Swagger")
    })
    @ResponseBody
    @RequestMapping(value="", method={RequestMethod.POST})
    public AjaxDoneResult swaggerPut(@RequestBody Swagger swagger){
        logger.debug(swagger.toString());
        return AjaxDoneResult.success();
    }
}

```
