## typeof 操作符

你可以使用 typeof 操作符来检测变量的数据类型。

## 实例

``` js
typeof "John"                // 返回 string  
typeof 3.14                  // 返回 number  
typeof false                 // 返回 boolean  
typeof [1,2,3,4]             // 返回 object  
typeof {name:'John', age:34} // 返回 object
```

|   |   |
|---|---|
|![Note](https://www.runoob.com/images/lamp.jpg)|在JavaScript中，数组是一种特殊的对象类型。 因此 typeof [1,2,3,4] 返回 object。|

---

## null

在 JavaScript 中 null 表示 "什么都没有"。

null是一个只有一个值的特殊类型。表示一个空对象引用。

|   |   |
|---|---|
|![Note](https://www.runoob.com/images/lamp.jpg)|用 typeof 检测 null 返回是object。|

你可以设置为 null 来清空对象:

## 实例

``` js
var person = null;           // 值为 null(空), 但类型为对象  
```


你可以设置为 undefined 来清空对象:

## 实例

``` js
var person = undefined;     // 值为 undefined, 类型为 undefined  
```


---

## undefined

在 JavaScript 中, **undefined** 是一个没有设置值的变量。

**typeof** 一个没有值的变量会返回 **undefined**。

## 实例

``` js
var person;                  // 值为 undefined(空), 类型是undefined
```


任何变量都可以通过设置值为 **undefined** 来清空。 类型为 **undefined**.

## 实例

``` js
person = undefined;          // 值为 undefined, 类型是undefined
```


---

## undefined 和 null 的区别

## 实例

null 和 undefined 的值相等，但类型不等：

``` js
typeof undefined             // undefined  
typeof null                  // object  
null === undefined           // false  
null == undefined            // true
```