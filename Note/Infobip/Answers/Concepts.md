## 模板语言

为了促进值的提取和内容的动态生成，我们  在 Answers 平台中引入了一种模板语言 – [Liquid ](https://shopify.github.io/liquid/)。

Liquid 是一种具有简单标记的模板语言，以便用户可以对其进行编辑。（与Django当中使用的html模板语言类似）

在 Answers 元素中可输入文本和变量的所有字段中使用模板语言。示例：文本元素和列表元素中的文本字段。

Liquid 代码在模板中使用 **对象、标签**和 **过滤器** ，然后呈现模板并动态显示内容。 **模板** 是您创建的包含模板语言元素的任何消息。

### 对象

对象用于在页面上显示内容。对象和变量名称由双大括号表示：{{ 和 }}。

**对象类型**：字符串、数字、布尔值、Nil 和数组。

![对话框中液体对象的示例](https://www.infobip.com/docs/images/uploads/articles/answers-liquid-object-example.png)

有关[[Liquid Object](https://shopify.github.io/liquid/basics/introduction/#objects)的更多信息。

### 标签

标签 为模板创建逻辑和控制流。它们由大括号和百分号表示：{% tag %}。标签不会呈现为任何可见文本；您可以使用它们在后台分配变量并创建条件和循环。

  
有五种不同类型的标签：

• _评论标签_——使用{% comment %}和{% endcomment %}标签将它们之间的任何内容变成评论。

• _控制流标签_——使用编程逻辑更改显示的信息：if、unless、elseif/else、case/when。

• _迭代标签_——for、else、break、continue，以及用于参数（limit、offset、range、reversed）、循环和tablerows（cols、limit、offset、range）的附加标签，以重复运行代码块。

• _原始标签_——用于暂时禁用标签处理，{% raw %}和{% endraw %}。

• _变量标签_——assign、capture、increment 和decrement 用于在模板语言中创建新变量。

|基本运算符（在标签中使用）：|   |
|---|---|
|`==`|等于|
|`!=`|不相等|
|`>`|比...更棒|
|`<`|少于|
|`>=`|大于或等于|
|`<=`|小于或等于|
|`or`|逻辑或|
|`and`|逻辑与|

  
了解有关[Liquid 标签](https://shopify.github.io/liquid/basics/introduction/#tags)的更多信息。

### 过滤器

过滤器更改模板语言对象的输出。它们在对象内使用并用 | 分隔。（管道）字符。您可以对一个输出使用多个过滤器，从左到右应用它们（每个过滤器由 | 字符分隔）。[检查所有可用过滤器](https://shopify.github.io/liquid/basics/introduction/#filters)的列表。

如果您想深入了解如何在 Answers 中使用模板语言的示例，请查看[HOW](https://www.infobip.com/docs/answers/how-to#templating-language)部分。

## 常用表达

[Regex](https://www.infobip.com/glossary/regex)（正则表达式）在您想要检查、匹配或验证不同输入的情况下非常有用，例如检查字符串是否包含数字，或验证输入是否为电话号码。

在 Attribute 和 Code 元素中使用正则表达式。

以下是正则表达式中最常用的操作的列表：

|Character classes| |
|---|---|
|`.`|any character except newline|
|`\w`  `\d ` `\s`|    word, digit, whitespace|
|`\W`  `\D`  `\S`  |not word, digit, whitespace|
|`[abc]`    |   any of a, b, or c|
|`[^abc]`   |  not a, b, or c|
|`[a-g] `   |   character between a & g|


|Anchors| |
|---|---|
|`^abc$`   | start / end of the string|
|`\b `     |     word boundary|


|Escaped characters| |
|---|---|
|`\. ` `\*`  `\\ ` |  escaped special characters|


|Groups & Lookaround| |
|---|---|
|`(abc) `   |  capture group|
|`\1 `     |      backreference to group #1|
|`(?:abc) `  |non-capturing group|
|`(?=abc)` | positive lookahead|
|`(?!abc)`  | negative lookahead|


|Quantifiers & Alternation| |
|---|---|
|`a* a+ a?`  | 0 or more, 1 or more, 0 or 1|
|`a{5} a{2,}`  |  exactly five, two or more|
|`a{1,3} `     |    between one & three|
|`a+? a{2,}?`  |  match as few as possible|
| ab\|cd     |   match ab or cd|

有关如何在聊天机器人中使用正则表达式的信息，请参阅[HOW](https://www.infobip.com/docs/answers/how-to#regular-expressions)部分。