条件语句用于基于不同的条件来执行不同的动作。

---

## 条件语句

通常在写代码时，您总是需要为不同的决定来执行不同的动作。您可以在代码中使用条件语句来完成该任务。

在 JavaScript 中，我们可使用以下条件语句：

- **if 语句** - 只有当指定条件为 true 时，使用该语句来执行代码
- **if...else 语句** - 当条件为 true 时执行代码，当条件为 false 时执行其他代码
- **if...else if....else 语句**- 使用该语句来选择多个代码块之一来执行
- **switch 语句** - 使用该语句来选择多个代码块之一来执行

---

## if 语句

只有当指定条件为 true 时，该语句才会执行代码。

### 语法

``` js
if (condition)  
{  
    当条件为 true 时执行的代码  
}
```

请使用小写的 **if**。使用大写字母（IF）会生成 JavaScript 错误！

## 实例

当时间小于 20:00 时，生成问候 "Good day"：

``` js
if (time<20) { x="Good day"; }
```

x 的结果是：

`Good day`


请注意，在这个语法中，没有 ..else..。您已经告诉浏览器只有在指定条件为 true 时才执行代码。

---

## if...else 语句

请使用 if....else 语句在条件为 true 时执行代码，在条件为 false 时执行其他代码。

### 语法

``` js
if (condition)  
{  
    当条件为 true 时执行的代码  
}  
else  
{  
    当条件不为 true 时执行的代码  
}
```

  

## 实例

当时间小于 20:00 时，生成问候 "Good day"，否则生成问候 "Good evening"。

``` js
if (time<20) { x="Good day"; } else { x="Good evening"; }
```

**x** 的结果是：

`Good day`
  

---

## if...else if...else 语句

使用 if....else if...else 语句来选择多个代码块之一来执行。

### 语法

``` js
if (condition1)  
{  
    当条件 1 为 true 时执行的代码 
}  
else if (condition2)  
{  
    当条件 2 为 true 时执行的代码  
}  
else  
{  
  当条件 1 和 条件 2 都不为 true 时执行的代码  
}
```

  

## 实例

如果时间小于 10:00，则生成问候 "Good morning"，如果时间大于 10:00 小于 20:00，则生成问候 "Good day"，否则生成问候 "Good evening"：

``` js
if (time<10) {
	document.write("<b>早上好</b>");
} 
else if (time>=10 && time<20) {
	document.write("<b>今天好</b>"); 
}
else {
	document.write("<b>晚上好!</b>"); 
	}
```

**x** 的结果是：

`今天好`


#### 注意

如`if (userIP)`语句中的条件判断会在`userIP`存在且不为假值（例如 `null`、`undefined`、`""`、`0`、`NaN` 或 `false`）的情况下生效。换句话说，当`userIP`不为假值时，`if`语句中的代码块会被执行。