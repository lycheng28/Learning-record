

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