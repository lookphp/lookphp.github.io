---
layout: post
title:  "Review MySQL"
date:   2015-09-13 18:46:32
categories: jekyll update
---

使用空格    在处理SQL语句时，其中所有空格都被忽略。SQL语句可以在一行上给出，也可以分成许多行。但是多少SQL开发人员认为将SQL语句分成多行更容易阅读和调试。

为了使用SELECT检索表数据，必须至少给出两条信息————想选择什么，以及从什么地方选择。

检索单列：
    SELECT prod_name FROM products;

检索多个列：
    SELECT prod_id , prod_name , prod_price FROM products;
//当心逗号  在选择多个列时，一定要在列名之前加上逗号，但最后一个列名后不能加。如果在最后一个列名后加了逗号，将出现错误。

检索所有列：
    SELECT * FROM products;     //给定一个通配符（*），则返回表中所有列。
//当心通配符  一般除非你确实需要表中的每个列，否则最好别使用*通配符，因为检索不需要的列通常会降低检索和应用程序的性能。

检索不同的行：
使用关键字DISTINCT关键字，此关键字指示MySQL只能返回不同的值。
    SELECT DISTINCT vend_id FROM products;
//不能部分使用DISTINCT关键字，它应用于所有列而不仅仅是前置它的列。如果给出SELECT DISTINCT vend_id prod_id ,除非指定的两个列都不同，否则所有行都将被检索出来。

限制结果：
    SELECT prod_name FROM products LIMIT 5;

    SELECT prod_name FROM products LIMIT 5,5;
    //LIMIT 5,5 指示MySQL返回从行5开始的5行，第一个数为开始位置，第二个数为要检索的行数。
//结论：带一个值的LIMIT总是从第一行开始，给出的数为返回的行数。带两个值的LIMIT可以指定从行号为第一个值的位置开始。
//行0：检索出来的第一行为行0而不是行1。因此，limit 1 ，1 将检索出第二行而不是第一行。

使用完全限定的表名和列名：
    SELECT products.prod_name FROM products;
//这条SQL语句在功能上等于前边最开始使用的那一条语句，但是这里指定了一个完全限定的列名。

    表名也是可以完全限定的，如下所示：
    SELECT products.prod_name FROM crashcourse.products;

第五章：排序检索数据

//关系数据库设计理论认为，如果不明确规定排序顺序，则不应该假定检索出的数据的顺序有意义。

排序数据（ORDER BY）：
    SELECT prod_name FROM products ORDER BY prod_name;

按多个列排序：
    SELECT prod_id ，prod_price , prod_name FROM products ORDER BY prod_price , prod_name;

指定排序方向(ASC DESC)：
    SELECT prod_id , prod_price ,prod_name FROM products ORDER BY prod_price DESC;

    多个列排序，先按产品，再按产品名排序
    SELECT prod_id , prod_price , prod_name FROM products ORDER BY prod_price DESC , prod_name;
    //DESC关键字只应用到直接位于其前面的列名。在上例中，只对prod_price列指定DESC,对prod_name列不指定。

    使用ORDER BY 和 LIMIT 的组合，能够找出一个列中最高或最低的值。
    SELECT prod_price 
    FROM products 
    ORDER BY prod_price 
    DESC LIMIT 1;
    
    ORDER BY子句的位置：在给出ORDER BY 子句是，应该保证它位于FROM子句之后。如果使用LIMIT ,它必须位于ORDER BY 之后，使用子句的次序不对将产生错误消息。

第六章：过滤数据

使用WHERE子句:
    SELECT prod_name , prod_price 
    FROM products
    WHERE prod_price = 2.30;

    WHERE子句的位置 在同时使用ORDER BY 和 WHERE 子句是，应该让ORDER BY 位于 WHERE 之后，否则将会产生错误。

WHERE子句操作符
    =   <>  !=  <   <=  >   >=  BETWEEN

    检查单个值
    SELECT prod_name , prod_price
    FROM products
    WHERE prod_name = 'fuses';
    //MYSQL在执行匹配时，默认不区分大小写，所以fuses和Fuses匹配。

    检查多个值
    SELECT prod_name , prod_price
    FROM products
    WHERE prod_price < 10;
    //WHERE prod_price <= 10;

    不匹配检查
    SELECT vend_id , prod_name
    FROM products
    WHERE vend_id <> 1003;
    //WHERE vend_id != 1003;

    范围值检查
    SELECT prod_name,prod_price
    FROM products
    WHERE prod_price BETWEEN 5 AND 10;

    空值检查
    在创建表时，表设计人员可以指定其中的列是否可以不包含值。
    在一个列不包含值时，称其为包含空值NULL.

    NULL 无值（no value）它与字段包含0，空字符串或仅仅包含空格不同。

    SELECT 语句有一个特殊的WHERE子句，可以用来检查具有NULL值的列。这个WHERE子句就是IS NULL子句。
    SELECT prod_name
    FROM products
    WHERE prod_price IS NULL;

第七章：数据过滤

    mysql 允许给出多个where子句。这些子句可以两种方式使用：以AND子句的方式或OR子句的方式使用。

AND操作符
    SELECT prod_id , prod_price , prod_name 
    FROM products
    WHERE vend_id = 1003 AND prod_price <= 10;

    此SQL语句检索由供应商1003制造且价格小于等于10美元的所有产品的名称和价格。
    
    我们可以添加多个过滤条件，每添加一条就要使用一个AND.

OR操作符
    SELECT prod_name , prod_price 
    FROM products
    WHERE vend_id = 1002 OR vend_id = 1001;

计算次序
    在处理OR操作符前，优先处理AND操作符。
    解决方法：使用圆括号明确地分组相应的操作符。

IN操作符
    IN操作符用来指定条件范围，范围中的每个条件都可以进行匹配。IN取合法值的有逗号分隔的清单，全都括在圆括号中。
    SELECT prod_name , prod_price 
    FROM products
    WHERE vend_id IN (1002,1003)
    ORDER BY prod_name;

    IN操作符完成了与OR相同的功能。如下：
    SELECT prod_name,prod_price
    FROM products
    WHERE vend_id = 1002 OR vend_id = 1003
    ORDER BY prod_name;

使用IN操作符的优点：
在使用长的合法选项清单时，IN操作符的语法更清楚且更直观；
在使用IN时，计算的次序更容易管理（因为使用的操作符更少）；
IN操作符一般比OR操作符清单执行更快；
IN的最大优点是可以包含其他SELECT语句，使得能够更动态的建立WHERE子句。

NOT操作符
    WHERE 子句中的NOT 操作符有且只有一个功能，那就是否定它之后所跟的任何条件
    SELECT prod_name,prod_price
    FROM products
    WHERE vend_id NOT IN (1002,1003)
    ORDER BY prod_name;

MYSQL支持使用NOT对IN,BETWEEN和EXISTS子句取反， 这与多数其他DBMS允许使用NOT对各种条件取反有很大的差别；

第八章：用通配符进行过滤

LIKE操作符
为在搜索子句中使用通配符，必须使用LIKE操作符。LIKE指示MySQL,后跟的搜索模式 利用通配符匹配 而不是直接相等匹配进行比较。
谓词    操作符何时不是操作符？答案是在他作为谓词时。从技术上说，LIKE是谓词而不是操作符。（术语）






百分号（%）通配符
    SELECT prod_id , prod_name
    FROM products
    WHERE prod_name LIKE 'jet%';
区分大小写  根据MYSQL的配置方式，搜索可以是区分大小写的。如果区分大小写，'jet%'与JetPack1000将不匹配。

注意尾空格  尾空格可能会干扰通配符匹配。

下划线（_）通配符
另一个有用的通配符是下划线（_）。下划线的用途与%一样，但是下划线只匹配单个字符而不是多个字符。
    SELECT prod_id , prod_name
    FROM products
    WHERE prod_name LIKE '_ ton anvil';





与%能匹配0个字符不一样，_总是匹配一个字符，不能多也不能少。

第九章：正则表达式

第十章：创建计算字段

计算字段
我们需要直接从数据库中检索出转换、计算或格式化过的数据；而不是检索出数据，然后再在客户机应用程序或报告程序中重新格式化。

计算字段并不实际存在于数据库表中，计算字段是运行时在SELECT语句内创建的。

拼接字段：
例如：某报表需要单个值，而表中数据存储在两个列vend_name和vend_country中。
解决办法是把两个列拼接起来，在MYSQL的SELECT语句中，可以使用Concat()函数来拼接两个列。

    SELECT Concat(vend_name,'(',vend_country,')')
    FROM vendors
    ORDER BY vend_name;

输出为：Jet Set(USA)

删除空格：
使用Trim()/RTrim()/LTrim()函数来完成。
    SELECT Concat(RTrim(vend_name),'(',RTrim(vend_country) ,')')
    FROM vendors
    ORDER BY vend_name;

使用别名：别名用AS关键字赋予
    SELECT Concat(RTrim(vend_name),'(', RTime(vend_country),')') AS vend_title
    FROM vendors
    ORDER BY vend_name;

执行算术计算(+ - * /)
    SELECT prod_id,
           quantity,
           item_price,
           quantity*item_price AS expanded_price
    FROM orderitems
    WHERE order_num = 2005;

第十一章    使用数据处理函数

函数
    文本处理函数
    Upper()
    SELECT vend_name , Upper(vend_name) AS vend_name_upcase
    FROM vendors
    ORDER BY vend_name;
    
常用的文本处理函数
    Left()      返回串左边的字符
    Rigth()     返回串右边的字符
    Length()    返回串的长度
    Locate()    找出串的一个子串
    Lower()     将串转换为小写
    LTrim()     去掉串左边的空格
    RTrim()
    Soundex()   返回串的SOUNDEX值
    SubString() 返回子串的字符
    Upper()     将串转换为大写

日期和时间处理函数
    SELECT cust_id , order_num
    FROM orders
    WHERE order_date = '2005-09-01';

如果有当天的下单时间，则匹配失败，怎么办？
解决办法是指示MYSQL仅将给出的日期与列中的日期部分进行比较，而不是将给出的日期与整个列值进行比较。为此，必须使用Date()函数。
    SELECT cust_id , order_num
    FROM orders
    WHERE Date(order_date) = '2015-09-01';

如果想检索出2005年9月下的所有订单，怎么办？
解决办法：
    SELECT cust_id , order_num
    FROM orders
    WHERE Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30';

还有另一种办法（一种不需要记住每个月总有多少天或不需要操心闰年2月的办法）：
    SELECT cust_id , order_num
    FROM orders
    WHERE Year(order_date) = 2005 AND Month(order_date) = 9;

数值处理函数
    abs()   cos()   exp()   mod()   pi()    rand()  sin()   sqrt()  tan()

第十二章    汇总数据

聚集函数
AVG()   返回某列的平均值
COUNT() 返回某列的行数
MAX()   返回某列的最大值
MIN()   
SUM()   返回某列值之和

    AVG()
    SELECT AVG(prod_price) AS avg_price
    FROM products;
    只用于单个列：AVG()只能用来确定特定数值列的平均值，而且列名必须作为函数参数给出。为了获得多个列的平均值，必须使用多个AVG()函数

    COUNT()
    SELECT COUNT(*) AS num_cust FROM customers; 利用COUNT(*)对所有行计数，不管行中各列有什么值。计数值在num_cust中返回。

    SELECT COUNT(cust_email) AS num_cust FROM customers;
    
    MAX()
    SELECT MAX(prod_price) AS max_price FROM products;
    
    MIN()
    SELECT MIN(prod_price) AS max_price FROM products;

    SUM()
    SELECT SUM(quantity) AS items_ordered
    FROM orderitems
    WHERE order_num = 2005;

注意尽量避免与DISTINCT关键字的搭配使用

第十三章    分组数据

GROUP BY子句 和 HAVING子句
分组运行把数据分为多个逻辑组，以便能对每个组进行聚集计算

创建分组：
    SELECT vend_id , COUNT(*) AS num_prods 
    FROM products
    GROUP BY vend_id;

GROUP BY 子句必须出现在WHERE 子句之后，ORDER BY子句之前。

过滤分组：
WHERE不能完成任务，因为WHERE过滤指定的是行而不是分组。事实上，WHERE没有分组的概念。
HAVING非常类似于WHERE，事实上，目前为止所学过的所有类型的WHERE子句都可以用HAVING来代替。唯一的差别是WHERE过滤行，而HAVING过滤分组。
    SELECT cust_id , COUNT(*) AS orders
    FROM orders
    GROUP BY cust_id
    HAVING COUNT(*) >= 2;
    在这里WHERE 子句是不起作用的，因为过滤是基于分组聚集值而不是特定行值的。

    HAVING和WHERE的差别：WHERE在数据分组之前进行过滤，HAVING在数据分组后进行过滤。

    SELECT vend_id, COUNT(*) AS num_prods
    FROM products
    WHERE prod_price >= 10
    GROUP BY vend_id
    HAVING COUNT(*) >= 2;

分组和排序
GROUP BY 和 ORDER BY 的不同
    一般在使用GROUP BY 子句是，应该也给出ORDER BY子句，这是保证数据正确排序的唯一方法。千万不要依赖GROUP BY 排序数据。

    SELECT order_num , SUM(quantity*item_price) AS ordertotal
    FROM orderitems
    GROUP BY order_num
    HAVING SUM(quality*item_price) >= 50;

    SELECT order_num , SUM(quantity*item_price) AS ordertotal
    FROM orderitems
    GROUP BY order_num
    HAVING SUM(quality*itme_price) >=50
    ORDER BY ordertotal;

第十四章 使用子查询

子查询（嵌套在其他查询中的查询）
    SELECT cust_id
    FROM orders
    WHERE order_num IN (SELECT order_num
                        FROM orderitems
                        WHERE prod_id = 'TNT2');

作为计算字段使用子查询（使用了完全限定列名）
    SELECT cust_name,
           cust_state,
           (SELECT COUNT(*)
           FROM orders
           WHERE orders.cust_id = customers.cust_id) AS orders
    FROM customers
    ORDER BY cust_name;

相关子查询  涉及外部查询的子查询
（任何时候只要列名可能有多义性，就必须使用这种语法（表名和列名由一个句点分隔））

逐渐增加子查询来建立查询


第十五章 联结表
相同数据出现多次绝不是一件好事，此因素是关系数据库设计的基础。
关系数据库的设计就是要保证把信息分解成多个表，一类数据一个表。

创建联结
    SELECT vend_name,prod_name,prod_price FROM vendors,products
    WHERE vendors.vend_id = products.vend_id
    ORDER BY vend_name,prod_name;

在联结两个表时，你实际上做的是将第一个表中的每一行与第二个表中的每一行配对。WHERE子句作为过滤条件，它只包含那些匹配给定条件的行。
没有WHERE子句，第一个表中的每个行将与第二个表中的每个行配对，而不管它们逻辑上是否可以匹配在一起。

笛卡尔积：由没有联结条件的表关系返回的结果为笛卡尔积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。

因此，不要忘了WHERE子句 应该保证所有联结都有WHERE子句，否则MySQL将返回比想要的数据多的多的数据。

内部联结
目前为止所有的联结称为等值联结(如上)，它基于两个表之间的相等测试。这种联结也称为内部联结。其实，对于这种联结可以使用稍微不同的语法来明确指定 联结的类型。如下：
    SELECT vend_name , prod_name , prod_price 
    FROM vendors INNER JOIN products 
    ON vendors.vend_id = products.vend_id;

    在使用这种语法时，联结条件用特定的ON子句而不是WHERE子句给出。

联结多个表
SQL对一条SELECT语句中可以联结的表的数目没有限制。
    SELECT prod_name , vend_name , prod_price ,quantity
    FROM orderitems,products,vendors
    WHERE products.vend_id = vendors.vend_id
    AND orderitems.prod_id = products.prod_id
    AND order_num = 2005;


第十六章：创建高级联结
    
使用表别名
别名除了用于列名和计算字段外，SQL还允许给表名起别名。这样做的有两个主要原因：
1、缩短SQL语句；
2、允许在单条SELECT语句中多次使用相同的表。
如下：
    SELECT cust_name , cust_contact
    FROM customers AS c , orders AS o , orderitems AS oi
    WHERE c.cust_id = o.cust_id
    AND oi.order_num = o.order_num
    AND prod_id = 'TNT2';

    注意：表别名只在查询执行中使用。与列别名不一样，表别名不返回到客户机。

使用不同类型的联结（自联结、自然联结、外部联结）：
    子查询：
    SELECT prod_id , prod_name
    FROM products
    WHERE vend_id = (SELECT vend_id
                    FROM products
                    WHERE prod_id = 'DTNTR');

    结果相同的自联结查询：
    SELECT p1.prod_id , p1.prod_name
    FROM products AS p1 , products AS p2
    WHERE p1.vend_id = p2.vend_id
    AND p2.prod_id = 'DTNTR';

    有时处理联结远比处理子查询快得多。应该试一下两种方法，以确定哪种性能更好。

自然联结：

外部联结：
联结包含了那些在相关表中没有关联行的行。这种类型的联结称为外部联结。
    SELECT customers.cust_id , orders.order_num
    FROM customers LEFT OUTER JOIN orders
    ON customers.cust_id = orders.cust_id;

    RIGHT指出的是OUTER JOIN右边的表，而LEFT指出的是OUTER JOIN左边的表。
    上面的例子使用LEFT OUTER JOIN 从 FROM 子句的左边表（customers表）中选择所有行。
    记住：永远用小的结果集驱动大的结果集。

注意：与内部联结关联两个表中的行不同的是，外部联结还包括没有关联行的行。

