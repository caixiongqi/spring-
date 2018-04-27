# RestTemplate参考手册

RestTemplate作为http客户端调用Rest风格的接口。

## 集成RestTemplate

### 普通项目集成RestTemplate

依赖的jar包

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.0.5.RELEASE</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>4.0.5.RELEASE</version>
    <scope>provided</scope>
</dependency>
```

- 使用RestTemplate需要spring-web与spring-web两个jar包
- spring版本需要4.0以上版本， spring 4.0 +

### spring boot项目集成RestTemplate

依赖的jar包

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>1.5.4.RELEASE</version>
</dependency>
```

注入RestTemplate的Bean

```java
@Configuration
public static class SpringConfig {
    @Bean public RestTemplate getRestTemplate() {
      return new RestTemplate();
    }
}
```

说明，此种方式注入RestTemplate的bean后，可以在项目中直接通过@Autowired使用，不用再次创建一个RestTemplate对象

## Quick start

```java
RestTemplate restTemplate = new RestTemplate();
// 使用RestTemplate客户端访问"http://localhost:8080/resttemplate/hello/{param}"
String responseData = restTemplate.getForObject("http://localhost:8080/resttemplate/hello/{param}", String.class, param);
```

创建`RestTempalte`对象，使用方法restTemplate.getForObject(String url, Class responseType, Object... uriVariables)方法访问[http://localhost:8080/resttemplate/hello/{param}](http://localhost:8080/resttemplate/hello/{param}),。其中三个参数为：rest接口访问路径，响应体数据类型，url路径上的参数。

## 常用方法

- getForObject(String, Class, String...)
- postForObject(String, Object, String...)
- put(String, Object, String...)
- delete(String, String...)
- headForHeaders(String, String...)
- optionsForAllow(String, String...)

这些方法的名称清楚地表明，他们调用哪个HTTP方法，而这个名字的第二部分指示返回什么。例如，`getForObject（）`将执行一个`GET`，转换HTTP响应到您选择的对象类型，并返回该对象

## GET请求

### 返回值为具体的bean：

```java
//被调用接口
@RequestMapping(path = "/{userid}", method = RequestMethod.GET, produces = {MediaType.APPLICATION_JSON_VALUE})
    public User getUser(@PathVariable("userid") String userId,
                        @RequestParam("username") String userName) {
        return sevice.getUser(userId, userName);
    }
```

```java
public User getUser(String userId, String userName) {
        return restTemplate.getForObject("http://localhost:8080/resttemplate/{param}?username={username}", User.class, userId, userName);
    }
```

执行方法为`public <T> T getForObject(String url, Class<T> responseType, Object... uriVariables)`,返回User实体。

### 返回值为`List<Object>`

```java
//被调用接口
@RequestMapping(path = "/user/list", method = RequestMethod.GET, produces = {MediaType.APPLICATION_JSON_VALUE})
public List<User> getUserList() {
      return service.getUserList();
}
```

```java
public List<User> getUserList() {
 String userListJson = restTemplate.getForObject("http://localhost:8080/user/list", String.class);
 ObjectMapper mapper = new ObjectMapper();
 return mapper.readValue(userListJson, mapper.getTypeFactory().constructParametricType(ArrayList.class, User.class))
}
```

执行方法getForObject（）方法，获取到userList的json串，通过ObjectMapper将json串转换为`List<User>`数据。

## post请求

```java
//被调用接口
@RequestMapping(path = "/user/save", method = RequestMethod.POST, consumes = { MediaType.APPLICATION_JSON_VALUE }, produces = { MediaType.APPLICATION_JSON_VALUE })
@ResponseBody
public User createUser(
      @RequestBody User user) {
   return service.save(user);
}
```

```java
public User createUser(User user) {
    restTemplate.postForObject("http://localhost:8080/user/save", user, User.class);
}
```

post请求一般都有请求体，如`@RequestBody`。执行postForObject(String url, Object requstBody, Class T)方法，访问post接口[http://localhost:8080/user/save](http://localhost:8080/user/save)。

### 上传文件

```java
/**
* 接口场景为：页面两个文件选择框，上传文件
* 接口作用为: 上传多个文件，得到数据集合
*/
@RequestMapping(value = "/salaryList", method = RequestMethod.POST, produces = { MediaType.APPLICATION_JSON_VALUE })
  @ResponseBody
  public List<Salary> getSalaryList(List<MultipartFile> caogaoFiles, List<MultipartFile> shifaFiles, @RequestParam(name = "date", required = true) String date) throws IOException {
    return salarySyncService.extraSalaryInfoFrom(date, caogaoFiles, shifaFiles);
  }
```

此接口为被调用接口。

```java
private FileSystemResource getSalaryFileResource(MultipartFile salaryFile) throws IOException {
    File salaryFileTemp = File.createTempFile(UUID.randomUUID().toString(), "." + salaryFile.getOriginalFilename());
    salaryFile.transferTo(salaryFileTemp);
    return new FileSystemResource(salaryFileTemp);
}
```

此方法提供将上传文件转换为缓存文件`FileSystemResource`。

```java
 public List<Salary> getSalaryList(List<MultipartFile> caogaoFiles, List<MultipartFile> shifaFiles, String date) throws IOException {
        MultiValueMap<String, Object> param = new LinkedMultiValueMap<>();
   //将caogaoFiles放入param集合中
        for (MultipartFile caogaoFile : caogaoFiles) {
            FileSystemResource salaryFileResource = getSalaryFileResource(caogaoFile);
            param.add("caogaoFiles", salaryFileResource);
        }
  //将shifaFiles放下param集合中
        for (MultipartFile shifaFile : shifaFiles) {
            FileSystemResource file = getSalaryFileResource(shifaFile);
            param.add("shifaFiles", file);
        }
        param.add("date", date);
        ObjectMapper mapper = new ObjectMapper();
  //调用上面接口，得到String类型的数据
     String jsonData = restTemplate.postForObject("http://localhost:8080/salaryList", param, String.class);
  //将接收到的数据json串转换为java实体集合List<Salary>
        return mapper.readValue(jsonData, mapper.getTypeFactory().constructParametricType(ArrayList.class, Salary.class));
    }
```

此方法调用上面的接口，上传文件`MultipartFile`,通过`MultiValueMap`集合，包装上传文件。传到请求体中，访问上传文件的post接口。

## RestTemplate设置底层连接方式

### 使用场景：当需要设置`连接超时时间`、`请求超时时间`、`异常最大重试次数`等的时候

RestTemplate底层实现：

- 调用默认的无参数构造函数，这将使用`java.net`包中的标准Java类作为底层实现来创建HTTP请求。
- 使用HttpClient作为底层实现

基于需要设置`连接超时`等参数，选择第二种实现方式。

```java
//生成一个设置了连接超时时间、请求超时时间、异常最大重试次数的httpClient
        RequestConfig config = RequestConfig.custom().setConnectionRequestTimeout(10000).setConnectTimeout(10000).setSocketTimeout(30000).build();
        HttpClientBuilder builder = HttpClientBuilder.create().setDefaultRequestConfig(config).setRetryHandler(new DefaultHttpRequestRetryHandler(5, false));
        HttpClient httpClient = builder.build();
        //使用httpClient创建一个ClientHttpRequestFactory的实现
        ClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory(httpClient);
         //ClientHttpRequestFactory作为参数构造一个使用作为底层的RestTemplate
        RestTemplate restTemplate = new RestTemplate(requestFactory);
```
