# schema约束的代码理解

## 命名空间

命名空间作用：当配置多个xsd文件时候，为了区分不同文件中的相同标签而存在
命名空间使用URL标识，但不访问具体网站，只是起到区分标识作用

命名空间声明方式：

* xmlns= "url" 隐式声明名称空间，即省略掉冒号和名称空间前缀。
* xmlns:name="url" name是名称空间前缀


schema也属于xml,所有schema与xml文件中都存在命名空间的使用
复杂的用法是默认命名空间和自定义的命名空间都使用，所以将情况细分更容易理解。

xml只有一个根标签，默认将命名空间都声明在这个根标签中。
命名空间主要是给别的文件看。

首先，要了解命名空间的作用范围。命名空间是针对标签的

1. 自定义的命名空间
    <schema xmlns:xs="http://www.w3.org/2001/XMLSchema"></schema>
    假如在只是在schema标签中进行了声明， 而没有给schema添加 xs:schema进行标识，  那这个xs命名空间并没有起作用。xmlns:xs属于自定义的命名空间，它给所有进行xs:label 的标签进行一个标识作用。对于自定义的命名空间，必须一一进行`xs:`的标签标识，才能使得它的声明所在的标签和该标签的子标签，成功添加进自定义的命名空间。
2. 默认命名空间
    <schema xmlns="http://www.w3.org/2001/XMLSchema"></schema>
    默认命名空间作用能够未添加命名空间的本标签和该标签的子标签。
    但是如果想对已经添加过命名空间的标签的子标签进行另外一种命名空间的标记。那么就出现了声明多个命名空间的情况。（多个自定义的，或者一个默认加多个自定义标签的情况）
3. 多种命名空间嵌套

    ```xsd
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
        targetNamespace="http://www.w3school.com.cn"
        xmlns="http://www.w3school.com.cn"
        elementFormDefault="qualified">

    ...
    ...
    </xs:schema>
    ```

    能够将一种命名空间的标签内添加另外的命名空间的标签
    

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">



## XML/XSD命名空间解析

前面提到 xsi:schemaLocation属性，其中用到了xsd的目标命名空间(targetNamespace)属性，也就是targetNamespace="http://www.library.com"行。它的作用是把我们自己写的xsd元素及属性保存到targetNamespace所声明的空间里，也就是xsi:schemaLocation属性所要引用的地址，这样就可以完成校验功能，有点像我们程序中package概念。
如果没有定义targetNamespace属性，就说明此xsd没有目标命名空间，那么在xml引用时使用xsi:schemaLocation即可。
