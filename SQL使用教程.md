### 关系模型

##### 主键

不可重复性，采用于任何业务无关的字段为主键 如：数值id（1，2，3....） `BIGINT NOT NULL AUTO_INCREMENT`类型，主键也不应该允许`NULL`

1.自增整数类型：数据库会在插入数据自动为每一个记录分配一个自增整数，无需自己预习生成主键

2.全局唯一GUID类型：使用一种全局唯一的字符串作为主键，类似`8f55d96b-8acc-4636-8cb8-76bf8abc2f57`

GUID算法通过网卡MAC地址，时间戳和随机数保证任意计算机在任意时间生成的字符串都是不同的，大部分语言内置了GUID算法，可自己预算出主键

###### 联合主键

同时拥有两或更多字段设置为主键，联合主键允许一列有重复，只要不是所有主键列重复即可

| id_num | id_type | other columns... |
| :----- | :------ | :--------------- |
| 1      | A       | ...              |
| 2      | A       | ...              |
| 2      | B       | ...              |

如把上述的id_num和id_type作为联合主键，上面的三条记录都是允许的

##### 外键

本表通过xxx字段，可以把数据与另一张表关联起来，这种列称为`外键`

外键通过定义外键约束实现

```mysql
# 添加外键
ALTER TABLE students #打开students表
ADD CONSTRAINT fk_class_id #外键约束的名称`fk_class_id`可以任意
FOREIGN KEY (class_id) #指定了`class_id`列作为外键
REFERENCES classes (id);#指定了这个外键将关联到`classes`表的`id`列（即`classes`表的主键）

# 删除外键
ALTER TABLE students #打开students表
DROP FOREIGN KEY fk_class_id; #删除fk_class_id 外键（并没有删除外键列，仅删除关联）
DROP COLUMN XXX #删除列
```

###### 多对多

指通过两个一对多关系实现的，即通过一个中间表，关联两个一对多关系，形成多对多关系

`teachers`表：

| id   | name   |
| :--- | :----- |
| 1    | 张老师 |
| 2    | 王老师 |
| 3    | 李老师 |
| 4    | 赵老师 |

`classes`表：

| id   | name |
| :--- | :--- |
| 1    | 一班 |
| 2    | 二班 |

中间表`teacher_class`关联两个一对多关系：

| id   | teacher_id | class_id |
| :--- | :--------- | :------- |
| 1    | 1          | 1        |
| 2    | 1          | 2        |
| 3    | 2          | 1        |
| 4    | 2          | 2        |
| 5    | 3          | 1        |
| 6    | 4          | 2        |

通过中间表`teacher_class`可知`teachers`到`classes`的关系：

- `id=1`的张老师对应`id=1,2`的一班和二班；
- `id=2`的王老师对应`id=1,2`的一班和二班；
- `id=3`的李老师对应`id=1`的一班；
- `id=4`的赵老师对应`id=2`的二班。

同理可知`classes`到`teachers`的关系：

- `id=1`的一班对应`id=1,2,3`的张老师、王老师和李老师；
- `id=2`的二班对应`id=1,2,4`的张老师、王老师和赵老师；

因此，通过中间表，我们就定义了一个“多对多”关系

###### 一对一

指一个表的记录对应另一个表的唯一一个记录

如students表的学生的联系方式填入另一个contacts，可得到`一对一`关系

| id   | student_id | mobile      |
| :--- | :--------- | :---------- |
| 1    | 1          | 135xxxx6300 |
| 2    | 2          | 138xxxx2209 |
| 3    | 5          | 139xxxx8086 |

[^]: 把一个大表拆成两个一对一的表，目的是把经常读取和不经常读取的字段分开，以获得更高的性能。例如，把一个大的用户表分拆为用户基本信息表`user_info`和用户详细信息表`user_profiles`，大部分时候，只需要查询`user_info`表，并不需要查询`user_profiles`表，这样就提高了查询速度

##### 索引

索引可以在使用数据库的过程中逐步优化，加快查询速度，对于用户，应用程序是否创建索引使用关系数据库没有任何区别

| id   | class_id | name | gender | score |
| :--- | :------- | :--- | :----- | :---- |
| 1    | 1        | 小明 | M      | 90    |
| 2    | 1        | 小红 | F      | 95    |

如经常根据score列进行查询，可对score列创建索引

```
ALTER TABLE students # 打开students表
ADD INDEX idx_score (score); #创建score列索引为idx_score(索引名称任意)
ADD INDEX idx_name_score (name, score); #如索引有多列，可在括号里依次写入
```

[]: 索引的效率取决于索引列的值是否散列，即该列的值越互不相同，索引效率越高，反之，如上图gender列	"，大约一半是m，另一半是f，该列索引没有意义"

###### 唯一索引

假设students表的name不能重复（唯一性)

```
ALTER TABLE students # 打开students表
ADD UNIQUE INDEX uni_name (name); # 添加唯一索引，并创建索引
ADD CONSTRAINT uni_name UNIQUE (name); # 添加唯一索引，不创建唯一索引（name列没有索引，但具有唯一性保证）
```



### 查询数据

##### 基本查询

 打开数据库

```
USE <数据库名>;
```

查看数据表

```
SELECT * FROM <表名>;
```

##### 条件查询

查看分数在80分以上的学生

```
SELECT * FROM <表名> WHERE <列名> >= 80;
```

 查看分数80分而且是男生, AND表达同时满足条件1和条件2

```
SELECT * FROM <表名> WHERE <列名> >= 80 AND <l列名> = 'M';（<条件表达式>）
```

查看满足条件1或满足条件2 (OR 或者)

```
SELECT * FROM <表名> WHERE <列名> >= 80 OR <列名> = ‘M’;
```

查看不满足该条件的记录 （NOT(条件)）

```
SELECT * FROM <表名> WHERE NOT <列名> = 2;
```

查看满足多个条件的记录(分数小于80或者大于90，而且是男)

```
SELECT * FROM <> WHERE (<列名> < 80 OR <列名> > 90) AND <列名> = 'M';
```

##### 常用的条件表达式

| 条件                 | 表达式举例1     | 表达式举例2      | 说明                                              |
| :------------------- | :-------------- | :--------------- | :------------------------------------------------ |
| 使用=判断相等        | score = 80      | name = 'abc'     | 字符串需要用单引号括起来                          |
| 使用>判断大于        | score > 80      | name > 'abc'     | 字符串比较根据ASCII码，中文字符比较根据数据库设置 |
| 使用>=判断大于或相等 | score >= 80     | name >= 'abc'    |                                                   |
| 使用<判断小于        | score < 80      | name <= 'abc'    |                                                   |
| 使用<=判断小于或相等 | score <= 80     | name <= 'abc'    |                                                   |
| 使用<>判断不相等     | score <> 80     | name <> 'abc'    |                                                   |
| 使用LIKE判断相似     | name LIKE 'ab%' | name LIKE '%bc%' | %表示任意字符，例如'ab%'将匹配'ab'，'abc'，'abcd' |

##### 投影查询

[^]: 只希望返回某些列的数据，而不是所有列的数据，我们可以用`SELECT 列1, 列2, 列3 FROM ...`，让结果集仅包含指定列。这种操作称为投影查询，
[^]: 使用`SELECT *`表示查询表的所有列，使用`SELECT 列1, 列2, 列3`则可以仅返回指定列，这种操作称为投影。`SELECT`语句可以对结果集的列进行重命名

从students表返回id，score，name

```
SELECT id, score, name, FROM students;
```

从students表返回id，score重命名为points，name

```
SELECT id, score points, name FROM students;
```

投影查询并WHERE条件 (从students表返回id，score重命名为points，name,并限制只有男的)

```
SELECT id， score points， name FROM students WHERE gender = ‘M’;
```

##### 排序

默认排序规则是ASC ‘升序’，从小到大

按score(分数)从低到高(ORDER BY 从低到高)

```
SELECT id，name，gender，score FROM students ORDER BY score
```

按score从高到低(ORDER BY <列名> DESC;)

```
SELECT id, name, gender, score FROM students ORDER BY score DESC;
```

如score有相同的数据(使用`ORDER BY score DESC, gender`表示先按`score`列倒序，如果有相同分数的，再按`gender`列排序)

```
SELECT id, name, gender, score FROM students ORDER BY score DESC, gender;
```

如果有`WHERE`子句，那么`ORDER BY`子句要放到`WHERE`子句后面

```
SELECT id, name, gender, score FROM students # 输出students表的id，name...
WHERE class_id = 1 # WHERE 条件 一班
ORDER BY score DESC; # 排序方法 以分数 倒序大到小
```

##### 分页

```
SELECT id, name, gender, score FROM students # 输出students表的id，name...

ORDER BY score DESC # 排序方法 以分数 倒序大到小

LIMIT 3 OFFSET 0; LIMIT 每页显示几条数据（0）全部，OFFSET 从第几条开始（0开始）
```

[^]: `OFFSET`是可选的，如果只写`LIMIT 15`，那么相当于`LIMIT 15 OFFSET 0`。在MySQL中，`LIMIT 15 OFFSET 30`还可以简写成`LIMIT 30, 15`。使用`LIMIT  OFFSET `分页时，随着`N`越来越大，查询效率也会越来越低
[^]: 使用`LIMIT  OFFSET `可以对结果集进行分页，每次查询返回结果集的一部分；分页查询需要先确定每页的数量和当前页数，然后确定`LIMIT`和`OFFSET`的值



##### 聚合查询

查询表一共有几条记录

```
SELECT COUNT(*) FROM students; # COUNT(*)表示查询所有列的行数，仅一行一列
```

查询并设置名字

```
SELECT COUNT(*) num FROM students; # 查询并命名为num
```

查询并设置条件 查询并命名为boys限制仅M'男'

```
SELECT COUNT(*) boys FROM students WHERE gender = 'M' 
```

| 函数     | 说明                                   |
| :------- | :------------------------------------- |
| COUNT(*) | 查询表单共几条记录 *可为列名           |
| SUM      | 计算某一列的合计值，该列必须为数值类型 |
| AVG      | 计算某一列的平均值，该列必须为数值类型 |
| MAX      | 计算某一列的最大值                     |
| MIN      | 计算某一列的最小值                     |

查询男生平均成绩 查询平均分数命名为average，并限制仅男

```
SELECT AVG(score) average FROM students WHERE gender = 'M';
```

查询并分组

```
SELECT COUNT(*) num FROM studnets GROUP BY class_id;
```

获取结果为三个，GROUP BY 为分组功能 按照class_id分组

| num  |
| :--- |
| 4    |
| 3    |
| 3    |

可以把class_id,gender列也放入结果集

```
SELECT class_id, gender,COUNT(*) num FROM students GROUP BY class_id, gender;
```

| class_id | gender | num  |
| :------- | :----- | :--- |
| 1        | M      | 2    |
| 1        | F      | 2    |
| 2        | F      | 1    |
| 2        | M      | 2    |
| 3        | F      | 2    |
| 3        | M      | 1    |



##### 多表查询

同时查询多张表

```
SELECT * FROM students, classes; # 同时查询students， classes
```

多表并投影，重新命名列

```
SELCET

	students.id sid,

	classes.name cname

FROM students, classes;
```

| sid  | cname |
| :--- | :---- |
| 1    | 一班  |
| 1    | 二班  |

多表并投影，重新命名列(简洁版)

```
SELECT
	s.id sid,
	c.name cname
FROM students s, classes c;
```

多表查询并添加WHERE条件

```
SELECT
	s.id sid,
	s.name,
	s.gender,
	s.score
	c.id cid,
	c.name cname
FROM students s, classes c
WHERE s.gender = 'M' AND c.id = 1;
```

| sid  | name | gender | score | cid  | cname |
| :--- | :--- | :----- | :---- | :--- | :---- |
| 1    | 小明 | M      | 90    | 1    | 一班  |
| 3    | 小军 | M      | 88    | 1    | 一班  |
| 6    | 小兵 | M      | 55    | 1    | 一班  |

##### 连接查询

内连接(INNER JOIN)

[^]: INNER JOIN只返回同时存在于两张表的行数据，由于`students`表的`class_id`包含1，2，3，`classes`表的`id`包含1，2，3，4，所以，INNER JOIN根据条件`s.class_id = c.id`返回的结果集仅包含1，2，3。

```
SELECT s.id, s.name, s.class_id, c.name class_name class_name, s.gender, s.score
FROM students s # 确定主表
INNER JOIN classes c # 确定需要连接的表
ON s.class_id = c.id; # 连接条件 表示s.class_id列与c.id列相同的行连接
```

| id   | name | class_id | class_name | gender | score |
| :--- | :--- | :------- | :--------- | :----- | :---- |
| 1    | 小明 | 1        | 一班       | M      | 90    |
| 2    | 小红 | 1        | 一班       | F      | 95    |
| 3    | 小军 | 1        | 一班       | M      | 88    |
| 4    | 小米 | 1        | 一班       | F      | 73    |
| 5    | 小白 | 2        | 二班       | F      | 81    |

外连接(RIGHT OUTER JOIN)

[^]: 有RIGHT OUTER JOIN，就有LEFT OUTER JOIN，以及FULL OUTER JOIN
[^]: RIGHT OUTER JOIN返回右表都存在的行。如果某一行仅在右表存在，那么结果集就会以`NULL`填充剩下的字段。

```
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s # 确定主表
RIGHT OUTER JOIN classes c # 确定需要连接的表
ON s.class_id = c.id;
```

| 9    | 小王 | 3    | 三班 | M    | 89   |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 10   | 小丽 | 3    | 三班 | F    | 88   |
| NULL | NULL | NULL | 四班 | NULL | NULL |

[^]: LEFT OUTER JOIN则返回左表都存在的行。如果我们给students表增加一行，并添加class_id=5，由于classes表并不存在id=5的行，所以，LEFT OUTER JOIN的结果会增加一行，对应的`class_name`是`NULL`：

```
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
LEFT OUTER JOIN classes c
ON s.class_id = c.id;
```

| 9    | 小王 | 3    | 三班 | M    | 89   |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 10   | 小丽 | 3    | 三班 | F    | 88   |
| 11   | 新生 | 5    | NULL | M    | 88   |

FULL OUTER JOIN，它会把两张表的所有记录全部选择出来，并且，自动把对方不存在的列填充为NULL

```
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
FULL OUTER JOIN classes c
ON s.class_id = c.id;
```

| 10   | 小丽 | 3    | 三班 | F    | 88   |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 11   | 新生 | 5    | NULL | M    | 88   |
| NULL | NULL | NULL | 四班 | NULL | NULL |

假设查询语句是

SELECT ... FROM tableA ？？？ JOIN tableB ON tableA .column1 = tableB.column2;

我们把tableA看作左表，把tableB看成右表，那么INNER JOIN是选出两张表都存在的记录：

![inner-join](https://www.liaoxuefeng.com/files/attachments/1246892164662976/l)

LEFT OUTER JOIN是选出左表存在的记录：

![left-outer-join](https://www.liaoxuefeng.com/files/attachments/1246893588481376/l)

RIGHT OUTER JOIN是选出右表存在的记录：

![right-outer-join](https://www.liaoxuefeng.com/files/attachments/1246893609222688/l)

FULL OUTER JOIN则是选出左右表都存在的记录：

![full-outer-join](https://www.liaoxuefeng.com/files/attachments/1246893632359424/l)

##### 小结

[^]: JOIN查询需要先确定主表，然后把另一个表的数据“附加”到结果集上；INNER JOIN是最常用的一种JOIN查询，它的语法是`SELECT ... FROM <表1> INNER JOIN <表2> ON <条件...>`；JOIN查询仍然可以使用`WHERE`条件和`ORDER BY`排序



### 修改数据

```
INSERT 插入数据
UPDATE 更新已有记录 
DELETE 删除已有记录
```

##### 插入新记录 INSERT 

INSERT INTO <表名>  (字段1， 字段2....) VALUES (值1, 值2)

```
INSERT INTO students (class_id, name, gender, score) VALUES (2, '大牛', 'M', 80); # 插入数据，更新至尾部
SELECT * FROM students; # 打开students表
```

| 9    | 3    | 小王 | M    | 89   |
| ---- | ---- | ---- | ---- | ---- |
| 10   | 3    | 小丽 | F    | 88   |
| 11   | 2    | 大牛 | M    | 80   |

插入多条记录

```
INSERT INTO studnets (class_id, name, gender, score) VALUES
  (1, '大宝', 'M', 87),
  (2, '二宝', 'M', 81);
SELECT * FROM students;
```

| 11   | 2    | 大牛 | M    | 80   |
| ---- | ---- | ---- | ---- | ---- |
| 12   | 1    | 大宝 | M    | 87   |
| 13   | 2    | 二宝 | M    | 81   |

##### 更新已有记录  UPDATE

更新id=1的记录

```
UPDATE students SET name='大牛', score=66 WHERE id=1;
SELECT * FROM studnets;
```

| id   | class_id | name | gender | score |
| :--- | :------- | :--- | :----- | :---- |
| 1    | 1        | 大牛 | M      | 66    |
| 2    | 1        | 小红 | F      | 95    |

更新id=5,6,7的记录

```
UPDATE students SET name='小牛', score=77 WHERE id>=5 AND id<=7;
SELECT * FROM students;
```

| 4    | 1    | 小米 | F    | 73   |
| ---- | ---- | ---- | ---- | ---- |
| 5    | 2    | 小牛 | F    | 77   |
| 6    | 2    | 小牛 | M    | 77   |
| 7    | 2    | 小牛 | M    | 77   |

更新score<80的记录

```
UPDATE students SET score=score+10 WHERE score<80;
SELECT * FROM students;
```

| id   | class_id | name | gender | score |
| :--- | :------- | :--- | :----- | :---- |
| 1    | 1        | 大牛 | M      | 86    |
| 2    | 1        | 小红 | F      | 95    |
| 3    | 1        | 小军 | M      | 88    |
| 4    | 1        | 小米 | F      | 83    |

如果WHERE条件没有匹配到任何记录，UPDATE语句不会报错，也不会更新

```
UPDATE students SET score=100 WHERE id=999; # 更新id=999的记录
SELECT *FROM students;
```

[^]: UPDATE语句可以在没有WHERE条件下更新 UPDATE studnets SET score=60；将更新全表

在使用MySQL这类关系数据库时，UPDATE语句会返回更新行数及WHERE条件匹配的行数

如更新id=1的记录

```
mysql> UPDATE students SET name='大宝' WHERE id=1;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

 MySQL会返回1，从打印结果Rows matched: 1 Changed: 1看到

当更新id=999的记录

```
mysql> UPDATE students SET name='大宝' WHERE id=999；
Query OK, 0 rows affected (0.00 sec)
Rows matched: 0 Changed: 0 Warnings: 0
```

MySQL会返回`0`，可以从打印的结果`Rows matched: 0 Changed: 0`看到

##### DELETE 删除已有记录

[^]: 基本语法： DELETE FROM <表名> WHERE....

删除id=1的记录

```
DELETE FROM studnets WHERE id=1;
SELECT * FROM studnets;
```

| id   | class_id | name | gender | score |
| :--- | :------- | :--- | :----- | :---- |
| 2    | 1        | 小红 | F      | 95    |
| 3    | 1        | 小军 | M      | 88    |

删除id=5,6,7的记录

```
DELETE FROM studnets WHERE id>=5 AND id<=7;
SELECT * FROM students; # 查看
```

| 4    | 1    | 小米 | F    | 73   |
| ---- | ---- | ---- | ---- | ---- |
| 8    | 3    | 小新 | F    | 91   |

如WHERE条件没有匹配到任何记录，DELETE语句不会报错，也不会删除

```
DELET FROM students WHERE id=999; # 删除id=999的记录
SELECT * FROM studnets;
```

[^]: 特别小心的是，和`UPDATE`类似，不带`WHERE`条件的`DELETE`语句会删除整个表的数据：DELETE FROM students;这时，整个表的所有记录都会被删除

使用MySQL删除，会返回删除的行数以及WHERE条件匹配的行数

```
mysql> DELETE FROM students WHERE id=1; # 删除id=1
Query OK, 1 row affected (0.01 sec)

mysql> DELETE FROM students WHERE id=999; # 删除id=999
Query OK, 0 rows affected (0.01 sec)
```

### MySQL

命令提示符，mysql -uroot -p密码 连接mysql server ，输入exit断开连接并返回命令提示符

[^]: 注意`EXIT`仅仅断开了客户端和服务器的连接，MySQL服务器仍然继续运行

默认端口3306，地址127.0.0.1:3306

##### 管理MySQL

###### 用户

查看用户

```
SELECT USER, HOST FROM MYSQL.USER; #mysql.user 表查询
SELECT CURRENT_USER; # 函数查询
```

添加用户

```
CREATE USER '用户名'@‘localhost’ # localhsot 仅本主机登录
```

删除用户

```
DROP USER '用户名'@‘localhost’
```

###### 数据库

列出所有数据库

```
SHOW DATABASES;
```

创建新的数据库

```
CREATE DATABASE <库名>;
```

删除数据库

```
DROP DATABASE <库名>;
```

切换为当前数据库

```
USE <库名>;
```

###### 表

列出当前数据库的所有表

```
SHOW TABLES;
```

查看一个表的结构

```
DESC <表名>；
```

查看创建表的SQL语句

```
SHOW CREATE TABLE <表名>；
```

创建表

```
CREATE TABLE <表名>；
```

删除表

```
DROP TABLE  <表名>；
```

修改表，新增birth列

```
ALTER TABLE students ADD COLUMN birth VARCHAR(10) NOT NILL;
```

修改列名如birth改为birthday，类型改为VARCHAR(20)

```
ALTER TABLE students CHANGE COLUMN birth birthday VARCHAR(20) NOT NULL;
```

删除列

```
ALTER TABLE students DROP COLUMN birthday;
```

##### 实用SQL语句

插入或替换(插入新记录)

```
REPLACE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
```

[^]: 如id=1 记录不存在则插入新记录，否则先删除当前记录并插入新记录

插入或更新

```
INSERT INTO students （id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99) ON DUPLICATE KEY UPDATE name='小明', gender='F', score=99;
```

[^ ]: 如id=1记录不存在，INSERT语句将插入新记录，否则，将更新，字段由UPDATE指定

插入或忽略

```
INSERT IGNORE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
```

[^ ]: 如id=1记录不存在，则插入新记录，否则不执行任何操作

快照

```
	对class_id=1的记录进行快照，并储存为新表students_of_class1
CREATE TABLE studnets_of_class1 SELECT * FROM students WHERE class_id=1;
```

[^ ]: 新创建的表结构和SELECT 使用的表结构完全一致

写入查询结果集

创建一个统计成绩的表students，记录各班的平均成绩

```
CREATE TABLE statistics(
	id BIGINT NOT UNLL AUTO_INCREMENT, 自增整数类型
	class_id BIGINT NOT UNLL,  输入格
	average DOUBLE NOT UNLL, 输入格
	PRIMARY KEY (id) 主键为id
);
```

写入各班的平均成绩

```
INSERT INTO statistics (class_id, average) SELECT class_id, AVG(score) FROM students GROUP BY class_id;
```

确保INSERT语句的列和SELECT语句的列能一一对应，就可以在statistics表中直接保存查询结果

```
> SELECT * FROM statistics;
+----+----------+--------------+
| id | class_id | average      |
+----+----------+--------------+
|  1 |        1 |         86.5 |
|  2 |        2 | 73.666666666 |
|  3 |        3 | 88.333333333 |
+----+----------+--------------+
3 rows in set (0.00 sec)
```

强制使用指定索引

```
SELECT * FROM students FORCE INDEX (idx_class_id) WHERE class_id=1 ORDER BY id DESC;
```

##### 事务

[^ ]:可见，数据库事务具有ACID这4个特性：
[^]:A：Atomic，原子性，将所有SQL作为原子工作单元执行，要么全部执行，要么全部不执行；
[^]:C：Consistent，一致性，事务完成后，所有数据的状态都是一致的，即A账户只要减去了100，B账户则必定加上了100；
[^]:I：Isolation，隔离性，如果有多个事务并发执行，每个事务作出的修改必须与其他事务隔离；
[^]:D：Duration，持久性，即事务完成后，对数据库数据的修改被持久化存储

显式事务

```
BEGIN; # 开启一个事务
UPDATE accouts SET balance = balance - 100 WHERE id=1; id=1的账号减100
UPDATE accouts SET balance = balance + 100 HWERE id=2; id=2的账号加100
COMMIT; # 指提交事务，若失败，整个事务也会失败
```

如主动让事务失败，用ROLLBACK回滚事务

```
BEGIN; 
UPDATE accounts SET balance = balance - 100 WHERE id=1;
UPDATE accounts SET balance =balance + 100 WHERE id=2;
ROLLBACK; # 主动让事务失败
```

隔离级别

SQL标准定义了4种隔离级别，分别对应可能出现的数据不一致的情况：

| Isolation Level  | 脏读（Dirty Read） | 不可重复读（Non Repeatable Read） | 幻读（Phantom Read） |
| :--------------- | :----------------- | :-------------------------------- | :------------------- |
| Read Uncommitted | Yes                | Yes                               | Yes                  |
| Read Committed   | -                  | Yes                               | Yes                  |
| Repeatable Read  | -                  | -                                 | Yes                  |
| Serializable     | -                  | -                                 | -                    |

###### Read Uncommitted 

[^ ]: Read Uncommitted是隔离级别最低的一种事务级别。在这种隔离级别下，一个事务会读到另一个事务更新后但未提交的数据，如果另一个事务回滚，那么当前事务读到的数据就是脏数据，这就是脏读（Dirty Read）

| 时刻 | 事务A                                                        | 事务B                                             |
| :--- | :----------------------------------------------------------- | :------------------------------------------------ |
| 1    | SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED; 设置隔离级别 | SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED; |
| 2    | BEGIN; 开启事务                                              | BEGIN;                                            |
| 3    | UPDATE students SET name = 'Bob' WHERE id = 1; 修改id=1的数据 |                                                   |
| 4    |                                                              | SELECT * FROM students WHERE id = 1;查询数据      |
| 5    | ROLLBACK; 回滚数据                                           |                                                   |
| 6    |                                                              | SELECT * FROM students WHERE id = 1;              |
| 7    |                                                              | COMMIT; 提交事务                                  |

[^ ]:  当事务A执行完第3步时，它更新了`id=1`的记录，但并未提交，而事务B在第4步读取到的数据就是未提交的数据。随后，事务A在第5步进行了回滚，事务B再次读取`id=1`的记录，发现和上一次读取到的数据不一致，这就是脏读。可见，在Read Uncommitted隔离级别下，一个事务可能读取到另一个事务更新但未提交的数据，这个数据有可能是脏数据

###### Read Committed 

[^ ]:在Read Committed隔离级别下，一个事务可能会遇到不可重复读（Non Repeatable Read）的问题.不可重复读是指，在一个事务内，多次读同一数据，在这个事务还没有结束时，如果另一个事务恰好修改了这个数据，那么，在第一个事务中，两次读取的数据就可能不一致

| 时刻 | 事务A                                           | 事务B                                           |
| :--- | :---------------------------------------------- | :---------------------------------------------- |
| 1    | SET TRANSACTION ISOLATION LEVEL READ COMMITTED; | SET TRANSACTION ISOLATION LEVEL READ COMMITTED; |
| 2    | BEGIN;                                          | BEGIN;                                          |
| 3    |                                                 | SELECT * FROM students WHERE id = 1;            |
| 4    | UPDATE students SET name = 'Bob' WHERE id = 1;  |                                                 |
| 5    | COMMIT;                                         |                                                 |
| 6    |                                                 | SELECT * FROM students WHERE id = 1;            |
| 7    |                                                 | COMMIT;                                         |

[^ ]:当事务B第一次执行第3步的查询时，得到的结果是`Alice`，随后，由于事务A在第4步更新了这条记录并提交，所以，事务B在第6步再次执行同样的查询时，得到的结果就变成了`Bob`，因此，在Read Committed隔离级别下，事务不可重复读同一条记录，因为很可能读到的结果不一致

###### Repeatable Read

[^ ]:在Repeatable Read隔离级别下，一个事务可能会遇到幻读（Phantom Read）的问题。幻读是指，在一个事务中，第一次查询某条记录，发现没有，但是，当试图更新这条不存在的记录时，竟然能成功，并且，再次读取同一条记录，它就神奇地出现了

| 时刻 | 事务A                                               | 事务B                                             |
| :--- | :-------------------------------------------------- | :------------------------------------------------ |
| 1    | SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;    | SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;  |
| 2    | BEGIN;                                              | BEGIN;                                            |
| 3    |                                                     | SELECT * FROM students WHERE id = 99;             |
| 4    | INSERT INTO students (id, name) VALUES (99, 'Bob'); |                                                   |
| 5    | COMMIT;                                             |                                                   |
| 6    |                                                     | SELECT * FROM students WHERE id = 99;             |
| 7    |                                                     | UPDATE students SET name = 'Alice' WHERE id = 99; |
| 8    |                                                     | SELECT * FROM students WHERE id = 99;             |
| 9    |                                                     | COMMIT;                                           |

[^ ]:事务B在第3步第一次读取`id=99`的记录时，读到的记录为空，说明不存在`id=99`的记录。随后，事务A在第4步插入了一条`id=99`的记录并提交。事务B在第6步再次读取`id=99`的记录时，读到的记录仍然为空，但是，事务B在第7步试图更新这条不存在的记录时，竟然成功了，并且，事务B在第8步再次读取`id=99`的记录时，记录出现了.可见，幻读就是没有读到的记录，以为不存在，但其实是可以更新成功的，并且，更新成功后，再次读取，就出现了

###### Serializable

[^ ]:Serializable是最严格的隔离级别。在Serializable隔离级别下，所有事务按照次序依次执行，因此，脏读、不可重复读、幻读都不会出现,但效率大大下降
[^ ]:默认隔离级别，使用InnoDB 级别为Repeatable Read

