# Java日期处理参考手册

Java中日期处理的类有Date/Calendar/GregorianCalendar/DateFormat/SimpleDateFormat类。

## 1、Date类（该类现在很少用了）

 Date类对象的创建：

 创建一个当前时间

//默认是创建一个代表系统当前日期的Date对象

  Date d = new Date();

创建一个我们指定的时间的Date对象：

下面是使用带参数的构造方法，可以构造指定日期的Date类对象，Date类中年份的参数应该是实际需要代表的年份减去1900，实际需要代表的月份减去1以后的值。

//创建一个代表2009年6月12号的Date对象

Date d1 = new Date(2009-1900, 6-1, 12); （注意参数的设置啊！）

 正确获得一个date对象所包含的信息

如：

```java
    Date d2 =  new Date(2009-1900, 6-1, 12);

        //获得年份（注意年份要加上1900，这样才是日期对象d2所代表的年份）

        int year = d2.getYear() + 1900;

        //获得月份 （注意月份要加1，这样才是日期对象d2所代表的月份）

        int month = d2.getMonth() + 1;

        //获得日期

        int date = d2.getDate();

        //获得小时

        int hour = d2.getHours();

        //获得分钟

        int minute = d2.getMinutes();

        //获得秒

        int second = d2.getSeconds();

        //获得星期（注意：0代表星期日、1代表星期1、2代表星期2，其他的一次类推了）

        int day = d2.getDay();
```

运行结果：

年份：2009

月份：6

日期：12

小时：0  //不设置默认是0

分钟：0  //不设置默认是0

秒：0   //不设置默认是0

星期：5  //09-6-12今天就是星期5，又是一个星期天哦


## 2、Calendar类

Calendar类的功能要比Date类强大很多，而且在实现方式上也比Date类要复杂一些

* Calendar类对象的创建

Calendar类是一个抽象类，在实际使用时实现特定的子类的对象。由于Calendar类是抽象类，且Calendar类的构造方法是protected的，所以无法使用Calendar类的构造方法来创建对象，API中提供了getInstance方法用来创建对象。

创建一个代表系统当前日期的Calendar对象

Calendar c = Calendar.getInstance();//默认是当前日期

* 创建一个指定日期的Calendar对象

使用Calendar类代表特定的时间，需要首先创建一个Calendar的对象，然后再设定该对象中的年月日参数来完成。

//创建一个代表2009年6月12日的Calendar对象

Calendar c1 = Calendar.getInstance();

c1.set(2009, 6 - 1, 12);


* Calendar类对象字段类型


Calendar类中用一下这些常量表示不同的意义，jdk内的很多类其实都是采用的这种思想

Calendar.YEAR——年份

Calendar.MONTH——月份

Calendar.DATE——日期

Calendar.DAY_OF_MONTH——日期，和上面的字段意义完全相同

Calendar.HOUR——12小时制的小时

Calendar.HOUR_OF_DAY——24小时制的小时

Calendar.MINUTE——分钟

Calendar.SECOND——秒

Calendar.DAY_OF_WEEK——星期几



* Calendar类对象信息的设置与获得

 Calendar类对象信息的设置

 Set设置

如：Calendar c1 = Calendar.getInstance();

调用：public final void set(int year,int month,int date)

c1.set(2009, 6 - 1, 12);//把Calendar对象c1的年月日分别设这为：2009、6、12

利用字段类型设置

如果只设定某个字段，例如日期的值，则可以使用如下set方法：

调用：public void set(int field,int value)

```java

//把c1对象代表的日期设置为10号，其它所有的数值会被重新计算

c1.set(Calendar.DATE,10);

//把c1对象代表的年份设置为2008年，其他的所有数值会被重新计算

c1.set(Calendar.YEAR,2008);

         其他字段属性set的意义以此类推

 Add设置

Calendar c1 = Calendar.getInstance();

//把c1对象的日期加上10，也就是c1所表的日期的10天后的日期，其它所有的数值会被重新计算

c1.add(Calendar.DATE, 10);

//把c1对象的日期加上10，也就是c1所表的日期的10天前的日期，其它所有的数值会被重新计算

c1.add(Calendar.DATE, -10);

其他字段属性的add的意义以此类推

n         Calendar类对象信息的获得

Calendar c1 = Calendar.getInstance();

// 获得年份

int year = c1.get(Calendar.YEAR);

// 获得月份

int month = c1.get(Calendar.MONTH) + 1;

// 获得日期

int date = c1.get(Calendar.DATE);

// 获得小时

int hour = c1.get(Calendar.HOUR_OF_DAY);

// 获得分钟

int minute = c1.get(Calendar.MINUTE);

// 获得秒

int second = c1.get(Calendar.SECOND);

// 获得星期几（注意（这个与Date类是不同的）：1代表星期日、2代表星期1、3代表星期二，以此类推）

int day = c1.get(Calendar.DAY_OF_WEEK);
```



## 3、GregorianCalendar类

GregorianCalendar 是 Calendar 的一个具体子类，提供了世界上大多数国家使用的标准日历系统。

GregorianCalendar类对象的创建

GregorianCalendar有自己的构造方法，而其父类Calendar没有公开的构造方法哦。

GregorianCalendar()
          在具有默认语言环境的默认时区内使用当前时间构造一个默认的 GregorianCalendar。

GregorianCalendar(int year, int month, int dayOfMonth)
          在具有默认语言环境的默认时区内构造一个带有给定日期设置的 GregorianCalendar。

GregorianCalendar(int year, int month, int dayOfMonth, int hourOfDay, int minute)
          为具有默认语言环境的默认时区构造一个具有给定日期和时间设置的 GregorianCalendar。

GregorianCalendar(int year, int month, int dayOfMonth, int hourOfDay, int minute, int second)
          为具有默认语言环境的默认时区构造一个具有给定日期和时间设置的 GregorianCalendar。

```java
//创建一个代表当前日期的GregorianCalendar对象

GregorianCalendar gc = new GregorianCalendar();

//创建一个代表2009年6月12日的GregorianCalendar对象(注意参数设置哦，与其父类是一样的哦，月份要减去1)

GregorianCalendar gc = new GregorianCalendar(2009,6-1,12);
```

字段属性什么的都是随其父Calendar了，呵

另外：GregorianCalendar有下面一个方法：

isLeapYear(int year)
          确定给定的年份是否为闰年

## 4、DateFormat类

DateFormat 是日期/时间格式化子类的抽象类，它以与语言无关的方式格式化并分析日期或时间。日期/时间格式化子类（如 SimpleDateFormat）允许进行格式化（也就是日期 -> 文本）、分析（文本-> 日期）和标准化。将日期表示为Date 对象，或者表示为从 GMT（格林尼治标准时间）1970 年，1 月 1 日 00:00:00 这一刻开始的毫秒数。

## 5、SimpleDateFormat类

public class SimpleDateFormat extends DateFormat

SimpleDateFormat 是一个以与语言环境相关的方式来格式化和分析日期的具体类。它允许进行格式化（日期 -> 文本）、分析（文本 -> 日期）和规范化。

所以本类可以实现：String 到 Date   Date到String的互转，如下：



SimpleDateFormat对象最常用的就是一下两招了：

```java

//注意构造函数中是SimpleDateFormat类解析日期的模式，大小写代表的意义完全不一样哦

       SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");

           //日期到字符串的转换

           String today = df.format(new Date());

           //字符串到日期的转换

           Date date = df.parse("2009-06-12 02:06:37");

           System.out.println(df.format(new Date()));
```



## 6、日期类对象之间的互转

* Date类对象与long型时间的互转

```java

//将Date类的对象转换为long型时间

Date d= new Date();

//使用对象的getTime（）方法完成

long dLong = d.getTime();


//将long型时间转换为Date类的对象

long time = 1290876532190L;

//使用Date的构造方法完成

Date d2 = new Date(time);

* Calendar对象和long型时间之间的互转

// 将Calendar对象转换为相对时间

Calendar c = Calendar.getInstance();

long t1 = c.getTimeInMillis();


// 将相对时间转换为Calendar对象

Calendar c1 = Calendar.getInstance();

long t = 1252785271098L;

c1.setTimeInMillis(t1);

* Calendar对象和Date对象之间的互转

// 将Calendar对象转换为相对时间

Calendar c = Calendar.getInstance();

Date d = c.getTime();


// 将相对时间转换为Calendar对象

Calendar c1 = Calendar.getInstance();

Date d1 = new Date();

//通过setTime（）方法后，日历c1所表示的日期就d1的日期

c1.setTime(d1);
```


## 7、 Ok，利用以上各个类的功能，我们可以很简单的实现一些时间计算的功能哦，呵呵，下面看几个方法：

```java

    /**

     *给定一个年份判断该年份是否为闰年createdate:2009-6-10author:Administrator

     *@paramyear

     *@return

     */

    publicstaticboolean isLeapYear(int year) {

       GregorianCalendar calendar =new GregorianCalendar();

       return calendar.isLeapYear(year);

    }
```


```java

    /**

     *利用SimpleDateFormat获取当前日期的字符串表示形式格式：2009-55-05

     *createdate:2009-6-5author:Administrator

     *@return

     */

    publicstatic String getCurrentDate() {

       //注意 SimpleDateFormat("yyyy-MM-dd")的参数间隔符号可以随意设置的，如：

       // yyyy年MM月dd日返回格式：2009年06月09日

       // yyyy-MM-dd返回格式： 2009-06-09

       // SimpleDateFormat dateFormat = new SimpleDateFormat(

       // "yyyy-MM-dd HH:mm:ss");

       SimpleDateFormat dateFormat =new SimpleDateFormat("yyyy-MM-dd");

       return dateFormat.format(System.currentTimeMillis());



    }
```


```java

    /**

     *给出任意一个年月日得到该天是星期几createdate:2009-6-10author:Administrator

     *@paramdate

     *           参数格式2009-6-10

     *  返回值：0代表星期日，1代表星期1，2代表星期2，以此类推

     */


    publicstaticint getWeek(String date) {


       //注意参数的大小写格式

       SimpleDateFormat dateFormat =new SimpleDateFormat("yyyyMMdd");

       Calendar c = Calendar.getInstance();

       try {

           Date d = dateFormat.parse(date);

           c.setTime(d);

       } catch (ParseException e) {

       }

       return c.get(Calendar.DAY_OF_WEEK)-1;


    }
```


```java

    /**

     *获得距离今天n天的那一天的日期createdate:2009-6-11author:Administrator

     *@paramday

     *@return

     */

    publicstatic String getDistanceDay(int day) {

       Calendar calen = Calendar.getInstance();

       calen.add(Calendar.DAY_OF_MONTH, day);

       Date date = calen.getTime();

       //这里也个用SimpleDateFormat的format（）进行格式化，然后以字符串形式返回格式化后的date

       SimpleDateFormat dateFormat =new SimpleDateFormat("yyyy-MM-dd");

       return dateFormat.format(date);

    }

```

```java

/**

     *获得距离指定日期n天的那一天的日期createdate:2009-6-11author:Administrator

     *@paramdate

     *           格式：2009-6-11

     *@paramday

     *@return

     */

    publicstatic String getDistanceDay(String date,int day) {


       SimpleDateFormat dateFormat =new SimpleDateFormat("yyyyMMdd");

       Date d;

       Calendar c =Calendar.getInstance();

       try {

           d = dateFormat.parse(date);

           c.setTime(d);

           c.add(Calendar.DATE, day);

       } catch (ParseException e) {

           //TODO Auto-generated catch block

           e.printStackTrace();

       }

       return dateFormat.format(c.getTime());

    }
```




```java
 /**

     * 获得给定两个日期相差度天数

     * create date:2009-6-23 author:Administrator

     * @param date1

     *            参数格式:2009-06-21

     * @param date2

     */

    public static long getGapDays(String date1, String date2) {


       String[] d1 = date1.split("-");

       String[] d2 = date2.split("-");

       Calendar c = Calendar.getInstance();

       c.set(Integer.parseInt(d1[0]), Integer.parseInt(d1[1]), Integer

              .parseInt(d1[2]), 0, 0, 0);

       long l1 = c.getTimeInMillis();

       c.set(Integer.parseInt(d2[0]), Integer.parseInt(d2[1]), Integer

              .parseInt(d2[2]), 0, 0, 0);

       long l2 = c.getTimeInMillis();


       return (Math.abs(l1 - l2) / (24 * 60 * 60 * 1000));

    }
```

## 8、joda-time

用上述的这些类对日期的计算，显得有些复杂，现在有joda-time类库对时间日期的操作很是方便。gradle中引用方式为 compile("joda-time:joda-time:2.9.9"), [官网](http://www.joda.org/joda-time/)可查看最新版本。

下面列举出了joda-time类库中常用的操作方法。

```java
//初始化时间
DateTime dateTime=new DateTime(2012, 12, 13, 18, 23,55); // 年,月,日,时,分,秒,毫秒
DateTime dt3 = new DateTime(2011, 2, 13, 10, 30, 50, 333);// 2010年2月13日10点30分50秒333毫秒

//下面就是按照一点的格式输出时间
String str2 = dateTime.toString("MM/dd/yyyy hh:mm:ss.SSSa");
String str3 = dateTime.toString("dd-MM-yyyy HH:mm:ss");
String str4 = dateTime.toString("EEEE dd MMMM, yyyy HH:mm:ssa");
String str5 = dateTime.toString("MM/dd/yyyy HH:mm ZZZZ");
String str6 = dateTime.toString("MM/dd/yyyy HH:mm Z");

DateTimeFormatter format = DateTimeFormat .forPattern("yyyy-MM-dd HH:mm:ss");
//时间解析
DateTime dateTime2 = DateTime.parse("2012-12-21 23:22:45", format);

//时间格式化，输出==> 2012/12/21 23:22:45 Fri
String string_u = dateTime2.toString("yyyy/MM/dd HH:mm:ss EE");
System.out.println(string_u);

//格式化带Locale，输出==> 2012年12月21日 23:22:45 星期五
String string_c = dateTime2.toString("yyyy年MM月dd日 HH:mm:ss EE",Locale.CHINESE);
System.out.println(string_c);

DateTime dt1 = new DateTime();// 取得当前时间

// 根据指定格式,将时间字符串转换成DateTime对象,这里的格式和上面的输出格式是一样的
DateTime dt2 = DateTimeFormat.forPattern("yyyy-MM-dd HH:mm:ss").parseDateTime("2012-12-26 03:27:39");

//计算两个日期间隔的天数
LocalDate start=new LocalDate(2012, 12,14);
LocalDate end=new LocalDate(2013, 01, 15);
int days = Days.daysBetween(start, end).getDays();

//计算两个日期间隔的小时数,分钟数,秒数

//增加日期
DateTime dateTime1 = DateTime.parse("2012-12-03");
dateTime1 = dateTime1.plusDays(30);
dateTime1 = dateTime1.plusHours(3);
dateTime1 = dateTime1.plusMinutes(3);
dateTime1 = dateTime1.plusMonths(2);
dateTime1 = dateTime1.plusSeconds(4);
dateTime1 = dateTime1.plusWeeks(5);
dateTime1 = dateTime1.plusYears(3);

// Joda-time 各种操作.....
dateTime = dateTime.plusDays(1) // 增加天
                    .plusYears(1)// 增加年
                    .plusMonths(1)// 增加月
                    .plusWeeks(1)// 增加星期
                    .minusMillis(1)// 减分钟
                    .minusHours(1)// 减小时
                    .minusSeconds(1);// 减秒数

//判断是否闰月
DateTime dt4 = new DateTime();
org.joda.time.DateTime.Property month = dt4.monthOfYear();
System.out.println("是否闰月:" + month.isLeap());

//取得 3秒前的时间
DateTime dt5 = dateTime1.secondOfMinute().addToCopy(-3);
dateTime1.getSecondOfMinute();// 得到整分钟后，过的秒钟数
dateTime1.getSecondOfDay();// 得到整天后，过的秒钟数
dateTime1.secondOfMinute();// 得到分钟对象,例如做闰年判断等使用

// DateTime与java.util.Date对象,当前系统TimeMillis转换
DateTime dt6 = new DateTime(new Date());
Date date = dateTime1.toDate();
DateTime dt7 = new DateTime(System.currentTimeMillis());
dateTime1.getMillis();

Calendar calendar = Calendar.getInstance();
dateTime = new DateTime(calendar);
```
