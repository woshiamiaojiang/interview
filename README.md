你好。我叫XX。之前在上海微创BPO部门做过三年Java开发。

1、开发中Java用了比较多的数据结构有哪些？（待补充arraylist,treemap）
ArrayList。HashMap。

map存数据是无序的，怎么有序输出
按插入序LinkedHashMap
按大小序TreeMap

HashTable和HashMap区别
1、继承的父类不同
Hashtable继承自Dictionary类，而HashMap继承自AbstractMap类。但二者都实现了Map接口。
2、线程安全性不同
Hashtable 中的方法是Synchronize的，而HashMap中的方法在是非Synchronize的。
3、是否提供contains方法
HashMap把Hashtable的contains方法去掉了，改成containsValue和containsKey，因为contains方法容易让人引起误解。
4、key和value是否允许null值
5、两个遍历方式的内部实现上不同
Hashtable、HashMap都使用了 Iterator。而由于历史原因，Hashtable还使用了Enumeration的方式 
6、hash值不同
哈希值的使用不同，HashTable直接使用对象的hashCode。而HashMap重新计算hash值。
7、内部实现使用的数组初始化和扩容方式不同
HashTable在不指定容量的情况下的默认容量为11，而HashMap为16，Hashtable不要求底层数组的容量一定要为2的整数次幂，而HashMap则要求一定为2的整数次幂。
Hashtable扩容时，将容量变为原来的2倍加1，而HashMap扩容时，将容量变为原来的2倍。

2、谈谈你对HashMap的理解，底层原理的基本实现，HashMap怎么解决碰撞问题的？
HashMap是数组+链表的结构。JDK8中，当链表的长度达到8时，链表会转成红黑树。通过红黑树快速增删改查的特点提高HashMap的性能。
第一次put操作时开辟一个16容量的哈希桶数组。每个元素是一个Entry对象。包含key,value,next,hash四个属性。
通过对key的hash算法确定entry元素的位置。hash算法的三个步骤：取key的hashCode值，位移异或，按位与运算。默认初始容量为16是为了符合Hash算法均匀分布原则。
key是null，放在0下标位置。如果没有碰撞，直接放在对应下标位置。如果碰撞了，看key是否相同，相同则覆盖value。不同则往链表头上插入元素，因为作者认为后插入的有更大概率被搜索。
此时如果元素个数达到数组容量*0.75负载因子时，数组会扩容为原来两倍。所有元素重新hash确定下标位置。频繁扩容会影响性能。所以《阿里巴巴开发手册》规定hashmap初始化时要指定大小。在多线程情况下同时rehash有可能会出现带环链表。所以高并发情况下使用concurrentHashmap。concurrenthashmap有2的n次方个segment。通过锁分段技术，每个segment高度自治，相互独立，降低锁的粒度。由此兼顾性能与安全。

3、What is an efficient way to implement a singleton pattern in Java?
```
public enum SingletonEnum {
    INSTANCE;
}
```
枚举的线程安全，底层已经做了保证。
枚举中的枚举项是通过static定义的。
当一个Java类第一次被真正使用到的时候静态资源被初始化、Java类的加载和初始化过程都是线程安全的（因为虚拟机在加载枚举的类的时候，会使用ClassLoader的loadClass方法，而这个方法使用同步代码块保证了线程安全）。所以，创建一个enum类型是线程安全的。

4、对JVM熟不熟悉？简单说说类加载过程，里面执行的那些操作？问了GC和内存管理，平时在tomcat里面有没有进行相关的配置。
类加载的过程包括了加载、验证、准备、解析、初始化五个阶段。
加载、验证、准备和初始化这四个阶段发生的顺序是确定的，而解析阶段则不确定，是为了支持Java语言的运行时绑定。
加载：通过类的全限定名取得类的二进制流，转为方法区数据结构，在Java堆中生成对应的Class对象，作为对方法区这些数据的访问入口。
验证：文件格式、元数据、字节码、符号引用验证。
准备：类变量分配内存并设置类变量初始值。
解析：符号引用替换为直接引用，类或接口的解析，字段解析，类方法解析，接口方法解析。
初始化：若该类具有超类，则对其进行初始化，执行静态初始化器和静态初始化成员变量。

双亲委派机制
要求除了顶层的启动类加载器外，其余的类加载器都应该有自己的父类加载器。这里父子关系是组合关系。
如果一个类加载器收到了类加载的请求，先把这个请求委派给父类加载器去完成，只有当父加载器反馈自己无法完成加载请求时，子加载器才会尝试自己去加载。
好处：Java类随着它的类加载器一起具备了一种带有优先级的层次关系，通过这种层级关可以避免类的重复加载。

如何判断对象死亡？
两种方法。引用计数法（给对象中添加一个引用计数器，每当有一个地方引用它，计数器就加1；当引用失效，计数器就减1；任何时候计数器为0的对象就是不可能再被使用的。）。
可达性分析算法。




5、然后问了http协议，get和post的基本区别，接着tcp/ip协议，三次握手，窗口滑动机制。
HTTP用于客户端和服务端之间的通信，请求资源的一端为客户端，而提供响应资源的一端为服务端。
客户端请求的内容包括：请求方法、URI、协议版本、请求头部字段、请求主体。

GET和POST请求都是http的请求方式，用户通过不同的http的请求方式完成对资源（url）的不同操作。GET，POST，PUT，DELETE就对应着对这个资源的查 ，改 ，增 ，删 4个操作,具体点来讲GET一般用于获取/查询资源信息，而POST一般用于更新资源信息
GET是幂等的，因为不会修改数据。POST不是幂等的。
GET参数通过URL传递，POST放在Request body中。
GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
GET请求在URL中传送的参数是有长度限制的，而POST没有。
对参数的数据类型，GET只接受ASCII字符，而POST没有限制。
GET请求只能进行url编码，而POST支持多种编码方式。
GET在浏览器回退时是无害的，而POST会再次提交请求。
GET产生的URL地址可以被收藏为书签，而POST不可以。
GET请求会被浏览器主动cache，而POST不会，除非手动设置。
GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。

TCP/IP协议是一个协议集合。包含HTTP、FTP、TCP、IP、DNS、UDP等。
TCP/IP协议的分层管理：应用层、传输层、网络层、链路层。及通信传输流。

TCP用三次握手和滑动窗口机制来保证传输的可靠性和进行流量控制。
第一次握手:
客户端发送一个TCP的SYN标志位置1的包指明客户打算连接的服务器的端口，以及初始序号X,保存在包头的序列号(Sequence Number)字段里。
第二次握手:
服务器发回确认包(ACK)应答。即SYN标志位和ACK标志位均为1同时，将确认序号(Acknowledgement Number)设置为客户的ISN加1以.即X+1。
第三次握手.
客户端再次发送确认包(ACK) SYN标志位为0,ACK标志位为1.并且把服务器发来ACK的序号字段+1,放在确定字段中发送给对方.并且在数据段放写ISN的+1



TCP四次挥手：（1）客户端A发送一个FIN，用来关闭客户A到服务器B的数据传送。       
（2）服务器B收到这个FIN，它发回一个ACK，确认序号为收到的序号加1。和SYN一样，一个FIN将占用一个序号。       
（3）服务器B关闭与客户端A的连接，发送一个FIN给客户端A。       
（4）客户端A发回ACK报文确认，并将确认序号设置为收到序号加1。






滑动窗口算法？

1. 首先是AB之间三次握手建立TCP连接。在报文的交互过程中，A将自己的缓冲区大小（窗口大小）3发送给B，B同理，这样双方就知道了对端的窗口大小。
2. A开始发送数据，A连续发送3个单位的数据，因为他知道B的缓冲区大小。在这一波数据发送完后，A就不能再发了，需等待B的确认。
3. A发送过来的数据逐渐将缓冲区填满。
4. 这时候缓冲区中的一个报文被进程读取，缓冲区有了一个空位，于是B向A发送一个ACK，这个报文中指示窗口大小为1。
A收到B发过来的ACK消息，并且知道B将窗口大小调整为1，因此他只发送了一个单位的数据并且等待B的下一个确认报文。
5. 如此反复。



6、开发中用了那些数据库？回答mysql，存储引擎有哪些？然后问了我悲观锁和乐观锁问题使用场景、分布式集群实现的原理。

![](https://i.imgur.com/GMJxtHB.png)
1. MyIsam , 2. InnoDB, 3. Memory, 4. Blackhole, 5. CSV, 6. Performance_Schema, 7. Archive, 8. Federated , 9 Mrg_Myisam


乐观锁假设认为数据一般情况下不会造成冲突，所以在数据进行提交更新的时候，才会正式对数据的冲突与否进行检测，如果发现冲突了，则让返回用户错误的信息，让用户决定如何去做。

相对于悲观锁，在对数据库进行处理的时候，乐观锁并不会使用数据库提供的锁机制。一般的实现乐观锁的方式就是记录数据版本。

悲观并发控制实际上是“先取锁再访问”的保守策略，为数据处理的安全提供了保证。但是在效率方面，处理加锁的机制会让数据库产生额外的开销，还有增加产生死锁的机会；另外，在只读型事务处理中由于不会产生冲突，也没必要使用锁，这样做只能增加系统负载；还有会降低了并行性，一个事务如果锁定了某行数据，其他事务就必须等待该事务处理完才可以处理那行数

分布式系统的CAP理论：  一致性、可用性、分区容错性。
用多个服务器来处理同一个功能或存储数据,来提高对多用户请求的处理,防止一个服务器处理不过来。一般会通过反向代理(分发请求)来实现集群的功能（nginx：一个反向代理的软件）。


7、然后问了我springmvc和mybatis的工作原理，有没有看过底层源码？
https://www.zhihu.com/question/21346206/answer/391667981

8、redis存储类型、事务、使用场景。

二、京东金融面试

1、Dubbo超时重试；Dubbo超时时间设置

2、如何保障请求执行顺序

3、分布式事物与分布式锁（扣款不要出现负数）

4、分布式session设置

5、执行某操作，前50次成功，第51次失败a全部回滚b前50次提交第51次抛异常，ab场景分别如何设置Spring（传播性）

6、Zookeeper有哪些用

7、JVM内存模型

8、数据库垂直和水平拆分

9、MyBatis如何分页；如何设置缓存；MySQL分页

10、熟悉IO么？与NIO的区别，阻塞与非阻塞的区别

11、分布式session一致性

12、分布式接口的幂等性设计「不能重复扣款」

三、美团面试

1、最近做的比较熟悉的项目是哪个？画一下项目技术架构图

2、JVM老年代和新生代的比例？

3、YGC和FGC发生的具体场景

4、jstack，jmap，jutil分别的意义？如何线上排查JVM的相关问题？

5、线程池的构造类的方法的5个参数的具体意义？

6、单机上一个线程池正在处理服务如果忽然断电该怎么办？（正在处理和阻塞队列里的请求怎么处理）？

7、使用无界阻塞队列会出现什么问题？

8、接口如何处理重复请求？

9、具体处理方案是什么？

10、如何保证共享变量修改时的原子性？

11、设计一个对外服务的接口实现类，在1,2,3这三个主机（对应不同IP）上实现负载均衡和顺序轮询机制（考虑并发）


四、滴滴面试

1、自我介绍，技术特点

2、兴趣是什么，优势是什么

3、jvm，jre以及jdk三者之间的关系？

4、Dubbo的底层原理，Zookeeper是什么

5、cincurrentMap的机制；TreeMap；Volatil关键字

6、快速排序；广度优先搜索（队列实现）

7、缓存的雪崩以及穿透的理解？

8、HashMap的key可以重复吗？

9、synchronized和lock的区别？

10.开发一个大型网站你会考虑哪些问题?



面试题
1、String和StringBuffer的区别
String字符串常量。StringBuffer字符串变量。
String线程安全。StringBUffer线程不安全。
字符串字面量可以复制给String。

2、gc的概念，如果A和B对象循环引用，是否可以被GC？
3、Java中的内存溢出是如何造成的
4、String s = “123”;这个语句有几个对象产生
5、Error、Exception和RuntimeException的区别，作用又是什么？
6、列举3个以上的RuntimeException
7、reader和inputstream区别
8、hashCode的作用
9、Object类中有哪些方法，列举3个以上（可以引导）
10、char型变量中能不能存贮一个中文汉字?为什么?
11、列举几个Java Collection类库中的常用类
12、List、Set、Map是否都继承自Collection接口？
13、HashMap和Hashtable的区别
14、HashMap中是否任何对象都可以做为key,用户自定义对象做为key有没有什么要求？
15、interface 和 abstrat class的区别 是否可以继承多个接口，是否可以继承多个抽象类
16、 启动一个线程是用run()还是start()? 多线程有几种实现 同步和并发是如何解决的 什么叫守护线程，用什么方法实现守护线程（Thread.setDeamon()的含义） 如何停止一个线程？ 解释是一下什么是线程安全？举例说明一个线程不安全的例子。解释Synchronized关键字的作用。 当一个线程进入一个对象的一个synchronized方法后，其它线程是否可进入此对象的其它方法?
17、了解过哪些JDK8的新特性，举例描述下相应的特性？
18、对sql进行优化的原则有哪些？
19、servlet生命周期是生命与cgi的区别？
20、StringBuffer有什么优势？为什么快？

other
谈谈你对HashMap的理解，底层原理的基本实现，HashMap怎么解决碰撞问题的？
开发中用了那些数据库？回答mysql，储存引擎有哪些？然后问了我悲观锁和乐观锁问题使用场景、分布式集群实现的原理。
springmvc和mybatis的工作原理，有没有看过底层源码？
熟悉IO么？与NIO的区别，阻塞与非阻塞的区别
微信红包怎么实现。
海量数据分析。
线程安全和非线程安全。
HTTP2.0、thrift。
java反射应用
分布式事务一致性。
nio的底层实现。
jvm基础是必问的，jvm GC原理，JVM怎么回收内存。
API接口与SDI接口的区别
dubbo如何一条链接并发多个调用。Dubbo的原理，序列化相关问题。
用过哪些中间件。
做过工作流引擎没有。
以前的工作经历，自己觉得出彩的地方
线程池的一些原理，锁的机制升降级
从系统层面考虑，分布式从哪些纬度考虑
Hadoop底层怎么实现
threadLocal，线程池，hashMap/hashTable/coccurentHashMap等
秒杀系统的设计
虚拟机，IO相关知识点
Linux的命令
一个整形数组，给定一个数，在数组中找出两个数的和等于这个数，并打印出来，我写的时间复杂度高，要求O(n)。
n个整数，找出连续的m个数加和是最大。
更重视开源技术
数据库锁隐原理
1000个线程同时运行，怎么防止不卡
并列的并发消费问题）
高并发量大的话怎么处理热点，数据等
如何获取一个本地服务器上可用的端口
流量控制相关问题
数据库TPS是多少，是否进行测试过
缓存击穿有哪些方案解决
Java怎么挖取回收器相关原理
Java的集合都有哪些，都有什么特点
分布式锁，redis缓存，spring aop，系统架构图，MySQL的特性
场景，同时给10万个人发工资，怎么样设计并发方案，能确保在1分钟内全部发完 打个比方会提出类似的场景

JVM调优
那么面试时，如何回答JVM调优？主要需要你能回答两方面的问题：如何观察JVM的运行参数。比如你发现Java程序跑得慢，定期卡顿，OOM挂掉。为了找原因，你必须学会怎么去看JVM到底怎么运行的。这时需要你了解一些JVM自带的工具命令，比如jps、jstack、jhat，还有比较新的Java自带的JMC图形界面工具。通过他们你可以了解到一个Java进程有多少线程，每个线程什么状态，是不是在等着锁：进程的CPU和内存占用了多少；GC状态怎么样，Full GC频繁不频繁等等；内存中是不是有泄露，哪个地方可能泄漏了等等。如何给出方案。你要明白JVM的工作原理，根据运行参数解决你的问题。比如你发现程序启动慢，你可能猜测有可能是代码没写好，就是跑得慢；也许是一开始load资源过大，而heap不大，造成反复Full GC。你必须通过运行参数来印证你的猜想，然后有针对的去解决那些问题。为此，你需要大致明白多线程的工作原理，Java内存管理，GC的工作原理（串行、CMS和G1）等。

在实际开发中，通过看JDK自带的图形化工具，检查线程的状态。如果程序运行慢，看看哪些线程在等待锁，有可能发生死锁，这时可以定位到具体的代码进行排查。这是我在开发中遇到过的情形。




整理了一些之前本人或者同学面试被问到的问题~~

1. java基础以及多个“比较”

1.Collections.sort排序内部原理

在Java 6中Arrays.sort()和Collections.sort()使用的是MergeSort，而在Java 7中，内部实现换成了TimSort，其对对象间比较的实现要求更加严格

2.hashMap原理，java8做的改变

从结构实现来讲，HashMap是数组+链表+红黑树（JDK1.8增加了红黑树部分）实现的。HashMap最多只允许一条记录的键为null，允许多条记录的值为null。HashMap非线程安全。ConcurrentHashMap线程安全。解决碰撞：当出现冲突时，运用拉链法，将关键词为同义词的结点链接在一个单链表中，散列表长m，则定义一个由m个头指针组成的指针数组T，地址为i的结点插入以T(i)为头指针的单链表中。Java8中，冲突的元素超过限制（8），用红黑树替换链表。

3.String 和 StringBuilder 的区别

1）可变与不可变：String不可变，每一次执行“+”都会新生成一个新对象，所以频繁改变字符串的情况中不用String，以节省内存。

2）是否多线程安全：StringBuilder并没有对方法进行加同步锁，所以是非线程安全的。StringBuffer和String均线程安全。

4.Vector 与 Array 的区别

1）ArrayList在内存不够时默认是扩展50% + 1个，Vector是默认扩展1倍。

2）Vector属于线程安全级别的，但是大多数情况下不使用Vector，因为线程安全需要更大的系统开销。

5.HashMap 与 Hashtable 的区别

1） 历史原因: Hashtable继承Dictonary类, HashMap继承自abstractMap

2） HashMap允许空的键值对, 但最多只有一个空对象，而HashTable不允许。 

3） HashTable同步，而HashMap非同步，效率上比HashTable要高

6.ConncurrentHashMap和hashtable比较，两个线程并发访问map中同一条链，一个线程在尾部删除，一个线程在前面遍历查找，问为什么前面的线程还能正确的查找到后面被另一个线程删除的节点）

ConcurrentHashMap融合了hashtable和hashmap二者的优势。hashtable是做了同步的，即线程安全，hashmap未考虑同步。所以hashmap在单线程情况下效率较高。hashtable在的多线程情况下，同步操作能保证程序执行的正确性。但是hashtable是阻塞的，每次同步执行的时候都要锁住整个结构，ConcurrentHashMap正是为了解决这个问题而诞生的，

ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术（一个Array保存多个Object，使用这些对象的锁作为分离锁，get/put时随机使用任意一个）。它使用了多个锁来控制对hash表的不同部分进行的修改。在JDK 1.6中，有HashEntry结构存在，每次插入将新添加节点作为链的头节点（同HashMap实现），而且每次删除一个节点时，会将删除节点之前的所有节点拷贝一份组成一个新的链，而将当前节点的上一个节点的next指向当前节点的下一个节点，从而在删除以后有两条链存 在，因而可以保证即使在同一条链中，有一个线程在删除，而另一个线程在遍历，它们都能工作良好，因为遍历的线程能继续使用原有的链。

Java8中，采用volatile HashEntry保存数据，table元素作为锁；从table数组+单向链表加上了红黑树。红黑树是一种特别的二叉查找树，特性为：1.节点为红或者黑 2.根节点为黑 3.叶节点为黑 4.一节点为红，则叶节点为黑 5.一节点到其子孙节点所有路径上的黑节点数目相同。

7.ArrayList
与 LinkedList 的区别？

最明显的区别是
ArrrayList 底层的数据结构是数组，支持随机访问，而 LinkedList 的底层数据结构书链表，不支持随机访问。使用下标访问一个元素，ArrayList 的时间复杂度是 O(1)，而 LinkedList 是 O(n)。LinkedList是双向链表

8.Java 中，Comparator 与
Comparable 有什么不同？

Comparable 接口用于定义对象的自然顺序，是排序接口，而 comparator 通常用于定义用户定制的顺序，是比较接口。我们如果需要控制某个类的次序，而该类本身不支持排序(即没有实现Comparable接口)，那么我们就可以建立一个“该类的比较器”来进行排序。Comparable 总是只有一个，但是可以有多个 comparator 来定义对象的顺序。

9.抽象类是什么？它与接口有什么区别？你为什么要使用过抽象类？

抽象类是指不允许被实例化的类；一个类只能使用一次继承关系。但是，一个类却可以实现多个interface。

abstract class和interface所反映出的设计理念不同。其实abstract class表示的是"is-a"关系，interface表示的是"like-a"关系

实现抽象类和接口的类必须实现其中的所有方法。抽象类中可以有非抽象方法。接口中则不能有实现方法。但在Java8中允许接口中有静态默认的方法。

接口中定义的变量默认是public static final 型，且必须给其初值，所以实现类中不能重新定义，也不能改变其值。抽象类中的变量默认是 friendly 型，其值可以在子类中重新定义，也可以重新赋值。
子类中实现父类中的抽象方法时，可见性可以大于等于父类中的；而接口实现类中的接口 方法的可见性只能与接口中相同（public）。
用抽象类是为了重用。减少编码量，降低耦合性。

10.描述 Java 中的重载和重写？

重载和重写都允许你用相同的名称来实现不同的功能，但是重载是编译时活动，而重写是运行时活动。你可以在同一个类中重载方法，但是只能在子类中重写方法。重写必须要有继承

重写：1、在子类中可以根据需要对从基类中继承来的方法进行重写。2、重写的方法和被重写的方法必须具有相同方法名称、参数列表和返回类型。3、重写方法不能使用比被重写的方法更严格的访问权限。

重载的时候，方法名要一样，但是参数类型和个数不一样，返回值类型可以相同也可以不相同。无法以返回型别作为重载函数的区分标准。

11.
Collection与Collections的区别是什么？

Collection<E>是Java集合框架中的基本接口；

Collections是Java集合框架提供的一个工具类，其中包含了大量用于操作或返回集合的静态方法。

12.
Java中多态的实现原理

所谓多态，指的就是父类引用指向子类对象，调用方法时会调用子类的实现而不是父类的实现。多态的实现的关键在于“动态绑定”。

13.
object中定义了哪些方法？

clone(), equals(), hashCode(), toString(), notify(), notifyAll(),
wait(), finalize(), getClass()

14. Java泛型和类型擦除

泛型即参数化类型，在创建集合时，指定集合元素的类型，此集合只能传入该类型的参数。类型擦除：java编译器生成的字节码不包含泛型信息，所以在编译时擦除：1.泛型用最顶级父类替换；2.移除。

15.说出 5 个 JDK 1.8 引入的新特性？

Java 8 在 Java 历史上是一个开创新的版本，下面 JDK 8 中 5 个主要的特性：
Lambda 表达式；允许像对象一样传递匿名函数 Stream API，充分利用现代多核 CPU，可以写出很简洁的代码 ；Date 与 Time API，最终，有一个稳定、简单的日期和时间库可供你使用 扩展方法，现在，接口中可以有静态、默认方法； 重复注解，现在你可以将相同的注解在同一类型上使用多次。

16.java中public，private，protected以及默认关键字的访问范围：

Protected可在包内及包外子类访问，default只能同一包内访问，prvate只能同一类

17. 常用数据结构：

集合，线性结构（数组，队列，链表和栈），树形结构，图状结构

18.Java 中的 TreeMap 是采用什么树实现的？(答案)

Java 中的 TreeMap 是使用红黑树实现的。

19. 匿名内部类是什么？如何访问在其外面定义的变量？

匿名内部类也就是没有名字的内部类，匿名内部类只能使用一次，它通常用来简化代码编写。

匿名内部类只能访问外部类的Final变量. Java 8更加智能：如果局部变量被匿名内部类访问，那么该局部变量相当于自动使用了final修饰。

20. 如何创建单例模式？说了双重检查，他说不是线程安全的。如何高效的创建一个线程安全的单例？

一种是通过枚举，一种是通过静态内部类。

21.poll() 方法和 remove() 方法的区别？

poll() 和
remove() 都是从队列中取出一个元素，但是 poll() 在获取元素失败的时候会返回空，但是 remove() 失败的时候会抛出异常。

22.写一段代码在遍历 ArrayList 时移除一个元素

使用迭代器。

Iterator itr = list.iterator();

while(itr.hasNext()) {if(...) { itr.remove();} }



2. JVM



1.JVM如何加载一个类的过程，双亲委派模型中有哪些方法

类加载过程：加载、验证（验证阶段作用是保证Class文件的字节流包含的信息符合JVM规范，不会给JVM造成危害）、准备（准备阶段为变量分配内存并设置类变量的初始化）、解析（解析过程是将常量池内的符号引用替换成直接引用）、初始化。

双亲委派模型中方法：双亲委派是指如果一个类收到了类加载的请求，不会自己先尝试加载，先找父类加载器去完成。当顶层启动类加载器表示无法加载这个类的时候，子类才会尝试自己去加载。当回到最开的发起者加载器还无法加载时，并不会向下找，而是抛出ClassNotFound异常。

方法：启动（Bootstrap）类加载器，标准扩展（Extension）类加载器，应用程序类加载器（Application ），上下文(Custom)类加载器。意义是防止内存中出现多份同样的字节码 。

2.GC算法（什么样的对象算是可回收对象，可达性分析），CMS收集器

jvm是如何判断一个对象已经变成了可回收的“垃圾”，一般是两个方法：引用记数法和根搜索算法。引用记数法没办法解决循环引用的问题，所以用根搜索。从一系列的”GC Roots“对象开始向下搜索，搜索走过的路径称为引用链。当一个对象到”GC Roots“之间没有引用链时，被称为引用不可达。引用不可到的对象被认为是可回收的对象。

         几种垃圾收集器：1，Serial New/Serial Old(串行)，2，Parrallel New (并行)，3，Parrallel Scavenge，4，Parrallel Old，5，CMS（CMS收集器是一个以获得最短回收停顿时间为目标的收集器，它是一种并发收集器，采用的是Mark-sweep算法。），6，G1（是一款并行与并发收集器，并且可建立可预测的停顿时间模型，整体上是基于标记清理，局部采用复制）
3.JVM分为哪些区，每一个区干吗的？

1）方法区(method)：被所有的线程共享。方法区包含所有的类信息和静态变量。

2）堆(heap)：被所有的线程共享，存放对象实例以及数组，Java堆是GC的主要区域。

3）栈(stack)：每个线程包含一个栈区，栈中保存一些局部变量等。

4）程序计数器：是当前线程执行的字节码的行指示器。

4.JVM新生代，老年代，持久代，都存储哪些东西？

持久代主要存放的是Java类的类信息，与垃圾收集要收集的Java对象关系不大。所有新生成的对象首先都是放在年轻代的，年老代中存放的都是一些生命周期较长的对象。

5.内存溢出和内存泄漏：

内存溢出：程序申请内存时，没有足够的内存，out of memory；内存泄漏值垃圾对象无法回收，可以使用memory analyzer工具查看泄漏。

6.进程与线程：

进程值运行中的程序（独立性，动态性，并发性），线程指进程中的顺序执行流。区别是：1.进程间不共享内存 2.创建进程进行资源分配的代价要大得多，所以多线程在高并发环境中效率高。

7.序列化与反序列化：

序列化指将java对象转化为字节序列，反序列化相反。主要是为了java线程间通讯，实现对象传递。只有实现了Serializable或Externalizable接口类对象才可被序列化。

8.64 位 JVM 中，int 的长度是多数？

Java 中，int 类型变量的长度是一个固定值，与平台无关，都是 32 位。意思就是说，在 32 位 和 64 位 的Java 虚拟机中，int 类型的长度是相同的。

9.Java 中 WeakReference 与 SoftReference的区别？

Java中一共有四种类型的引用。StrongReference、 SoftReference、 WeakReference 以及 PhantomReference。

StrongReference 是 Java 的默认引用实现, 它会尽可能长时间的存活于 JVM 内，当没有任何对象指向它时将会被GC回收

WeakReference，顾名思义, 是一个弱引用, 当所引用的对象在
JVM 内不再有强引用时, 将被GC回收

虽然 WeakReference 与 SoftReference 都有利于提高 GC 和 内存的效率，但是 WeakReference ，一旦失去最后一个强引用，就会被 GC 回收，而 SoftReference 会尽可能长的保留引用直到 JVM 内存不足时才会被回收(虚拟机保证), 这一特性使得
SoftReference 非常适合缓存应用

10.解释 Java 堆空间及 GC？

当通过 Java 命令启动
Java 进程的时候，会为它分配内存。内存的一部分用于创建堆空间，当程序中创建对象的时候，就从对空间中分配内存。GC 是 JVM 内部的一个进程，回收无效对象的内存用于将来的分配。

11.Java 中堆和栈有什么区别？

JVM 中堆和栈属于不同的内存区域，使用目的也不同。栈常用于保存方法帧和局部变量，而对象总是在堆上分配。栈通常都比堆小，也不会在多个线程之间共享，而堆被整个 JVM 的所有线程共享。

3. 并发，锁



1.volatile关键字， Lock

并发编程中：原子性问题，可见性问题，有序性问题。

volatile关键字能保证可见性，字能禁止指令重排序，但是不能保证原子性。可见性只能保证每次读取的是最新的值，但是volatile没办法保证对变量的操作的原子性。在生成的会变语句中加入Lock关键字和内存屏障。

Lock 实现提供了比使用synchronized 方法和语句可获得的更广泛的锁定操作，它能以更优雅的方式处理线程同步问题。用sychronized修饰的方法或者语句块在代码执行完之后锁自动释放，而用Lock需要我们手动释放锁

2.MYSQL常用优化（sql优化，表结构优化等）

SQL优化、表机构优化、索引优化、缓存参数优化

3.java每改一点都需要重新编译打包部署，有没有更好的方法

可以使用热加载

4.进程间通信有哪几种方式？

1）管道（Pipe），2）命名管道（named pipe），3）信号（Signal），4）消息（Message）队列，5）共享内存，6）内存映射（mapped memory），7）信号量（semaphore），8）套接口（Socket）

5.Sychronized修饰静态方法，锁定类本身而不是实例，非静态方法锁定实例。

6. 操作系统什么情况下会死锁？

所谓死锁：是指多个进程在运行过程中因争夺资源而造成的一种僵局。产生的原因：竞争资源：当系统中多个进程使用共享资源，并且资源不足以满足需要，会引起进程对资源的竞争而产生死锁。进程间推进的顺序非法：请求和释放资源的顺序不当，也同样会导致产生进程死锁

7.产生死锁的四个条件：

1.互斥条件（进程独占资源）2.请求与保持（进程因请求资源而阻塞时，对已获得的资源保持不放） 3.不剥夺条件（进程已获得的资源，在末使用完之前，不能强行剥夺） 4.循环等待（若干进程之间形成一种头尾相接的循环等待资源关系）

8. 如何理解分布式锁？

由于在平时的工作中，线上服务器是分布式多台部署的，经常会面临解决分布式场景下数据一致性的问题，那么就要利用分布式锁来解决这些问题。

9. 线程同步与阻塞的关系？同步一定阻塞吗？阻塞一定同步吗？

线程同步与否 跟 阻塞非阻塞没关系，同步是个过程，阻塞是线程的一种状态。多个线程操作共享变量时可能会出现竞争。这时需要同步来防止两个以上的线程同时进入临界区内，在这个过程中后进入临界区的线程将阻塞，等待先进入的线程走出临界区。

10. 同步和异步有什么区别？

同步和异步最大的区别就在于。一个需要等待，一个不需要等待。同步可以避免出现死锁，读脏数据的发生，一般共享某一资源的时候用，如果每个人都有修改权限，同时修改一个文件，有可能使一个人读取另一个人已经删除的内容，就会出错，同步就会按顺序来修改。

11. 线程池

根据系统自身的环境情况，有效的限制执行线程的数量，使得运行效果达到最佳。线程主要是通过控制执行的线程的数量，超出数量的线程排队等候，等待有任务执行完毕，再从队列最前面取出任务执行

12. 如何调用 wait（）方法？使用 if 块还是循环？为什么？

wait() 方法应该在循环调用，因为当线程获取到 CPU 开始执行的时候，其他条件可能还没有满足，所以在处理前，循环检测条件是否满足会更好。

wait()，notify（）和notifyall（）方法是java.lang.Object类为线程提供的用于实现线程间通信的同步控制方法。等待或者唤醒

13. 实现线程的几种方法

(1)继承Thread类，重写run函数 (2)实现Runnable接口，重写run函数 (3)实现Callable接口，重写call函数

14. 什么是多线程环境下的伪共享（false
sharing）？

伪共享是多线程系统（每个处理器有自己的局部缓存）中一个众所周知的性能问题。缓存系统中是以缓存行（cache line）为单位存储的。缓存行是2的整数幂个连续字节，一般为32-256个字节。最常见的缓存行大小是64个字节。当多线程修改互相独立的变量时，如果这些变量共享同一个缓存行，就会无意中影响彼此的性能，这就是伪共享。



4. 网络、数据库



1.TCP如何保证可靠传输？三次握手过程？

在TCP的连接中，数据流必须以正确的顺序送达对方。TCP的可靠性是通过顺序编号和确认（ACK）来实现的。TCP 连接是通过三次握手进行初始化的。三次握手的目的是同步连接双方的序列号和确认号并交换 TCP 窗口大小信息。第一次是客户端发起连接；第二次表示服务器收到了客户端的请求；第三次表示客户端收到了服务器的反馈。

2. Linux下你常用的命令有哪些？

                 1. cd命令用来改变所在目录。cd /  转到根目录中cd ~  转到用户目录下 
                 2. ls命令用来查看目录的内容。
                 3. cp命令用来拷贝文件cp <source filename> <target filename>
                 4.mv命令 mv t.txt Document    把文件t.txt 移动到目录Document中。 
3. 常用的hash算法有哪些？

1.加法hash：所谓的加法Hash就是把输入元素一个一个的加起来构成最后的结果。

2.位运算hash：这类型Hash函数通过利用各种位运算（常见的是移位和异或）来充分的混合输入元素

3.乘法hash：33*hash + key.charAt(i)

4. 什么是一致性哈希？

设计目标是为了解决因特网中的热点(Hot spot)问题，一致性hash算法提出了在动态变化的Cache环境中，判定哈希算法好坏的四个定义：1、平衡性(Balance) 2、单调性(Monotonicity) 3、分散性(Spread) 4、负载(Load)

5. 数据库中的范式有哪些？

第一范式----数据库中的表(所有字段值)都是不可分割的原子数据项。

第二范式----数据库表中的每一列都和主键相关，而不能只和主键的某一部分相关。

第三范式----数据库表中每一列数据都和主键直接相关，不能间接相关。范式是为了减小数据冗余。

6. 数据库中的索引的结构？什么情况下适合建索引？

数据库中索引的结构是一种排序的数据结构，数据库索引是通过B树和变形的B+树实现的。什么情况下不适合建立索引：1.对于在查询过程中很少使用或参考的列；对于那些只有很少数据值的列；对于那些定义为image，text和bit数据类型的列；当修改性能远大于检索性能。

根据系统自身的环境情况，有效的限制执行线程的数量，使得运行效果达到最佳。线程主要是通过控制执行的线程的数量，超出数量的线程排队等候，等待有任务执行完毕，再从队列最前面取出任务执行

7. concurrent包下面，都用过什么？

java.util.concurrent、java.util.concurrent.atomic和java.util.concurrent.lock

8. 常用的数据库有哪些？redis用过吗？

Orcale，MySQL，DB2

9. 你知道的开源协议有哪些？

GPL （GNU General Public License） ：GNU通用公共许可协议

LGPL （GNU Lesser General Public License） ：GNU宽通用公共许可协议

BSD
(Berkeley Software Distribution) :伯克利软件分发许可协议

MIT （Massachusetts Institute of Technology）：MIT之名源自麻省理工学院

Apache （Apache License） ：Apache许可协议

MPL （Mozilla Public License） ：Mozilla公共许可协议

10.表单提交中，get和post区别

1.get从服务器获取信息，post向服务器传信息 2.get传送数据量比较小，post可以比较大 3.get安全性比较低

11. TCP 协议与 UDP 协议有什么区别？(answer答案)

TCP（Tranfer Control Protocol）的缩写，是一种面向连接的保证传输的协议，在传输数据流前，双方会先建立一条虚拟的通信道。可以很少差错传输数据。


UDP(User DataGram Protocol)的缩写，是一种无连接的协议，使用UDP传输数据时，每个数据段都是一个独立的信息，包括完整的源地址和目的地，在网络上以任何可能的 路径传到目的地，因此，能否到达目的地，以及到达目的地的时间和内容的完整性都不能保证。

所以TCP必UDP多了建立连接的时间。相对UDP而言，TCP具有更高的安全性和可靠性。

TCP协议传输的大小不限制，一旦连接被建立，双方可以按照一定的格式传输大量的数据，而UDP是一个不可靠的协议，大小有限制，每次不能超过64K。
