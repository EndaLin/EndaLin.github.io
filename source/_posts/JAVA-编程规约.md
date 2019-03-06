---
title: JAVA 编程规约
date: 2019-03-06 20:54:51
tags: JAVA
---
# 编程规约
#### (一)　命名风格
- 代码中的命名均不能以** 下划线或美元符号 **开始，　也不能以** 下划线或美元符号 ** 结束
- 代码中的命名禁止使用**拼音与中文混合的方式**，　应当使用**正确的英文拼写**，杜绝完全不规范的缩写，避免望文不知义, 为了达到代码自解释的目标，任何自定义编程元素在命名时，使用**尽量完整的单词组合**来表达其意。
***注 :***　alibaba/taobao等国际通用的名称，可视同英文

- **类名**使用UpperCamelCase风格，但以下情形例外：DO/ BO / DTO/ VO/ AO/ PO/ UID等。
如UserDO
- 方法名、参数名、成员变量、局部变量都统一使用lowerCamelCase风格，必须遵从***驼峰形式***。
- 常量命名全部大写，单词间用下划线隔开，力求语义表达完整清楚，不要嫌名字长。
- 抽象类命名使用Abstract或Base开头
- 异常类命名使用Exception结尾
- 测试类命名以它要测试的类的名称开始，以Test结尾。
- 定义整形数组int[] arrayDemo
- POJO类中布尔类型的变量，都不要加is前缀，否则部分框架解析会引起序列化错误。（重要）
- 包名统一使用小写, 单数形式
- 类名如果有复数含义，类名可以使用复数形式
- 如果模块、接口、类、方法使用了设计模式，在命名时需体现出具体模式。
如　public　classOrder　Factory；
　　public　class　LoginProxy；
- 接口类的方法和属性不要添加任何的修饰符号(public 也不能添加), 以求保持代码的简洁性能，而且接口类的变量均为与接口方法相关的常量．
- 接口和实现类的命名有两套规则
> 对于Service和DAO类, 暴露出来的服务一定是接口，而其中的实现类命名以Impl为后缀

- 枚举类名建议带上Enum后缀，枚举成员名称需要全大写，单词间用下划线隔开

- 各层命名规约整合
> Service/DAO层方法命名规约
> > 获取单个对象的方法用get做前缀
> > 获取多个对象的方法用list做前缀，复数形式结尾如：listObjects
>> 获取统计值的方法用count做前缀
>> 插入的方法用save/insert做前缀
>> 删除的方法用remove/delete做前缀
>> 修改的方法用update做前缀
>
> 领域模型命名规约
>> 数据对象：xxxDO，xxx即为数据表名
>> 数据传输对象：xxxDTO，xxx为业务领域相关的名称
>> 展示对象：xxxVO，xxx一般为网页名称
>> POJO是DO/DTO/BO/VO的统称，禁止命名成xxxPOJO

#### (二)　常量定义
- 不允许任何魔法值（即未经预先定义的常量）直接出现在代码中
- 在long或者Long赋值时，数值后使用大写的L
如: public Long data = 2L;
- 不要使用一个常量类维护所有常量，要按常量功能进行归类，分开维护。

- 常量的复用层次有五层：跨应用共享常量、应用内共享常量、子工程内共享常量、包内共享常量、类内共享常量。
> 跨应用共享常量：放置在二方库中，通常是client.jar中的constant目录下。
> 应用内共享常量：放置在一方库中，通常是子模块中的constant目录下。
> 子工程内部共享常量：即在当前子工程的constant目录下。
> 包内共享常量：即在当前包下单独的constant目录下。
> 类内共享常量：直接在类内部private static final定义。

- 如果变量值仅在一个固定范围内变化用enum类型来定义
注：注意区分常量类和枚举类的区别

#### (三)　代码格式
对于代码格式的规范，阿里巴巴给出了一个正例，这个例子基本囊括了所以基本的规范操作
```java
public class Main {
    public static void main(String[] args) {
        // 注释的双斜线与注释内容之间有且仅有一个空格。
        //　缩进四格
        String say = "hello";
        //　运算符左右必须有一个空格
        int flag = 0;
        // if语句与括号之间必须有一个空格, 左大括号前加空格不换行，　左大括号后换行
        if (flag == 0) {
            System.out.println(say);
            //右大括号前换行，右大括号有else，　不用换行
        } else {
            System.out.println("ok");
            //右大括号结束必须换行
        }
    }
}
```
#### 补充
- 单行字符数限制不超过120个，超出需要换行，换行时遵循如下原则
> 第二行相对第一行缩进4个空格，从第三行开始，不再继续缩进
> 运算符与下文一起换行
> 方法调用的点符号与下文一起换行
> 方法调用中的多个参数需要换行时，在逗号后进行
> 在括号前不要换行

- 方法参数在定义和传入时，多个参数逗号后边必须加空格，如method(args1, args2, args3);
- IDE的textfileencoding设置为UTF-8;IDE中文件的换行符使用Unix格式，不要使用Windows格式。
- 单个方法的总行数不超过80行
- 不同逻辑、不同语义、不同业务的代码之间插入一个空行分隔开来以提升可读性。

#### (四)　OOP(面向对象程序设计)规约
- 避免通过一个类的对象引用访问此类的静态变量或静态方法，无谓增加编译器解析成本，直接用类名来访问即可。
- 所有的覆写方法，必须加@Override注解。
- 相同参数类型，相同业务含义，才可以使用Java的可变参数，避免使用Object
- 不能使用过时的类或方法, 接口过时必须加@Deprecated注解，作为调用方来说，有义务去考证过时方法的新实现是什么
- Object的equals方法容易抛空指针异常，应使用常量或确定有值的对象来调用equals，如"test".equals(object)
此处，阿里巴巴推荐使用java.util.Objects#equals（JDK7引入的工具类）
- 所有的相同类型的包装类对象之间值的比较，全部使用equals方法比较。
- 所有的POJO类属性必须使用包装数据类型
- 定义DO/DTO/VO等POJO类时，不要设定任何属性默认值
- POJO类必须写toString方法
- 序列化类新增属性时，请不要修改serialVersionUID字段，避免反序列失败；如果完全不兼容升级，避免反序列化混乱，那么请修改serialVersionUID值。
- 构造方法里面禁止加入任何业务逻辑，如果有初始化逻辑，请放在init方法中。
- 禁止在POJO类中，同时存在对应属性xxx的isXxx()和getXxx()方法
- 使用索引访问用String的split方法得到的数组时，需做最后一个分隔符后有无内容的检查，否则会有抛IndexOutOfBoundsException的风险。
- 当一个类有多个构造方法，或者多个同名方法，这些方法应该按顺序放置在一起，便于阅读，此条规则优先于第16条规则
- 类内方法定义的顺序依次是：公有方法或保护方法> 私有方法> getter/setter方法
**说明**：公有方法是类的调用者和维护者最关心的方法，首屏展示最好；保护方法虽然只是子类关心，也可能是“模板设计模式”下的核心方法；而私有方法外部一般不需要特别关心，是一个黑盒实现；因为承载的信息价值较低，所有Service和DAO的getter/setter方法放在类体最后
- setter方法中，参数名称与类成员变量名称一致，this.成员名=参数名。在getter/setter方法中，不要增加业务逻辑，增加排查问题的难度。
- 循环体内，字符串的连接方式，使用StringBuilder的append方法进行扩展，**避免资源浪费**
- final可以声明类、成员变量、方法、以及本地变量，下列情况使用final关键字
> 不允许被继承的类，如：String类。
> 不允许修改引用的域对象。
> 不允许被重写的方法，如：POJO类的setter方法。　
> 不允许运行过程中重新赋值的局部变量.
> 避免上下文重复使用一个变量，使用final描述可以强制重新定义一个变量，方便更好地进行重构。

- 慎用Object的clone方法来拷贝对象，因为clone方法默认的是**浅拷贝**
- 类成员与方法访问控制从严
> 如果不允许外部直接通过new来创建对象，那么构造方法必须是private。
> 工具类不允许有public或default构造方法
> 类非static成员变量并且与子类共享，必须是protected
> 类非static成员变量并且仅在本类使用，必须是private
> 类static成员变量如果仅在本类使用，必须是private
> 若是static成员变量，考虑是否为final
> 类成员方法只供类内部调用，必须是private
> 类成员方法只对继承类公开，那么限制为protected

**注**:任何类、方法、参数、变量，严控访问范围。过于宽泛的访问范围，不利于模块解耦。

#### (五)　集合处理
- 当equals()方法被override时，hashCode()也要被override。按照一般hashCode()方法的实现来说，相等的对象，它们的hash code一定相等。
> 因为Set存储的是不重复的对象，依据hashCode和equals进行判断，所以Set存储的对象必须重写这两个方法
> 如果自定义对象作为Map的键，那么必须重写hashCode和equals
> String重写了hashCode和equals方法，所以我们可以非常愉快地使用String对象作为key来使用
- ArrayList的subList结果不可强转成ArrayList，否则会抛出ClassCastException
> subList返回的是ArrayList的内部类SubList，并不是ArrayList而是ArrayList的一个视图，对于SubList子列表的所有操作最终会反映到原列表上
> 在subList场景中，高度注意对原集合元素的增加或删除，均会导致子列表的遍历、增加、删除产生ConcurrentModificationException异常。
- 使用集合转数组的方法，必须使用集合的toArray(T[]array)，传入的是类型完全一样的数组，大小就是list.size()
> 直接使用toArray无参方法存在问题，此方法返回值只能是Object[]类，若强转其它类型数组将出现ClassCastException错误。
> 正例:
String[] array = new String[list.size()];
array = list.toArray(array);
- 使用工具类Arrays.asList()把数组转换成集合时，不能使用其修改集合相关的方法，它的add/remove/clear方法会抛出UnsupportedOperationException异常
> asList的返回对象是一个Arrays内部类，并没有实现集合的修改方法。Arrays.asList体现的是适配器模式，只是转换接口，后台的数据仍是数组
> String[] str = new String[] { "you", "wu" };
> Listlist = Arrays.asList(str);
> 第一种情况：list.add("yangguanbao"); 运行时异常。
> 第二种情况：str[0]= "gujin";那么list.get(0)也会随之修改。
- 泛型通配符<? extendsT>来接收返回的数据，此写法的泛型集合不能使用add方法，而<? superT>不能使用get方法，作为接口调用赋值时易出错
> 繁往外读取内容的，适合用<? extendsT>
> 经常往里插入的，适合用<? superT>
- 不要在foreach循环里进行元素的remove/add操作。remove元素请使用Iterator方式，如果并发操作，需要对Iterator对象加锁。iterator.remove.
- 在JDK7版本及以上，Comparator实现类要满足如下三个条件，不然Arrays.sort，Collections.sort会报IllegalArgumentException异常。
> x，y的比较结果和y，x的比较结果相反
> x>y，y>z，则x>z
> x=y，则x，z比较结果和y，z比较结果相同
- 集合泛型定义时，在JDK7及以上，使用diamond语法或全省略。说明：菱形泛型，即diamond，直接使用<>来指代前边已经指定的类型。
```java
// <> diamond方式
HashMap<String, String> userCache = new HashMap<>(16);
// 全省略方式
ArrayList<User> users = new ArrayList(10);
```
- 集合初始化时，指定集合初始值大小。
> initialCapacity=(需要存储的元素个数/负载因子) + 1。注意负载因子（即loader factor）默认为0.75，如果暂时无法确定初始值大小，请设置为16（即默认值）。
- 使用entrySet遍历Map类集合KV，而不是keySet方式进行遍历
> keySet其实是遍历了2次，一次是转为Iterator对象，另一次是从hashMap中取出key所对应的value。而entrySet只是遍历了一次就把key和value都放到了entry中，效率更高。如果是JDK8，使用Map.foreach方法
- 高度注意Map类集合K/V能不能存储null值的情况
- 利用Set元素唯一的特性，可以快速对一个集合进行去重操作，避免使用List的contains方法进行遍历、对比、去重操作。
- 合理利用好集合的有序性(sort)和稳定性(order)，避免集合的无序性(unsort)和不稳定性(unorder)带来的负面影响
> 有序性是指遍历的结果是按某种比较规则依次排列的
> 稳定性指集合每次遍历的元素次序是一定的
> 如：ArrayList是order/unsort；HashMap是unorder/unsort；TreeSet是order/sort

#### (六)　并发处理
- 获取单例对象需要保证线程安全，其中的方法也要保证线程安全
> 资源驱动类、工具类、单例工厂类都需要注意。
- 创建线程或线程池时请指定有意义的线程名称，方便出错时回溯
- 线程资源必须通过线程池提供，不允许在应用中自行显式创建线程
- 线程池不允许使用Executors去创建，而是通过ThreadPoolExecutor的方式
- SimpleDateFormat是线程不安全的类，一般不要定义为static变量，如果定义为static，必须加锁，或者使用DateUtils工具类。
> 如果是JDK8的应用，可以使用Instant代替Date，LocalDateTime代替Calendar，DateTimeFormatter代替SimpleDateFormat，官方给出的解释：simplebeautifulstrongimmutablethread-safe
- 高并发时，同步调用应该去考量锁的性能损耗。能用无锁数据结构，就不要用锁；能锁区块，就不要锁整个方法体；能用对象锁，就不要用类锁
> 尽可能使加锁的代码块工作量尽可能的小，避免在锁代码块中调用RPC方法
- 对多个资源、数据库表、对象同时加锁时，需要保持一致的加锁顺序，否则可能会造成死锁
> 举个例子：线程一需要对表A、B、C依次全部加锁后才可以进行更新操作，那么线程二的加锁顺序也必须是A、B、C，否则可能出现死锁。

- 并发修改同一记录时，避免更新丢失，需要加锁。要么在应用层加锁，要么在缓存加锁，要么在数据库层使用乐观锁，使用version作为更新依据
> 如果每次访问冲突概率小于20%，推荐使用乐观锁，否则使用悲观锁。乐观锁的重试次数不得小于3次
- 多线程并行处理定时任务时，Timer运行多个TimeTask时，只要其中之一没有捕获抛出的异常，其它任务便会自动终止运行，使用ScheduledExecutorService则没有这个问题
- 使用CountDownLatch进行异步转同步操作，每个线程退出前必须调用countDown方法，线程执行代码注意catch异常，确保countDown方法被执行到，避免主线程无法执行至await方法，直到超时才返回结果
> 注意，子线程抛出异常堆栈，不能在主线程try-catch到
- 避免Random实例被多线程使用，虽然共享该实例是线程安全的，但会因竞争同一seed导致的性能下降
> 在JDK7之后，可以直接使用APIThreadLocalRandom，而在JDK7之前，需要编码保证每个线程持有一个实例
- 在并发场景下，通过双重检查锁（double-checkedlocking）实现延迟初始化的优化问题隐患(可参考The"Double-CheckedLockingisBroken" Declaration)，推荐解决方案中较为简单一种（适用于JDK5及以上版本），将目标属性声明为volatile型
- HashMap在容量不够进行resize时由于高并发可能出现死链，导致CPU飙升，在开发过程中可以使用其它数据结构或加锁来规避此风险
- ThreadLocal无法解决共享对象的更新问题，ThreadLocal对象建议使用static修饰。这个变量是针对一个线程内所有操作共享的，所以设置为静态变量，所有此类实例共享此静态变量，也就是说在类第一次被使用时装载，只分配一块存储空间，所有此类的对象(只要是这个线程内定义的)都可以操控这个变量
#### (六)　控制语句
- 在一个switch块内，每个case要么通过break/return等来终止，要么注释说明程序将继续执行到哪一个case为止；在一个switch块内，都必须包含一个default语句并且放在最后，即使空代码
- 在if/else/for/while/do语句中必须使用大括号。
- 在高并发场景中，避免使用”等于”判断作为中断或退出的条件
- 表达异常的分支时，少用if-else方式，这种方式可以改写成
```java
if (condition) {
...
return obj;
}
// 接着写else的业务逻辑代码;
```
> 如果非得使用if()...else if()...else...方式表达逻辑，【强制】避免后续代码维护困难，请勿超过3层
> 超过3层的if-else的逻辑判断代码可以使用卫语句、策略模式、状态模式等来实现

其中卫语句示例如下：
```
public void today() {
if (isBusy()) {
System.out.println(“change time.”);
return;
}

if (isFree()) {
System.out.println(“go to travel.”);
return;
}

System.out.println(“stay at home to learn Alibaba Java Coding Guidelines.”);
return;
}
```
- 除常用方法（如getXxx/isXxx）等外，不要在条件判断中执行其它复杂的语句，将复杂逻辑判断的结果赋值给一个有意义的布尔变量名，以提高可读性。
如：
```
finalboolean existed = (file.open(fileName, "w") != null) &&(...) || (...);finalboolean existed = (file.open(fileName, "w") != null) &&(...) || (...);
```
- 循环体中的语句要考量性能，以下操作尽量移至**循环体外**处理，如定义对象、变量、获取数据库连接，进行不必要的try-catch操作（这个try-catch是否可以移至循环体外）。
- 避免采用取反逻辑运算符 -- !
- 接口入参保护，这种场景常见的是用作批量操作的接口
- 下列情形，需要进行参数校验
> 调用频次低的方法
> 执行时间开销很大的方法。此情形中，参数校验时间几乎可以忽略不计，但如果因为参数错误导致中间执行回退，或者错误，那得不偿失
> 需要极高稳定性和可用性的方法
> 对外提供的开放接口，不管是RPC/API/HTTP接口
> 敏感权限入口
- 下列情形，不需要进行参数校验
> 极有可能被循环调用的方法。但在方法说明里必须注明外部参数检查要求
> 底层调用频度比较高的方法。毕竟是像纯净水过滤的最后一道，参数错误不太可能到底层才会暴露问题。一般DAO层与Service层都在同一个应用中，部署在同一台服务器中，所以DAO的参数校验，可以省略
> 被声明成private只会被自己代码所调用的方法，如果能够确定调用方法的代码传入参数已经做过检查或者肯定不会有问题，此时可以不校验参数
#### (八)　代码规约
- 类、类属性、类方法的注释必须使用Javadoc规范，使用/**内容*/格式，不得使用//xxx方式。
> 在IDE编辑窗口中，Javadoc方式会提示相关注释，生成Javadoc可以正确输出相应注释；在IDE中，工程调用方法时，不进入方法即可悬浮提示方法、参数、返回值的意义，提高阅读效率
- 所有的抽象方法（包括接口中的方法）必须要用Javadoc注释、除了返回值、参数、异常说明外，还必须指出该方法做什么事情，实现什么功能。
- 所有的类都必须添加创建者和创建日期
- 方法内部单行注释，在被注释语句上方另起一行，使用//注释。方法内部多行注释使用/* */注释，注意与代码对齐
- 所有的枚举类型字段必须要有注释，说明每个数据项的用途
- 与其“半吊子”英文来注释，不如用中文注释把问题说清楚。专有名词与关键字保持英文原文即可
- 代码修改的同时，注释也要进行相应的修改，尤其是参数、返回值、异常、核心逻辑等的修改
- 注释的要求：
> 第一、能够准确反应设计思想和代码逻辑
> 第二、能够描述业务含义，使别的程序员能够迅速了解到代码背后的信息
> 第三、注释力求精简准确、表达到位, 避免泛滥
- 特殊注释标记，请注明标记人与标记时间。注意及时处理这些标记，通过标记扫描，经常清理此类标记。线上故障有时候就是来源于这些标记处的代码
> 待办事宜（TODO）:（标记人，标记时间，[预计处理时间]）
> 错误，不能工作（FIXME）:（标记人，标记时间，[预计处理时间]）
#### (九)其它
- 在使用正则表达式时，利用好其预编译功能，可以有效加快正则匹配速度
> 说明：不要在方法体内定义：Patternpattern= Pattern.compile(“规则”)
- velocity调用POJO类的属性时，建议直接使用属性名取值即可，模板引擎会自动按规范调用POJO的getXxx()，如果是boolean基本数据类型变量（boolean命名不需要加is前缀），会自动调用isXxx()方法
- 后台输送给页面的变量必须加$!{var}——中间的感叹号
> 如果var等于null或者不存在，那么${var}会直接显示在页面上
- 注意Math.random()这个方法返回是double类型，注意取值的范围0≤x<1（能够取到零值，注意除零异常），如果想获取整数类型的随机数,直接使用Random对象的nextInt或者nextLong方法
- 获取当前毫秒数System.currentTimeMillis();而不是newDate().getTime();
> 如果想获取更加精确的纳秒级时间值，使用System.nanoTime()的方式。在JDK8中，针对统计时间等场景，推荐使用Instant类
- 不要在视图模板中加入任何复杂的逻辑
> 根据MVC理论，视图的职责是展示，不要抢模型和控制器的活
- 任何数据结构的构造或初始化，都应指定大小，避免数据结构无限增长吃光内存
- 及时清理不再使用的代码段或配置信息
