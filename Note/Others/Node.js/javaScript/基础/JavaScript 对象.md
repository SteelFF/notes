JavaScript 对象是拥有属性和方法的数据。

---

## 真实生活中的对象，属性和方法

真实生活中，一辆汽车是一个对象。

对象有它的属性，如重量和颜色等，方法有启动停止等:

|对象|属性|方法|
|---|---|---|
|![](https://www.runoob.com/images/objectExplained.gif)|car.name = Fiat  <br>  <br>car.model = 500  <br>  <br>car.weight = 850kg  <br>  <br>car.color = white|car.start()  <br>  <br>car.drive()  <br>  <br>car.brake()  <br>  <br>car.stop()|

所有汽车都有这些属性，但是每款车的属性都不尽相同。

所有汽车都拥有这些方法，但是它们被执行的时间都不尽相同。

---

## JavaScript 对象

在 JavaScript中，几乎所有的事物都是对象。

|   |   |
|---|---|
|![Note](https://www.runoob.com/images/lamp.jpg)|在 JavaScript 中，对象是非常重要的，当你理解了对象，就可以了解 JavaScript 。|

你已经学习了 JavaScript 变量的赋值。

以下代码为变量 **car** 设置值为 "Fiat" :

``` JS
var car = "Fiat";
```

对象也是一个变量，但对象可以包含多个值（多个变量），每个值以 name:value 对呈现。

``` JS
var car = {name:"Fiat", model:500, color:"white"};
```

在以上实例中，3 个值 ("Fiat", 500, "white") 赋予变量 car。

|   |   |
|---|---|
|![Note](https://www.runoob.com/images/lamp.jpg)|JavaScript 对象是变量的容器。|

---

## 对象定义

你可以使用字符来定义和创建 JavaScript 对象:

## 实例

``` JS
var person = {firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"};  
```


定义 JavaScript 对象可以跨越多行，空格跟换行不是必须的：

## 实例

``` js
var person = {  
    firstName:"John",  
    lastName:"Doe",  
    age:50,  
    eyeColor:"blue"  
};
```


  

---

## 对象属性

可以说 "JavaScript 对象是变量的容器"。

但是，我们通常认为 "JavaScript 对象是键值对的容器"。

键值对通常写法为 **name : value** (键与值以冒号分割)。

键值对在 JavaScript 对象通常称为 **对象属性**。

|   |   |
|---|---|
|![Note](https://www.runoob.com/images/lamp.jpg)|JavaScript 对象是属性变量的容器。|

对象键值对的写法类似于：

- PHP 中的关联数组
- Python 中的字典
- C 语言中的哈希表
- Java 中的哈希映射
- Ruby 和 Perl 中的哈希表

---

## 访问对象属性

你可以通过两种方式访问对象属性:

## 实例 1

``` js
person.lastName;
```
  

## 实例 2

``` js
person["lastName"];
```


---

## 对象方法

对象的方法定义了一个函数，并作为对象的属性存储。

对象方法通过添加 () 调用 (作为一个函数)。

该实例访问了 person 对象的 fullName() 方法:

## 实例

``` js
name = person.fullName();
```


如果你要访问 `person` 对象的 `fullName `属性，它将作为一个定义函数的字符串返回：

## 实例

``` js
name = person.fullName;`
```

|   |   |
|---|---|
|![Note](https://www.runoob.com/images/lamp.jpg)|JavaScript 对象是属性和方法的容器。|

在随后的教程中你将学习到更多关于函数，属性和方法的知识。

---

## 访问对象方法

你可以使用以下语法创建对象方法：

``` js
methodName : function() {
    // 代码 }
```

你可以使用以下语法访问对象方法：

## 实例

``` js
objectName.methodName()
```

通常` fullName()` 是作为` person `对象的一个方法，` fullName` 是作为一个属性。

如果使用 `fullName `属性，不添加 **()**, 它会返回函数的定义：

## 实例

``` js
objectName.methodName
```

有多种方式可以创建，使用和修改 JavaScript 对象。

同样也有多种方式用来创建，使用和修改属性和方法。

|   |   |
|---|---|
|![Note](https://www.runoob.com/images/lamp.jpg)|在随后的教程中，你将学习到更多关于对象的知识。|