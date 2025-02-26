# List

> An ordered collection (also known as a <i>sequence</i>).  The user of this interface has precise control over where in the list each element is inserted.  The user can access elements by their integer index (position in the list), and search for elements in the list.

list，是一个带有顺序的集合，每个元素都可以是不唯一的，拥有唯一的索引，第一个元素的索引为0。

list接口有三种实现方式：`ArrayList`、`LinkedList`和`Vector`，他们的区别体现在不同操作下的不同性能。

## ArrayList 

`ArrayList`的底层使用Array数组实现的，通过源码我们可知`ArrayList`是如何把Array不可变的长度转化为动态的。

```java
/**
 * Default initial capacity.默认的初始化容量
 */
private static final int DEFAULT_CAPACITY = 10;

/**
 * Shared empty array instance used for empty instances.
 * 当列表的数量变为0时，返回该静态空数组，多个空数组引用指向相同。
 */
private static final Object[] EMPTY_ELEMENTDATA = {};

/**
 * Shared empty array instance used for default sized empty instances. We
 * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
 * first element is added.
 * 当初始化新的无参列表时，默认返回此数组。
 */
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

/**
 * The array buffer into which the elements of the ArrayList are stored.
 * The capacity of the ArrayList is the length of this array buffer. Any
 * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
 * will be expanded to DEFAULT_CAPACITY when the first element is added.
 * elementData用来储存列表中的数据，当添加第一个元素时，elementData会扩容至初始容量。
 */
transient Object[] elementData; // non-private to simplify nested class access
/**
 * The size of the ArrayList (the number of elements it contains).
 * size用来记录elementData容器中真实元素的个数
 * @serial
 */
private int size;
```

假设现在我们初始化了一个新的ArrayList，此时

```java
/**
 * Constructs an empty list with an initial capacity of ten.
 */
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

在列表中添加一个新的元素：

```java
/**
 * Appends the specified element to the end of this list.
 * 在列表的末尾添加一个实例化过的对象
 * @param e element to be appended to this list
 * @return <tt>true</tt> (as specified by {@link Collection#add})
 */
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!增加modCount！！
    elementData[size++] = e;  //缓存数组赋值
    return true;
}

/**
 * 确保elementData容器的大小满足需求
 * ensureCapacityInternal确保内部容量
 */
private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

/**
 * 计算需求容量，当element为空时，需求容量为初始大小10，否则需求容量为真实元素数量。
 */
private static int calculateCapacity(Object[] elementData, int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}

/**
 * 确保许可的容量，如果需求容量大于最大容量（elementData长度)执行grow方法。
 */
private void ensureExplicitCapacity(int minCapacity) {
    //列表被修改次数+1
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

/**
  * Increases the capacity to ensure that it can hold at least the
  * number of elements specified by the minimum capacity argument.
  * 增加最大容量使其大于需求容量
  * @param minCapacity the desired minimum capacity
  */
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;  //保存之前的大小
    int newCapacity = oldCapacity + (oldCapacity >> 1);  //新容量为旧容量的1.5倍
    //如果新容量仍小于需求容量，则以需求容量为新容量
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;  
    //如果新容量大于了JVM所允许的数组最大长度 MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    // 返回一个新的数组，长度为newCapacity
    elementData = Arrays.copyOf(elementData, newCapacity);
}
/**
  * 因为新容量是旧容量的1.5倍，当扩容时如果大于了MAX_ARRAY_SIZE，
  * 断需求容量是否真的有这么大，如果确实大，则新容量定义为Integer.MAX_VALUE，否则为
  * MAX_ARRAY_SIZE
  */
private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
    MAX_ARRAY_SIZE;
}
```

`MAX_ARRAY_SIZE`是JDK规定的最大数组长度，因为要在数组头部保存一些数组信息，所以安全的最大数字：

```java
/**
 * The maximum size of array to allocate.
 * Some VMs reserve some header words in an array.
 * Attempts to allocate larger arrays may result in
 * OutOfMemoryError: Requested array size exceeds VM limit
 */
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
```

又因为数组是保存在内存中的，所以往往数组长度没有达到最大定义数量，就已经发生了内存溢出，

所以这种极端情况不做深入了解，如果在初始化时没有规定长度，则第一次添加数据的时候`elementData`扩容至默认的10容量，如果初始化定义了长度，则`elementData`被赋值为相同长度的数组。通常情况下，新最大容量为旧最大容量的1.5倍。

## LinkedList 

一个`LinkedList`由n个`Node`节点组成。

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

一个`Node`节点包括它上一个节点的引用，以及自身的元素，和下一个节点的引用，首节点的`prev`和尾节点的`next`为`null`。链表自身没有索引的概念，或者说每个节点并不知道自己的索引是多少，每个节点的索引都为前一个节点的索引+1，认为首节点索引为0；

当我们执行`get(n)`方法时：

```java
public E get(int index) {
    // 检查index是否大于0小于size
    checkElementIndex(index);
    return node(index).item;
}

/**
 * Returns the (non-null) Node at the specified element index.
 */
Node<E> node(int index) {
    // assert isElementIndex(index);

    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```

首先根据`index`与`size>>1`的大小关系可知，要得到的节点是距离链表首部近还是尾部近，通过正序（循环index-1次）或倒序（循环size-index-2次）的方式，得到`index`。

## Vector 

`Vector`和`ArrayList`一样，底层都是通过数组实现的，但`Vector`中的方法都是同步的(Synchronized)，是线程安全的，在一个线程修改`Vector`时，其他的线程不能再对它进行修改，线程安全必然使得读写速度较慢，效率低。

还有一点区别在于：`Vector`在初始化的时候可以定义扩容的大小：

```java
/**
 * The amount by which the capacity of the vector is automatically
 * incremented when its size becomes greater than its capacity.  If
 * the capacity increment is less than or equal to zero, the capacity
 * of the vector is doubled each time it needs to grow.
 *
 * @serial
 */
protected int capacityIncrement;

/**
 * Constructs an empty vector with the specified initial capacity and
 * capacity increment.
 *
 * @param   initialCapacity     the initial capacity of the vector
 * @param   capacityIncrement   the amount by which the capacity is
 *                              increased when the vector overflows
 * @throws IllegalArgumentException if the specified initial capacity
 *         is negative
 */
public Vector(int initialCapacity, int capacityIncrement) {
    super();
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    this.elementData = new Object[initialCapacity];
    this.capacityIncrement = capacityIncrement;
}
```

`ArrayList`扩容默认为1.5倍，`Vector`若不指定`capacityIncrement`则默认为2倍。

## 总结

`ArrayList`、`LinkedList`和`Vector`都是我们在使用列表工具时的具体实现类。在不考虑线程安全的情况下，优先使用`ArrayList`和`LinkedList`，`ArrayList`注重`get`和`set`的用法，`LinkedList`在对首尾元素的`add`和`remove`上效率更高；在需要线程安全的时候，`Vector`用时间效率来换取程序的安全性。

# Map 

首先了解一下源码中对于 `Map` 的基本描述。

```
/**
 * An object that maps keys to values.  A map cannot contain duplicate keys;
 * each key can map to at most one value.
 *
 * <p>This interface takes the place of the <tt>Dictionary</tt> class, which
 * was a totally abstract class rather than an interface.
 *
 * <p>The <tt>Map</tt> interface provides three <i>collection views</i>, which
 * allow a map's contents to be viewed as a set of keys, collection of values,
 * or set of key-value mappings.  The <i>order</i> of a map is defined as
 * the order in which the iterators on the map's collection views return their
 * elements.  Some map implementations, like the <tt>TreeMap</tt> class, make
 * specific guarantees as to their order; others, like the <tt>HashMap</tt>
 * class, do not.
 */
```

- 一个将键映射到值的对象。一个`Map`对象最多只能映射到一个值。
- 这个接口取代了`Dictionary`类，这个类是一个完全抽象的类而不是一个接口。
- `Map`接口提供了三个集合视图，允许将Map的内容视为一组键、一组值或一组键值映射。映射的顺序定义为映射集合视图上的迭代器返回其元素的顺序。一些`Map`实现，比如`TreeMap`类，对它们的顺序做出了特定的保证；而另一些map实现，比如`HashMap`类，则没有。

## HashMap

学习`HashMap`前要明确以下基本的概念

### 1. 实现方式

jdk1.8中`HashMap`储存结构为数组+链表+红黑树，无序，线程不安全。

### 2. 哈希算法

广义上的哈希算法是指，将任意长度的输入通过一定的计算转化成固定长度的输出，输出的结果为哈希值。

在Java中，`Object`类定义了`hashcode`方法，但是底层代码调用了C/C++的方法，并且在sunJDK中不可见，`int`类型。

```
public native int hashCode(); // Object.hashcode()
```

```
// HashMap.hash(Object key)
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

### 3. 哈希冲突

两个不同的对象在经过哈希计算后得到相同的哈希值

```java
public static void main(String[] args) {
    Integer a = 49;
    String b = "1";
    System.out.println(a.hashCode()); // 49
    System.out.println(b.hashCode()); // 49
    System.out.println(a.hashCode() == b.hashCode()); // true
}
```

### 4. 链地址法

`HashMap`中的储存结构为：没有哈希碰撞的节点在一个数组中，具有相同哈希值的节点在一个链表中，头结点在数组中。

```java
/**
 * Basic hash bin node, used for most entries.  (See below for
 * TreeNode subclass, and in LinkedHashMap for its Entry subclass.)
 */
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }

    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + "=" + value; }

    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}
/**
 * The table, initialized on first use, and resized as
 * necessary. When allocated, length is always a power of two.
 * (We also tolerate length zero in some operations to allow
 * bootstrapping mechanics that are currently not needed.)
 */
transient Node<K,V>[] table;
```

### 5. put过程

- 当初始化`HashMap`后，添加值之前，内部`table`数组为`null`

- 添加键值对时，会先判断是否是第一次添加（`table`为`null`或长度为0），如果是第一次就先执行`resize()`方法，会初始化`table`长度为16，并初始化一个`Node`节点。之后求得此节点在数组中的位置`i`，为`table`的长度-1和`key`的哈希值做`&`运算得到的数字。

  - 如果`i`上的节点为`null`，直接为数组赋值，并计算是否需要扩容，返回值为`null`。

  - 如果此位置上有节点

    1. 通过`==`比较键的哈希值和`equals`比较键值，如果都相同说明是重复添加，新的节点会直接取代旧的节点，并返回旧值。
    2. 通过比较之后如果键不相等，则说明发生了哈希冲突。在链表的情况下：因为新节点`p`与链表头节点发生了哈希碰撞，则判断头节点的下一个节点`e`是否为`null`，如果为是就赋值并且判断是否转变为红黑树（链表长度等于8），否就通过2.1的方式判断`e`和`q`是否相同，相同就替换，不同则继续从下一个节点开始重新执行此过程。在红黑树的情况下，则用红黑树的方式判断，我不会（没办法，没文化是这样的）。

### 6. get过程

同样先通过`key`的哈希算法得到目标在数组中应该存放的位置（数组索引），判断数组此位置是否有节点，有节点再判断是否是哈希冲突，是哈希冲突再判断是否有下一个节点（是否存在链表或树），在遍历（链表或树）与每一个节点的`key`进行`equals()`比较值、`hash()`比较哈希值，最后返回结果。

### 7. 部分源码

```java
/**
 * Hash table based implementation of the <tt>Map</tt> interface.  This
 * implementation provides all of the optional map operations, and permits
 * <tt>null</tt> values and the <tt>null</tt> key.  (The <tt>HashMap</tt>
 * class is roughly equivalent to <tt>Hashtable</tt>, except that it is
 * unsynchronized and permits nulls.)  This class makes no guarantees as to
 * the order of the map; in particular, it does not guarantee that the order
 * will remain constant over time.
 * 
 * 基于哈希表的Map接口实现。这个实现提供了所有可选的map操作，并允许null键和null值。
 * HashMap类大致相当于Hashtable，但它不是线程安全的并且允许null。
 * 这个类不能保证顺序，尤其它不能保证顺序随时间推移保持不变。
 */
 
 
/**
 * The default initial capacity - MUST be a power of two.
 * 默认初始容量-必须是2的幂。
 */
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

/**
 * The maximum capacity, used if a higher value is implicitly specified
 * by either of the constructors with arguments.
 * MUST be a power of two <= 1<<30.
 * 最大容量
 */
static final int MAXIMUM_CAPACITY = 1 << 30;

/**
 * The load factor used when none specified in constructor.
 * 构造函数中未指定时使用的负载因子
 */
static final float DEFAULT_LOAD_FACTOR = 0.75f;

/**
 * The bin count threshold for using a tree rather than list for a
 * bin.  Bins are converted to trees when adding an element to a
 * bin with at least this many nodes. The value must be greater
 * than 2 and should be at least 8 to mesh with assumptions in
 * tree removal about conversion back to plain bins upon
 * shrinkage.
 * 树结构的门槛，当元素添加到8个时，容器会转变为树。
 */
static final int TREEIFY_THRESHOLD = 8;

/**
 * The bin count threshold for untreeifying a (split) bin during a
 * resize operation. Should be less than TREEIFY_THRESHOLD, and at
 * most 6 to mesh with shrinkage detection under removal.
 * 当树的长度等于6时，会转变为链表
 */
static final int UNTREEIFY_THRESHOLD = 6;

/**
 * The smallest table capacity for which bins may be treeified.
 * (Otherwise the table is resized if too many nodes in a bin.)
 * Should be at least 4 * TREEIFY_THRESHOLD to avoid conflicts
 * between resizing and treeification thresholds.
 * 链表转化为红黑树的前提是table数组中的容量最少为64
 */
static final int MIN_TREEIFY_CAPACITY = 64;

```

### 7. 扩容机制

当数组中的元素个数达到0.75倍长度（阈值=容量*负载因子）时会触发扩容，新的容量为旧容量的2倍，元素索引不变或变为原索引+原容量，可以在构造器中定义负载因子的大小（默认0.75）和初始化容量（默认16）。

## TreeMap

基于红黑树结构，实现了`SortedMap`接口，默认照键的自然顺序排序，因为需要排序所以键不能为`null`，线程不安全。~~此部分内容等我什么时候学会红黑树在写。~~

## Hashtable

1. 类名不是驼峰命名
2. 父类为`Dictionary`
3. 键值均不允许为`null`
4. 方法用`synchronized`修饰，线程安全
5. 默认初始容量为11，负载因子为0.75，扩容策略是：新容量=旧容量*2+1

6. 一般认为`HashMap`替代了`Hashtable`

## LinkedHashMap

继承于`HashMap`，链表结构为双向链表。此链表定义了迭代的顺序，通常为插入顺序。

## ConcurrentHashMap

是完全线程安全的类，但和`Hashtable`有区别。

- `Hashtable`是完全锁住所有的数据
- `ConcurrentHashMap`采用分段锁，性能上优于`Hashtable`

# Set 

> A collection that contains no duplicate elements.  More formally, sets contain no pair of elements e1 and e2 such that e1.equals(e2), and at most one null element.  As implied by its name, this interface models the mathematical set abstraction.
>
> 不包含重复元素的集合。更正式地说，集合不包含一对元素e1和e2，例如e1.equals（e2），最多包含一个空元素。正如它的名字所暗示的，这个接口为数学集合抽象建模。

主要有 `HashSet` 和 `TreeSet` 两种使用方法。

## HashSet

`HashSet`实际上就是`value`全部为常量的`HashMap`，具有和`HashMap`相同的特性（无序、允许null、线程不安全等）。

```java
/**
 * This class implements the <tt>Set</tt> interface, backed by a hash table
 * (actually a <tt>HashMap</tt> instance).  It makes no guarantees as to the
 * iteration order of the set; in particular, it does not guarantee that the
 * order will remain constant over time.  This class permits the <tt>null</tt>
 * element.
 * 
 * 这个类实现了Set接口，由一个哈希表支持（实际上是一个HashMap）。它不保证集合的迭代顺序，
 * 特别是它不能保证随着时间的推移，秩序将保持不变。这个类允许null元素。
 */

private transient HashMap<E,Object> map;

// Dummy value to associate with an Object in the backing Map
// Map中的虚拟值
private static final Object PRESENT = new Object();

/**
 * Constructs a new, empty set; the backing <tt>HashMap</tt> instance has
 * default initial capacity (16) and load factor (0.75).
 */
public HashSet() {
    map = new HashMap<>();
}
```

可以知道 `HashSet` 内部通过 `HashMap` 类储存数据，所以内部方法都调用了 `HashMap` 的方法 。

```java
/**
 * Adds the specified element to this set if it is not already present.
 * More formally, adds the specified element <tt>e</tt> to this set if
 * this set contains no element <tt>e2</tt> such that
 * <tt>(e==null&nbsp;?&nbsp;e2==null&nbsp;:&nbsp;e.equals(e2))</tt>.
 * If this set already contains the element, the call leaves the set
 * unchanged and returns <tt>false</tt>.
 *
 * 如果指定的元素尚未存在，则将其添加到此集合。更正式的说，如果set中不包含指定元素，
 * 则将制定的元素添加到此集合。如果集合已经包含元素，则保持集合不变并返回false。
 * 
 * @param e element to be added to this set
 * @return <tt>true</tt> if this set did not already contain the specified
 * element
 */
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```

向 `set` 集合中添加元素时，添加的元素作为 `map` 集合中的 `key` ，所有的 `value` 都是 `PRESENT` 常量。

```java
/**
 * Removes the specified element from this set if it is present.
 * More formally, removes an element <tt>e</tt> such that
 * <tt>(o==null&nbsp;?&nbsp;e==null&nbsp;:&nbsp;o.equals(e))</tt>,
 * if this set contains such an element.  Returns <tt>true</tt> if
 * this set contained the element (or equivalently, if this set
 * changed as a result of the call).  (This set will not contain the
 * element once the call returns.)
 *
 * @param o object to be removed from this set, if present
 * @return <tt>true</tt> if the set contained the specified element
 */
public boolean remove(Object o) {
    return map.remove(o)==PRESENT;
}

/**
 * Removes all of the elements from this set.
 * The set will be empty after this call returns.
 */
public void clear() {
    map.clear();
}
```

## TreeSet

`TreeSet`默认为类的自然顺序，当我们存入自定义类时必须实现`Comparable`接口。

不实现`Comparable`时：

```java
public class Student{
    int age;
    String name;

    @Override
    public String toString() {
        return "Student{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    public Student(int age, String name) {
        this.age = age;
        this.name = name;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Set<Student> set = new TreeSet<>();
        set.add(new Student(30,"张三"));
        set.add(new Student(20,"李四"));
        set.add(new Student(40,"王二麻子"));
        set.add(new Student(10,"小淘气"));
        System.out.println(set);
    }
}
```


正确的方式为：

```java
public class Student implements Comparable<Student> {
    int age;
    String name;

    @Override
    public String toString() {
        return "Student{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    public Student(int age, String name) {
        this.age = age;
        this.name = name;
    }

    @Override
    public int compareTo(Student o) {
        return this.age - o.age;
    }
}
```

```Java
public class Main {
    public static void main(String[] args) {
        Set<Student> set = new TreeSet<>();
        set.add(new Student(30,"张三"));
        set.add(new Student(20,"李四"));
        set.add(new Student(40,"王二麻子"));
        set.add(new Student(10,"小淘气"));
        System.out.println(set);
    }
}
```
