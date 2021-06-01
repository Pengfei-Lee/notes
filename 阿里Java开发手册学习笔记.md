# 一、编程规约

## （一）命名风格

1. 代码中的命名均不能以下划线或美元符号开始，也不能以下划线或美元符号结束。

2. 类名使用 UpperCamelCase 风格，但以下情形例外:DO / BO / DTO / VO / AO / PO / UID等。

3. 常量命名全部大写，单词间用下划线隔开，力求语义表达完整清楚，不要嫌名字长。

   正例:MAX_STOCK_COUNT

   反例:MAX_COUNT

4. 抽象类命名使用 Abstract 或 Base 开头;异常类命名使用 Exception 结尾;测试类 命名以它要测试的类的名称开始，以 Test 结尾。

5. 类型与中括号紧挨相连来表示数组

6. 接口类中的方法和属性不要加任何修饰符号(public 也不要加)，保持代码的简洁 性，并加上有效的 Javadoc 注释。

7. 领域模型命名规约
    1) 数据对象:xxxDO，xxx即为数据表名。
    2) 数据传输对象:xxxDTO，xxx为业务领域相关的名称。 
    3) 展示对象:xxxVO，xxx一般为网页名称。
    4) POJO是DO/DTO/BO/VO的统称，禁止命名成xxxPOJO。

## （二）常量定义

1. 如果变量值仅在一个固定范围内变化用 enum 类型来定义。

## （三）代码格式

1. 大括号的使用约定。如果是大括号内为空，则简洁地写成{}即可，不需要换行;如果 是非空代码块则:

   1) 左大括号前不换行。
    2) 左大括号后换行。
    3) 右大括号前换行。
    4) 右大括号后还有else等代码则不换行;表示终止的右大括号后必须换行。

   > 即如下格式
   >
   > ```java
   > while (flag){}
   > 
   > if (a == b){
   >   ...
   > }
   > 
   > if (a == b){
   >   ...
   > } else {
   >   ...
   > }
   > ```

2. 左小括号和字符之间不出现空格;同样，右小括号和字符之间也不出现空格;而左大 括号前需要空格。

   ```java
   if (a == b)   // 正确
   if ( a == b ) // 错误
   ```

3. if/for/while/switch/do 等保留字与括号之间都必须加空格。

4. 任何二目、三目运算符的左右两边都需要加一个空格。即除了单目运算符`!`以外两边都要加上空格。

5. 采用 4 个空格缩进，**禁止使用 tab 字符**。 

6. 注释的双斜线与注释内容之间有且仅有一个空格。 

   ```java
   // 正确的注释格式
   ```

7. 单行字符数限制不超过 120 个，超出需要换行，换行时遵循如下原则:
    1) 第二行相对第一行缩进 4 个空格，从第三行开始，不再继续缩进，参考示例。
    2) 运算符与下文一起换行。
    3) 方法调用的点符号与下文一起换行。
    4) 方法调用中的多个参数需要换行时，在逗号后进行。 
    5) 在括号前不要换行，见反例。

   正例:

   ```java
   StringBuffer sb = new StringBuffer();
   
   // 超过 120 个字符的情况下，换行缩进 4 个空格，点号和方法名称一起换行 
   sb.append("zi").append("xin")...
       .append("huang")...
       .append("huang")...
       .append("huang");
   ```

   反例:

   ```java
   StringBuffer sb = new StringBuffer();
   
   // 超过 120 个字符的情况下，不要在括号前换行 
   sb.append("zi").append("xin")...append
       ("huang");
   
   // 参数很多的方法调用可能超过 120 个字符，不要在逗号前换行 
   method(args1, args2, args3, ...
   , argsX);
   
   ```

8. IDE的text file encoding设置为UTF-8; IDE中文件的换行符使用Unix格式， 不要使用 Windows 格式。

9. 单个方法的总行数不超过 80 行。

## （四） OOP规约

1. 所有的相同类型的包装类对象之间值的比较，全部使用 equals 方法比较。 

   说明:对于 Integer var = ? 在-128 至 127 范围内的赋值，Integer 对象是在 IntegerCache.cache 产生，会复用已有对象，这个区间内的 Integer 值可以直接使用==进行 判断，但是这个区间之外的所有数据，都会在堆上产生，并不会复用已有对象，这是一个大坑， 推荐使用 equals 方法进行判断。

2. 关于基本数据类型与包装数据类型的使用标准如下:
    1) 【强制】所有的POJO类属性必须使用包装数据类型。
    2) 【强制】RPC方法的返回值和参数必须使用包装数据类型。
    3) 【推荐】所有的局部变量使用基本数据类型。

   > 包装数据类型的 null 值，能够表示额外的信息，如:远程调用失败，异常退出。
   
3. 类内方法定义的顺序依次是:公有方法或保护方法 > 私有方法 > getter/setter 方法。

    > 越有信息价值的放在越前面。

4. 慎用 Object 的 clone 方法来拷贝对象。
     说明:对象的 clone 方法默认是浅拷贝，若想实现深拷贝需要重写 clone 方法实现域对象的 深度遍历式拷贝

## （五）集合处理

1. 因为Set存储的是不重复的对象，依据hashCode和equals进行判断，所以Set存储的对象必须重写这两个方法。

2. ArrayList的subList结果不可强转成ArrayList，否则会抛出ClassCastException 异常。

3. 使用集合转数组的方法，必须使用集合的toArray(T[] array)，传入的是类型完全一样的数组，大小就是 list.size()。

   说明:使用 toArray 带参方法，入参分配的数组空间不够大时，toArray 方法内部将重新分配内存空间，并返回新数组地址;如果数组元素个数大于实际所需，下标为[ list.size() ]的数组元素将被置为 null，其它数组元素保持原值，因此最好将方法入参数组大小定义与集合元素个数一致。

   正例:

   ```java
List<String> list = new ArrayList<String>(2); 
   list.add("guan"); 
list.add("bao");
   String[] array = new String[list.size()];
array = list.toArray(array);

4. 泛型通配符<? extends T>来接收返回的数据，此写法的泛型集合不能使用add方 法，而<? super T>不能使用get方法，作为接口调用赋值时易出错。 说明:扩展说一下PECS(Producer Extends Consumer Super)原则:第一、频繁往外读取内 容的，适合用<? extends T>。第二、经常往里插入的，适合用<? super T>。

   > [<? extends T> 和 <? super T>](https://www.cnblogs.com/drizzlewithwind/p/6100164.html)

5. 不要在 foreach 循环里进行元素的 remove/add 操作。remove 元素请使用 Iterator方式，如果并发操作，需要对 Iterator 对象加锁。

6. 集合泛型定义时，在 JDK7 及以上，使用 diamond 语法或全省略。

   说明:菱形泛型，即 diamond，直接使用<>来指代前边已经指定的类型。

   ```java
   正例:
   // <> diamond 方式
    HashMap<String, String> userCache = new HashMap<>(16); // 全省略方式
    ArrayList<User> users = new ArrayList(10);
   ```
   
7. 集合初始化时，指定集合初始值大小。

   > 反例：HashMap 需要放置 1024 个元素，由于没有设置容量初始大小，随着元素不断增加，容 量 7 次被迫扩大，resize 需要重建 hash 表，严重影响性能。

8. 高度注意 Map 类集合 K/V 能不能存储 null 值的情况，如下表格:

   ![image-20210506115221234](https://tva1.sinaimg.cn/large/008i3skNly1gq8kf6cka6j31be0j8tcj.jpg)

## （六）并发处理

1. 线程资源必须通过线程池提供，不允许在应用中自行显式创建线程

2. 线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。 

   说明: Executors 返回的线程池对象的弊端如下:

    1) FixedThreadPool 和 SingleThreadPool:

   ​	允许的请求队列长度为 Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM。 

    2) CachedThreadPool 和 ScheduledThreadPool:

   ​	允许的创建线程数量为 Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM。

3. SimpleDateFormat 是线程不安全的类，一般不要定义为static变量，如果定义为static，必须加锁，或者使用 DateUtils 工具类。

   > 说明:如果是 JDK8 的应用，可以使用 Instant 代替 Date，LocalDateTime 代替 Calendar， DateTimeFormatter 代替 SimpleDateFormat，官方给出的解释:simple beautiful strong immutable thread-safe。

4. 在并发场景下，通过双重检查锁(double-checked locking)实现延迟初始化的优 化问题隐患(可参考 The "Double-Checked Locking is Broken" Declaration)，推荐解 决方案中较为简单一种(适用于 JDK5 及以上版本)，将目标属性声明为 volatile 型。

5. volatile 解决多线程内存不可见问题。对于一写多读，是可以解决变量同步问题， 但是如果多写，同样无法解决线程安全问题。如果是 count++操作，使用如下类实现: AtomicInteger count = new AtomicInteger(); count.addAndGet(1); 如果是 JDK8，推 荐使用 LongAdder 对象，比 AtomicLong 性能更好(减少乐观锁的重试次数)。

## （七）控制语句

1. 在一个 switch 块内，都必须包含一个 default 语句并且 放在最后，即使空代码。

2. 在 if/else/for/while/do 语句中必须使用大括号。即使只有一行代码，避免采用 单行的编码方式:

   `if (condition) statements;`

3. 在高并发场景中，避免使用”等于”判断作为中断或退出的条件。

   说明:如果并发控制没有处理好，容易产生等值判断被“击穿”的情况，使用大于或小于的区间判断条件来代替。

   反例:判断剩余奖品数量等于 0 时，终止发放奖品，但因为并发处理错误导致奖品数量瞬间变 成了负数，这样的话，活动无法终止。

4. 超过 3 层的 if-else 的逻辑判断代码可以使用卫语句、策略模式、状态模式等来实现。

   > 卫语句：即把一个嵌套的if-else判断条件，拆开拿到外面单独放置。

5. 避免使用`!`

## （八）注释规约

1. 类、类属性、类方法的注释必须使用 Javadoc 规范，使用`/**内容*/`格式，不得使用 // xxx方式。
2. 所有的抽象方法(包括接口中的方法)必须要用 Javadoc 注释、除了返回值、参数、 异常说明外，还必须指出该方法做什么事情，实现什么功能。
3. 方法内部单行注释，在被注释语句上方另起一行，使用//注释。方法内部多行注释使用/* */注释，注意与代码对齐。

## （九）其它

1. 注意 Math.random() 这个方法返回是 double 类型，注意取值的范围 0≤x<1(能够 取到零值，注意除零异常)，如果想获取整数类型的随机数，不要将 x 放大 10 的若干倍然后 取整，直接使用 Random 对象的 nextInt 或者 nextLong 方法。
2. 获取当前毫秒数 System.currentTimeMillis(); 而不是 new Date().getTime()

# 二、异常日志

1. 异常不要用来做流程控制，条件控制。异常设计的初衷是解决程序运行中的各种意外情况，且异常的处理效率比条件判断方式 要低很多。

2. NPE (NullPointerException)，OOM（OutOfMemory）

3. 防止 NPE，是程序员的基本修养，注意 NPE 产生的场景: 

   1) 返回类型为基本数据类型，return 包装数据类型的对象时，自动拆箱有可能产生 NPE。

   反例: public int f( ) { return Integer 对象}， 如果为 null，自动解箱抛 NPE。

   2)  数据库的查询结果可能为null。

   3)  集合里的元素即使isNotEmpty，取出的数据元素也可能为null。

   4)  远程调用返回对象时，一律要求进行空指针判断，防止NPE。

   5)  对于Session中获取的数据，建议NPE检查，避免空指针。

   6)  级联调用obj.getA().getB().getC();一连串调用，易产生NPE。

   正例:使用 JDK8 的 Optional 类来防止 NPE 问题。

4. 避免出现重复的代码(Don’t Repeat Yourself)，即DRY原则。

   说明: 随意复制和粘贴代码，必然会导致代码的重复，在以后需要修改时，需要修改所有的副本，容易遗漏。必要时抽取共性方法，或者抽象公共类，甚至是组件化。

   正例: 一个类中有多个 public 方法，都需要进行数行相同的参数校验操作，这个时候请抽取: private boolean checkParam(DTO dto) {...}

# 三、单元测试

1. 好的单元测试必须遵守 AIR 原则。
   - A: Automatic (自动化)
   - I:  Independent (独立性) 
   - R: Repeatable (可重复)

2. 测粒度至多是类级别，一般是方法级别。
3. 编写单元测试代码遵守 BCDE 原则，以保证被测试模块的交付质量。
   - B: Border，边界值测试，包括循环边界、特殊取值、特殊时间点、数据顺序等。  C:Correct，正确的输入，并得到预期的结果。
   - D: Design，与设计文档相结合，来编写单元测试。
   - E: Error，强制错误信息输入(如:非法数据、异常流程、非业务允许输入等)，并得到预期的结果。

# 四、安全规约

1. 用户输入的 SQL 参数严格使用参数绑定或者 METADATA 字段值限定，防止 SQL 注入， 禁止字符串拼接 SQL 访问数据库。
2. 表单、AJAX 提交必须执行 CSRF 安全验证。
    说明:CSRF(Cross-site request forgery)跨站请求伪造是一类常见编程漏洞。对于存在 CSRF 漏洞的应用/网站，攻击者可以事先构造好 URL，只要受害者用户一访问，后台便在用户 不知情的情况下对数据库中用户参数进行相应修改

# 五、MySQL数据库

## （一）建表规约

1. 表达是与否概念的字段，必须使用 is_xxx 的方式命名，数据类型是 unsigned tinyint (1 表示是，0 表示否)。

2. 数据库名、 表名、字段名，都不允许出现任何大写字母，避免节外生枝。

3. 表名、字段名必须使用小写字母或数字，禁止出现数字开头，禁止两个下划线中间只 出现数字。

4. 表名不使用复数名词。

5. 主键索引名为 pk_字段名;唯一索引名为 uk_字段名;普通索引名则为 idx_字段名。

   说明: pk_ 即 primary key;uk_ 即 unique key;idx_ 即 index 的简称。

6. 小数类型为 decimal，禁止使用 float 和 double。

   说明: float 和 double 在存储的时候，存在精度损失的问题，很可能在值的比较时，得到不 正确的结果。

7. varchar 是可变长字符串，不预先分配存储空间，长度不要超过 5000，如果存储长 度大于此值，定义字段类型为 text，独立出来一张表，用主键来对应，避免影响其它字段索引效率。

8. 表必备三字段:id, gmt_create, gmt_modified。 

   说明:其中id必为主键，类型为bigint unsigned、单表时自增、步长为1。gmt_create, gmt_modified 的类型均为 datetime 类型，前者现在时表示主动创建，后者过去分词表示被动更新。

9. 单表行数超过 500 万行或者单表容量超过 2GB，才推荐进行分库分表。

## （二）索引规约

1. 超过三个表禁止 join。需要 join 的字段，数据类型必须绝对一致;多表关联查询时， 保证被关联的字段需要有索引。

2. 在 varchar 字段上建立索引时，必须指定索引长度，没必要对全字段建立索引，根据实际文本区分度决定索引长度即可。

   说明: 索引的长度与区分度是一对矛盾体，一般对字符串类型数据，长度为 20 的索引，区分度会高达 90%以上，可以使用 count(distinct left(列名, 索引长度))/count(*)的区分度来确定

3. 建组合索引的时候，区分度最高的在最左边。

## （三）SQL语句

1. 不要使用 count(列名)或 count(常量)来替代 count(\*)，count(*)是 SQL92 定义的 标准统计行数的语法，跟数据库无关，跟 NULL 和非 NULL 无关。

   说明: count(*)会统计值为 NULL 的行，而 count(列名)不会统计此列为 NULL 值的行。

2. 使用 ISNULL()来判断是否为 NULL 值。 

   说明: NULL 与任何值的直接比较都为 NULL。

   1) NULL<>NULL的返回结果是NULL，而不是false。 

   2) NULL=NULL的返回结果是NULL，而不是true。 

   3) NULL<>1的返回结果是NULL，而不是true。

3. 不得使用外键与级联，一切外键概念必须在应用层解决。

4. 禁止使用存储过程，存储过程难以调试和扩展，更没有移植性。

5. 数据订正(特别是删除、修改记录操作)时，要先 select，避免出现误删除，确认

   无误才能执行更新语句。

## （四）ORM映射

1. POJO 类的布尔属性不能加 is，而数据库字段必须加 is_，要求在 resultMap 中进行 字段与属性之间的映射。
2. 不允许直接拿 HashMap 与 Hashtable 作为查询结果集的输出。
3. 更新数据表记录时，必须同时更新记录对应的 gmt_modified 字段值为当前时间。

# 六、工程结构

## （一）应用分层

1. 图中默认上层依赖于下层，箭头关系表示可直接依赖。 
    ![image-20210506160436974](https://tva1.sinaimg.cn/large/008i3skNly1gq8rpomzamj30og0jm45x.jpg)

- 开放接口层:可直接封装 Service 方法暴露成 RPC 接口;通过 Web 封装成 http 接口;进行 网关安全控制、流量控制等。
- 终端显示层:各个端的模板渲染并执行显示的层。当前主要是 velocity 渲染，JS 渲染， JSP 渲染，移动端展示等。
- Web 层:主要是对访问控制进行转发，各类基本参数校验，或者不复用的业务简单处理等。
- Service 层:相对具体的业务逻辑服务层。
- Manager 层:通用业务处理层，它有如下特征:
   1) 对第三方平台封装的层，预处理返回结果及转化异常信息;
   2) 对Service层通用能力的下沉，如缓存方案、中间件通用处理; 3) 与DAO层交互，对多个DAO的组合复用。
- DAO 层:数据访问层，与底层 MySQL、Oracle、Hbase 等进行数据交互。
- 外部接口或第三方平台:包括其它部门RPC开放接口，基础平台，其它公司的HTTP接口。

## （二）二方库依赖

1. 二方库版本号命名方式:主版本号.次版本号.修订号

   从后往前，数字的改变代表版本的改动越大。

   > 1) 主版本号:产品方向改变，或者大规模API不兼容，或者架构不兼容升级。
   > 2) 次版本号:保持相对兼容性，增加主要功能特性，影响范围极小的API不兼容修改。 
   > 3) 修订号:保持完全兼容性，修复BUG、新增次要功能特性等。
   >
   > 当前版本:1.3.3，那么下一个 合理的版本号:1.3.4 或 1.4.0 或 2.0.0

## （三）服务器

1. 高并发服务器建议调小 TCP 协议的 time_wait 超时时间。
   说明: 操作系统默认 240 秒后，才会关闭处于 time_wait 状态的连接，在高并发访问下，服务器端会因为处于 time_wait 的连接数太多，可能无法建立新的连接，所以需要在服务器上调小此等待值。
2. 调大服务器所支持的最大文件句柄数(File Descriptor，简写为fd)。主流的 linux 服务器默认所支持最大 fd 数量为 1024，当并发连接数很大时很容易因为 fd 不足而出现“open too many files”错误，导致新的连接无法建立。 建议将 linux服务器所支持的最大句柄数调高数倍(与服务器的内存数量相关)。

# 七、设计规约

1. 系统设计时，注意对扩展开放，对修改闭合。
2. 如果某个业务对象的状态超过 3 个，使用状态图来表达并且明确状态变化的各个触发 条件。
3. 如果系统中模型类超过 5 个，并且存在复杂的依赖关系，使用类图来表达并且明确类 之间的关系。

# 附：专有名词解释

1. 一方库: 本工程内部子项目模块依赖的库(jar 包)。

2. 二方库: 公司内部发布到中央仓库，可供公司内部其它应用依赖的库(jar包)。
3. 三方库: 公司之外的开源库(jar 包)。
4. ORM(Object Relation Mapping): 对象关系映射，对象领域模型与底层数据之间的转换， 本文泛指iBATIS, mybatis等框架。
5. SOA(Service-Oriented Architecture): 面向服务架构，它可以根据需求通过网络对松散耦合的粗粒度应用组件进行分布式部署、组合和使用，有利于提升组件可重用性，可维护性。
