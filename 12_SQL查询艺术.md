# 创建、修改和删除 表
	1  创建表
		· CREATE TABLE table_name (column_name1 data_type(size) NOT NULL, column_name2 data_type NOT NULL DEFAULT 'xx', column_name3 data_type ....);
		/*
		 · 表的名字对大小写不敏感，表名要紧接在Create Table 关键词后面。
		 · 创建表，必须要保证该表名在数据库中不存在
		 · data_type(size): size 指定表字段的最大长度
		 · NOT NULL: 创建非空约束
		 · DEFAULT 'value': 指定缺省值
		*/
	2 表的修改
		1）增加新列
			· ALTER TABLE table_name ADD column_name1 data_type, ADD column_name2 data_type;
			/*
				· 当用ALTER TABLE语句向表中添加新列时，DBMS向表的列定义的尾部添加列，即在查询中将位置表的最右边。除非指定默认值，DBMS为已有行上的新列设NULL值。
			*/
		2）删除列
			· ALTER TABLE table_name DROP COLUMN column_name1, column_name2;
	3 表的删除与重命名
		1）重命名表
			· 不同的DBMS对表的重命名提供的命令不尽相同。在DB2、MySQL、Orcle、数据库系统中可采用RENAME 关键词，而在SQL Server 和 Sybase 数据库系统中可采用SP_RENAME关键词重命名表。
			· SP_RENAME table_name1, table_name2
		2）删除表
			· DROP TABLE table_name
				/*
				 · 当某表与其他的表存在关联关系时，一些DBMS会不允许用户通过DROP TABLE语句来删除该表，只有当这种关联关系被删除后，才能删除
				*/
	4 创建、删除数据库
		1）数据库的创建
			· CREATE DATABASE database_name additional parameters  
		2）删除数据库
			· DROP DATABASE database_name

# 索引与视图的创建
	1）索引的创建
		· 索引是一个单独的、物理的数据库结构，是数据库的一个表中所包含的值的列表。其中注明了表的各个值所在的存储位置。索引是依赖于表建立的，提供了编排表中数据的方法。
		· 基本创建语句
			 CREATE INDEX index_name ON table_name(column_name,...)
		· SQL允许用户在一个表中，在两个或多个字段上创建多字段索引，这种索引又被称为复合索引。在创建多字段索引时，各字段的排列顺序决定了其优先级，排列越靠前，具有越高的优先级。
		· 使用UNIQUE关键字创建惟一索引
			· CREATE UNIQUE INDEX index_name ON table_name(column_name,...)
			· 惟一索引是指不允许在两行中存在相同的索引值。
	2）索引的销毁
			· DROP INDEX index_name
	3）视图
		· 视图是从一个或多个表中查询数据的另外一种方式。利用视图，用户可以集中、简化、定制数据库，同时还可以提供安全保证。
		· 视图是从一个或多个表中导出的表，其结构和数据是建立在对表的查询基础上的。视图不是真实存在的基础表，而是一张虚表。
		· 视图的优缺点：
			1）优点：
				简化操作、定制数据、合并分隔数据、安全性
			2）缺点：
				性能 、 更新限制 
		· 创建视图
			CREATE VIEW view_name (column,...) AS SELECT * FROM table_name;
				/*  
					(column,...) : 缺省时，为子查询结果中的字段名。
				*/
		· 删除视图
			DROP VIEW view_name;

# 简单的查询
	1）SELECT语句的结构
		· 基本语法 
			SELECT [DISTINCT | ALL] select_list FROM table_name_source 
			[WHERE search_condition]
			[GROUP BY group_by_expression]
			[HAVING search_condition]
			[ORDER BY order_expression [ASC | DESC]]
			/*
				· 使用'*' 通配符时我们要慎重，在不需要查询所有列时，尽量采用单列查询或多列查询，以免占用过多的资源。
				· DISTINCT : 删除查询结果中相同的行。DISTINCT的使用是要付出代价的。因为我们要去掉重复值，必须对结果进行排序，相同的元组排在一起，只有按照这种方法对元组分组，才能去掉重复值，而这一工作甚至比查询本身还费时间
				· GROUP BY 子句用于分组查询结果，根据group_by_expression中列出的列，归纳信息类型，汇总相关数据。
				· HAVING 子句列出另外的行选择标准，以便根据GROUP BY子句产生的结果筛选行。
				· ORDER BY 子句一定要放在所有子句的最后（无论包含多少子句），子句的各字段的排列顺序决定了其优先级，排列越靠前，具有越高的优先级。
			*/

# 复杂搜索条件查询
	1 组合查询条件 
		AND  OR 运算符 ：AND 的优先级大于 OR 的，当它们同时运用时，采用"()"来实现需要的执行顺序，这样可以增强程序的可读性。
	2 IN 运算符
		· test expression IN (fist value, ...., last value)
		· 在大多数情况下，OR运算符与IN运算符可以实现相同的功能，但与OR运算符相比，IN运算符具有以下两个优点：
			1）当选择的条件很多时，采用IN运算符就显得很简便，只需在括号内用逗号间隔依次罗列即可，运行效率比OR运算符要高。
			2）使用IN运算符，其后面所列的条件可以是另一条SELECT语句，即子查询。
	3 模糊查询
		· LIKE   NOT LIKE
		· LIKE与通配符使用
			'%' : 匹配任意字符，且不计字符的多少
			'_' : 一个下划线只表示任意一个字符的匹配
			'[]': 用于指定一系列的字符，只要满足这些字符其中之一且位置出现在"[]"的位置，就能匹配。 在'[]'中列出的字符前加 '^' , 表示否定。
		· 使用ESCAPE定义转义符
			在使用LIKE关键字进行模糊查询时 ，'%','_'和'[]'单独出现则会被认为是通配符。为了在字符数据类型的列中查询是否存在这些字符，关键字ESCAPE允许确定一个转义字符，告诉DBMS紧跟在转义字符之后的字符看作是实际值，而不是通配符。
				如： LIKE 'AB&_%' ESCAPE '%'
				/*
					该表达式的查询条件以'AB_'开始的所有字符串。
				*/

# 连接符、数值运算与函数
	1）连接符
		· 在SQL中，连接符用于连接表中的两列或者多列数据，使它们作为一列供用户查询操作。在Access、SQL Server 、和Sybase 数据库系统中，采用的连接符为"+"; 在DB2、Oracle、 PostgreSQL，采用的连接符是"||"; 在My SQL 中采用的是CONCAT()函数。
			SQL Server:
				SELECT name+'('+depart+')' AS info, age FROM user;
			My SQL
				SELECT CONCAT(name,'(',depart,')') AS info, age FROM user;
		· 使用别名 ： 关键字 AS 
			1）在ORDER BY 子句中可以通过指定的连接列的别名操作该列，然而，在WHERE子句中，就不可以使用该别名。
			2）SELECT语句的各子句的执行顺序
				FROM 子句 -> WHERE 子句 -> GROUP BY 子句 -> HAVING 子句 -> SELECT 子句 -> ORDER BY 子句
			3）列的别名在SELECT 子句定义； 表的别名在 FROM 子句定义
	2）数值运算
		· CAST 转换数据类型
			从根本上来说，使用CAST可将任何数据类型转换为CHAR 或者 VARCHAR类型；而仅当非数值类型的值中包括的全部是数字(0~9)、单个小数点或(-)或(+)（要求必须在开头，用于说明数字是正还是负）时，才能将其转换为数值类型。
		· 使用CASE 表达式
			CASE表达式是SQL本身的一部分，给出了IF-THEN-ELSE类型的数据结构，可以根据其操作的表列中来决定哪一条SQL语句被执行。
			CASE 
			WHEN search_condition THEN result_epxression
			[...WHEN last_search_condition THEN last result_expression]
			[ELSE else_result_expression]
			END
	3）函数
		查看手册

# 聚合分析与分组
	1）聚合分析 
		· 在访问数据库时，我们经常需要对表中 的某列数据进行统计分析，如求其最大值、最小值、平均值等。所有这些针对表中一列或者多列数据的分析就称为聚合分析
		· SQL提供的聚合函数有SUM()、MAX()、MIN()、AVG()、COUNT()等
		· SUM()
			SUM()函数只能作用于数值型数据；对某列数据进行求和时，如果该列存在NULL值，则SUM函数会忽略该值。
		· COUNT()
			COUNT(*), 计算表中行的总数，即使表中的数据为NULL, 也被计入在内
			COUNT(column)， 计算column列包含的行的数目，如果该列中某行数据为NULL，则该行不读入统计总数。
		· MAX() / MIN() 
			1）SELECT MAX(column)/MIN(column) FROM table_name; 
				/*
					问题：返回单一的数据列，其他列的信息没有查询成功
				*/
				若要获得某最大/最小值的结果及其相关信息，用WHERE子句中使用子查询来返回最大/最小值，然后再基于这个返回的最大/最小值,查询相关信息。
				 如：
					SELECT name,depart,age FROM user WHERE age = (SELECT MAX(age) FROM user)
			2）对字符型数据的最大值，按照首字母由A~Z的顺序排列；对于汉字则是按照其全拼拼音排列的；对于日期时间类型的数据，按照日期时间的早晚。
		· AVG()
			AVG()函数在计算一列的平均值时忽略NULL值，不能直接用于WHERE子句，必须以子查询的形式使用。
		· 在所有5种聚合函数中，除了COUNT(*)函数外，其他的函数在计算进程中都忽略NULL值。
	2）组合查询
		· 创建分组是通过GROUP BY子句实现的。GROUP BY 子句用于归纳信息类型，以汇总相关数据。
		· 在使用GROUP BY子句根据多列组合行时，我们可以在GROUP BY子句中使用ROLLUP运算符和CUBE运算符扩展查询结果。
		· 当GROUP BY子句中用于分组的列出现NULL值时，将所有的NULL值分在同一组。
		· DBMS将HAVING子句中的搜索条件应用于GROUP BY 子句产生的行组，如果行组不满足搜索条件，就将其从结果表中删除。
		· HAVING子句与WHERE子句
			1）HAVING子句与组有关，而不是与单个的行有关。
			2）如果指定了GROUP BY子句，那么HAVING子句定义的搜索条件将用于这个GROUP BY子句创建的那些组。
			3）如果指定WHERE子句而没有指定GROUP BY子句，那么HAVING子句定义 的搜索条件将作用于WHERE子句的输出，并把这个输出看作是一个组。
			4）如果既没有指定GROUP BY子句也没有指定WHERE子句，那么HAVING子句定义的搜索条件将作用于FROM子句的输出并把这个输出看作是一个组。

# 多表查询
	1 在WHERE子句中连接
		表的连接，是通过FROM子句列出要连接的表、WHERE子句列出连接条件来实现的。
		· SELECT column FROM table1, table2 WHERE table1.column = table2.column
	2 采用JOIN关键字连接
		· 在FROM子句中通过连接关键字实现表的连接，这样有助于将连接操作与WHERE的搜索条件区分开来
		· SELECT column FROM　join_table JOIN_TYPE join_table ON (join_condition)
		/*
			· JOIN_TYPE为连接类型，可分为4种：自然连接、内连接、外连接和交叉连接。
			 1）自然连接JOIN_TYPE 的形式为NATURAL JOIN。
			 2）内连接JOIN_TYPE 的形式为INNER JOIN。
			 3）外连接，
				 · 左外连接，JOIN_TYPE 的形式为LEFT OUTER JOIN 或LEFT JOIN；
				 · 右外连接，JOIN_TYPE 的形式为RIGHT OUTER JOIN或RIGHT JOIN；
				 · 全外连接，JOIN_ TYPE 的形式为FULL OUTER JOIN或FULL JOIN。
			 4）交叉连接中JOIN_TYPE 的形式为CROSS JOIN。
			· ON (join_condition)子句指出连接条件，由被连接表中的列和比较运算符、逻辑运算符等构成。
		*/

# 子查询
	1 返回单值的子查询
		· 一个子查询如果产生一个单纯的数据，该子查询就如同一个常量，那么就可以像使用常量一样使用它。在实际应用中，我们经常要求子查询只返回一个值，这样就可以将一列值和单个子查询返回值进行比较。
		· 应用：
			1）在多表查询中使用子查询
			2）利用子查询得到聚合函数的返回值，应用在WHERE子句的搜索条件中。因为聚合函数返回单个值，所以在SELECT语句中的WHERE子句中，比较判式的任意一边都可以采用聚合分析的子查询。
			3）应用于SELECT子句中，这种情况下，在子查询中要返回的单一值经常是聚合分析的结果。子查询必须是返回单值。
		2 返回多行的子查询
			1）IN子查询
				SELECT column_name FROM table_name WHERE test expression [NOT] IN (subquery)
				/*
					· test expression 可以是实际值、列名、表达式或是另一个返回单一值的子查询
					· 由运算符IN引入的子查询返回的列值，既可以来自主查询的表，也可以来自其他表。SQL对查询的唯一要求就是它必须返回单一列的数据值，并且其数据类型必须与IN前面表达式的数据类型相兼容。
				*/
			2) EXISTS子查询
				· 在某些情况下，我们只需要子查询返回一个True或者False, 子查询数据内容本身并不重要，这时，可使用EXISTS判式来定义子查询。EXISTS判式用来测试集合是否为空，它问题与子查询结合使用，而且只要子查询中至少返回一个传下，EXISTS判式的值就为TRUE。
				· SELECT column_name FROM table_name WHERE [NOT] EXISTS (subquery)
				/*要使EXISTS判式有意义，我们应该在子查询中建立搜索条件以匹配子查询连接起来的两个表中的值。*/
			3）SOME/ALL子查询
				· 实际上，用聚合函数实现子查询通常比直接使用SOME或ALL查询效率要高。
			4）UNIQUE子查询
				· UNIQUE运算符用来测试集合是否存在重复元组。
				SELECT column_name FROM table_name WHERE [NOT] UNIQUE(subquery)
				/*要使UNIQUE判断有意义，应该在子查询中建立搜索条件，以匹配子查询连接起来的两个表中的值*/
			5）












