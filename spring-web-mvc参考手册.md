# spring-web-mvc参考手册

Spring MVC与前端交互有两种形式，一种为传统的页面流形式，通过model将数据传递到模板中，填充模板后服务器端生成html页面返回前端；还有一种方式为RESTful方式，返回的是json等格式的数据。

## 页面流形式与前端交互

页面流形式主要是通过Mode对象传递参数到模板，数据填充到模板后生成html文件。

本例采用前端模版为thymeleaf模版，展示spring mvc通过model传递数据到模板，填充模板生成html文件。

```java
@Controller
@RequestMapping("/page")
public class ModelController {

    @RequestMapping(method = RequestMethod.GET)
    public String helloWorld(@RequestParam(value = "username", required = true) String username, Model model) {
        //model作为数据的载体，将数据传递到模板中
        model.addAttribute("username", username);
        //到templates目录下寻找/hello/hello模板，填充模板，生成html文件。
        return "/hello/hello";
    }
}
```

templates/hello/hello.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>学生管理页面</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />

    <link rel="stylesheet" th:href="@{bootstrap/css/bootstrap.min.css}" />
    <link rel="stylesheet" th:href="@{vendor/style.css}" />
    <script th:src="@{vendor/jquery.min.js}"></script>
</head>
<body>
    <span th:text="'用户姓名：' + ${username}"></span>
</body>
</html>
```

访问方式：[http://localhost:9999/api/hello?username=张三](http://localhost:9999/api/hello?username=张三), 这个过程model会将username传递到模板中，填充模板后，生成html文件展示出"用户姓名：张三"的html页面。

如果为其他模版，比如JSP模版，效果也是一样，到默认的页面路径下寻找对应名字的模板文件。

## forward关键字

适应场景：访问一个接口，转发到另一个接口上去。样例如下：

```java
@Controller
@RequestMapping(path = "api")
public class SpringmvcController {

    @RequestMapping(path = "/hello", method = RequestMethod.GET, produces = {"application/json", "text/plain"})
    @ResponseBody
    public String requestMappingHello() {
        return "hello spring-mvc";
    }

    @RequestMapping(path = "/world", method = RequestMethod.GET, produces = {"application/json", "text/plain"})
    public String requestMappingWorld() {
      //加入forward关键字：转发到requestMappingHello()方法上
      //注：foword：后的路径是相对路径，相对于根目录的路径
        return "forward:hello";
    }

}
```

访问地址：[localhost:9999/api/world](localhost:9999/api/world),访问requestMappingWorld()方法时，会转发到转发到requestMappingHello()方法，最终得到数据String类型的”hello spring-mvc“。

## RESTful定义api形式与前端交互

RESTful有两种方式定义api：

- @Controller与@ResponseBody组合使用
- RestController

@Controller与@ResponseBody组合使用方式：

```java
@Controller
@RequestMapping(path = "api")
public class RestfulController {

    @RequestMapping(value = "/student", method = RequestMethod.GET, produces =
            {MediaType.APPLICATION_JSON_VALUE })
    @ResponseBody
    public Student getStudent() {
        //返回的是json类型的Student实体数据
        return new Student("1","张三","18","男");
    }
}
```

@RestController方式：

```java
@RestController
@RequestMapping(path = "api")
public class RestfulController {

    @RequestMapping(value = "/student", method = RequestMethod.GET, produces =
                    {MediaType.APPLICATION_JSON_VALUE })
    public Student getStudent() {
        //返回的是json类型的Student实体数据
        return new Student("1","张三","18","男");
    }
}
```

访问地址为[http://localhost:9999/api/student](http://localhost:9999/api/student)。

## @RequestMapping详解

### RequestMapping的作用

Spring Web MVC通过@RequestMapping定义访问路径，请求方法，请求参数类型，响应参数类型等规则找到可以处理某个http请求的Controller的方法，这个过程称之为“路由”，@RequestMapping定义的规则称之为路由规则。

@RequestMapping可以定义在类级别上，也可以定义在方法级别上。样例如下：

```java
@RestController
//@RequestMapping定义在类级别上
@RequestMapping(path = "api")
public class RequestMappingController {
    //@RequestMapping定义在方法级别上
@RequestMapping(path = "/mapping", method = RequestMethod.GET)
    public String requestMapping() {
    //返回的是"hello spring-mvc"字符串
        return "hello spring-mvc";
    }
}
```

访问地址：[http://localhost:9999/api/mapping](http://localhost:9999/api/mapping)

### @RequestMapping定义规则

- 类级别上@RequestMapping的path的值与方法级别上@RequestMapping的path的值对应上才能找到具体的方法
- 请求路径定义  —— value属性或者path属性
- 请求方法 —— method属性
- 请求参数 —— params属性
- 请求头 —— headers属性
- 请求参数类型 —— consumes属性
- 响应参数类型 —— produces属性

### @RequestMapping下的属性详解

@RequestMapping中有`value`、`path`,`method`、`params`、`headers`、`consumes`、`produces`等参数,下面说明这些参数的含义。

### value, path,  method

  path : 指定请求的实体地址，指定的地址可以是URI Template模式，与value属性等价;

  value : 指定请求的实际地址，指定的地址可以是URI Template模式, 与path属性等价;

  method : 指定请求的method类型，如`GET`,`POST`,`PUT`,`DELETE` 等;

  value或者path的URI值为以下三类:

  A) 可以指定为普通的具体值；

  B)  可以指定为含有某变量的一类值(URI Template Patterns with Path Variables)；

```java
@RequestMapping(path="/student/{id}", method=RequestMethod.GET)
    @ResponseBody
    public Student findStudent(@PathVariable String id) {
        return new Student(id, "张三", "18","男");
    }
```

C) 可以指定为含正则表达式的一类值( URI Template Patterns with Regular Expressions);

```java
@RequestMapping("/spring-web/{symbolicName:[a-z-]+}-{version:\d\.\d\.\d}.{extension:\.[a-z]}")
public void handle(@PathVariable String version, @PathVariable String extension) {
    // ...
}
```

### consumes,  produces

  consumes：指定处理请求的提交内容类型(Content-Type)，例如application/json, text/html;

  produces：指定返回的内容类型，仅当http请求头中的(Accept)类型中包含该指定类型才返回, 类型可以指定多个。

  cousumes的样例：

```java
@RequestMapping(path = "/cousumes", method = RequestMethod.POST, consumes="application/json")
@ResponseBody
public Student addStudent(@RequestBody Student student) {
    return student;
}
```

方法仅处理Content-type为“application/json”类型的请求。

produces的样例：

```java
@RequestMapping(path = "/produces/{id}", method = RequestMethod.GET, produces ={"application/json", "text/plain"})
    public Student getStudent(@PathVariable("id") String id) {
        return new Student(id, "张三", "18","男");
}
```

方法仅处理http请求中Accept头中包含了"application/json"的请求，同时暗示了返回的内容类型为application/json;  produces中可以定义多个类型。

### params,  headers

  params： 指定request中必须包含某些指定的参数，才能让该方法处理请求;

  headers： 指定request中必须包含某些指定的header值，才能让该方法处理请求;

 params的样例：

```java
@RequestMapping(value = "/params/{id}", method = RequestMethod.GET, params="myParam=myValue")
public Student findStudentWithParam(@PathVariable("id") String id) {
     return new Student(id, "张三", "18","男");
}
```

 仅处理请求中包含了名为“myParam”，值为“myValue”的请求；

headers的样例：

```java
@RequestMapping(path = "/student", method = RequestMethod.GET,headers="Referer=http://www.baidu.com/")
  public void findStudent() {
    // implementation omitted
  }
```

  仅处理request的header中包含了指定“Referer”请求头和对应值为“`http://www.baidu.com/`”的请求；

## 参数绑定的注解

handler method 参数绑定常用的注解,我们根据他们处理Request的不同内容分为四类:

A、处理request uri 部分（这里指uri template中variable，不含queryString部分）的注解：@PathVariable;

B、处理request header部分的注解：@RequestHeader, @CookieValue;

C、处理request body部分的注解：@RequestParam,  @RequestBody;

D、处理attribute类型是注解：@SessionAttributes, @ModelAttribute;

注：参数绑定注解全都有defaultValue，required，如果需要参数绑定的值不为空，required必须设置为true，如果默认为空的情况下需要默认值，在dafaultValue中设置

### @PathVariable

当使用URI template 模型时， 即 someUrl/{paramId}, 这时的paramId可通过 @Pathvariable注解绑定它传过来的值到方法的参数上。

```java
@Controller
@RequestMapping("/api")
public class PathVariableController {

  @RequestMapping("/student/{id}")
  @ResponseBody
  public Student findStudent(@PathVariable("id") String id) {
       return new Student(id,"张三","18","男");
  }
}
```

上面代码把URI template 中变量id的值，绑定到方法的参数上。

若方法参数名称和需要绑定的uri template中变量名称不一致，需要在@PathVariable("name")指定uri template中的名称。

### @RequestHeader,  @CookieValue

  @RequestHeader 注解，可以把http请求header部分的值绑定到方法的参数上。

  示例代码：

  这是一个Request 的header部分：

```java
Host                    localhost:8080
Accept                  text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language         fr,en-gb;q=0.7,en;q=0.3
Accept-Encoding         gzip,deflate
Accept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7
Keep-Alive              300
```

```java
@RequestMapping("/displayHeaderInfo")
public void displayHeaderInfo(@RequestHeader("Accept-Encoding") String encoding,
                              @RequestHeader("Keep-Alive") long keepAlive)  {
  //...
}
```

上面的代码，把request header部分的 Accept-Encoding的值，绑定到参数encoding上了， Keep-Alive header的值绑定到参数keepAlive上。

 @CookieValue 可以把Request header中关于cookie的值绑定到方法的参数上。

例如有如下Cookie值：

```java
JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84
```

参数绑定的代码：

```java
@RequestMapping("/displayHeaderInfo")
public void displayHeaderInfo(@CookieValue("JSESSIONID") String cookie)  {
  //...
}
```

即把JSESSIONID的值绑定到参数cookie上。

### @RequestParam,  @RequestBody

@RequestParam

  A） 常用来处理简单类型的绑定，可以处理get或者post 方式中queryString的值，也可以处理post方式中 body data的值；

  B）用来处理Content-Type: 为 `application/x-www-form-urlencoded`编码的内容，提交方式POST；

  C) 该注解有两个属性： value、required； value用来指定要传入值的id名称，required用来指示参数是否必须绑定；

  示例代码：

```java
@Controller
@RequestMapping("/api")
public class RequestParamController {

    @RequestMapping(path = "/requestparam", method = RequestMethod.GET)
    @ResponseBody
    public Student setupStudent(@RequestParam("id") String id) {
        return new Student(id,"张三","18","男");
    }
}
```

@RequestBody

详见下文`RequestBody`。

## @SessionAttributes,  @ModelAttribute

@SessionAttributes

  该注解用来绑定HttpSession中的attribute对象的值，便于在方法中的参数里使用。

  该注解有value、types两个属性，可以通过名字和类型指定要使用的attribute对象；

  示例代码：

``` java
@Controller
@RequestMapping("/api")
public class StudentController {

    @RequestMapping(method = RequestMethod.GET)
    @ReponseBody
    public Student setupStudent(@RequestParam("id") int id, @SessionAttributes("studentId") String studentId) {
       return this.service.loadStudent(studentId);
    }
 }
```

@ModelAttribute

  该注解有两个用法，一个是用于方法上，一个是用于参数上；

  用于方法上时：  通常用来在处理@RequestMapping之前，为请求绑定需要从后台查询的model；

  用于参数上时： 用来通过名称对应，把相应名称的值绑定到注解的参数bean上；要绑定的值来源于：

  A） @SessionAttributes 启用的attribute 对象上；

  B） @ModelAttribute 用于方法上时指定的model对象；

  C） 上述两种情况都没有时，new一个需要绑定的bean对象，然后把request中按名称对应的方式把值绑定到bean中。

  用到方法上@ModelAttribute的示例代码：

```java
// Add one attribute
// The return value of the method is added to the model under the name "account"
// You can customize the name via @ModelAttribute("myAccount")

@ModelAttribute
public Account addAccount(@RequestParam String number) {
    return accountManager.findAccount(number);
}
```

这种方式实际的效果就是在调用@RequestMapping的方法之前，为request对象的model里put（“account”， Account）;

用在参数上的@ModelAttribute示例代码:

```java
@RequestMapping(path = "/student/{id}/edit", method = RequestMethod.POST)
public String saveStudent(@ModelAttribute Student student) {

}
```

## @RequestBody

该注解用于读取http请求的body部分数据，使用系统默认配置的HttpMessageConverter进行解析，然后把相应的数据绑定到要返回的对象上；再把HttpMessageConverter返回的对象数据绑定到 controller中方法的参数上。

使用时机：

A) GET、POST方式提时， 根据request header Content-Type的值来判断:

- application/x-www-form-urlencoded， 可选（即非必须，因为这种情况的数据@RequestParam, @ModelAttribute也可以处理，当然@RequestBody也能处理）；
- multipart/form-data, 不能处理（即使用@RequestBody不能处理这种格式的数据）；
- 其他格式， 必须（其他格式包括application/json, application/xml等。这些格式的数据，必须使用@RequestBody来处理）；

B) PUT方式提交时， 根据request header Content-Type的值来判断:

- application/x-www-form-urlencoded， 必须；
- multipart/form-data, 不能处理；
- 其他格式， 必须；

```java
@RequestMapping(path = "/evection", method = RequestMethod.POST, consumes = { MediaType.APPLICATION_JSON_VALUE }, produces = { MediaType.APPLICATION_JSON_VALUE })
@ResponseBody
public EvectionApply createEvection(@RequestBody EvectionApply evectionApply) {
    return evectionApplyService.create(evectionApply);
}
```

说明：request的body部分的数据编码格式由header部分的Content-Type指定；

### @ResponseBody

该注解用于将Controller的方法返回的对象，通过适当的HttpMessageConverter转换为指定格式后，写入到Response对象的body数据区。

使用时机：

返回的数据不是html标签的页面，而是其他某种格式的数据时（如json、xml等）使用；

## 分页查询

分页查询使用Pageable对象存储分页信息，使用`@PageableDefault` 注解设置分页信息，`@PageableDefault` 中有三个属性：

- page —— 表示当前页
- size —— 表示每页显示的条数
- sort —— 设置排序

样例如下：

```java
@RequestMapping(path = "/list", method = RequestMethod.GET, produces = "application/json")
    @ResponseBody
    public List<Student> getStudentList( @PageableDefault(page = 0, size = 15) Pageable pageable) {
        return new ArrayList<Student>();
    }
```

## form表单提交

form表单提交的api示例：

```java
@Controller
@RequestMapping(path = "api")
public class FormController {
    @RequestMapping(path = "/form", method = RequestMethod.POST， produces = "application/json")
    @ResponseBody
    public Student saveStudent(Student student){
        return student;
    }
}
```

templates/student.html

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>学生管理页面</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<form method="POST" enctype="application/x-www-form-urlencoded" action="api/form" th:object="${student}>
    <div>
        <table>
            <tr>
                <th>id</th>
                <th>姓名</th>
                <th>年龄</th>
                <th>性别</th>
            </tr>
            <tr>
                <td><input type="text" name="id" value="" th:field="*{id}"/></td>
                <td><input type="text" name="name" value="" th:field="*{name}"/></td>
                <td><input type="text" name="age" value="" th:field="*{age}"/></td>
                <td><input type="text" name="sex" value="" th:field="*{sex}"/></td>
            </tr>
        </table>
    </div>
    <div class="alt-button">
        <buttontype="submit">保存</button>
    </div>
</form>
</body>
</html>
```

点击页面保存按钮，提交表单访问api：api/form，后端处理数据。

## 上传文件

上传文件Content-type为multipart/form-data，使用`@RequestParam MultipartFile file`接收需要上传的文件 。

样例如下：

```java
  @RequestMapping(value = "/upload")
    @ResponseBody
    public String upload(@RequestParam MultipartFile file) throws IOException {
        InputStream fileStream = file.getInputStream();
        long size = file.getSize();
        logger.info("文件大小为：" + size);
        return String.valueOf(size);
    }
```

templates/upload.html

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>文件导入</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
    <form method="POST" enctype="multipart/form-data" action="/upload">
        <div>
            <input type="file" name="file" class="file" data-preview-file-type="text" />
            <button type="submit">导入</button>
        </div>
    </form>
</body>
</html>
```

## 补充：

使用@ResponseBody注解返回的对象传入Object参数内。若返回的对象为已经格式化好的json串时，不使用@RequestBody注解，而应该这样处理：

 1、response.setContentType("application/json; charset=UTF-8");

 2、response.getWriter().print(jsonStr);

 直接输出到body区，然后视图为void。

样例如下:

```java
@RequestMapping(path = "/json", method = { RequestMethod.GET })
    public void getStudent(@RequestParam("id") String id, HttpServletResponse response) {
        Student student = this.service.getStudent(id);
        ObjectMapper mapper = new ObjectMapper();
        String json = mapper.writeValueAsString(student);

        response.setContentType("application/json; charset=UTF-8");
    response.getWriter().print(json);
}
```
