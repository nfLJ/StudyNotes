# Java 泛型中的通配符

## 简介

Java 泛型（generics) 是 JDK 5 中引入的一个新特性，泛型提供了编译时类型安全检测机制，该机制允许开发者在编译时检测到非法的类型。泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。

## 使用泛型的好处

Java泛型可以保证如果程序在编译时没有发出警告，运行时就不会产生`ClassCastException`异常。同时，代码更加简洁、健壮。

## 泛型中的通配符

### 常用的 T，E，K，V，？

本质上这些个都是通配符，没啥区别，只不过是编码时的一种约定俗成的东西。比如上述代码中的 T ，我们可以换成 A-Z 之间的任何一个 字母都可以，并不会影响程序的正常运行，但是如果换成其他的字母代替 T ，在可读性上可能会弱一些。**通常情况下，T，E，K，V，？ 是这样约定的：**

- ？ 表示不确定的 Java 类型
- T (type) 表示具体的一个Java类型
- K V (key value) 分别代表 Java 键值中的Key Value
- E (element) 代表Element

### 上界通配符 < ? extends E>

> 上界：用 `extends` 关键字声明，使用时指定的类型是 E 或者 E 的子类，或者实现某个接口，即 **<=**

**举例：**

- < ? extends Number >	(无穷小，Number]
  只允许泛型为 Number 及 Number 的子类
- < ? extends Comparable>   
  只允许泛型为实现 Comparable 接口的实现类

### 下界通配符 < ? super E>

> 下界：用`super`关键字生明，使用时指定的类型不能小于操作的类，即 **>=**

**举例：**

- < ? super Number >    [Number，无穷大）
  只允许泛型为 Number 及 Number 的父类

### ？ 和 T 的区别

？和 T 都表示不确定的类型，区别在于我们可以对 T 进行操作，但是对 ？ 不行，比如如下这种 ：

```java
// 可以
T t = eat();

// 不可以
？ cat = eat();
```

简单总结下：

T 是一个**确定**的类型，通常用于泛型类和泛型方法的定义，？是一个**不确定**的类型，通常用于泛型方法的调用代码和形参，不能用于定义类和泛型方法。

#### 区别1：通过 T 来 确保 泛型参数的一致性

```java
// 通过 T 来 确保 泛型参数的一致性
public <T extends Number> void test(List<T> dest, List<T> src)

//通配符是 不确定的，所以这个方法不能保证两个 List 具有相同的元素类型
public void test(List<? extends Number> dest, List<? extends Number> src)
```

像下面的代码中，约定的 T 是 Number 的子类才可以，但是申明时是用的 String ，所以就会飘红报错。

#### 区别2：类型参数可以多重限定而通配符不行

![](https://gitee.com/nfLJ/Pic/raw/master/JavaSE/20201231103406.png)

使用 & 符号设定多重边界（Multi Bounds)，指定泛型类型 T 必须是 MultiLimitInterfaceA 和 MultiLimitInterfaceB 的共有子类型，此时变量 t 就具有了所有限定的方法和属性。对于通配符来说，因为它不是一个确定的类型，所以不能进行多重限定。

#### 区别3：通配符可以使用超类限定而类型参数不行

类型参数 T 只具有 一种 类型限定方式：

```java
T extends A
```

但是通配符 ? 可以进行 两种限定：

```java
? extends A
? super A
```

## 参考

> https://juejin.cn/post/6844903917835419661#heading-9

