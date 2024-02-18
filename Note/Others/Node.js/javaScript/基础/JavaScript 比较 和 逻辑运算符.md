比较和逻辑运算符用于测试 _true_ 或者 _false_。

---

## 比较运算符

比较运算符在逻辑语句中使用，以测定变量或值是否相等。

x=5，下面的表格解释了比较运算符：

|运算符|描述|比较|返回值|实例|
|:--|:--|:--|:--|:--|
|`==`|等于|x`==`8|_false_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison1)|
|||x`==`5|_true_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison2)|
|`===`|绝对等于（值和类型均相等）|x`===`"5"|_false_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison3)|
|||x`===`5|_true_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison4)|
|`!=`|不等于|x`!=`8|_true_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison5)|
|`!==`|不绝对等于（值和类型有一个不相等，或两个都不相等）|x!=="5"|_true_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison6)|
|||x!==5|_false_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison7)|
|>|大于|x>8|_false_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison8)|
|<|小于|x<8|_true_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison9)|
|>=|大于或等于|x>=8|_false_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison10)|
|<=|小于或等于|x<=8|_true_|[实例 »](https://www.runoob.com/try/try.php?filename=tryjs_comparison11)|

  

---

## 如何使用

可以在条件语句中使用比较运算符对值进行比较，然后根据结果来采取行动：

if (age<18) x="Too young";

您将在本教程的下一节中学习更多有关条件语句的知识。

---

## 逻辑运算符

逻辑运算符用于测定变量或值之间的逻辑。

给定 x=6 以及 y=3，下表解释了逻辑运算符：

|运算符|描述|例子|
|:--|:--|:--|
|&&|and|(x < 10 && y > 1) 为 true|
|\||or|(x==5 \| y==5) 为 false|
|!|not|!(x==y) 为 true|

  

---

## 条件运算符

JavaScript 还包含了基于某些条件对变量进行赋值的条件运算符。

### 语法

``` js
variablename=(condition)?value1:value2 
```

### 例子

## 实例

如果变量 age 中的值小于 18，则向变量 voteable 赋值 "年龄太小"，否则赋值 "年龄已达到"。

``` js
voteable=(age<18)?"年龄太小":"年龄已达到";
```