# 快速搭建一个WebFlux应用

## start.spring.io 在线生成

在[start.spring.io](start.spring.io)中在线生成一个项目。生成项目时的各项参数设置为：

- Generate a `Maven Project`
- with `Java`
- and Spring Boot `2.1.2`(or newer)
- Project Metadata (you may change to your own project coordinate)
  - Group: `com.example`
  - artifactId: `demo`
- Dependencies: `Reactive Web`

## 编写处理器类 Handler

**com/example/demo/hander/CityHandler.java**

```java
import org.springframework.http.MediaType;
import org.springframework.stereotype.Component;
import org.springframework.web.reactive.function.BodyInserters;
import org.springframework.web.reactive.function.server.ServerRequest;
import org.springframework.web.reactive.function.server.ServerResponse;
import reactor.core.publisher.Mono;

@Component
public class CityHandler {

    public Mono<ServerResponse> helloCity(ServerRequest request) {
        return ServerResponse.ok().contentType(MediaType.TEXT_PLAIN)
                .body(BodyInserters.fromObject("Hello, City!"));
    }
}
```
## 编写路由器类 Router

**com/example/demo/router/CityRouter.java**

```java
import org.spring.springboot.handler.CityHandler;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.MediaType;
import org.springframework.web.reactive.function.server.RequestPredicates;
import org.springframework.web.reactive.function.server.RouterFunction;
import org.springframework.web.reactive.function.server.RouterFunctions;
import org.springframework.web.reactive.function.server.ServerResponse;

@Configuration
public class CityRouter {


    @Bean
    public RouterFunction<ServerResponse> routeCity(CityHandler cityHandler) {
        return RouterFunctions
                .route(RequestPredicates.GET("/hello")
                                .and(RequestPredicates.accept(MediaType.TEXT_PLAIN)),
                        cityHandler::helloCity);
    }

}
```

## 启动运行项目

使用Maven工具执行`mvn clean install`，或者使用其他IDE中Maven的`install`命令执行安装，然后执行 **com/example/demo/DemoApplication.java** 启动应用程序。之后就可以访问[localhost:8080/hello]()得到预期结果。

# WebFlux Web CRUD

## 编写实体对象类

**com/example/demo/domain/City.java**

```java
/**
 * 城市实体类
 *
 */
public class City {

    /**
     * 城市编号
     */
    private Long id;

    /**
     * 省份编号
     */
    private Long provinceId;

    /**
     * 城市名称
     */
    private String cityName;

    /**
     * 描述
     */
    private String description;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public Long getProvinceId() {
        return provinceId;
    }

    public void setProvinceId(Long provinceId) {
        this.provinceId = provinceId;
    }

    public String getCityName() {
        return cityName;
    }

    public void setCityName(String cityName) {
        this.cityName = cityName;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}
```



