# JavaScript常用对象

## 1. 数组对象

#### 1.1 创建数组

#### 1.1.1 使用对象创建

```javascript
var arr = new Array();
arr[0] = 1;
arr[1] = 2;
arr[2] = 3;
arr[3] = 4;
arr[4] = 5;
arr[5] = 6;
arr[6] = 7;
arr[7] = 8;
arr[8] = 9;
```

#### 1.1.2 使用字面量创建

```javascript
var arr = ["1", 2, 3, 4, "5", 6, 7, "8", 9];
```

### 1.2 遍历数组

```javascript
for (var i = 0; i < arr.length; i++) {
    console.log(arr[i]);
}
```

### 1.3 数组属性

- **constructor** : 返回创建数组的原型函数

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029104853.png)

-  **length** : 设置或者返回数组元素的个数

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029104645.png)

### 1.4 数组方法

-  **push()** : **可以向数组的末尾添加一个或多个元素，并返回数组的新的长度**

```javascript
var arr = ["李白", "杜甫", "孟浩然"];
var result = arr.push("李贺", "李清照", "苏轼", "刘禹锡");
console.log(arr);
console.log(result);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029105419.png)

- **pop()** : **可以删除数组的最后一个元素，并将被删除的元素作为返回值返回**

```javascript
var arr = ["李白", "杜甫", "孟浩然"];
var result = arr.pop();
console.log(arr);
console.log(result);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029105552.png)

-  **unshift()** : **向数组开头添加一个或多个元素，并返回新的数组长度**

```javascript
var arr = ["李白", "杜甫", "孟浩然"];
var result = arr.unshift("李清照", "苏轼");
console.log(arr);
console.log(result);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029105719.png)



- **shift()** : **可以删除数组的第一个元素，并将被删除的元素作为返回值返回**

```javascript
var arr = ["李白", "杜甫", "孟浩然"];
var result = arr.shift();
console.log(arr);
console.log(result);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029105828.png)



- **forEach()** : **可以用来遍历数组**

forEach()方法需要一个函数作为参数，像这种函数，由我们创建但是不由我们调用的，我们称为回调函数。数组中有几个元素函数就会执行几次，每次执行时，浏览器会将遍历到的元素，以实参的形式传递进来，我们可以来定义形参，来读取这些内容，浏览器会在回调函数中传递三个参数：

- 第一个参数：当前正在遍历的元素
- 第二个参数：当前正在遍历的元素的索引
- 第三个参数：正在遍历的数组

```javascript
var arr = ["李白", "杜甫", "孟浩然"];
arr.forEach(function (value, index, obj) {
    console.log(value + " --- " + index + " --- " + obj);
});
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029110320.png)

- **slice()** : **可以用来从数组提取指定元素，该方法不会改变元素数组，而是将截取到的元素封装到一个新数组中返回**

参数：

- 第一个参数：截取开始的位置的索引，包含开始索引
- 第二个参数：截取结束的位置的索引，不包含结束索引，第二个参数可以省略不写，此时会截取从开始索引往后的所有元素

> 索引可以传递一个负值，如果传递一个负值，则从后往前计算，-1代表倒数第一个，-2代表倒数第二个。

```javascript
var arr = ["李白", "杜甫", "孟浩然","李清照","苏轼"];
var result = arr.slice(1, 5);
console.log(result);
result = arr.slice(2);
console.log(result);
result = arr.slice(1, -1);
console.log(result);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029110810.png)

- **splice()** : **可以用于删除数组中的指定元素，该方法会影响到原数组，会将指定元素从原数组中删除，并将被删除的元素作为返回值返回**

参数：

- 第一个参数：表示开始位置的索引
- 第二个参数：表示要删除的元素数量
- 第三个参数及以后参数：可以传递一些新的元素，这些元素将会自动插入到开始位置索引前边

```javascript
var arr = ["李白", "杜甫", "孟浩然","李清照","苏轼"];
var result = arr.splice(3, 3);
console.log(arr);
console.log(result);
result = arr.splice(1, 0, "刘禹锡", "李贺", "王维");
console.log(arr);
console.log(result);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029111142.png)

- **concat()** : **可以连接两个或多个数组，并将新的数组返回，该方法不会对原数组产生影响**

```javascript
var arr = ["李白", "杜甫", "孟浩然"];
var arr2 = ["刘禹锡", "李贺", "王维"];
var arr3 = ["李清照","苏轼"];
var result = arr.concat(arr2, arr3, "王维");
console.log(result);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029111342.png)

- **join()** : **可以将数组转换为一个字符串，该方法不会对原数组产生影响，而是将转换后的字符串作为结果返回，在join()中可以指定一个字符串作为参数，这个字符串将会成为数组中元素的连接符，如果不指定连接符，则默认使用，作为连接符**

```javascript
var arr = ["李白", "杜甫", "孟浩然"];
var result = arr.join("-");
console.log(result);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029111628.png)

-  **reverse()** : **用来反转数组（前边的去后边，后边的去前边），该方法会直接修改原数组**

```javascript
var arr = ["李白", "杜甫", "孟浩然"];
arr.reverse();
console.log(arr);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029111744.png)

-  **sort()** : **可以用来对数组中的元素进行排序，也会影响原数组，默认会按照Unicode编码进行排序**

```javascript
var arr = ["b", "c", "a"];
arr.sort();
console.log(arr);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029111917.png)

> 注意 : 对于纯数字的数组，使用sort()排序时，也会按照Unicode编码来排序，所以对数字进排序时，可能会得到错误的结果。

```javascript
var arr = [3, 11, 5, 2, 1, 4];
arr.sort();
console.log(arr);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029112809.png)

因此，我们需要自己定义排序规则，可以在sort()添加一个回调函数，来指定排序规则，回调函数中需要定义两个形参，浏览器将会分别使用数组中的元素作为实参去调用回调函数，使用哪个元素调用不确定，但是肯定的是在数组中a一定在b前边，浏览器会根据回调函数的返回值来决定元素的顺序，如下：

- 如果返回一个大于0的值，则元素会交换位置
- 如果返回一个小于0的值，则元素位置不变
- 如果返回一个等于0的值，则认为两个元素相等，也不交换位置

经过上边的规则，我们可以总结下：

- 如果需要升序排列，则返回 a-b
- 如果需要降序排列，则返回 b-a

```javascript
var arr = [3, 11, 5, 2, 1, 4];
arr.sort(function (a, b) {
    return a - b;
});
console.log(arr);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029113426.png)

## 2. Date对象

在JS中，用Date对象来处理时间和日期

```javascript
var date = new Date();
console.log(date);

console.log(date.getFullYear());//获取当前日期对象的年份(四位数字年份)
console.log(date.getMonth());//获取当前日期对象的月份(0 ~ 11)
console.log(date.getDate());//获取当前日期对象的日数(1 ~ 31)
console.log(date.getHours());//获取当前日期对象的小时(0 ~ 23)
console.log(date.getMinutes());//获取当前日期对象的分钟(0 ~ 59)
console.log(date.getSeconds());//获取当前日期对象的秒钟(0 ~ 59)
console.log(date.getMilliseconds());//获取当前日期对象的毫秒(0 ~ 999)
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029150618.png)

## 3. Math对象

Math和其它的对象不同，它不是一个构造函数，它属于一个工具类不用创建对象，它里边封装了数学运算相关的属性和方法。

```javascript
/*固定值*/
console.log("PI = " + Math.PI);
console.log("E  = " + Math.E);
console.log("===============");
/*正数*/
console.log(Math.abs(1));        //可以用来计算一个数的绝对值
console.log(Math.ceil(1.1));     //可以对一个数进行向上取整，小数位只有有值就自动进1
console.log(Math.floor(1.99));   //可以对一个数进行向下取整，小数部分会被舍掉
console.log(Math.round(1.4));    //可以对一个数进行四舍五入取整
console.log("===============");
/*负数*/
console.log(Math.abs(-1));       //可以用来计算一个数的绝对值
console.log(Math.ceil(-1.1));    //可以对一个数进行向上取整，小数部分会被舍掉
console.log(Math.floor(-1.99));  //可以对一个数进行向下取整，小数位只有有值就自动进1
console.log(Math.round(-1.4));   //可以对一个数进行四舍五入取整
console.log("===============");
/*随机数*/
//Math.random()：可以用来生成一个0-1之间的随机数
//生成一个0-x之间的随机数：Math.round(Math.random()*x)
//生成一个x-y之间的随机数：Math.round(Math.random()*(y-x)+x)
console.log(Math.round(Math.random() * 10));            //生成一个0-10之间的随机数
console.log(Math.round(Math.random() * (10 - 1) + 1));  //生成一个1-10之间的随机数
console.log("===============");
/*数学运算*/
console.log(Math.pow(12, 3));   //Math.pow(x,y)：返回x的y次幂
console.log(Math.sqrt(4));      //Math.sqrt(x) ：返回x的平方根
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029150902.png)

## 4. String对象

### 4.1 属性

- **constructor** : **返回创建字符串对象的原型函数**

```javascript
var str = "Hello,JavaScript!";
console.log(str.constructor);	// ƒ String() { [native code] }
```

-  **length** : **可以用来获取字符串的长度**

```javascript
var str = "Hello,JavaScript!";
console.log(str.length);	// 17
```

### 4.2 字符串方法

- **charAt** ：**可以根据索引获取指定位置的字符**

```javascript
var str = "Hello,JavaScript!";
console.log(str.charAt(1));		// e
```

-  **charCodeAt()** : **获取指定位置字符的字符编码（Unicode编码）**

```javascript
var str = "Hello,JavaScript!";
console.log(str.charCodeAt(1));		// 101
```

- **concat()** ：**可以用来连接两个或多个字符串**

```javascript
var str = "Hello,JavaScript!";
console.log(str.concat("Nice to meet you!"));
// Hello,JavaScript!Nice to meet you!
```

- **indexOf()** : **可以检索一个字符串中是否含有指定内容，如果字符串中含有该内容，则会返回其第一次出现的索引，如果没有找到指定的内容，则返回-1，可以指定一个第二个参数，指定开始查找的位置**

```javascript
var str = "Hello,JavaScript!";
console.log(str.indexOf("o"));	// 4
console.log(str.indexOf("a", 5));	// 7
```

- **lastIndexOf()** : **用法和indexOf()一样，不同的是indexOf是从前往后找，而lastIndexOf是从后往前找，也可以指定开始查找的位置**

```javascript
var str = "Hello,JavaScript!";
console.log(str.lastIndexOf("Hello"));	// 0
console.log(str.lastIndexOf("Java", 10));	// 6
console.log(str.lastIndexOf("java", 10));	// -1  大小写敏感
```

- **slice()** : **可以从字符串中截取指定的内容，不会影响原字符串，而是将截取到内容返回**

参数：

- 第一个参数：开始位置的索引（包括开始位置）
- 第二个参数：结束位置的索引（不包括结束位置），如果省略第二个参数，则会截取到后边所有的

```javascript
var str = "Hello,JavaScript!";
var result = str.slice(1, 4);	
console.log(result);	// ell
result = str.slice(1);	
console.log(result);	// ello,JavaScript!
result = str.slice(1, -1);
console.log(result);	// ello,JavaScript
```

- **substring()** : **可以用来截取一个字符串，它和slice()类似**

参数：

- 第一个参数：开始截取位置的索引（包括开始位置）
- 第二个参数：结束位置的索引（不包括结束位置），如果省略第二个参数，则会截取到后边所有的

```javascript
var str = "Hello,JavaScript!";
var result = str.substring(1, 4);
console.log(result);	// ell
result = str.substring(1);
console.log(result);	// ello,JavaScript!
result = str.substring(1, -1);
console.log(result);	// H
result = str.substring(1, -2);
console.log(result);	// H
result = str.substring(2, -2);
console.log(result);	// He
```

> 当第二个参数为负数时，截取的字符串是顺序输出，且不会到负数索引位置

-  **substr()** : **用来截取字符串**

参数：

- 第一个参数：截取开始位置的索引
- 第二个参数：截取的长度

```javascript
var str = "Hello,JavaScript!";
var result = str.substr(6,10);
console.log(result);	// JavaScript
```

-  **split()** : **可以将一个字符串拆分为一个数组，需要一个字符串作为参数，将会根据该字符串去拆分数组**

```javascript
var str = "Hello,JavaScript!";
var result = str.split(",");
console.log(result);
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201029160400.png)

-  **toUpperCase()** : **将一个字符串转换为大写并返回**

```javascript
var str = "Hello,JavaScript!";
var result = str.toUpperCase();
console.log(result);	// HELLO,JAVASCRIPT!
```

-  **toLowerCase()** : **将一个字符串转换为小写并返回**

```javascript
var str = "Hello,JavaScript!";
var result = str.toLowerCase();
console.log(result);	// hello,javascript!
```

## 5. RegExp对象

### 5.1 概述

正则表达式是描述字符模式的对象。

正则表达式用于对字符串模式匹配及检索替换，是对字符串执行模式匹配的强大工具。

### 5.2 创建正则表达式

#### 5.2.1 使用对象创建

语法格式：

```javascript
var 变量名 = new RegExp("正则表达式","匹配模式");
```

匹配模式：

- i：忽略大小写
- g：全局匹配模式
- ig：忽略大小写且全局匹配模式

示例：

```javascript
// 这个正则表达式可以来检查一个字符串中是否含有a
var reg = new RegExp("ab", "i");
var str = "ABC";
var result = reg.test(str);
console.log(result);	// true
```

#### 5.2.2 使用字面量创建

语法格式：

```javascript
var 变量名 = /正则表达式/匹配模式;
```

匹配模式：

- i：忽略大小写
- g：全局匹配模式
- m：执行多行匹配

示例：

```javascript
// 这个正则表达式可以来检查一个字符串中是否含有a
var reg = /a/i;
var str = "ABc";
var result = reg.test(str);
console.log(result);	// true
```

### 5.3 方括号

方括号用于查找某个范围内的字符：

| 表达式             | 描述                               |
| :----------------- | :--------------------------------- |
| [abc]              | 查找方括号之间的任何字符。         |
| [^abc]             | 查找任何不在方括号之间的字符。     |
| [0-9]              | 查找任何从 0 至 9 的数字。         |
| [a-z]              | 查找任何从小写 a 到小写 z 的字符。 |
| [A-Z]              | 查找任何从大写 A 到大写 Z 的字符。 |
| [A-z]              | 查找任何从大写 A 到小写 z 的字符。 |
| [adgk]             | 查找给定集合内的任何字符。         |
| [^adgk]            | 查找给定集合外的任何字符。         |
| (red\|blue\|green) | 查找任何指定的选项。               |

### 5.4 元字符

元字符（Metacharacter）是拥有特殊含义的字符：

| 元字符 | 描述                                        |
| :----- | :------------------------------------------ |
| .      | 查找单个字符，除了换行和行结束符。          |
| \w     | 查找数字、字母及下划线。                    |
| \W     | 查找非单词字符。                            |
| \d     | 查找数字。                                  |
| \D     | 查找非数字字符。                            |
| \s     | 查找空白字符。                              |
| \S     | 查找非空白字符。                            |
| \b     | 匹配单词边界。                              |
| \B     | 匹配非单词边界。                            |
| \0     | 查找 NULL 字符。                            |
| \n     | 查找换行符。                                |
| \f     | 查找换页符。                                |
| \r     | 查找回车符。                                |
| \t     | 查找制表符。                                |
| \v     | 查找垂直制表符。                            |
| \xxx   | 查找以八进制数 xxx 规定的字符。             |
| \xdd   | 查找以十六进制数 dd 规定的字符。            |
| \uxxxx | 查找以十六进制数 xxxx 规定的 Unicode 字符。 |

### 5.5 量词

| 量词   | 描述                                                         |
| :----- | :----------------------------------------------------------- |
| n+     | 匹配任何包含至少一个 n 的字符串。例如，/a+/ 匹配 "candy" 中的 "a"，"caaaaaaandy" 中所有的 "a"。 |
| n*     | 匹配任何包含零个或多个 n 的字符串。例如，/bo*/ 匹配 "A ghost booooed" 中的 "boooo"，"A bird warbled" 中的 "b"，但是不匹配 "A goat grunted"。 |
| n?     | 匹配任何包含零个或一个 n 的字符串。例如，/e?le?/ 匹配 "angel" 中的 "el"，"angle" 中的 "le"。 |
| n{X}   | 匹配包含 X 个 n 的序列的字符串。例如，/a{2}/ 不匹配 "candy," 中的 "a"，但是匹配 "caandy," 中的两个 "a"，且匹配 "caaandy." 中的前两个 "a"。 |
| n{X,}  | X 是一个正整数。前面的模式 n 连续出现至少 X 次时匹配。例如，/a{2,}/ 不匹配 "candy" 中的 "a"，但是匹配 "caandy" 和 "caaaaaaandy." 中所有的 "a"。 |
| n{X,Y} | X 和 Y 为正整数。前面的模式 n 连续出现至少 X 次，至多 Y 次时匹配。例如，/a{1,3}/ 不匹配 "cndy"，匹配 "candy," 中的 "a"，"caandy," 中的两个 "a"，匹配 "caaaaaaandy" 中的前面三个 "a"。注意，当匹配 "caaaaaaandy" 时，即使原始字符串拥有更多的 "a"，匹配项也是 "aaa"。 |
| n$     | 匹配任何结尾为 n 的字符串。                                  |
| ^n     | 匹配任何开头为 n 的字符串。                                  |
| ?=n    | 匹配任何其后紧接指定字符串 n 的字符串。                      |
| ?!n    | 匹配任何其后没有紧接指定字符串 n 的字符串。                  |

### 5.6 支持正则表达式的 String 对象的方法

| 方法    | 描述                             |
| :------ | :------------------------------- |
| search  | 检索与正则表达式相匹配的值。     |
| match   | 找到一个或多个正则表达式的匹配。 |
| replace | 替换与正则表达式匹配的子串。     |
| split   | 把字符串分割为字符串数组。       |

### 5.7 RegExp 对象属性

| 属性        | 描述                                               |
| :---------- | :------------------------------------------------- |
| constructor | 返回一个函数，该函数是一个创建 RegExp 对象的原型。 |
| global      | 判断是否设置了 "g" 修饰符                          |
| ignoreCase  | 判断是否设置了 "i" 修饰符                          |
| lastIndex   | 用于规定下次匹配的起始位置                         |
| multiline   | 判断是否设置了 "m" 修饰符                          |
| source      | 返回正则表达式的匹配模式                           |

### 参考

[JavaScript RegExp 对象](https://www.runoob.com/jsref/jsref-obj-regexp.html)