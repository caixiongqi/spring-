# Spring Session参考手册

## 集成步骤

* 在项目的build.gradle中添加依赖

```java
    compile('org.springframework.session:spring-session-data-redis:1.3.0.RELEASE')
    compile('org.springframework.session:spring-session:1.3.1.RELEASE')
```


* 在项目的application.yaml添加配置

     开发测试阶段的配置：

```groovy
    spring:
      redis:
          host: localhost
          port: 6379
      session:
          store-type: redis
```

        正式环境的配置：

```groovy
    spring:
      redis:
        host: inneroa-redis
        port: 6379
```

* 在项目的启动类中添加注释

```java
    @EnableRedisHttpSession
```

## 例子

```java
        package org.exam.web;
        import org.springframework.stereotype.Controller;
        import org.springframework.ui.Model;
        import org.springframework.web.bind.annotation.RequestMapping;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpSession;

        @Controller
        public class DefaultController {
            @RequestMapping("/")
            public String index(Model model,HttpServletRequest request,String action,String msg){
                HttpSession session=request.getSession();
                if ("set".equals(action)){
                    session.setAttribute("msg", msg);
                }else if ("get".equals(action)){
                    String message=(String)session.getAttribute("msg");
                    model.addAttribute("msgFromRedis",message);
                }
                return "index";
            }
        }
```


## redis安装使用

redis是spring session存储数据的一个容器。需要下载安装使用。[redis官网下载地址](http://www.redis.cn/download.html).

redis-server\redis-server是应用启动文件，双击文件，即可启动redis。

redis-server\redis-cli是测试用文件，双击文件，打开测试窗口。

可用`APPEND key value`命令添加键值对到redis中，用`keys *`命令查看redis中已有的key。[查看redis命令](http://www.redis.cn/commands.html).
