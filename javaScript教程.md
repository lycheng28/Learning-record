### javascript教程

#### 快速入门

第一种 直接将JavaScript代码放到<head>中

<head> // 标题
    <script> // javascript代码范围
        alert('Hello, world'); // alert 弹窗
    </script> 
</head>

第二种 将JavaScript代码放进单独的.js文件

<head>
    <script src="/static/js/abc.js"></script>
    <!-- 通过script引入所需要的文件-->
</head>

<script type="text/javascript"> 
//默认就是JavaScript，不需要显式把type指定为JavaScript
</script>

#### 基本语法

赋值

```html
var x = 1;
```

字符串依然可以视为完整的语句

```html
'Hello, world';
```

一行代码可包含多语句，用；表示语句结束

```
var x = 1; var y = 2; //不建议一行写多个语句
```

语句块是一组语句的集合

```
if (2 > 1) { 
	x = 1;
	y = 2;
	z = 3;
} //先判断条件是否成立，再执行{...}中的语句内容
```

[^]: 花括号{...}内的语句具有缩进，通常为4个空格，但缩进不是javascript语法必要的

{...}可以嵌套，形成层级结构

```
if (2 > 1) {
	x = 1;
	y = 2;
	z = 3;
	if (x < y) {
		z = 4;
	}
	if (x > y) {
		z = 5;
	}
}
```

注释

```
以//开头直到行末都字符被视为注释，javascript自动忽略

以/*...*/将多行字符包裹起来，把一大块视为一个注释
```

#### 数据类型和变量

##### 数据类型

###### Number

```
javascript不区分整数和浮点数，统一用 Nnmber 表示，以下均是合法的Number类型
123； //int 整数
0.456; //float 浮点数
1.2345e3；// 科学计数法表示1.2345x1000. 等同于1234.5
-99;// 负数
NaN; //NaN表示Not a Number, 当无法计算结果时用NaN表示
Infinity;// 表示无限大，当数值超过JavaScript的Number所能表示的最大值时，表示为Infinity
```

Number可以做四则运算，规则和数学一致

```
1 + 2; // 3
(1 + 2) * 5 / 2; // 7.5
2 / 0; // Infinity
0 / 0; // NaN
10 % 3; // 1
10.5 % 3; // 1.5 //%是求余运算
```

###### 字符串

以单引号’或双引号"括起来的任意文本，如’abc‘

###### 布尔值

布尔值和布尔代数的表示完全一致，只有true，false两种

```
true; //这是一个true值
false; //这是一个false值
2 > 1;// ture值
2 >= 3;// false值
```

###### &&运算是与(和, and)运算，只有所有都为true，&&运算结果才是true

```
true && true; // 这个结果为true
true && false; // 为false
false && true && false; //为false
```

###### ||运算是或(or)运算, 只要其中有一个为true，||运算结果为true

```
false || false; // 结果为false
true || false;// true
false || true || false; // true
```

###### !运算是非(!=)运算，它是一个单目运算符，把true变为false，false变为true

```
! true; // 结果为false
! false; // true
! (2 > 5); // true
```

布尔值经常用在条件判断

```
var age = 15;
if (age >= 18) {
	alert('adult');
} else {
	alert('teenager');
}
```

