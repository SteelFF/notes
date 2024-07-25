在 Pandas 中，数据主要通过两种数据结构来存储和操作：Series 和 DataFrame。

1. **Series**：
    
    - Series 是一维标记数组，类似于 Python 中的列表或数组。
    - 每个 Series 对象都有一个索引（index），用于标识每个数据点。
    - 可以将 Series 视为由数据和索引标签组成的字典。
    - 例如，一个简单的 Series 可以是一列数据，如 `[1, 2, 3, 4, 5]`，每个元素都有一个对应的索引。
2. **DataFrame**：
    
    - DataFrame 是一个二维的、带有行标签和列标签的数据结构，类似于电子表格或数据库表。
    - DataFrame 可以看作是由多个 Series 对象组成的字典。
    - 每列可以是不同的数据类型（整数、浮点数、字符串等）。
    - DataFrame 提供了灵活的方法来处理数据，包括选择、过滤、合并、重塑和聚合数据。
    - 例如，一个简单的 DataFrame 可以包含多列数据，如下所示：

|Name|Age|Gender|
|---|---|---|---|
|0|Alice|25|Female|
|1|Bob|30|Male|
|2|Charlie|35|Male|

Pandas 的数据结构是基于 NumPy 数组构建的，因此 Pandas 提供了高效的数据操作和计算功能。
（** <b style='color:red'>NumPy 数组相对于普通 Python 列表在处理数值计算和大规模数据集时具有更高的效率和性能。如果需要处理大量数值数据并执行复杂的数学运算，通常推荐使用 NumPy 数组</b>）
