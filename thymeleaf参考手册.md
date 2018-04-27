# thymeleaf 参考手册

## 1、依赖包添加

```java
compile("org.springframework.boot:spring-boot-starter-thymeleaf")
```

另外，需要在html文件中需添加html标签:

```html
<html xmlns:th="http://www.thymeleaf.org">
```

在application.yml中添加:

```javascript
#thymeleaf start
  thymeleaf:
    mode: HTML5
    encoding: UTF-8
    content-type: text/html
    #开发时关闭缓存,不然没法看到实时页面
    cache: false
#thymeleaf end
```

或者application.properties中添加:

```javascript
#thymeleaf start
  spring.thymeleaf.mode=HTML5
  spring.thymeleaf.encoding=UTF-8
  spring.thymeleaf.content-type=text/html
  #开发时关闭缓存,不然没法看到实时页面
  spring.thymeleaf.cache=false
#thymeleaf end
```

详细说明可查阅[官网文档](http://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html)。

## 2、表达式用法

### 2.1 简单的表达式

* 变量表达式： `${...}`；

```html
<p>Today is: <span th:text="${today}">13 february 2011</span>.</p>
```

其中，“13 february 2011”只是对`today`变量的说明或例子，不管`today`的值是否为空，`today`都不会被其替换掉。若要使用默认表达式，可用：

```html
<p>Today is: <span th:text="(${test} == null)? 'test为空的默认值' :'test不为空的默认值'">13 february 2011</span>.</p>
```

* 选择变量表达式：`*{...}`；

```html
<div th:object="${session.user}">
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
</div>
```

等价于：

```html
<div>
  <p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p>
  <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
  <p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p>
</div>
```

* 消息变量表达式：`#{...}`;

  1.使用`#`表示消息变量同OGNL表达方式。

  2.使用`#`表达基本对象

   -- #ctx：上下文对象。

   -- #vars: 上下文变量。

   -- \#locale：上下文的语言环境。

   -- #request（仅在web上下文）的HttpServletRequest对象。

   -- #response（仅在web上下文）的HttpServletResponse对象。

   -- #session（仅在web上下文）的HttpSession对象。

   -- \#servletContext（仅在web上下文）的ServletContext对象。

  **注意：** 请求参数param、session属性、以及application全局属性在引用的时候，可以省略`#`符号。

  ​3.使用`#`引用工具类的方法

```JavaScript
${#dates.format(date, 'yyyy/MM/dd')}
${#strings.isEmpty(name)}
${#lists.size(list)}
```

更多工具类的用法可查阅文档后面的附录部分。

* 链接URL表达式：`@{...}` ；

```html
<p>Please select an option</p>
<ol>
 <li><a href="user.html" th:href="@{/user}">本地uer页面</a></li>
 <li><a th:href="@{//www.baidu.com}">去百度</a></li>
</ol>
```

相对于页面的url：`user/login.html`

相对于上下文的url：`/itemdetails?id=3`

相对于服务器的url： `~/billing/processInvoic`

相对于协议类的url：`//code.jquery.com/jquery-2.0.3.min.js`

含有路径参数的url：`@{/order/{orderId}/details(orderId=${orderId})}`

* 片段表达式：`~{...}`。

片段表达式用来表示标记片段和移动它们周围模板的简便方法。这使我们能够复制它们，将它们传递到其他模板作为参数，等等。常见的用途是用做片段的插入th:insert或th:replace（在后面的章节有片段定义及引用的例子）：

```html
<div th:insert="~{commons :: main}">...</div>
```

或：

```html
<div th:replace="~{commons :: main}">...</div>
```

### 2.2 文本表达式

文字：'one text'，'Anotherone!'，... ；

数字：0，34，3.0，12.3，...；

布尔文字：true，false；

null文本： null；

文字token：one，sometext，main，...；

文字token指的是由字母（A-Z和a-z），数字（0-9），方括号（[ ]），点（.），连字符（-）和下划线（_）等组成的字符串，并且没有空格，没有标点符号。

### 2.3 文本操作

字符串连接： +

文字替换：` |The name is ${name}|`

```html
<span th:text="|Welcome to our application, ${user.name}!|"></span>
```

等价于：

```html
<span th:text="'The name of the user is ' + ${user.name}"></span>
```

### 2.4 算术运算

二元运算符：+，-，*，/，% 

```html
<td th:text="${salary1.preTaxSalary/100}"></td>
```

取负（一元运算符）：-；

### 2.5 布尔操作

二元运算符：and，or；

 布尔否定（一元运算符）：!，not；

### 2.6 比较和相等

比较：>，<，>=，<=（gt，lt，ge，le）；

```html
<div th:if="${prodStat.count} &gt; 1">
```

等价于：

```html
<div th:if="${prodStat.count} > 1">
```

也等价于：

```html
<div th:if="${prodStat.count} gt 1">
```

相等运算符：==，!=（eq，ne）；

### 2.7 条件表达式

如果- 则：( if) ? (then )

如果- 则 - 否则：( if) ? (then) : (else ) 默认： ( value) ?:(defaultvalue )

```html
<div th:if="${prodStat.count} &gt; 1">
<span th:text="'Execution mode is ' + ( (${execMode} == 'dev')? 'Development' : 'Production')">
</span>
</div>
```

### 2.8 特殊符号：无操作：_ 。

所有这些运算可以被组合并嵌套。

```html
<div th:if="${prodStat.count} &gt; 1">
<span th:text="'Execution mode is ' + ( (${execMode} == 'dev')? 'Development' : 'Production')">
</span>
</div>
```

## 3、迭代用法 th:each

```html
<table>
  <tr>
    <th>INDEX</th>
    <th>NAME</th>
    <th>PRICE</th>
    <th>INSTOCK</th>
  </tr>
  <tr th:each="prod, index: ${prods}">
    <td th:text="${index.index}">1</td>
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}?'true' : 'false'">yes</td>
  </tr>
</table>  
```

## 4、分支用法th:case

``` html
<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="'manager'">User is a manager</p>
  <p th:case="*">User is some other thing</p>
</div>
```

## 5、条件用法th:if

```html
<div th:if="${test} > 1">
    <p>Today is: <span th:text=" (${test} == null)? '' :'test'">13 february 2011</span>.</p>
</div>
```

## 6、片段表达式用法

有些组件如页脚、标题等组件经常在多个页面中使用，为了能够代码复用，我们可以在/WEB-INF/templates

目录下定义一个模板，以供其他地方引用。下面举个例子：文件目录为/WEB-INF/templates/footer.html，具体代码如下。

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <body>  
    <div th:fragment="copy">
      &copy; 2011 The Good Thymes Virtual Grocery
    </div>  
     <div th:fragment="name">
      &copy; 2011 The Good Thymes Virtual Grocery - name
    </div>  
  </body>  
</html>
```

这就分别定义了一个名为copy和一个名为name的代码片段。可以参考下面方式引用它们。

```html
<body>
  ...
  <div th:insert="~{footer :: copy}"></div>  
  <div th:replace="~{footer :: name}"></div>  
</body>
```

如果想使代码片段的参数可定制化，可以参考以下方式。

```html
<div th:fragment="frag (onevar,twovar)">
    <p th:text="${onevar} + ' - ' + ${twovar}">...</p>
</div>
```

## 7、fragment的layout

### 7.1 配置

如果要引入fragment的layout，需要添加依赖：

```java
compile('nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect:1.2.1')
```

### 7.2 layout的引用

定义一个layout文件，目录为`/WEB-INF/views/task/layout.html`，内容如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <!--/*  Each token will be replaced by their respective titles in the resulting page. */-->
    <title layout:title-pattern="$DECORATOR_TITLE - $CONTENT_TITLE">Task List</title>
    ...
  </head>
  <body>
    <!--/* Standard layout can be mixed with Layout Dialect */-->
    <div th:replace="fragments/header :: header">
      ...
    </div>
    <div class="container">
      <div layout:fragment="content">
        <!-- ============================================================================ -->
        <!-- This content is only used for static prototyping purposes (natural templates)-->
        <!-- and is therefore entirely optional, as this markup fragment will be included -->
        <!-- from "fragments/header.html" at runtime.                                     -->
        <!-- ============================================================================ -->
        <h1>Static content for prototyping purposes only</h1>
        <p>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit.
          Praesent scelerisque neque neque, ac elementum quam dignissim interdum.
          Phasellus et placerat elit. Lorem ipsum dolor sit amet, consectetur adipiscing elit.
          Praesent scelerisque neque neque, ac elementum quam dignissim interdum.
          Phasellus et placerat elit.
        </p>
      </div>
      <div th:replace="fragments/footer :: footer">&copy; 2014 The Static Templates</div>
    </div>
  </body>
</html>
```

上面文件的layout核心配置代码为`layout:fragment="content"`。在目录`WEB-INF/views/task/list.html`

下创建内容页面，代码如下：

```html
<!DOCTYPE html>
<html layout:decorator="task/layout">
  <head>
    <title>Task List</title>
    ...
  </head>
  <body>
    <!-- /* Content of this page will be decorated by the elements of layout.html (task/layout) */ -->
    <div layout:fragment="content">
      <table class="table table-bordered table-striped">
        <thead>
          <tr>
            <td>ID</td>
            <td>Title</td>
            <td>Text</td>
            <td>Due to</td>
          </tr>
        </thead>
        <tbody>
          <tr th:if="${tasks.empty}">
            <td colspan="4">No tasks</td>
          </tr>
          <tr th:each="task : ${tasks}">
            <td th:text="${task.id}">1</td>
            <td><a href="view.html" th:href="@{'/' + ${task.id}}" th:text="${task.title}">Title 				...</a></td>
            <td th:text="${task.text}">Text ...</td>
            <td th:text="${#calendars.format(task.dueTo)}">July 11, 2012 2:17:16 PM CDT</td>
          </tr>
        </tbody>
      </table>
    </div>
  </body>
</html>
```

页面`task/list`的内容由`task/layout`的标签配置布局。注意在`<html>`标签中的属性 `layout:decorator="task/layout"`。该属性会被标记到Layout Dialect，而Layout Dialect

就是layout用来给指定视图配置布局用的。

### 7.3 在Layout Dialect中用include方式配置布局

下面为复用alert片段的例子，其目录为`layout:fragment` (`task/alert.html`)，代码如下：

```html
<!DOCTYPE html>
<html>
  <body>
    <th:block layout:fragment="alert">
      <div class="alert alert-dismissable" th:classappend="'alert-' + ${type}">
        <button type="button" class="close" data-dismiss="alert" aria-hidden="true">&times </button>
        <h4 th:text="${header}">Alert header</h4>
        <!--/* 'layout:fragment' attribute defines a replaceable content section */-->
        <th:block layout:fragment="alert-content">
          <p>Default alert content</p>
        </th:block>
      </div>
    </th:block>
  </body>
</html>
```

若要引用上面的片段，可用下面方式：

```html
<div layout:include="task/alert :: alert" th:with="type='info', header='Info'" th:remove="tag">
  <!--/* Implements alert content fragment with simple content */-->
  <th:block layout:fragment="alert-content">
    <p><em>This is a simple list of tasks!</em></p>
  </th:block>
</div>
```

也可以这样引用：

```html
<div layout:include="task/alert :: alert" th:with="type='danger', header='Oh snap!'" th:remove="tag">
  <!--/* Implements alert content fragment with full-blown HTML content */-->
  <th:block layout:fragment="alert-content">
    <p>Some text</p>
    <p>
      <button type="button" class="btn btn-danger">Take this action</button>
      <button type="button" class="btn btn-default">Or do this</button>
    </p>
  </th:block>
</div>
```



## 附录（使用`#`引用工具类的方法）：

- **#strings** : utility methods for `String` objects:

```JavaScript
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Strings
 * ======================================================================
 */

/*
 * Null-safe toString()
 */
${#strings.toString(obj)}                           // also array*, list* and set*

/*
 * Check whether a String is empty (or null). Performs a trim() operation before check
 * Also works with arrays, lists or sets
 */
${#strings.isEmpty(name)}
${#strings.arrayIsEmpty(nameArr)}
${#strings.listIsEmpty(nameList)}
${#strings.setIsEmpty(nameSet)}

/*
 * Perform an 'isEmpty()' check on a string and return it if false, defaulting to
 * another specified string if true.
 * Also works with arrays, lists or sets
 */
${#strings.defaultString(text,default)}
${#strings.arrayDefaultString(textArr,default)}
${#strings.listDefaultString(textList,default)}
${#strings.setDefaultString(textSet,default)}

/*
 * Check whether a fragment is contained in a String
 * Also works with arrays, lists or sets
 */
${#strings.contains(name,'ez')}                     // also array*, list* and set*
${#strings.containsIgnoreCase(name,'ez')}           // also array*, list* and set*

/*
 * Check whether a String starts or ends with a fragment
 * Also works with arrays, lists or sets
 */
${#strings.startsWith(name,'Don')}                  // also array*, list* and set*
${#strings.endsWith(name,endingFragment)}           // also array*, list* and set*

/*
 * Substring-related operations
 * Also works with arrays, lists or sets
 */
${#strings.indexOf(name,frag)}                      // also array*, list* and set*
${#strings.substring(name,3,5)}                     // also array*, list* and set*
${#strings.substringAfter(name,prefix)}             // also array*, list* and set*
${#strings.substringBefore(name,suffix)}            // also array*, list* and set*
${#strings.replace(name,'las','ler')}               // also array*, list* and set*

/*
 * Append and prepend
 * Also works with arrays, lists or sets
 */
${#strings.prepend(str,prefix)}                     // also array*, list* and set*
${#strings.append(str,suffix)}                      // also array*, list* and set*

/*
 * Change case
 * Also works with arrays, lists or sets
 */
${#strings.toUpperCase(name)}                       // also array*, list* and set*
${#strings.toLowerCase(name)}                       // also array*, list* and set*

/*
 * Split and join
 */
${#strings.arrayJoin(namesArray,',')}
${#strings.listJoin(namesList,',')}
${#strings.setJoin(namesSet,',')}
${#strings.arraySplit(namesStr,',')}                // returns String[]
${#strings.listSplit(namesStr,',')}                 // returns List<String>
${#strings.setSplit(namesStr,',')}                  // returns Set<String>

/*
 * Trim
 * Also works with arrays, lists or sets
 */
${#strings.trim(str)}                               // also array*, list* and set*

/*
 * Compute length
 * Also works with arrays, lists or sets
 */
${#strings.length(str)}                             // also array*, list* and set*

/*
 * Abbreviate text making it have a maximum size of n. If text is bigger, it
 * will be clipped and finished in "..."
 * Also works with arrays, lists or sets
 */
${#strings.abbreviate(str,10)}                      // also array*, list* and set*

/*
 * Convert the first character to upper-case (and vice-versa)
 */
${#strings.capitalize(str)}                         // also array*, list* and set*
${#strings.unCapitalize(str)}                       // also array*, list* and set*

/*
 * Convert the first character of every word to upper-case
 */
${#strings.capitalizeWords(str)}                    // also array*, list* and set*
${#strings.capitalizeWords(str,delimiters)}         // also array*, list* and set*

/*
 * Escape the string
 */
${#strings.escapeXml(str)}                          // also array*, list* and set*
${#strings.escapeJava(str)}                         // also array*, list* and set*
${#strings.escapeJavaScript(str)}                   // also array*, list* and set*
${#strings.unescapeJava(str)}                       // also array*, list* and set*
${#strings.unescapeJavaScript(str)}                 // also array*, list* and set*

/*
 * Null-safe comparison and concatenation
 */
${#strings.equals(first, second)}
${#strings.equalsIgnoreCase(first, second)}
${#strings.concat(values...)}
${#strings.concatReplaceNulls(nullValue, values...)}

/*
 * Random
 */
${#strings.randomAlphanumeric(count)}
```

- **#dates** : utility methods for `java.util.Date` objects:

```JavaScript
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Dates
 * ======================================================================
 */

/*
 * Format date with the standard locale format
 * Also works with arrays, lists or sets
 */
${#dates.format(date)}
${#dates.arrayFormat(datesArray)}
${#dates.listFormat(datesList)}
${#dates.setFormat(datesSet)}

/*
 * Format date with the ISO8601 format
 * Also works with arrays, lists or sets
 */
${#dates.formatISO(date)}
${#dates.arrayFormatISO(datesArray)}
${#dates.listFormatISO(datesList)}
${#dates.setFormatISO(datesSet)}

/*
 * Format date with the specified pattern
 * Also works with arrays, lists or sets
 */
${#dates.format(date, 'dd/MMM/yyyy HH:mm')}
${#dates.arrayFormat(datesArray, 'dd/MMM/yyyy HH:mm')}
${#dates.listFormat(datesList, 'dd/MMM/yyyy HH:mm')}
${#dates.setFormat(datesSet, 'dd/MMM/yyyy HH:mm')}

/*
 * Obtain date properties
 * Also works with arrays, lists or sets
 */
${#dates.day(date)}                    // also arrayDay(...), listDay(...), etc.
${#dates.month(date)}                  // also arrayMonth(...), listMonth(...), etc.
${#dates.monthName(date)}              // also arrayMonthName(...), listMonthName(...), etc.
${#dates.monthNameShort(date)}         // also arrayMonthNameShort(...), listMonthNameShort(...), etc.
${#dates.year(date)}                   // also arrayYear(...), listYear(...), etc.
${#dates.dayOfWeek(date)}              // also arrayDayOfWeek(...), listDayOfWeek(...), etc.
${#dates.dayOfWeekName(date)}          // also arrayDayOfWeekName(...), listDayOfWeekName(...), etc.
${#dates.dayOfWeekNameShort(date)}     // also arrayDayOfWeekNameShort(...), listDayOfWeekNameShort(...), etc.
${#dates.hour(date)}                   // also arrayHour(...), listHour(...), etc.
${#dates.minute(date)}                 // also arrayMinute(...), listMinute(...), etc.
${#dates.second(date)}                 // also arraySecond(...), listSecond(...), etc.
${#dates.millisecond(date)}            // also arrayMillisecond(...), listMillisecond(...), etc.

/*
 * Create date (java.util.Date) objects from its components
 */
${#dates.create(year,month,day)}
${#dates.create(year,month,day,hour,minute)}
${#dates.create(year,month,day,hour,minute,second)}
${#dates.create(year,month,day,hour,minute,second,millisecond)}

/*
 * Create a date (java.util.Date) object for the current date and time
 */
${#dates.createNow()}

${#dates.createNowForTimeZone()}

/*
 * Create a date (java.util.Date) object for the current date (time set to 00:00)
 */
${#dates.createToday()}

${#dates.createTodayForTimeZone()}
```

- **#lists** : utility methods for lists

```JavaScript
/*
 * ======================================================================
 * See javadoc API for class org.thymeleaf.expression.Lists
 * ======================================================================
 */

/*
 * Converts to list
 */
${#lists.toList(object)}

/*
 * Compute size
 */
${#lists.size(list)}

/*
 * Check whether list is empty
 */
${#lists.isEmpty(list)}

/*
 * Check if element or elements are contained in list
 */
${#lists.contains(list, element)}
${#lists.containsAll(list, elements)}

/*
 * Sort a copy of the given list. The members of the list must implement
 * comparable or you must define a comparator.
 */
${#lists.sort(list)}
${#lists.sort(list, comparator)}
```