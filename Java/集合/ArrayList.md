## Java集合框架——ArrayList

### 简介

> ArrayList 的底层是数组队列，相当于动态数组。与 Java 中的数组相比，它的容量能动态增长。在添加大量元素前，应用程序可以使用`ensureCapacity`操作来增加 ArrayList 实例的容量。这可以减少递增式再分配的数量。

### ArrayList继承体系

ArrayList继承AbstractList抽象父类，实现了List接口（**规定了List的操作规范**）、RandomAccess（**可随机访问**）、Cloneable（**可拷贝**）、Serializable（**可序列化**）。

![](https://gitee.com//nfLJ/Pic/raw/master//collection/20200707141519.png)

### ArrayList 源码分析

#### 1.构造方法

```java
 /**
  * 带初始容量参数的构造函数（用户可以在创建ArrayList对象时自己指定集合的初始大小）
  */
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        //如果传入的参数大于0，创建initialCapacity大小的数组
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        //如果传入的参数等于0，创建空数组
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}
/**
 *默认无参构造函数
 *DEFAULTCAPACITY_EMPTY_ELEMENTDATA 为0.初始化为10，也就是说初始其实是空数组 当添加第一个元素的时候数组容量才变成10
 */
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
/**
 * 构造一个包含指定集合的元素的列表，按照它们由集合的迭代器返回的顺序。
 */
public ArrayList(Collection<? extends E> c) {
    //将指定集合转换为数组
    elementData = c.toArray();
    //如果elementData数组的长度不为0
    if ((size = elementData.length) != 0) {
        // 如果elementData不是Object类型数据（c.toArray可能返回的不是Object类型的数组所以加上下面的语句用于判断）      
        if (elementData.getClass() != Object[].class)
            //将原来不是Object类型的elementData数组的内容，赋值给新的Object类型的elementData数组
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // 其他情况，用空数组代替
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

#### 2.主要成员变量

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    // 版本号
    private static final long serialVersionUID = 8683452581122892189L;
    // 默认初始容量
    private static final int DEFAULT_CAPACITY = 10;
    // 空对象数组
    private static final Object[] EMPTY_ELEMENTDATA = {};
    // 默认空对象数组
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    // 元素数组
    transient Object[] elementData;
    // 实际元素大小，默认为0
    private int size;
    // 最大数组容量
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
}
```

#### 3.add(E e)方法

```java
// 往ArrayList中添加元素
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // 加入元素前检查数组的容量是否足够
    elementData[size++] = e;
    return true;
}
private void ensureCapacityInternal(int minCapacity) {
    modCount++;
    // 如果添加元素后大于当前数组的长度，则进行扩容
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
} 
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    //将数组的长度增加原来数组的一半。
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    //如果扩充一半后仍然不够，则 newCapacity = minCapacity;minCapacity实际元素的个数。
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    //数组最大位2^32
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}     
```

#### 4.add(int index, E element)方法

```java
// 往ArrayList中下标为index位置添加元素，添加元素后ArrayList的大小增1。index及以后的元素都会向后移一位。
public void add(int index, E element) {
    rangeCheckForAdd(index);
    //检查index的值是否在0到size之间，可以为size。
    ensureCapacityInternal(size + 1);  // 看elementData的长度是否足够，不够扩容
    //将elementData从index开始后面的元素往后移一位。       
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    elementData[index] = element;
    size++;
}
private void rangeCheckForAdd(int index) {
    if (index > size || index < 0)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
private void ensureCapacityInternal(int minCapacity) {
    modCount++;
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```

#### 5.get(int index)方法

```java
// 返回ArrayList中下标为index的元素
public E get(int index) {
    //检查index的值是否大于ArrayList的大小
    rangeCheck(index);
    //返回index下标的元素       
    return elementData(index);
}

E elementData(int index) {
    return (E) elementData[index];
}  
//这里值检查index >= size的情况，因为index<0时会自动抛出异常，所以并未检查index<0的情况。
private void rangeCheck(int index) {
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}      
```

#### 6.set(int index, E element)方法

```java
// 将element放入下标为index的位置，如果index存在元素，则覆盖并返回被覆盖的元素
public E set(int index, E element) {
//检查index是否小于size，如果不是抛异常
    rangeCheck(index);
    E oldValue = elementData(index);
    elementData[index] = element;
    //覆盖ArrayList中index上的元素。
    return oldValue;
    //返回被覆盖的元素。
} 
private void rangeCheck(int index) {
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```

#### 7.remove(int index)方法

```java
// 删除ArrayList中下标为index的元素
public E remove(int index) {
    rangeCheck(index);
    //如果index>=size抛出异常
    modCount++;
    E oldValue = elementData(index);
    //获取删除元素的值
    int numMoved = size - index - 1;
    //将index后面所有的元素往前移一位。
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // Let gc do its work
    //返回要删除的原数。
    return oldValue;
}
```

#### 8.remove(Object o)方法

```java
// 删除ArrayList中值为 o 的元素
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}
```

### ArrayList扩容机制

当我们以无参的构造方法创建ArrayList时，实际上是初始化一个空数组。在添加元素的时候，才分配容量。即向数组添加第一个元素的时候，数组扩容至10。

#### 回顾`add(E e)`方法

```java
/**
 * 将指定的元素追加到此列表的末尾。 
 */
public boolean add(E e) {
    //添加元素之前，先调用ensureCapacityInternal方法
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    //这里看到ArrayList添加元素的实质就相当于为数组赋值
    elementData[size++] = e;
    return true;
}
```

#### `ensureCapacityInternal()`方法

从`add`方法可以看出，首先调用了`ensureCapacityInternal(size + 1)`方法

```java
//得到最小扩容量
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        // 获取默认的容量和传入参数的较大值
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
}
```

当 add 第一个元素时，minCapacity为1，在Math.max() 方法比较后，minCapacity为10

#### `ensureExplicitCapacity()` 方法

```java
//判断是否需要扩容
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        //调用grow方法进行扩容，调用此方法代表已经开始扩容了
        grow(minCapacity);.
}
```

**分析：**

- 当我们add第1个元素时，`elementData.length`为0（空数组），执行`ensureCapacityInternal()`方法，此时`minCapacity`为10。因此`minCapacity - elementData.length > 0`成立，进入`grow(minCapacity)`方法
- 当我们add第2~10个元素时，`minCapacity`为2，而`elementData.length`为10，因此`minCapacity - elementData.length > 0`不成立，不会进入`grow(minCapacity)`方法
- 当我们add第11个元素时，`minCapacity`为11，`minCapacity - elementData.length > 0`成立，进入`grow(minCapacity)`方法

#### `grow()`方法

```java
/**
 * ArrayList扩容的核心方法。
 */
private void grow(int minCapacity) {
    // oldCapacity为旧容量，newCapacity为新容量
    int oldCapacity = elementData.length;
    //将oldCapacity 右移一位，其效果相当于oldCapacity /2，
    //我们知道位运算的速度远远快于整除运算，整句运算式的结果就是将新容量更新为旧容量的1.5倍，
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    //然后检查新容量是否大于最小需要容量，若还是小于最小需要容量，那么就把最小需要容量当作数组的新容量，
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    // 如果新容量大于 MAX_ARRAY_SIZE,进入(执行) `hugeCapacity()` 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，
    //如果minCapacity大于最大容量，则新容量则为`Integer.MAX_VALUE`，否则，新容量大小则为 MAX_ARRAY_SIZE 
    //即为 `Integer.MAX_VALUE - 8`。
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

> 从源码可以看出，ArrayList每次扩容之后的容量为原来的1.5倍（`int newCapacity = oldCapacity + (oldCapacity >> 1);`）

#### `System.arraycopy()`方法

```java
/* src - 源数组。
   srcPos - 源数组中的起始位置。
   dest - 目标数组。
   destPos - 目的地数据中的起始位置。
   length - 要复制的源数组元素的数量。
   */
public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
```

可以看出，这是一个**本地方法**，作用是**将指定源数组中的数组从指定位置复制到目标数组的指定位置**。

#### `Arrays.copyOf()`方法

```java
/**
 * 以正确的顺序返回一个包含此列表中所有元素的数组（从第一个到最后一个元素）; 返回的数组的运行时类型是指定数组的运行时类型。 
 */
public Object[] toArray() {1
    //elementData：要复制的数组；size：要复制的长度
    return Arrays.copyOf(elementData, size);
}
```



![img](https://gitee.com//nfLJ/Pic/raw/master//collection/20200707162401.png)



可以看出`Arrays.copyOf()`有很多重载方法，我们使用其中一个举例：

```java
 public static int[] copyOf(int[] original, int newLength) {
     // 新建一个数组
     int[] copy = new int[newLength];
     // 调用了System.arraycopy()方法
     System.arraycopy(original, 0, copy, 0,
                      Math.min(original.length, newLength));
     // 返回新建的数组
     return copy;
 }
```

### 推荐阅读

[第二弹！「Java面试题精华集」1w字的Java集合框架篇（2020最新版） ！](https://juejin.cn/post/6844904201202450445)

