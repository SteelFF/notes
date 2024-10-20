数组中可以存放多个值。Bash Shell 只支持一维数组（不支持多维数组），初始化时不需要定义数组大小（与 PHP 类似）。

与大部分编程语言类似，数组元素的下标由 0 开始。

Shell 数组用括号来表示，元素用"空格"符号分割开，语法格式如下：

array_name=(value1 value2 ... valuen)

### 实例

创建一个简单的数组 **my_array**：

## 实例

``` shell
#!/bin/bash  
# author:菜鸟教程  
# url:www.runoob.com  
  
my_array=(A B "C" D)  
```

我们也可以使用数字下标来定义数组:

## 实例

``` shell
array_name[0]=value0  
array_name[1]=value1  
array_name[2]=value2  
```

### 读取数组

读取数组元素值的一般格式是：

``` shell
${array_name[index]}
```

以下实例通过数字索引读取数组元素：

## 实例

``` shell
#!/bin/bash  
# author:菜鸟教程  
# url:www.runoob.com  
  
my_array=(A B "C" D)  
  
echo "第一个元素为: ${my_array[0]}"  
echo "第二个元素为: ${my_array[1]}"  
echo "第三个元素为: ${my_array[2]}"  
echo "第四个元素为: ${my_array[3]}"  
```
执行脚本，输出结果如下所示：

``` shell
$ chmod +x test.sh 
$ ./test.sh
第一个元素为: A
第二个元素为: B
第三个元素为: C
第四个元素为: D
```
### 关联数组

Bash 支持关联数组，可以使用任意的字符串、或者整数作为下标来访问数组元素。

关联数组使用 [declare](https://www.runoob.com/linux/linux-comm-declare.html) 命令来声明，语法格式如下：

`declare -A array_name`

**-A** 选项就是用于声明一个关联数组。

关联数组的键是唯一的。

以下实例我们创建一个关联数组 **site**，并创建不同的键值：

## 实例

``` shell
declare -A site=(["google"]="www.google.com" ["runoob"]="www.runoob.com" ["taobao"]="www.taobao.com")  
```

我们也可以先声明一个关联数组，然后再设置键和值：

## 实例

``` shell
declare -A site  
site["google"]="www.google.com"  
site["runoob"]="www.runoob.com"  
site["taobao"]="www.taobao.com"  
```
也可以在定义的同时赋值：

访问关联数组元素可以使用指定的键，格式如下：

`array_name["index"]`

以下实例我们通过键来访问关联数组的元素：

## 实例

``` shell
declare -A site  
site["google"]="www.google.com"  
site["runoob"]="www.runoob.com"  
site["taobao"]="www.taobao.com"  
  
echo ${site["runoob"]}  
```

执行脚本，输出结果如下所示：

`www.runoob.com`

### 获取数组中的所有元素

使用 @ 或 * 可以获取数组中的所有元素，例如：

## 实例

``` seh
#!/bin/bash  
# author:菜鸟教程  
# url:www.runoob.com  
  
my_array[0]=A  
my_array[1]=B  
my_array[2]=C  
my_array[3]=D  
  
echo "数组的元素为: ${my_array[*]}"  
echo "数组的元素为: ${my_array[@]}"  
```

执行脚本，输出结果如下所示：

``` shell
$ chmod +x test.sh 
$ ./test.sh
数组的元素为: A B C D
数组的元素为: A B C D
```

## 实例

``` shell
declare -A site  
site["google"]="www.google.com"  
site["runoob"]="www.runoob.com"  
site["taobao"]="www.taobao.com"  
  
echo "数组的元素为: ${site[*]}"  
echo "数组的元素为: ${site[@]}"  
```

执行脚本，输出结果如下所示：

``` shell
$ chmod +x test.sh 
$ ./test.sh
数组的元素为: www.google.com www.runoob.com www.taobao.com
数组的元素为: www.google.com www.runoob.com www.taobao.com
```

在数组前加一个感叹号 ! 可以获取数组的所有键，例如：

## 实例

``` shell
declare -A site  
site["google"]="www.google.com"  
site["runoob"]="www.runoob.com"  
site["taobao"]="www.taobao.com"  
  
echo "数组的键为: ${!site[*]}"  
echo "数组的键为: ${!site[@]}"  
```
执行脚本，输出结果如下所示：

``` shell
数组的键为: google runoob taobao
数组的键为: google runoob taobao
```

### 获取数组的长度

获取数组长度的方法与获取字符串长度的方法相同，例如：

## 实例

``` shell
#!/bin/bash  
# author:菜鸟教程  
# url:www.runoob.com  
  
my_array[0]=A  
my_array[1]=B  
my_array[2]=C  
my_array[3]=D  
  
echo "数组元素个数为: ${#my_array[*]}"  
echo "数组元素个数为: ${#my_array[@]}"  
```

执行脚本，输出结果如下所示：

``` shell
$ chmod +x test.sh 
$ ./test.sh
数组元素个数为: 4
数组元素个数为: 4
```