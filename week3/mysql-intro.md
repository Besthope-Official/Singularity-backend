# MySQL速览

## 数据库介绍

**数据库** (Database) 是一个有组织的相互关联数据的集合, 对现实世界的某些方面进行建模 (例如, 对班级中的学生或数字音乐商店的建模).

人们常常混淆“数据库”和“数据库管理系统”(例如，MySQL、Oracle、MongoDB). 数据库管理系统(DBMS) 是管理数据库的软件.

让我们用一个数字音乐商店的模型作为例子.

## 为什么要有数据库

不就是存数据吗.

flat file(平面文件), 例如 csv (comma-separated value) 文件

```plaintext
Wu-Tang Clan, 1992, USA
Notorious BIG, 1992, USA
GZE, 1990, USA
```

这样存储数据有一些问题:

* Data Integrity (数据可靠性)
  * 作者相同怎么处理?
  * 非法字符串覆盖了某一字段怎么处理?
* 实现
  * 怎么找到一条特定的纪录?
  * 两个线程同时写同一条纪录怎么办?
* 持久性
  * 存储数据的机器崩了怎么办?
  * 怎么在多台机器上存储副本以保证高可获取性?

专业的事情交给专业的处理.

## 数据库管理系统

数据库管理系统（DBMS）是一种允许应用程序在数据库中存储和分析信息的软件。

通用的DBMS旨在根据某种数据模型允许定义、创建、查询、更新和管理数据库。

数据模型是用于描述数据库中数据的一组概念。

示例：关系型（最常见）、NoSQL（键值、图形）、数组/矩阵/向量等。

模式是基于数据模型描述特定数据集合的说明。

### 早期的DBMS

由于逻辑层和物理层之间存在紧密耦合，数据库应用程序的构建和维护很困难。

* 逻辑层描述数据库具有的实体和属性
* 物理层则描述这些实体和属性的存储方式

早期，物理层是在应用程序代码中定义的，因此如果我们想要更改应用程序正在使用的物理层，就必须更改所有代码以匹配新的物理层。

### 关系模型

1970年，Ted Codd 注意到每当人们想要更改物理层时，就会重写数据库管理系统（DBMS），因此他提出了关系模型以避免这种情况。 关系模型基于关系定义了一个数据库抽象，以避免维护开销。它有三个关键要点：

* 使用简单的数据结构（关系）来存储数据库。
* 通过高级语言访问数据，DBMS找出最佳执行策略。
* 物理存储由DBMS实现决定。

关系数据模型定义了三个概念:&#x20;

* **结构**：关系及其内容的定义。这是关系具有的属性和这些属性可以保存的值。
* **完整性**：确保数据库内容满足约束条件。例如，约束条件可能是年份属性的任何值必须是一个数字。
* **操作**：如何访问和修改数据库的内容。

_关系_是一个无序集合，包含表示实体的属性关系。由于关系是无序的，DBMS可以以任何希望的方式存储它们，以实现优化。

_元组_是关系中属性值（也称为其_域_）的集合。最初，值必须是原子或标量的，但现在值也可以是列表或嵌套的数据结构。每个属性都可以是一个特殊值NULL，这意味着对于给定的元组，该属性未定义。

具有 n 个属性的关系称为 n 元关系。

## SQL数据库

关系型数据库把数据存放在表中. 列数在设计表的时候就确定了, 行数则是可变的.

类比: Excel 表格

* 主键: 表中的唯一标识符
* 外键: 引用其他表[^1]的主键
* 行之间的这种联系就是关系.

关系型数据库引擎为联结操作提供了必要的支持.

### 关系代数

回顾: 你肯定学过二元关系.

二元关系不就是一个 pair 的集合

让我们推广下...关系就是一个元组 tuple 的集合

谓词: $$P(x)$$

为什么通常用 P 开头? 因为是 predicate.

$$x \geqslant 2$$ 就是一个谓词. 当然你得约束下 $$x$$ 的类型.

Select 传入一个关系, 输出关系中满足特定条件元组的子集. (条件筛查)

众所周知谓词可以复合, 所以析取/合取也是没有问题的.

$$\sigma_{\text{pred}}(R)$$

例如

$$\sigma_{x\geqslant 2}(R)$$

对应SQL语句

```sql
SELECT * FROM R WHERE x >= 2;
```

Projection 投影

$$\pi_{A_1,A_2,\dots,A_n}(R)$$

```sql
SELECT age, gender FROM staff;
```

其实就是多个 Select 复合

Union $$R \cup S$$

```sql
(SELECT * FROM R) UNION ALL (SELECT * FROM S);
```

Intersection $$R \cap S$$

```sql
(SELECT * FROM R) INTERSECT (SELECT * FROM S);
```

Difference $$R-S$$

```sql
(SELECT * FROM R) EXCEPT (SELECT * FROM S);
```

Product 笛卡尔乘积 $$R\times S$$

```sql
(SELECT * FROM R) CROSS JOIN (SELECT * FROM S);
```

其实你也可以写成

```sql
SELECT * FROM R, S;
```

Join 会把两个关系_连接_到一起, 生成一个新的关系. 怎么连, 就看两个关系之间存在什么共同性质, 以这些性质为根据, 把相同值的元组关联起来.

比如说学生选课情况, `courses` 和 `enrollment` 都存有课程的名称, 那么

```sql
SELECT *
FROM courses, enrollment;
WHERE num = c_num
```

就可以得到类似于下面的新表

| num   | name | c\_num | students |
| ----- | ---- | ------ | -------- |
| CS186 | DB   | CS186  | 700      |
| CS186 | DB   | CS188  | 800      |
| CS188 | AI   | CS186  | 700      |
| CS188 | AI   | CS188  | 800      |
| CS189 | ML   | CS186  | 700      |
| CS189 | ML   | CS188  | 800      |

下面的其实是 Natural Join. 这个世界上还有 ⟕ ⟖ 和 ⟗

$$R \bowtie S$$

```SQL
SELECT * FROM R JOIN S USING (ATTRIBUTE1, ATTRIBUTE2...)
```

[数据库常用关系代数符号在 LaTeX 中的表示](https://billc.io/2020/04/latex-relational-algebra/)

{% hint style="info" %}
**关系代数是一个procedural language(过程化语言)**

$$\sigma_{\text{id=102}}(R\bowtie S)$$和$$R\bowtie\sigma_{\text{id=102}}(S)$$

实际上结果是一样的, 但如果 S 中 `id` 有 10亿条而 `id=102` 的数据只有一条, 显然第二个查询会快得多.

实际上计算顺序我们会交给DBMS来决定. SQL就是干这个活的. 而怎么设计这个程式, 这又是另一个故事了.
{% endhint %}

## SQL 语法

接下来我们会介绍少部分SQL的语法. 有关这部分, 它将会是下学期各位计科同学的学习内容, 所以可以提前了解下!

### 检索数据

#### 基础操作: SELECT

* 所有的 SQL 语句用 `;` 结尾.
* SQL 关键字大写, 变量名小写是好习惯.
* 结果是未排序的.

```sql
-- 查找单列
SELECT course_id FROM courses;

-- 查找多列
SELECT course_id, course_name FROM courses;

-- 检索所有列
-- * 是个通配符. 检索不必要的列通常会降低性能.
SELECT * FROM courses;

-- 检索不同的行
-- 不能部分使用 DISTINCT
SELECT DISTINCT course_id FROM courses;

-- 限制结果
-- 返回结果不多于 5 行
SELECT course_id FROM courses
LIMIT 5;

-- 完全限定名称
-- 为了避免命名空间冲突
SELECT courses.course_id FROM courses;
```

#### 排序检索: ORDER BY

SQL 语句由子句(clause)构成, 有些子句是必须的, 有些则不是.

`ORDER BY` 字句

```sql
-- 按 course_name 排序
SELECT course_name FROM courses
ORDER BY course_name;

-- 按多列排序
SELECT course_id, course_name FROM courses
ORDER BY course_id, course_name;

-- 按指定排序方向, 关键字: DESC(ascending)
-- 默认是 ASC(ascending)
-- 本例按 id 高排序
SELECT course_id, course_name FROM courses
ORDER BY course_id DESC, course_name;
```

#### 数据过滤: WHERE

* 通常数据过滤在服务器而不是在客户端解决. 想想为什么?
* `ORDER BY` 得放在 `WHERE` 后面.
* 使用圆括号分组操作符是个好习惯.
* MySQL 里的 `NOT` 和其他 SQL 语句略有不同. `NOT IN`, `NOT BETWEEN` 也支持.

| 操作符           | 说明        |
| ------------- | --------- |
| `=`           | 等于        |
| `<>`          | 不等于\[^1]  |
| `!=`          | 不等于       |
| `<`           | 小于        |
| `<=`          | 小于等于      |
| `>`           | 大于        |
| `>=`          | 大于等于      |
| `BETWEEN AND` | 在指定的两个值之间 |

```sql
-- 按指定的搜索条件条件过滤
SELECT prod_name, prod_price
FROM products
WHERE prod_price = 2.50;

-- 空值检查
SELECT prod_name FROM products
WHERE prod_price IS NULL;

-- AND, OR
-- AND 运算优先级高于 OR
SELECT prod_name, prod_price
FROM products
WHERE (vend_id = 1002 OR vend_id = 1003) AND prod_price >= 10;

-- IN
-- 像是个语法糖
-- 但比常规的 OR 要快
SELECT prod_name, prod_price
FROM products
WHERE vend_id IN (1002, 1003) AND prod_price >= 10;

-- NOT
-- 和 Python 里的一样
SELECT prod_name, prod_price
FROM products
WHERE vend_id NOT IN (1002, 1003)
ORDER BY prod_name;
```

#### 通配符和 regex

* **通配符**(wildcard)用来匹配值的一部分的特殊字符.
* **搜索模式**(search pattern)由字面值(literal), 通配符或两者组合构成的搜索条件.
* 通配符就是 WHERE 字句中具有特殊含义的字符.
* `%` 匹配不了 `NULL`.
* 不要过度使用通配符, 注意通配符位置.
* MySQL 只实现 REGEX 里的很小一块子集.
* `LIKE` 和 `REGEXP`的区别: 完全匹配整个列和部分匹配.

```sql
-- % 通配符: 任何字符出现次数
-- 'jet%' 匹配模式: 任何以词jet开头的产品
-- % 可以多次出现, 可以出现在任何位置
SELECT prod_id, prod_name
FROM products
WHERE prod_name LIKE 'jet%';

-- _ 通配符: 只匹配单个字符
SELECT prod_id, prod_name
FROM products
WHERE prod_name LIKE '_ ton anvil';

-- 使用正则表达式
-- '.000' 匹配诸如 `1000`, `2000`...
-- '1000|2000' 类似于 OR
-- '[1234]000' 几个字符之一 简写`[1-4]`
-- 剩下的 REGEX 就不介绍了
SELECT prod_name, prod_price
FROM products
WHERE prod_name REGEXP '.000'
ORDER BY prod_name;

-- 区分大小写: BINARY 关键字
SELECT prod_id, prod_name
FROM products
WHERE prod_name REGEXP 'JetPack .000';
```

#### 计算字段

* 字段(field)基本上就是列的意思.
* 拼接字段得到一个导出列(derived column)
* `AS` 关键字: 别名(alias)

```sql
-- 拼接字段
-- 示例输出:
/*
+----------------------+
| vend_title           |
+----------------------+
| Vendor 1 (USA)       |
| Vendor 2 (Germany)   |
+----------------------+
*/
SELECT Concat(vend_name, ' (', vend_country, ')') AS vend_title
FROM vendors
ORDER BY vend_name;

-- 执行算术运算
-- 支持加减乘除
SELECT prod_id,
       quantity,
       item_price,
       quantity * item_price AS expanded_price
FROM orderitems
WHERE order_num = 20005;

-- SELECT 可以拿来测试计算
SELECT 3 * 2
SELECT Trim('abc')
SELECT Now()
```

#### 数据处理函数

文本处理函数:

| 函数名             | 描述                            |
| --------------- | ----------------------------- |
| CONCAT          | 用于连接多个字符串                     |
| UPPER           | 将字符串转换为大写                     |
| LOWER           | 将字符串转换为小写                     |
| SUBSTRING       | 从字符串中提取子字符串                   |
| LENGTH          | 返回字符串的长度                      |
| TRIM            | 去除字符串首尾的空格                    |
| LTRIM           | 去除字符串开头的空格                    |
| RTRIM           | 去除字符串末尾的空格                    |
| LEFT            | 返回字符串左边指定长度的子字符串              |
| RIGHT           | 返回字符串右边指定长度的子字符串              |
| REPLACE         | 替换字符串中的指定子字符串                 |
| CHARINDEX       | 返回指定子字符串在字符串中第一次出现的位置         |
| REVERSE         | 反转字符串                         |
| FORMAT          | 将数值或日期格式化为指定的字符串表示            |
| COALESCE        | 返回第一个非空表达式的值                  |
| CASE            | 条件语句，用于在结果中根据条件返回不同的值         |
| REGEXP\_REPLACE | 使用正则表达式替换字符串中的内容              |
| TRANSLATE       | 替换字符串中的指定字符                   |
| SOUNDEX         | 返回字符串的 SOUNDEX 码，用于模糊匹配字符串的发音 |
| CONCAT\_WS      | 用指定的分隔符连接多个字符串，并去除空字符串        |
| INITCAP         | 将字符串的首字母大写                    |
| STRPOS          | 返回指定子字符串在字符串中第一次出现的位置         |
| LEFTPAD         | 在字符串的左侧填充指定字符                 |
| RIGHTPAD        | 在字符串的右侧填充指定字符                 |

日期函数:

| 函数名          | 说明              |
| ------------ | --------------- |
| AddDate      | 增加一个日期（天、周等）    |
| AddTime      | 增加一个时间（时、分等）    |
| CurDate      | 返回当前日期          |
| CurTime      | 返回当前时间          |
| Date         | 返回日期时间的日期部分     |
| DateDiff     | 计算两个日期之差        |
| Date\_Add    | 高度灵活的日期运算函数     |
| Date\_Format | 返回一个格式化的日期或时间串  |
| Day          | 返回一个日期的天数部分     |
| DayOfWeek    | 对于一个日期，返回对应的星期几 |
| Hour         | 返回一个时间的小时部分     |
| Minute       | 返回一个时间的分钟部分     |
| Month        | 返回一个日期的月份部分     |
| Now          | 返回当前日期和时间       |
| Second       | 返回一个时间的秒部分      |
| Time         | 返回一个日期时间的时间部分   |
| Year         | 返回一个日期的年份部分     |

不同的 DBMS 对函数的实现和语法有些区别. ~~这么多谁记得住.~~ 建议看[文档](https://dev.mysql.com/doc/refman/8.0/en/functions.html).

```sql
-- 使用范例:
SELECT cust_id, order_num
FROM orders
WHERE Year(order_date) = 2005 AND Month(order_date) = 9;
```

#### 汇总数据: 聚集函数

* 汇总数据严格来说**不是检索数据**.
* 聚集函数(aggregate function): 运行在行组上, 计算和返回单个值的函数.
* `COUNT(*)`: 行的值为空依然计数 `COUNT(column)`: column 有值的计数
* `MAX()` 要求**指定**列名
* `ALL` 默认 `DISTINCT` 只包含不同的值
* 可以组合聚集函数.

聚合函数:

| 函数名     | 说明       |
| ------- | -------- |
| AVG()   | 返回某列的平均值 |
| COUNT() | 返回某列的行数  |
| MAX()   | 返回某列的最大值 |
| MIN()   | 返回某列的最小值 |
| SUM()   | 返回某列值之和  |

```sql
-- 聚集函数
-- 效率更高. 想想看和下面的差在哪?
SELECT MAX(column_name) AS max_value
FROM table_name;

-- 普通的查询
SELECT column_name FROM table_name
ORDER BY column_name
DESC LIMIT 1;
```

#### 分组数据: GROUP BY 和 HAVING

* `GROUP BY`(创建分组) 和 `HAVING`(过滤分组)
* `GROUP BY` 支持多级分组. 数据将在最后规定的分组上进行汇总.
* `GROUP BY` 在 `WHERE` 后, `ORDER BY` 前
* `WHERE` 过滤行, 而 `HAVING` 过滤分组(两者很相似, 操作符通用)
* 可以这么想: `WHERE` 在分组前过滤, `HAVING` 在分组后过滤.
* 而且 `HAVING` 可以和 `WHERE` 混着用

|      | ORDER BY         | GROUP BY                  |
| ---- | ---------------- | ------------------------- |
| 输出   | 按指定列排序的结果        | 分组后的结果，但输出可能不按照分组的顺序      |
| 列的使用 | 任意列，甚至非选择的列也可以使用 | 只能使用选择列或表达式列，必须使用每个选择列表达式 |
| 必要性  | 不一定需要            | 如果与聚集函数一起使用列（或表达式），则必须使用  |

```sql
-- 创建分组
-- GROUP BY 按 vend_id 排序并分组数据
/*
| vend_id | num_prods |
|---------|-----------|
|   1     |    5      |
|   2     |    3      |
|   3     |    7      |
|   4     |    2      |
*/
SELECT vend_id, COUNT(*) AS num_prods
FROM products
GROUP BY vend_id;

-- WITH ROLLUP: 添加汇总行
/*
| vend_id | num_prods |
|---------|-----------|
|   1     |    5      |
|   2     |    3      |
|   3     |    7      |
|   4     |    2      |
|  NULL   |    17     |
*/
SELECT vend_id, COUNT(*) AS num_prods
FROM products
GROUP BY vend_id WITH ROLLUP;

-- GROUP BY 多级分组
/*
| customer_id | order_date | order_amount |
|-------------|------------|--------------|
|     1001    | 2023-01-01 |     50.00    |
|     1002    | 2023-01-01 |     75.00    |
|     1001    | 2023-01-02 |     30.00    |
|     1003    | 2023-01-02 |     20.00    |
|     1002    | 2023-01-03 |     45.00    |
|     1001    | 2023-01-03 |     60.00    |
|     1003    | 2023-01-03 |     35.00    |
*/
SELECT customer_id, order_date, SUM(order_amount) AS total_amount
FROM orders
GROUP BY customer_id, order_date;

-- 过滤分组
/*
| cust_id | orders |
|---------|--------|
|   1001  |   3    |
|   1003  |   2    |
|   1002  |   2    |
*/
SELECT cust_id, COUNT(*) AS orders
FROM orders
GROUP BY cust_id
HAVING COUNT(*) >= 2;
```

在SQL查询中, `SELECT` 子句的一般顺序如下:

* `SELECT`：指定要检索的列或表达式。
* `FROM`：指定要从中检索数据的表或视图。
* `WHERE`：应用条件来筛选满足特定条件的行。
* `GROUP BY`：根据指定的列或表达式对结果进行分组。
* `HAVING`：筛选分组后的结果集。
* `ORDER BY`：指定结果集的排序顺序。
* `LIMIT` / `OFFSET`（在某些数据库中可用）：限制返回的行数或指定结果集的偏移量。

#### 使用子查询

* 子查询(subquery): 查找一遍又一遍
* 子查询总是**从内向外**处理.
* 相关子查询(correlated subquery): 涉及外部查询的子查询.
* 逐渐增加子查询来建立查询. 也就是先分成多部查询, 确定正常后再写进子查询.

```sql
-- 用子查询
-- 括号里的先算
SELECT cust_id
FROM orders
WHERE order_num IN (SELECT order_num
                    FROM orderitems
                    WHERE prod_id = 'TNT2');

-- 其实你可以拆成两个查询. 最好先写成两步, 然后合并为子查询.

-- 作为计算字段使用子查询
/*
| cust_name | cust_state | orders |
|-----------|------------|--------|
|   John    |     NY     |   3    |
|   Mary    |     CA     |   2    |
|   David   |     TX     |   0    |
|   Lisa    |     NY     |   1    |
*/
SELECT cust_name,
       cust_state,
       (SELECT COUNT(*) FROM orders 
       WHERE orders.cust_id = customers.cust_id) AS orders
FROM customers
ORDER BY cust_name
```

[^1]: 也可以是本表.
