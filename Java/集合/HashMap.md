## Java集合框架——HashMap

### 简介

> HashMap 是一个散列表，它存储的内容是键值对(key-value)映射。该类实现了Map接口，根据键的HashCode值存储数据，具有很快的访问速度，最多允许一条记录的键为null，不支持线程同步。

### 底层数据结构

#### JDK1.8之前

HashMap 底层是 **数组和链表** 结合在一起使用也就是 **链表散列**。HashMap 通过 key 的 hashCode 经过扰动函数处理过后得到 hash 值，然后通过 `(n - 1) & hash` 判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。

**扰动函数**就是HashMap 的 hash方法，使用扰动函数可以减少碰撞。

**JDK1.7 HashMap 的 hash 方法源码**

```java
static int hash(int h) {
    // This function ensures that hashCodes that differ only by
    // constant multiples at each bit position have a bounded
    // number of collisions (approximately 8 at default load factor)
    h ^= (h >>> 20) ^ (h >>> 12);
    return h ^ (h >>> 7) ^ (h >>> 4);
}
```

**拉链法**：将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。

#### JDK1.8之后

与之前版本相比，JDK1.8在解决哈希冲突是有了较大的变化，当链表长度大于阈值（默认为8）时，将链表转化为红黑树，以减少搜索时间。

![](https://gitee.com/nfLJ/Pic/raw/master//blog/20200614145813.png)

### HashMap源码分析

#### 构造方法

HashMap 中有四个构造方法，它们分别如下：

```java
// 默认构造函数。
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all   other fields defaulted
}

// 包含另一个“Map”的构造函数
public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
}

// 指定“容量大小”的构造函数
public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}

// 指定“容量大小”和“加载因子”的构造函数
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " + initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " + loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
}
```

#### 类的属性

```java
public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable {
    // 序列号
    private static final long serialVersionUID = 362498820763181265L;    
    // 默认的初始容量是16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;   
    // 最大容量
    static final int MAXIMUM_CAPACITY = 1 << 30; 
    // 默认的填充因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    // 当桶(bucket)上的结点数大于这个值时会转成红黑树
    static final int TREEIFY_THRESHOLD = 8; 
    // 当桶(bucket)上的结点数小于这个值时树转链表
    static final int UNTREEIFY_THRESHOLD = 6;
    // 桶中结构转化为红黑树对应的table的最小大小
    static final int MIN_TREEIFY_CAPACITY = 64;
    // 存储元素的数组，总是2的幂次倍
    transient Node<k,v>[] table; 
    // 存放具体元素的集
    transient Set<map.entry<k,v>> entrySet;
    // 存放元素的个数，注意这个不等于数组的长度。
    transient int size;
    // 每次扩容和更改map结构的计数器
    transient int modCount;   
    // 临界值 当实际大小(容量*填充因子)超过临界值时，会进行扩容
    int threshold;
    // 加载因子
    final float loadFactor;
}
```

##### loadFactor

loadFactor译为装载因子。装载因子用来衡量HashMap满的程度。loadFactor的默认值为0.75f。计算HashMap的实时装载因子的方法为：size/capacity，而不是占用桶的数量去除以capacity。

##### **threshold**

**threshold = capacity \* loadFactor**，当**Size >= threshold**的时候，就要对数组进行扩增了，换句话说，**threshold** 是 **衡量数组是否需要扩增的一个指标**。

#### put方法

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // table未初始化或者长度为0，进行扩容
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // (n - 1) & hash 确定元素存放在哪个桶中，桶为空，新生成结点放入桶中(此时，这个结点是放在数组中)
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    // 桶中已经存在元素
    else {
        Node<K,V> e; K k;
        // 比较桶中第一个元素(数组中的结点)的hash值相等，key相等
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
                // 将第一个元素赋值给e，用e来记录
                e = p;
        // hash值不相等，即key不相等；为红黑树结点
        else if (p instanceof TreeNode)
            // 放入树中
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        // 为链表结点
        else {
            // 在链表最末插入结点
            for (int binCount = 0; ; ++binCount) {
                // 到达链表的尾部
                if ((e = p.next) == null) {
                    // 在尾部插入新结点
                    p.next = newNode(hash, key, value, null);
                    // 结点数量达到阈值，转化为红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    // 跳出循环
                    break;
                }
                // 判断链表中结点的key值与插入的元素的key值是否相等
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    // 相等，跳出循环
                    break;
                // 用于遍历桶中的链表，与前面的e = p.next组合，可以遍历链表
                p = e;
            }
        }
        // 表示在桶中找到key值、hash值与插入元素相等的结点
        if (e != null) { 
            // 记录e的value
            V oldValue = e.value;
            // onlyIfAbsent为false或者旧值为null
            if (!onlyIfAbsent || oldValue == null)
                //用新值替换旧值
                e.value = value;
            // 访问后回调
            afterNodeAccess(e);
            // 返回旧值
            return oldValue;
        }
    }
    // 结构性修改
    ++modCount;
    // 实际大小大于阈值则扩容
    if (++size > threshold)
        resize();
    // 插入后回调
    afterNodeInsertion(evict);
    return null;
} 
```

![](https://gitee.com/nfLJ/Pic/raw/master//blog/20200614150125.png)

**put过程**

1. hash(key)，取key的hashcode进行高位运算，返回hash值

2. 如果hash数组为空，直接resize()

3. 对hash进行取模运算计算，得到key-value在数组中的存储位置i

   (1). 如果table[i] == null，直接插入Node<key,value>

   (2). 如果table[i] != null，判断是否为红黑树p instanceof TreeNode。

   (3). 如果是红黑树，则判断TreeNode是否已存在，如果存在则直接返回oldnode并更新；不存在则直接插入红黑树，++size， 超出threshold容量就扩容

   (4). 如果是链表，则判断Node是否已存在，如果存在则直接返回oldnode并更新；不存在则直接插入链表尾部，判断链表长 度，如果大于8则转为红黑树存储，++size，超出threshold容量就扩容

**与JDK1.7 put 方法的区别**

- 如果定位到的数组位置没有元素 就直接插入。
- 如果定位到的数组位置有元素，遍历以这个元素为头结点的链表，依次和插入的key比较，如果key相同就直接覆盖，不同就采用头插法插入元素。

```java
public V put(K key, V value)
    if (table == EMPTY_TABLE) { 
    inflateTable(threshold); 
}  
    if (key == null)
        return putForNullKey(value);
    int hash = hash(key);
    int i = indexFor(hash, table.length);
    for (Entry<K,V> e = table[i]; e != null; e = e.next) { // 先遍历
        Object k;
        if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
            V oldValue = e.value;
            e.value = value;
            e.recordAccess(this);
            return oldValue; 
        }
    }

    modCount++;
    addEntry(hash, key, value, i);  // 再插入
    return null;
}
```

#### get方法

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 数组元素相等
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        // 桶中不止一个节点
        if ((e = first.next) != null) {
            // 在树中get
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            // 在链表中get
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

**get过程**

1. 通过 hash & (table.length - 1)获取该key对应的数据节点的hash槽
2. 判断首节点是否为空, 为空则直接返回空
3. 再判断首节点.key 是否和目标值相同, 相同则直接返回(首节点不用区分链表还是红黑树)
4. 首节点.next为空, 则直接返回空
5. 首节点是树形节点, 则进入红黑树数的取值流程, 并返回结果
6. 进入链表的取值流程, 并返回结果

#### resize方法

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table; //当前所有元素所在的数组，称为老的元素数组
    int oldCap = (oldTab == null) ? 0 : oldTab.length; //老的元素数组长度
    int oldThr = threshold;    // 老的扩容阀值设置
    int newCap, newThr = 0;    // 新数组的容量，新数组的扩容阀值都初始化为0
    if (oldCap > 0) {    // 如果老数组长度大于0，说明已经存在元素
        // PS1
        if (oldCap >= MAXIMUM_CAPACITY) { // 如果数组元素个数大于等于限定的最大容量（2的30次方）
            // 扩容阀值设置为int最大值（2的31次方 -1 ），因为oldCap再乘2就溢出了。
            threshold = Integer.MAX_VALUE;    
            return oldTab;    // 返回老的元素数组
        }

        /*
            * 如果数组元素个数在正常范围内，那么新的数组容量为老的数组容量的2倍（左移1位相当于乘以2）
            * 如果扩容之后的新容量小于最大容量  并且  老的数组容量大于等于默认初始化容量（16），那么新数组的扩容阀值设置为老阀值的2倍。（老的数组容量大于16意味着：要么构造函数指定了一个大于16的初始化容量值，要么已经经历过了至少一次扩容）
            */
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }

    // PS2
    // 运行到这个else if  说明老数组没有任何元素
    // 如果老数组的扩容阀值大于0，那么设置新数组的容量为该阀值
    // 这一步也就意味着构造该map的时候，指定了初始化容量。
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        // 能运行到这里的话，说明是调用无参构造函数创建的该map，并且第一次添加元素
        newCap = DEFAULT_INITIAL_CAPACITY;    // 设置新数组容量 为 16
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY); // 设置新数组扩容阀值为 16*0.75 = 12。0.75为负载因子（当元素个数达到容量了4分之3，那么扩容）
    }

    // 如果扩容阀值为0 （PS2的情况）
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);  // 参见：PS2
    }
    threshold = newThr; // 设置map的扩容阀值为 新的阀值
    @SuppressWarnings({"rawtypes","unchecked"})
    // 创建新的数组（对于第一次添加元素，那么这个数组就是第一个数组；对于存在oldTab的时候，那么这个数组就是要需要扩容到的新数组）
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;    // 将该map的table属性指向到该新数组
    if (oldTab != null) {    // 如果老数组不为空，说明是扩容操作，那么涉及到元素的转移操作
        for (int j = 0; j < oldCap; ++j) { // 遍历老数组
            Node<K,V> e;
            if ((e = oldTab[j]) != null) { // 如果当前位置元素不为空，那么需要转移该元素到新数组
                oldTab[j] = null; // 释放掉老数组对于要转移走的元素的引用（主要为了使得数组可被回收）
                if (e.next == null) // 如果元素没有有下一个节点，说明该元素不存在hash冲突
                    // PS3
                    // 把元素存储到新的数组中，存储到数组的哪个位置需要根据hash值和数组长度来进行取模
                    // 【hash值  %   数组长度】   =    【  hash值   & （数组长度-1）】
                    //  这种与运算求模的方式要求  数组长度必须是2的N次方，但是可以通过构造函数随意指定初始化容量呀，如果指定了17,15这种，岂不是出问题了就？没关系，最终会通过tableSizeFor方法将用户指定的转化为大于其并且最相近的2的N次方。 15 -> 16、17-> 32
                    newTab[e.hash & (newCap - 1)] = e;

                // 如果该元素有下一个节点，那么说明该位置上存在一个链表了（hash相同的多个元素以链表的方式存储到了老数组的这个位置上了）
                // 例如：数组长度为16，那么hash值为1（1%16=1）的和hash值为17（17%16=1）的两个元素都是会存储在数组的第2个位置上（对应数组下标为1），当数组扩容为32（1%32=1）时，hash值为1的还应该存储在新数组的第二个位置上，但是hash值为17（17%32=17）的就应该存储在新数组的第18个位置上了。
                // 所以，数组扩容后，所有元素都需要重新计算在新数组中的位置。


                else if (e instanceof TreeNode)  // 如果该节点为TreeNode类型
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);  // 此处单独展开讨论
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;  // 按命名来翻译的话，应该叫低位首尾节点
                    Node<K,V> hiHead = null, hiTail = null;  // 按命名来翻译的话，应该叫高位首尾节点
                    // 以上的低位指的是新数组的 0  到 oldCap-1 、高位指定的是oldCap 到 newCap - 1
                    Node<K,V> next;
                    // 遍历链表
                    do {  
                        next = e.next;
                        // 这一步判断好狠，拿元素的hash值  和  老数组的长度  做与运算
                        // PS3里曾说到，数组的长度一定是2的N次方（例如16），如果hash值和该长度做与运算，那么该hash值可参与计算的有效二进制位就是和长度二进制对等的后几位，如果结果为0，说明hash值中参与计算的对等的二进制位的最高位一定为0.
                        //因为数组长度的二进制有效最高位是1（例如16对应的二进制是10000），只有*..0**** 和 10000 进行与运算结果才为00000（*..表示不确定的多个二进制位）。又因为定位下标时的取模运算是以hash值和长度减1进行与运算，所以下标 = (*..0**** & 1111) 也= (*..0**** & 11111) 。1111是15的二进制、11111是16*2-1 也就是31的二级制（2倍扩容）。
                        // 所以该hash值再和新数组的长度取摸的话mod值也不会放生变化，也就是说该元素的在新数组的位置和在老数组的位置是相同的，所以该元素可以放置在低位链表中。
                        if ((e.hash & oldCap) == 0) {  
                            // PS4
                            if (loTail == null) // 如果没有尾，说明链表为空
                                loHead = e; // 链表为空时，头节点指向该元素
                            else
                                loTail.next = e; // 如果有尾，那么链表不为空，把该元素挂到链表的最后。
                            loTail = e; // 把尾节点设置为当前元素
                        }

                        // 如果与运算结果不为0，说明hash值大于老数组长度（例如hash值为17）
                        // 此时该元素应该放置到新数组的高位位置上
                        // 例：老数组长度16，那么新数组长度为32，hash为17的应该放置在数组的第17个位置上，也就是下标为16，那么下标为16已经属于高位了，低位是[0-15]，高位是[16-31]
                        else {  // 以下逻辑同PS4
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) { // 低位的元素组成的链表还是放置在原来的位置
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {  // 高位的元素组成的链表放置的位置只是在原有位置上偏移了老数组的长度个位置。
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead; // 例：hash为 17 在老数组放置在0下标，在新数组放置在16下标；    hash为 18 在老数组放置在1下标，在新数组放置在17下标；                   
                    }
                }
            }
        }
    }
    return newTab; // 返回新数组
}
```

### 总结

HashMap搞的这么复杂不是白搞的，它的最大优点就是快，尤其是 get数据，是 O(1)级别的，直接定位索引位置。HashMap不是单纯的数组结构，当发生哈希碰撞时，会采用拉链法生成链表，当链表大于 8 的时候会转换成红黑树，红黑树可以很大程度上提高性能。

### 推荐阅读

[7000 字说清楚 HashMap，面试点都在里面了](https://juejin.cn/post/6844904197746343950#heading-19)

