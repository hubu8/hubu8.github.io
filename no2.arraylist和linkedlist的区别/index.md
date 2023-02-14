# No2.ArrayList和LinkedList的区别？


<!--more-->

#### 回答一：

ArrayList和LinkedList都实现了List接口，他们有以下不同点：

ArrayList是基于索引的数据接口，它的底层是数组，它可以以O（1）的时间复杂度对数组进行随机访问，与此对应的LinkedList是以元素列表的形式存储它的数据结构，每一个元素都和它的前一个和后一个元素链接在一起，在这种情况下，查找某个元素的时间复杂度为O(n)。

相对于Array LIst，Linked List的插入、添加、删除操作速度更快，因为当元素被添加到集合任意位置的时候，不需要像数组一样重新计算大小或者更新索引。

LinkedList比ArayList更占内存，因为Linked List为每一个节点存储了两个引用，一个指向前一个元素，一个指向后一元素。

#### 回答二：

1、因为Array是基于索引的数据结构，它使用索引在数组中搜索和读取数据是很快的，Array获取数据的时间复杂度为O(1),但是要删除数据确是开销很大，因为这需要重拍数组中的所有数据。

2、相对于ArrayList,LinkedList插入是更快的，因为Linked List不想Array List一样，不需要改变数组的大小，也不需要在数组装满的时候要将数组的所有元素重新装入一个新的数组，这是ArrayList最坏的一种情况，时间复杂度是O（n）,而LInked List中插入或者删除只需要更新引用，时间复杂度为O(1),ArrayList在插入时还需要更新索引。

3、类似于插入数据、删除数据，LinkedList也优于Array List；

4、LinkedList需要更多的内存，因为ArrayList 的每个索引的位置是实际的数据，而Linked List的每个节点中存储的是实际数据和前后两个位置节点，（Node item，Node next,Node pre）

#### 什么场景下更适宜用Linked List，而不用ArrayList？

1、你的应用不会访问随机数据，因如果你需要第N个数据，需要从第一个元素节点访问，直到找到需要的元素。

2、你的应用更多的是插入和删除元素，更少的读取数据，因为插入和删除元素不涉及重排数据，只需要更改引用。
