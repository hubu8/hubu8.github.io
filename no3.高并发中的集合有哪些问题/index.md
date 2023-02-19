# No3.高并发中的集合有哪些问题


<!--more-->

**第一代线程安全集合类 **

Vector、Hashtable

是怎么保证线程安排的： 使用synchronized修饰方法*

缺点：效率低下

**第二代线程非安全集合类**

ArrayList、HashMap

线程不安全，但是性能好，用来替代Vector、Hashtable

使用ArrayList、HashMap，需要线程安全怎么办呢？

使用 Collections.*synchronizedList*(list); Collections.*synchronizedMap*(m);

底层使用synchronized代码块锁 虽然也是锁住了所有的代码，但是锁在方法里边，并所在方法外边性能可以理解为稍有提高吧。毕竟进方法本身就要分配资源的

**第三代线程安全集合类**

在大量并发情况下如何提高集合的效率和安全呢？

java.util.concurrent.*

ConcurrentHashMap：

CopyOnWriteArrayList ：

CopyOnWriteArraySet： 注意 不是CopyOnWriteHashSet*

底层大都采用Lock锁（1.8的ConcurrentHashMap不使用Lock锁），保证安全的同时，性能也很高。
