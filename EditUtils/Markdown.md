# Markdown知识 （方便自己翻阅）

---

## Markdown概念

Markdown  轻量级标记语言  使用易读易写的纯文本格式编写文档，然后转换成有效的XHTML（或者HTML）文档。这种语言吸收了很多在电子邮件中已有的纯文本标记的特性。

Markdown的轻量化、易读易写特性，并且对于图片，图表、数学式都有支持

支持网站： 如GitHub、Reddit、Diaspora、Stack Exchange、OpenStreetMap 、SourceForge、简书等，甚至还能被使用来撰写电子书。
世界上最流行的博客平台WordPress和大型CMS如Joomla、Drupal都能很好的支持Markdown。完全采用Markdown编辑器的博客平台有Ghost和Typecho等。

Markdown可以快速转化为演讲PPT、Word产品文档甚至是用非常少量的代码完成最小可用原型。
易于阅读、易于撰写的纯文字格式，并选择性的转换成有效的XHTML（或是HTML）

## Markdown基本语法

### 大总结

>https://markdown.com.cn/basic-syntax/  基本语法官方教程

``` hson
    标题    1、[Setext]：h1         2、h2
                        =            -
            2、[atx]   ：h1#  h2## h3### h4#### h5##### h6#######

    段落    创建段落，请使用空白行将一行或多行文本进行分隔。 不要用空格（spaces）或制表符（ tabs）缩进段落。  

    换行    1、一行的末尾添加--两个或多个空格--，然后按回车键,即可创建一个换行(<br>)。
            2、HTML 的 <br> 标签。

    强调    1、粗体（Bold）   ：**粗体**（中间不用带空格）
            2、斜体（Italic） ：*斜体*（中间不用带空格）
            3、粗体和斜体     ：***粗体和斜体***（中间不用带空格）

    引用    >  创建单行或多行引用
            >> 嵌套块引用
            > #/*/- 带有其它元素的块引用

    列表    1、有序列表  ：1. content(中间有空格，只需要第一行得序号为数字1)
            2、无序列表  ：-->破折号 (-)、星号 (*) 或加号 (+) 。缩进一个或多个列表项可创建嵌套列表。
                          -->保留列表连续性,在列表中添加另一种元素(段落，引用块，代码块，图片，列表)，缩进四个空格或一个制表符
    
    代码    1、短对象               :`单词或短语`( `` ：转义反引号)
            2、长对象（围栏代码块）  :1、行缩进四个空格或一个制表符来创建代码块
                                    2、受保护的代码块    ```/~~~  括住代码（加语言类型进行高亮）

    分割线  一行上使用三个或多个星号 (***)、破折号 (---) 或下划线 (___) ，不能包含其他内容。

    链接    1、markdown语法    ： [超链接显示名](超链接地址 "超链接title")
            2、HTML代码        ： <a href="超链接地址" title="超链接title">超链接显示名</a>
            3、链接加title     :     [Markdown语法](https://markdown.com.cn "鼠标悬停title")。
            4、网址和Email地址 ：<https://markdown.com.cn>   <fake@example.com>
            5、带格式化的链接  ：强调链接   **[EFF](https://eff.org)**
                                链接代码化 [`code`](#code)
            6、引用类型链接    ：[链接的文本] [指向其他位置的标签]      [标签]链接的URL(链接的可选标题)

    图片    1、Markdown语法代码：![图片alt](图片链接 "图片title")  
            2、HTML代码：<img src="图片链接" alt="图片alt" title="图片title">
            3、[ ![图片alt](图片链接 "图片title") ](链接https://...)
            
    转义字符    \转义   对象（\   `  * _ { }  [ ] ( )  # + - .  ! | ）
                特殊字符自动转义    < 和 & 。 < 符号用于起始标签，& 符号则用于标记 HTML 实体     使用方法：  &lt; 和 &amp;
    
    HTML 标签   行级內联标签    <span>、<cite>、<del> 
                区块标签      <div>、<table>、<pre>、<p>
                注意：块级元素前后使用空行分隔。不要使用制表符（tabs）或空格（spaces）对 HTML 标签做缩进，否则将影响格式。

    表格        | Syntax      | Description | Test Text     |
                | :---        |    :----:   |          ---: |
                | Header      | Title       | Here's this   |
                | Paragraph   | Text        | And more      |

    脚注        [^lable]

    链接到标题ID ### My Great Heading {#custom-id}

    定义列表   First Term
               : This is the definition of the first term.

    删除线      ~content~

    任务列表语法     - [x] Write the press release
                    - [ ] Update the website
    
     Emoji 表情 :表情符号简码：
                简码库：https://gist.github.com/rxaviers/7360908

     自动网址链接：https://.....
                  禁用`https://.....

```

## Markdown技巧

### 1.表格内换行

用html的代码，插入代码`<br>`

```java
        |姓名|爱好|
        |-|-|
        |张三|足球<br>篮球
        |李四|羽毛球<br>乒乓球
```

|姓名|爱好|
|-|-|
|张三|足球 <br> 篮球
|李四|羽毛球 <br> 乒乓球

### 2.缩进问题

缩进的“容忍度”:行首如果有大于等于4个Space，本行被视为代码段落。代码段落选然后会原样输出所有符号。

### 3.多行换行 `<br/>`

### 4

## Markdown注意事项

1、两种标题格式不能混用，且一个文档中只能有一个最高级标题
2、空行问题：每一类样式之间都需要空行，且严格为一行 Headings should be surrounded by blank lines
3、列表换行：如果一行被标记为**列表项**。那么不论与**上一行**之间有没有空行，都被视为**两个段落**。但是如果**后边**没有空行且下一行**不是列表项**，则本行仍可与后一行**属于同一段落**。Tab一次取消列表的控制，Tab第二次自动变为代码段。

## 常见错误

https://www.cnblogs.com/linbudu/p/11367763.html