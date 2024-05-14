## 创建表
create table 表名( 属性名 数据类型[完整性约束条件],  
           属性名 数据类型[完整性约束条件],

完整性约束条件  
primary key    标识该属性为该表的主键，可以唯一的标识对应的元组  

foreign key references `Table_name(Attribute_name)` / references  `Table_name(Attribute_name)`  标识该属性为该表的外键，是与之联系的某表的主键  

not null       标识该属性不能为空  

unique         标识该属性的值是唯一的  

auto_increment   标识该属性的值自动增加  

default        为该属性设置默认值

check (conditions)   自定义check条件


## 创建Trigger

