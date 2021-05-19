# SQL DQL查询语句专项

## SELECT基础查询

顺序:FROM-WHERE（指定记录行）-GROUP BY(分组)- HAVING（选取特定分组）-SELECT

* SELECT语句基础
  * 列的查询(书写具体列，以书写顺序显示)
  * 查询出表的所有列 * （无法设定列的显示顺序）
  * 为列设置别名 （别名使用中文时候用**双引号**括起来）
  * 常数的查询 （设置所有查询结果有相同内容的常数列）
  * 从结果中删除重复行 distinct （distinct只能用于第一个列名之前）
  * 根据**WHERE**语句来选择记录
* 算数运算符和比较运算符
  * 算术运算符 `+ - * /`（对查询结果进行算术操作得到想要的结果，譬如翻倍）
  * 需要注意NULL （包含NULL的计算结果也是NULL，对于含NULL的比较运算结果查询结果不确定，一般**设置NOT NULL约束**）
  * 比较运算符 `= <> >= > <= <`
  * 对字符串使用不等号时的注意事项
  * 不能对NULL使用比较运算符 `IS NULL`
* 逻辑运算符
  * NOT运算符 -- 否定某一条件，不能滥用
  * AND运算符和OR运算符 -- 多个查询条件组合
  * 使用括号强化处理
  * 逻辑运算符和真值
  * 含有NULL时的真值
* 聚合查询--所谓聚合就是将多行聚合为一行
  * 计算表中数据的行数 `COUNT(*)`
  * 计算NULL之外的数据的行数 `COUNT(列名) -- 排除列中的NULL`
  * 计算合计值 `SUM(列名) -- 所有针对某列的聚合函数，默认把NULL排除再外`
  * 计算平均值 `AVG()`
  * 计算最大值和最小值 `MAX() MIN()`
  * 使用聚合函数删除重复值（DISTINCT） `COUNT( DISTINCT <列名>)`
* 分组
  * GROUP BY 子句
  * 聚合键中包含NULL情况 （将NULL单独分为一组）
  * 使用WHERE子句时GROUP BY 的执行结果
  * 与聚合函数和GROUP BY 子句有关的常见错误
    * 使用COUNT（）聚合函数时候，SELECT子句中只能存在3种元素:常数、聚合函数、GROUP BY子句中指定的列名（也就是聚合键）
    * GROUP BY子句中不得使用别名，由于SELECT 子句声明别名，但是却再GROUP后执行
    * GROUP BY的结果顺序无序
    * WHERE中不得使用聚合函数，如COUNT(*)=2。只有SELECT和HAVING子句中可以使用聚合函数。
* 为聚合结果指定条件 HAVING --  **选取特定的分组**
* 对查询结果进行排序 OREDR BY
  * ASC升序 DESC降序
  * OREDR BY子句可以指定**多个排序键**
  * 排序键中包含NULL时，会在开头或者末尾进行汇总
  * OREDR BY子句中可以使用SELECT子句中定义的列的别名
  * OREDR BY子句中可以使用SELECT子句中未出现的列或者聚合函数
  * OREDR BY子句中不能使用列的编号


```SQL
SELECT  <列名> （别名） -- 指定列，并别名
SELECT  * -- 所有列
SELECT "中文常数（列的内容值）" AS <列名>
SELECT DISTINCT <列名> -- 对结果中指定列（可以是多列）删除重复行，多行NULL会合并为一行NULL
SELECT  算术运算( <列名> )
-- -----------------------
FROM <表名>
FROM <表名>,<表名>,<表名>..... -- 多表查询，笛卡儿积

-- -----------------------
WHERE <条件表达式> -- 指定行所对应的条件，即聚合键所对应的条件

-- -----------------------
GROUP BY  <列名>, <列名>, <列名>...

-- -----------------------
HAVING <条件表达式> -- 指定组所对应的条件

-- -----------------------
ORDER BY

```