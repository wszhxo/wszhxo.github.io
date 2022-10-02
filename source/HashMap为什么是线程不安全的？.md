## HashMap为什么是线程不安全的？

**jdk1.7中**

当元素A与元素B的hash值不相同时，那么同时插入A元素与插入B元素，一般不会产生问题，因为不在一个链上，分别在各自链上进行操作，但是当二者hash值相同时，就极易发生冲突:。

### 场景1：在同一个链上进行添加操作

```java
void addEntry(int hash, K key, V value, int bucketIndex) {
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);//当size超过临界阈值threshold，并且即将发生哈希冲突时进行扩容
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        createEntry(hash, key, value, bucketIndex); //采用头插法
    }

```

我们知道头插法，就是假设存在链表A，把新的元素A1插入链表A内时，会把A1.next=链表A，然后重新赋值table[bucketIndex]=A1，假设此时另一个线程往链表A内插入元素B1，我们期望的步骤是：

```java
A1.next=链表A
table[bucketIndex]=A1
B1.next=链表A
table[bucketIndex]=B1

最终元素是table[bucketIndex]=>A1=>B1=>原有的其他元素

```

但可能的执行步骤是：

```java
A1.next=链表A
B1.next=链表A
table[bucketIndex]=A1
table[bucketIndex]=B1

最终元素是table[bucketIndex]=>B1=>原有的其他元素

```

A1元素被丢掉了

> 同样的，如果是remove()操作也发生在同一个链上时，也会存在类似的问题。

### 场景2：数据扩容

addEntry()中当加入新的键值对后键值对总数量超过门限值的时候会调用一个resize()操作，这个操作会新生成一个新的容量的数组，然后对原数组的所有键值对重新进行计算和写入新的数组，之后指向新生成的数组。

我们看下源码，同样存在一个赋值操作：

```java
void resize(int newCapacity) {
       ...
        Entry[] newTable = new Entry[newCapacity];
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;  //重新赋值操作
      }

```

同样的，假设线程A和线程B都执行插入操作，发现需要扩容时，都会各自创建一个新的数据，然后重新赋值，那么就存在先后顺序，后者会覆盖前者，导致数据丢失。



## jdk1.8中HashMap

在jdk1.8中对HashMap进行了优化，在发生hash碰撞，不再采用头插法方式，而是直接插入链表尾部，因此不会出现环形链表的情况，但是在多线程的情况下仍然不安全， 如果没有hash碰撞则会直接插入元素。如果线程A和线程B同时进行put操作，刚好这两条不同的数据hash值一样，并且该位置数据为null，  假设一种情况，线程A进入后还未进行数据插入时挂起，而线程B正常执行，从而正常插入数据，然后线程A获取CPU时间片，此时线程A不用再进行hash判断了，问题出现：线程A会把线程B插入的数据给覆盖，发生线程不安全。 

HashMap是线程不安全的，其主要体现：

> 在jdk1.7中，在多线程环境下，扩容时会造成环形链或数据丢失。
> 在jdk1.8中，在多线程环境下，会发生数据覆盖的情况。