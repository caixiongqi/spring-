# JPA实体注解参考手册

## @Entity

作用：设置一个类为实体类

```java
@Entity
public class User {
}
```

## @Table

作用：设置实体类对应的表，常与`@Entity`一起使用
参数：name指定表名，不写的话，为实体类的类名

```java
@Entity
@Table(name = "sino_user")
public class User {
}
```

## @id

作用：设置对象标识符

```java
@Id
private Integer id;
```

## @GeneratedValue

作用：设置标识符的生成策略，常与`@Id`一起使用
参数：`strategy`指定具体的生成策略

方式一：`@GeneratedValue(strategy=GenerationType.AUTO)`也是默认策略， 即写成`@GeneratedValue`也可以。
类似于hibernate的native策略，生成方式取决于底层的数据库。

方式二：`@GeneratedValue(strategy = GenerationType.IDENTITY)`指定“自动增长”策略，适用于MySQL。

方式三：`@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "seq_tbl_user")`指定“序列”策略，适用于Oracle。
其中generator表示生成器的名字。
而且还要指定`@SequenceGenerator(name = "seq_tbl_user", sequenceName = "seq_tbl_user", allocationSize = 1)`注解配合使用
其中name指定生成器的名字（与`generato`的值一样），`sequenceName`指定数据库中定义序列的名字，`allocationSize`指定序列每次增长1

方式四：`@GenericGenerator(name = "idGenerator", strategy = "uuid")` 指定uuid生成策略。

方式五：若不指定`@GeneratedValue`注解，则要手动提供Id值。user.setId();

```java
@Id
/** 自增 用于MySQL */
@GeneratedValue(strategy = GenerationType.IDENTITY)

/** 序列 用于Oracle */
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "seq_tbl_user")
@SequenceGenerator(name = "seq_tbl_user", sequenceName = "seq_tbl_user", allocationSize = 1)

/** 默认的 等同于@GeneratedValue */
@GeneratedValue(strategy = GenerationType.AUTO)
private Integer id;
```

## @column

作用：设置列

参数：

- name：指定列名
- unique：指定唯一约束
- nullable：指定是否允许为空
- length：长度

```java
@Column(name = "user_name", length = 255, nullable = true, unique = true)
private String name;
```

注意：若不写`@Column`注解，则一切使用`@Column`注解的默认值。

## @Temporal

作用：设置日期时间

- 方式一：@Temporal(TemporalType.DATE)映射为日期 // birthday date （只有日期）
- 方式二：@Temporal(TemporalType.TIME)映射为日期 // birthday time （只有时间）
- 方式三：@Temporal(TemporalType.TIMESTAMP)映射为日期 //birthday datetime （日期+时间）

```java
@Temporal(TemporalType.DATE)
private Date birthday;
```

## @Lob

作用：设置大数据类型

```java
@Lob
private String text;   //text longtext
@Lob
private byte[] image;// image longblob
```

## @Enumerated

作用：设置枚举类型

```java
/** 保存字符串到数据库 */
@Enumerated(EnumType.STRING)
private RoleOne roleOne;
/** 保存整数到数据库 */
@Enumerated(EnumType.ORDINAL)
private RoleTwo roleTwo;
```

上面定义的枚举：RoleOne,RoleTwo

```java
/** 角色 */
public enum RoleOne {
游客, 会员, 管理员
}
```

```java
/** 角色 */
public enum RoleTwo {
1, 2, 3
}
```

使用：

```java
User user = new User();
user.setRoleOne(RoleOne.管理员);
```

## @Transient

作用：修饰的字段不会被持久化，不与数据库表映射

```java
@Transient
private String temp;
```

### 注：以上的注解全部定义在javax.persistence下面。
