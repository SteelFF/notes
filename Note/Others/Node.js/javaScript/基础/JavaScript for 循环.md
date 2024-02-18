循环可以将代码块执行指定的次数。

---

## JavaScript 循环

如果您希望一遍又一遍地运行相同的代码，并且每次的值都不同，那么使用循环是很方便的。

我们可以这样输出数组的值：

## 一般写法：

``` js
document.write(cars[0] + "<br>"); document.write(cars[1] + "<br>"); document.write(cars[2] + "<br>"); document.write(cars[3] + "<br>"); document.write(cars[4] + "<br>"); document.write(cars[5] + "<br>");
```

  

## 使用for循环

``` js
for (var i=0;i<cars.length;i++) {
	document.write(cars[i] + "<br>"); 
}
```


---

## 不同类型的循环

JavaScript 支持不同类型的循环：

- **for** - 循环代码块一定的次数
- **for/in** - 循环遍历对象的属性
- **while** - 当指定的条件为 true 时循环指定的代码块
- **do/while** - 同样当指定的条件为 true 时循环指定的代码块

---

## For 循环

for 循环是您在希望创建循环时常会用到的工具。

下面是 for 循环的语法：

for (_语句 1_; _语句 2_; _语句 3_)  
{  
    _被执行的代码块_  
}  

**语句 1** （代码块）开始前执行

**语句 2** 定义运行循环（代码块）的条件

**语句 3** 在循环（代码块）已被执行之后执行

## 实例

``` js
for (var i=0; i<5; i++) { 
	x=x + "该数字为 " + i + "<br>"; 
}
```


从上面的例子中，您可以看到：

Statement 1 在循环开始之前设置变量 (var i=0)。

Statement 2 定义循环运行的条件（i 必须小于 5）。

Statement 3 在每次代码块已被执行后增加一个值 (i++)。

---

## 语句 1

通常我们会使用语句 1 初始化循环中所用的变量 (var i=0)。

语句 1 是可选的，也就是说不使用语句 1 也可以。

您可以在语句 1 中初始化任意（或者多个）值：

## 实例:

``` js
for (var i=0,len=cars.length; i<len; i++) { 
	document.write(cars[i] + "<br>"); 
}
```


同时您还可以省略语句 1（比如在循环开始前已经设置了值时）：

## 实例:

``` js
var i=2,len=cars.length; 
for (; i<len; i++) { 
	document.write(cars[i] + "<br>"); 
}
```

---

## 语句 2

通常语句 2 用于评估初始变量的条件。

语句 2 同样是可选的。

如果语句 2 返回 true，则循环再次开始，如果返回 false，则循环将结束。

|   |   |
|---|---|
|![lamp](https://www.runoob.com/images/lamp.jpg)|如果您省略了语句 2，那么必须在循环内提供 _break_。否则循环就无法停下来。这样有可能令浏览器崩溃。请在本教程稍后的章节阅读有关 break 的内容。|

  

---

## 语句 3

通常语句 3 会增加初始变量的值。

语句 3 也是可选的。

语句 3 有多种用法。增量可以是负数 (i--)，或者更大 (i=i+15)。

语句 3 也可以省略（比如当循环内部有相应的代码时）：

## 实例:

``` js
var i=0,len=cars.length; 
for (; i<len; ) { 
	document.write(cars[i] + "<br>"); 
	i++; 
}
```


---

## For/In 循环

JavaScript for/in 语句循环遍历对象的属性：

## 实例

``` js
var person={fname:"Bill",lname:"Gates",age:56}; for (x in person){ // x 为属性名 
	txt=txt + person[x]; 
}
```

您将在有关 JavaScript 对象的章节学到更多有关 for / in 循环的知识。

