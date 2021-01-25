## ES6新特性

### 一、let 和 const 命令

> 变量和常量严格区分

例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <script>
         // 1： 在javascrit定义数据类型只有一种标识 - var
         // 灵活拷问：js有数据类型吗？
         // 有，string,number,Object, boolean, undefined
         // 什么是语言：电脑内存中，人通过代码和电脑沟通。
         let name = "zhangsan";
         let age = 100;
         let flag = false;   
         //js被骂没常量
         const PI = Math.PI;
         // 修改会报错
         //PI = 1245;
         console.log(PI)
         //var或造成变量穿透
         for(let i=0;i<5;i++){
             console.log(i);
         };
         //console.log("===这里就是变量穿透===>" + i)
    </script>
</body>
</html>
```

### 二、模板字符串

以前：我们都是使用  `''` 或 `""` 来把字符串连起来

现在：``` `【反引号】

用途一：基本的字符串格式化。将表达式嵌入字符串中进行拼接。用 ` ${}` 来界定

```javascript
//es5    
let name = 'itcast'    
console.log('hello ' + name)    //es6    
const name = 'itcast'    
console.log(`hello ${name}`) //hello itcast
```

用途二：在ES5时我们通过反斜杠来做多行字符串或者字符串一行行拼接。ES6使用反引号 ` `` `直接搞定

例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>模板字符串</title>
</head>
<body>
    <script>
        var username = "张三";
        var age = 30;
        // 1: 原始的做法就是去拼接字符串
        var str = "我名字叫 " + username+",年龄是: "+age;
        console.log(str);  
        // 2:用模板字符串来拯救 注意：这里是 `（飘键） (tab键盘的上面那个键)
        // jdk1.9 
        var str2 = `我名字叫 ${username},年龄是: ${age}`;
        console.log(str2);
    </script>
</body>
</html>
```

### 三、函数默认参数与箭头函数

#### **函数默认参数**

在方法的参数后面加上一个默认值即可

例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <script>
        // 默认参数 给参数列表设定初始值
        function add(a =100,b=100) {
            console.log(a,b);    
        }
        // 执行方法，会用默认值填充，打印出来100,200
        add();
        // 覆盖默认值打印  结果是1，2      
        add(1,2);
    </script>
</body>
</html>
```

#### **箭头函数** 

箭头函数简化函数的定义，可以让我们不用使用`function`关键字

```
/*
箭头函数最直观的三个特点。
    1不需要function关键字来创建函数
    2省略return关键字
    3继承当前上下文的 this 关键字
*/
```

例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>箭头函数</title>
</head>
<body>
    <script>
        // 箭头函数
        // 它也是一种函数的定义，它简化定义仅此而已。
        // 步骤：1：去掉function  2: 括号后面加箭头。
        // 1:声明式的定义
        function add (){
        };
        // 2:表达式的定义
        var add2 = function(){
        }     
        // 3:箭头函数的定义
        var sum = (a = 100,b = 300)=>{
            console.log(a+b);
        };
        // 这里执行箭头函数
        sum(50,50);
        // 这里执行箭头函数
        sum();
        // 这里执行箭头函数
        sum(400);
    </script>
</body>
</html>
```

箭头函数深入学习

例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>箭头函数</title>
</head>
<body>
    <script>
        // 箭头函数
        // 它也是一种函数的定义，它简化定义仅此而已。
        // 步骤：1：去掉function  2: 括号后面加箭头。
        // 无参数的函数
        //var sum = function(){
        //}
        // 箭头改造如下
        //var sum = ()=>{}
        // 有参数
        // 第一种情况  一个参数的如下
        //var sum2 = function(a){
        //};
        // 箭头改造如下
        var sum2 = (a)=>{};
        var sum2 = a=>{
            return a;
        };
        // 第二种情况 二个参数的以上,记住括号一定要加
        //var sum3 = function(a,b){
        //    return a + b;
        //};
        // 箭头改造如下
        var sum3 = (a,b)=>{
            return a + b;
        };
        // 第三种情况，如果没有逻辑体，只有返回值可以简化如下
        //var sum4 = function(a,b){
        //    return a + b;
        //};
        // 箭头改造如下
        var sum4 = (a,b)=>a+b
        // 执行
        console.log(sum2(100));
        console.log(sum3(100,100));
        console.log(sum4(100,100));
    </script>
</body>
</html>
```

### 四、对象初始化简写

> 如果一个对象中的 key 和 value 的名字一样的情况下，可以定义成一个

例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>对象简写</title>
</head>
<body>
<script>
    function person(name, age) {
        //return {name:name,age:age};
        // 对象简写
        return { name, age };
    };
    // 调用和执行
    var json = person("小花花美美", 20);
    console.log(json.name, json.age);
    //========================= 实战应用 =========================
    //<button onclick="login()">登录</button>
    function login() {
        var username = $("#username").val();
        var password = $("#password").val();
        // 发送ajax
        $.ajax({
            type: "post",
            // 对象简写
            data: { username, password },
            // 原始写分
            //data:{username:username,password:password},
            success() {
            }
        });
    }
</script>
</body>
</html>
```

### 五、对象解构

> ES6提供一些快捷获取对象属性和行为方式

例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>对象解构</title>
</head>
<body>
    <script>
        // 对象解构 --- es6提供一些获取快捷获取对象属性和行为方式
        var person = {
            name:'zhangsan',
            age:32,
            language:"cn",
            // 函数也有处理
            /* say:function(){
                console.log(this.name+"年龄是：" + this.age);
            }   */
            /* say:()=>{
                console.log(this.name+"年龄是：" + this.age);
            }  */
            say(){
                console.log(this.name+"年龄是：" + this.age);
            } 
        };
        // ===========================传统的做法========================
        var name = person.name;
        var age = person.age;
        person.say();
        // ===========================对象解构做法========================
        //es6的做法 前提：默认情况name,age必须是jsonkey.
        var {name,age} = person;
        console.log(name,age);
        // 可以用冒号取小名
        var {name,age,language:lan} = person;
        console.log(name,age,lan);
    </script>
</body>
</html>
```

### 六、传播操作符【…】

> 把一个对象的属性传播到另一个对象中

例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>ES6的语法：传播操作符【...】</title>
</head>
<body>
    <script>
        // 1： 定义一个对象
        var person1 = {
            name: '小飞飞',
            age: 16,
        };
        // 2： 对象解构
        var {name,age} = person1;
        // =========================== ... 对象融合=====================
        var person2 = {
            ...person1,
            gender:1,
            tel:13478900
        };
        console.log(person2);
        // =========================== ... 对象取值=====================
        // ... 对象取值
        var person3 = {
            name:"李四",
            gender:1,
            tel:"11111",
            address:'广州'
        };
        // ...person4 把剩下没取走给我。
        var {name,gender,...person4} = person3;
        console.log(name)
        console.log(age)
        console.log(person4)
        // =================场景分析 -----伪代码========================
        // 模拟后台：异步查询返回用户数据 如下：
        function findUsers(){
            $.get("xxxxx",function(res){
                var res = {
                    pages:11,
                    pageSize:10,
                    pageNo:1,
                    firstFlag:true,
                    lastFlag:false,
                    total:123,
                    data:[{},{},{},{}],
                };
                // ==========================对象 ... 取值===============
                var {data:users,...pagesjon} = res;
                //等价于
                /*  var users = res.data;
                   var pagesjon = {
                    res = {
                        pages:11,
                        pageSize:10,
                        pageNo:1,
                        firstFlag:true,
                        lastFlag:false,
                        total:123,
                    }; */
            })
        }
    </script>
</body>
</html>
```

### 七、数组 map 和 reduce 方法的使用

#### map()

>  方法可以将原数组中的所有元素通过一个函数进行处理并放入到一个新数组中并返回该新数组。

例子：有一个字符串数组，希望转为int数组

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>数组方法的扩展-map reduce</title>
</head>
<body>
    <script>
        /*********************************map*********************/
        let arr = ['1', '20', '-5', '3'];
        console.log(arr)
        // 1:数据类型的更改 map自身循环的功能
        // 2:map方法可以将原数组中的所有元素通过一个函数进行处理并放入到一个新数组中并返回该新数组。
        var newarr = arr.map(function (value) {
            return parseInt(value) * 2;
        });
        /* 
        // 箭头简化01
        var newarr = arr.map(value=>{
            return parseInt(value) * 2;
        });
        // 箭头简化02
        var newarr = arr.map(value => parseInt(value) * 2);
        */
        console.log("原数组：", arr);
        console.log("map的newarr数组：", newarr); // 2 40 -10 6
        /*********************************end map*********************/
        /*********************************reduce*********************/
        //reduce 计算方法
        let arr2 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
        var result = arr2.reduce((a, b) => a + b);
        console.log(result);
        /*********************************end reduce*********************/
    </script>
</body>
</html>
```

#### reduce()

> reduce(function(),初始值（可选）) ：
>
> 接收一个函数（必须）和一个初始值（可选），该函数接收两个参数：
>
> - 第一个参数是上一次reduce处理的结果
> - 第二个参数是数组中要处理的下一个元素
>   reduce() 会从左到右依次把数组中的元素用reduce处理，并把处理的结果作为下次reduce的第一个参数。如果是 第一次，会把前两个元素作为计算参数，或者把用户指定的初始值作为起始参数

例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>数组方法的扩展-map reduce</title>
</head>
<body>
    <script>
        /*********************************reduce*********************/
        //reduce 计算方法 
        let arr2 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
        // 箭头写法 -- 1到10相加 
        var result = arr2.reduce((a, b) => a + b);
        // 原始写法  -- 1到10相加
        //var result = arr2.reduce(function(a,b){
        //    return a+b;
        //})
        console.log(result);
        /*********************************end reduce*********************/
    </script>
</body>
</html>
```

