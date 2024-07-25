
#### 读取数据

``` python
import pandas as pd

df1 = pd.read_csv(file_path)
df2 = pd.read_excel(file_path,sheet_name='SheetXX')
```

#### 写入数据

``` python
import pandas as pd

df1.to_csv(file_name,index = false)
df1.to_excel(file_name,index = false)
```

#### 按列筛选数据

``` Python
import pandas as pd

df = df.loc[:,['col_1','col_2',....]]
```

#### Filter按条件筛选行数据

``` Python
import pandas as pd

df = df[(df[col_1] == xxxx) | df[col_2].isnull() & df[col_2].notnull()]
```

#### 创建新列

``` Python
import pandas as pd

## 单一赋值
df[new_col1] = true

## 按col赋值
df[new_col2] = df[col_1].str.split(' ',expand=True)[1]

## 按col将值赋值给多个new col
df[[new_col3, new_col4]] = df[col_1].str.split(' ',expand=True)

```


#### 添加行数据

``` Python
import pandas as pd

data = {'A': [1, 2, 3], 'B': [4, 5, 6]}
df = pd.DataFrame(data)

# 要添加的新行数据
new_data = {'A': 7, 'B': 8}

# 使用 append() 方法将新行添加到 DataFrame
df = df.append(new_data, ignore_index=True)

print(df)

new_data2 = {'A': 7, 'B': 8} 
# 通过 loc[] 方法直接赋值添加新行数据 

df.loc[len(df)] = new_data2 
print(df)

```


#### Group By 计算数据作为新列
``` Python
mport pandas as pd

data = {'A': [1, 2, 3], 'B': [4, 5, 6]}
df = pd.DataFrame(data)

result = df.groupby('A').count().reset_index()

print(result)

```
