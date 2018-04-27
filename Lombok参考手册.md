# Lombok参考手册

[Lombok](https://projectlombok.org/)是一个可以通过简单的注解形式来帮助我们简化消除一些必须有但显得臃肿的Java代码的工具，通过使用对应的注解，可以在编译源码的时候生成对应的方法。官方地址：[https://projectlombok.org/](https://projectlombok.org/)，github项目地址：[https://github.com/rzwitserloot/lombok](https://github.com/rzwitserloot/lombok)。

## lombok常用注解

使用lombok注解需要在项目中引用`lombok` jar包。

- `@Data`：注解在类上；提供类所有属性的`getting`和`setting`方法，此外还提供了`equals`、`canEqual`、`hashCode` 、`toString` 方法
- `@Setter`：注解在属性上；为属性提供setting方法
- `@Getter`：注解在属性上；为属性提供getting方法
- `@Slf4j`：注解在类上；为类提供一个属性名为`log` 的`slf4j`日志对象
- `@NoArgsConstructor`：注解在类上：为类提供一个无参的构造方法
- `@AllArgsConstructor` ：注解在类上；为类提供一个全参的构造方法
- `@NonNull`：注解在参数上；如果该参数为null 会throw new NullPointerException(参数名);
- `@Cleanup`：注释在引用变量前，自动回收资源 默认调用close方法
- `@SneakyThrows` ：注解在方法上，为方法抛出指定异常

样例如下：

使用lombok注解的java代码：

```java
import lombok.*;
import lombok.extern.slf4j.Slf4j;
import java.io.ByteArrayInputStream;
import java.io.*;
import java.util.ArrayList;

@Data
@Slf4j
@NoArgsConstructor
@AllArgsConstructor
public class Something {

    private String name;
    private final String country;
    private final Object lockObj = new Object();

    public void sayHello(@NonNull String target) {
        String content = String.format("hello,%s", target);
        System.out.println(content);
        log.info(content);
    }

    public void addBalabala() {
        val list = new ArrayList<String>();
        list.add("haha");
        System.out.println(list.size());
    }

    @SneakyThrows(IOException.class)
    public void closeBalabala() {
        @Cleanup InputStream is = new ByteArrayInputStream("hello world".getBytes());
        System.out.println(is.available());
    }

}
```

等效于下面的这段java代码：

```java
import java.beans.ConstructorProperties;
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Collections;
import lombok.NonNull;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Something {
    private static final Logger log = LoggerFactory.getLogger(Something.class);
    private String name;
    private final String country;
    private final Object lockObj = new Object();

    public void sayHello(@NonNull String target) {
        if(target == null) {
            throw new NullPointerException("target");
        } else {
            String content = String.format("hello,%s", new Object[]{target});
            System.out.println(content);
            log.info(content);
        }
    }

    public void addBalabala() {
        ArrayList list = new ArrayList();
        list.add("haha");
        System.out.println(list.size());
    }

    public void closeBalabala() {
        try {
            ByteArrayInputStream bis = new ByteArrayInputStream("hello world".getBytes());

            try {
                System.out.println(bis.available());
            } finally {
                if(Collections.singletonList(bis).get(0) != null) {
                    bis.close();
                }
            }
        } catch (IOException e) {
            throw e;
        }
    }
    public String getName() {
        return this.name;
    }
    public String getCountry() {
        return this.country;
    }
    public Object getLockObj() {
        return this.lockObj;
    }
    public void setName(String name) {
        this.name = name;
    }
    public boolean equals(Object o) {
        //...
    }
    protected boolean canEqual(Object other) {
        return other instanceof Something;
    }
    public int hashCode() {
        //...
    }
    public String toString() {
       //...
    }
    @ConstructorProperties({"name", "country"})
    public Something(String name, String country) {
        this.name = name;
        this.country = country;
    }
}
```

## 与IDE集成

### 与Eclipse集成

第一步：下载`lombok.jar` 安装包

 下载地址：[http://projectlombok.org/](http://projectlombok.org/)

第二步： 与Eclipse集成

1、 将 lombok.jar 复制到`eclipse.ini`所在的文件夹目录下
2、 打开 `eclipse.ini` ，在最后面插入以下两行并保存：
           -Xbootclasspath/a:lombok.jar
           -javaagent:lombok.jar
3、 重启`eclipse` 。

### 与IntelliJ IDEA集成

第一步：安装lombok插件

方式一，通过Plugins安装

打开IDEA，`Settings`  ->  `Plugin`，在搜索框中输入`lombok plugin` ，根据提示安装，安装后重启即可。

方式二，下载安装包安装

下载lombok插件，下载地址为：[https://github.com/mplushnikov/lombok-intellij-plugin/releases](https://github.com/mplushnikov/lombok-intellij-plugin/releases) ;

`Plugin` -> `Install plugin from disk...` ，选择下载的zip包安装，安装后重启即可;

第二步：`Enable annotation processing`

`Settings -> Build,Exectution,Deployment` -> `Compiler` -> `Annotation Processors` ，勾选`Enable annotation processing` ，点击`apply` 保存，重启后即可使用`lombok` 注解编码了。
