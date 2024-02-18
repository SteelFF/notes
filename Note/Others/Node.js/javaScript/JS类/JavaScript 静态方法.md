静态方法是使用 static 关键字修饰的方法，又叫类方法，属于类的，但不属于对象，在实例化对象之前可以通过 类名.方法名 调用静态方法。

静态方法不能在对象上调用，只能在类中调用。
<font color="green"><strong>(与JAVA不同，对象中不能调用静态方法，只能在类模板上调用)</strong></font>
<font color="red"><strong>(但是JAVA不推荐使用对象调用静态函数，因为在编译时会把对象转换为模板，通过模板调用静态函数，增加无用的编译)</strong></font>
## 实例

``` js
class Runoob { 
	constructor(name) { 
		this.name = name; 
	} 
	static hello() { 
		return "Hello!!"; 
	} 
} 
let noob = new Runoob("菜鸟教程"); // 可以在类中调用 'hello()' 方法 
document.getElementById("demo").innerHTML = Runoob.hello(); 
// 不能通过实例化后的对象调用静态方法 
// document.getElementById("demo").innerHTML = noob.hello(); 
// 以上代码会报错
```

实例对象调用静态方法会报错：

![](https://www.runoob.com/wp-content/uploads/2022/01/68226CCF-F662-479F-B812-5D358B530F2E.jpg)

如果你想在对象 noob 中使用静态方法，可以作为一个参数传递给它：

## 实例

``` js
class Runoob { 
	constructor(name) { 
		this.name = name; 
	} 
	static hello(x) { 
		return "Hello " + x.name; 
	} 
} 
let noob = new Runoob("菜鸟教程"); 
document.getElementById("demo").innerHTML = Runoob.hello(noob);
```