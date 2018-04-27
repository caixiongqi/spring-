# spring-data-jpa参考手册

## 继承的接口

* Repository：Spring Data的一个核心接口，它不提供任何方法，开发者需要在自己定义的接口中声明需要的方法。
* CrudRepository：继承Repository，提供增删改查方法，可以直接调用。
* PagingAndSortingRepository：继承CrudRepository，具有分页查询和排序功能（本类实例）
* JpaRepository继承PagingAndSortingRepository，针对JPA技术提供的接口
* JpaSpecificationExecutor：可以执行原生SQL查询

   注：继承不同的接口，有两个不同的泛型参数，他们是该持久层操作的类对象和主键类型。

## 基本查询

基本查询分为两种，一种是spring data默认已经实现，一种是根据查询的方法来自动解析成SQL。

### 预先生成方法

spring data jpa 默认预先生成了一些基本的CURD的方法，例如：增、删、改等等。

1、继承JpaRepository

```java
    public interface StudentRespository extends JpaRepository<StudentEntity, String> {
    }
```

2、使用默认方法

```java
@Test
public void testBaseQuery() throws Exception {
    StudentEntity studentEntity=new StudentEntity();

    // 查询所有，类似于select * from Student
    studentRespository.findAll();

    // 根据主键id查询一条，类似于select * from student where id = ?1
    studentRespository.findOne("1");

    // 保存studentEntity到数据库中，类似于insert into values(?,?,?,?)
    studentRespository.save(studentEntity);

    // 删除studentEntity在数据库中，类似于delete from student where id = ?1
    studentRespository.delete(studentEntity);

    // 返回实体的数量，类似于select count(*) from student
    studentRespository.count();

    // 查询表中主键是否有值为"1"的，如果存在，返回true，否则返回false
    studentRespository.exists("1");

    // 查询所有数据，并且加入排序
    Iterable<T> findAll(Sort sort);

    // 分页查询，查询第二页，每页20条数据
    studentRespository.findAll(new PageRequest(1, 20));

    // 根据某个字段条件查询，统计条数
    Long countByName(String name);

    // 根据某个字段删除，统计删除的条数
    Long deleteByName(String name);

    // 根据某个字段删除，返回删除的数据集合
    List<StudentEntity> removeByName(String name);

}
```

## 自定义简单查询

自定义简单查询就是根据方法名来自动生成SQL，主要的语法有`findXXBy`,`readAXXBy`,`queryXXBy`,`countXXBy`, `getXXBy`后面跟属性名称：

```java
StudentEntity findByName(String name);
```

也可以加一些关键字`And`、 `Or`

```java
StudentEntity findByNameOrSex(String name, String sex);
```

修改、删除、统计也是类似语法

```java
Long deleteById(String id);

Long countByName(String name);
```

基本上SQL体系中的关键词都可以使用，例如：`LIKE`、 `IgnoreCase`、 `OrderBy`。

```java
List<StudentEntity> findByNameLike(String name);

StudentEntity findByNameIgnoreCase(String name);

List<StudentEntity> findByNameOrderByAgeDesc(String age);
```

具体的关键字，使用方法和生产成SQL如下表所示:

| Keyword           | Sample                                  | JPQL snippet                             |
| ----------------- | --------------------------------------- | ---------------------------------------- |
| And               | findByLastnameAndFirstname              | … where x.lastname = ?1 and x.firstname = ?2 |
| Or                | findByLastnameOrFirstname               | … where x.lastname = ?1 or x.firstname = ?2 |
| Is,Equals         | findByFirstnameIs,findByFirstnameEquals | … where x.firstname = ?1                 |
| Between           | findByStartDateBetween                  | … where x.startDate between ?1 and ?2    |
| LessThan          | findByAgeLessThan                       | … where x.age < ?1                       |
| LessThanEqual     | findByAgeLessThanEqual                  | … where x.age ⇐ ?1                       |
| GreaterThan       | findByAgeGreaterThan                    | … where x.age > ?1                       |
| GreaterThanEqual  | findByAgeGreaterThanEqual               | … where x.age >= ?1                      |
| After             | findByStartDateAfter                    | … where x.startDate > ?1                 |
| Before            | findByStartDateBefore                   | … where x.startDate < ?1                 |
| IsNull            | findByAgeIsNull                         | … where x.age is null                    |
| IsNotNull,NotNull | findByAge(Is)NotNull                    | … where x.age not null                   |
| Like              | findByFirstnameLike                     | … where x.firstname like ?1              |
| NotLike           | findByFirstnameNotLike                  | … where x.firstname not like ?1          |
| StartingWith      | findByFirstnameStartingWith             | … where x.firstname like ?1 (parameter bound with appended %) |
| EndingWith        | findByFirstnameEndingWith               | … where x.firstname like ?1 (parameter bound with prepended %) |
| Containing        | findByFirstnameContaining               | … where x.firstname like ?1 (parameter bound wrapped in %) |
| OrderBy           | findByAgeOrderByLastnameDesc            | … where x.age = ?1 order by x.lastname desc |
| Not               | findByLastnameNot                       | … where x.lastname <> ?1                 |
| In                | findByAgeIn(Collection ages)            | … where x.age in ?1                      |
| NotIn             | findByAgeNotIn(Collection age)          | … where x.age not in ?1                  |
| TRUE              | findByActiveTrue()                      | … where x.active = true                  |
| FALSE             | findByActiveFalse()                     | … where x.active = false                 |
| IgnoreCase        | findByFirstnameIgnoreCase               | … where UPPER(x.firstame) = UPPER(?1)    |

## 分页查询

分页查询在实际使用中非常普遍了，spring data jpa已经帮我们实现了分页的功能，在查询的方法中，需要传入参数`Pageable`

```java
Page<StudentEntity> findAll(Pageable pageable);

Page<StudentEntity> findByName(String name,Pageable pageable);
```

`Pageable` 是spring封装的分页实现类，使用的时候需要传入页数、每页条数和排序规则

```java
@Test
public void testPageQuery() throws Exception {
    int page=1,size=10;
    Sort sort = new Sort(Direction.DESC, "id");
    Pageable pageable = new PageRequest(page, size, sort);
    studentRespository.findAll(pageable);
    studentRespository.findByName("testName", pageable);
}
```

## 自定义sql查询

其实Spring data jpa大部分的SQL都可以根据方法名定义的方式来实现，但是由于某些原因我们想使用自定义的SQL来查询，spring data也是完美支持的；在SQL的查询方法上面使用`@Query`注解，如涉及到删除和修改需要加上`@Modifying`.也可以根据需要添加 `@Transactional` 对事物的支持，查询超时的设置等

```java
@Modifying
@Query("update student u set u.name = ?1 where u.id = ?2")
int modifyByIdAndName(String  id, String name);

@Transactional
@Modifying
@Query("delete from StudentEntity where id = ?1")
void deleteById(String id);

@Transactional(timeout = 10)
@Query("select u from StudentEntity u where u.Sex = ?1")
    StudentEntity findBySex(String sex);

# 多表查询

多表查询在spring data jpa中有两种实现方式，第一种是利用hibernate的级联查询来实现，第二种是创建一个结果集的接口来接收连表查询后的结果，这里主要第二种方式。
首先需要定义一个结果集的接口类。

public interface HotelSummary {

    City getCity();

    String getName();

    Double getAverageRating();

    default Integer getAverageRatingRounded() {
        return getAverageRating() == null ? null : (int) Math.round(getAverageRating());
    }

}
```

查询的方法返回类型设置为新创建的接口

```java
@Query("select h.name as name, avg(r.rating) as averageRating from Hotel h left outer join h.reviews r  group by h.name")
Page<HotelSummary> findByCity(Pageable pageable);
```

    使用

```java
Page<HotelSummary> hotels = this.hotelRepository.findByCity(new PageRequest(0, 10, Direction.ASC, "name"));
for(HotelSummary summay:hotels){
    logger.info("name = ", summary.getName());
}
```

在运行中Spring会给接口（HotelSummary）自动生产一个代理类来接收返回的结果，代码汇总使用`getXX`的形式来获取。

## 复杂查询,动态条件查询

我们在使用SpringData JPA框架时，进行条件查询，如果是固定条件的查询，我们可以使用符合框架规则的自定义方法以及@Query注解实现。

如果是查询条件是动态的，框架也提供了查询接口JpaSpecificationExecutor。和其他接口使用方式一样，只需要在你的Dao接口继承即可。

```java
public interface CustomerRepository extends JpaRepository<Customer, Long>, JpaSpecificationExecutor {
 …
}
```

JpaSpecificationExecutor提供很多条件查询方法。

```java
public interface JpaSpecificationExecutor<T> {
    T findOne(Specification<T> var1);

    List<T> findAll(Specification<T> var1);

    Page<T> findAll(Specification<T> var1, Pageable var2);

    List<T> findAll(Specification<T> var1, Sort var2);

    long count(Specification<T> var1);
}
```

我们看到它需要的参数是一个`org.springframework.data.jpa.domain.Specification`

```java
Specification specification = new Specification() {
            @Override
            public Predicate toPredicate(Root root, CriteriaQuery criteriaQuery, CriteriaBuilder criteriaBuilder) {
                return null;
            }
        }
```

IDE自动生成了要重写的方法toPredicate。root参数是我们用来对应实体的信息的。criteriaBuilder可以帮助我们制作查询信息。
CriteriaBuilder对象里有很多条件方法，比如制定条件:某条数据的创建日期小于今天。

```java
criteriaBuilder.lessThan(root.get("createDate"), today);
```

示例:

```java
public Page<Student> search(final Student student, PageInfo page) {
    return studentRepository.findAll(new Specification<Student>() {
        @Override
        public Predicate toPredicate(Root<Student> root, CriteriaQuery<?> query, CriteriaBuilder cb) {

            Predicate stuNameLike = null;
            if(null != student && !StringUtils.isEmpty(student.getName())) {
                //模糊查询的条件，name like '%sudent.getName()%'
                stuNameLike = cb.like(root.<String> get("name"), "%" + student.getName() + "%");
            }

            Predicate clazzNameLike = null;
            if(null != student && null != student.getClazz() && !StringUtils.isEmpty(student.getClazz().getName())) {
                clazzNameLike = cb.like(root.<String> get("clazz").<String> get("name"), "%" + student.getClazz().getName() + "%");
            }
            //query.where(predicate)作用为：组合where条件
            if(null != stuNameLike) query.where(stuNameLike);
            if(null != clazzNameLike) query.where(clazzNameLike);
            return null;
        }
    }, new PageRequest(page.getPage() - 1, page.getLimit(), new Sort(Direction.DESC, page.getSortName())));
}
```

## 通过调用 JPA 命名查询语句创建查询

命名查询是 JPA 提供的一种将查询语句从方法体中独立出来，以供多个方法共用的功能。

在代码中使用 `@NamedQuery`（或 `@NamedNativeQuery`）定义查询语句，通俗的讲就是自定义查询语句。

样例如下：

Student.java

```java
@Entity
@Table(name = "STUDENT")
@NamedQuery(name = "Student.getStudentList",query = "select * from student age >= ?1")
public class Student() {
  @Id
  @GenericGenerator(name = "idGenerator", strategy = "uuid")
  @GeneratedValue(generator = "idGenerator")
  @Column(name="id")
  private String id;// 主键
  @Column(name="name")
  private String name;// 姓名
  @Column(name="age")
  private String age;//年龄
  @Column(name="sex")
  private String sex;//性别
}
```

StudentRepository.java

```java
public interface StudentRepository extends JpaRepository<Student, String> {
  public List<Student> getStudentList(String age);
}
```

在实体类`Student`上使用`@NamedQuery`,在自己实现的`StudentRepository`接口里面定义一个同名的方法,然后就可以使用了，Spring会先找是否有同名的`NamedQuery`，如果有，那么就不会按照接口定义的方法来解析。

## 其它

### 使用枚举

使用枚举的时候，我们希望数据库中存储的是枚举对应的String类型，而不是枚举的索引值，需要在属性上面添加`@Enumerated(EnumType.STRING)` 注解

```java
@Enumerated(EnumType.STRING)
@Column(nullable = true)
private UserType type;
```

### 不需要和数据库映射的属性

正常情况下我们在实体类上加入注解`@Entity`，就会让实体类和表相关连如果其中某个属性我们不需要和数据库来关联只是在展示的时候做计算，只需要加上`@Transient`属性既可

```java
@Transient
private String  name;
```

## Custom扩展

以扩展jdbc查询为例：

```java
public interface StudentRepository extends JpaRepository<DeviceBinding, String>, CustomStudentRepository{
  @Query(" from Student")
  List<Student> getStudentList();
}
```

```java
public interface CustomStudentRepository {
  List<Student> queryStudentList();
}
```

```java
public class CustomStudentRepositoryImpl implements CustomStudentRepository {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public List<Student> queryStudentList();
        String sql = " select * from student";
        return (List<Student>)jdbcTemplate.queryForList(sql);
    }
}
```

`StudentRepository`继承`CustomStudentRepository`接口，`CustomStudentRepositoryImpl`实现`CustomStudentRepository`接口，可以在`CustomStudentRepositoryImpl` 扩展其他的查询方式如JDBC查询。

### 更具体的用法详见：

[spring date jpa中文api](https://www.gitbook.com/book/ityouknow/spring-data-jpa-reference-documentation/details)