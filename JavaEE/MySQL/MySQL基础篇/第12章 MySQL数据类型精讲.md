# 第12章 MySQL数据类型精讲

## 一、MySQL中的数据类型

| 类型             | 举例                                                         |
| ---------------- | ------------------------------------------------------------ |
| 整数类型         | TINYINT、SMALLINT、MEDIUMINT、INT(或INTEGER)、BIGINT         |
| 浮点类型         | FLOAT、DOUBLE                                                |
| 定点数类型       | DECIMAL                                                      |
| 位类型           | BIT                                                          |
| 日期时间类型     | YEAR、TIME、DATE、DATETIME、TIMESTAMP                        |
| 文本字符串类型   | CHAR、VARCHAR、TINYTEXT、TEXT、MEDIUMTEXT、LONGTEXT          |
| 枚举类型         | ENUM                                                         |
| 集合类型         | SET                                                          |
| 二进制字符串类型 | BINARY、VARBINARY、TINYBLOB、BLOB、MEDIUMBLOB、LONGBLOB      |
| JSON类型         | JSON对象、JSON数组                                           |
| 空间数据类型     | 单值类型：GEOMETRY、POINT、LINESTRING、POLYGON； <br />集合类型：MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、 GEOMETRYCOLLECTION |

常见数据类型的属性，如下：

| MySQL关键字        | 含义                     |
| ------------------ | ------------------------ |
| NULL               | 数据列可包含NULL值       |
| NOT NULL           | 数据列不允许包含NULL值   |
| DEFAULT            | 默认值                   |
| PRIMARY KEY        | 主键                     |
| AUTO_INCREMENT     | 自动递增，适用于整数类型 |
| UNSIGNED           | 无符号                   |
| CHARACTER SET name | 指定一个字符集           |



## 二、整数类型

### 2.1 类型介绍

整数类型一共有 5 种，包括 TINYINT、SMALLINT、MEDIUMINT、INT（INTEGER）和 BIGINT。 

它们的区别如下表所示：

| 整数类型     | 字节 | 有符号数取值范围                         | 无符号数取值范围       |
| ------------ | ---- | ---------------------------------------- | ---------------------- |
| TINYINT      | 1    | -128~127                                 | 0~255                  |
| SMALLINT     | 2    | -32768~32767                             | 0~65535                |
| MEDIUMINT    | 3    | -8388608~8388607                         | 0~16777215             |
| INT、INTEGER | 4    | -2147483648~2147483647                   | 0~4294967295           |
| BIGINT       | 8    | -9223372036854775808~9223372036854775807 | 0~18446744073709551615 |



### 2.2 可选属性

整数类型的可选属性有三个：

#### 1） `M`

​		`M` : 表示显示宽度，M的取值范围是(0， 255)。例如，int(5)：当数据宽度小于5位的时候在数字前面需要用 字符填满宽度。该项功能需要配合“ `ZEROFILL` ”使用，**表示用“0”填满宽度**，**否则指定显示宽度无效**。 

​		<u>思考：如果设置了显示宽度，那么插入的数据宽度超过显示宽度限制，会不会被截断或插入失败？</u> 

​		答案：不会对插入的数据有任何影响，还是按照类型的实际宽度进行保存。即 **<font color="blue">显示指定的宽度（M）与可以存储的数值范围无关</font>** 。从**MySQL 8.0.17**开始，**整数数据类型不推荐使用显示宽度属性**。 

​		整型数据类型可以在定义表结构时指定所需要的显示宽度，如果不指定，则系统为每一种类型指定默认 的宽度值。还是那句话，指不指定，都差不多。

举例：

```mysql
CREATE TABLE test_int1 ( x TINYINT， y SMALLINT， z MEDIUMINT， m INT， n BIGINT );
```

查看表结构 （**<font color="red">MySQL5.7中显式如下，MySQL8中不再显式范围</font>**）注意：只是针对==整数类型==不再显示范围！！！

```mysql
# MySQL5.7
mysql> DESC test;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| DEPTNO | int(2)      | NO   |     | NULL    |       |
| DNAME  | varchar(14) | YES  |     | NULL    |       |
| LOC    | varchar(13) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+

# MySQL8.x
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| DEPTNO | int         | NO   |     | NULL    |       |
| DNAME  | varchar(14) | YES  |     | NULL    |       |
| LOC    | varchar(13) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
```

​		TINYINT有符号数和无符号数的取值范围分别为-128\~127和0\~255，由于**负号占了一个数字位**，因此 **TINYINT默认的显示宽度为4（负号1位+数值长度最大3位）**。同理，**其他整数类型的默认显示宽度与其有符号数的最小值的宽度相同。**

---



- 举例

```mysql
CREATE TABLE test_int2(
    f1 INT,
    f2 INT(5),
    f3 INT(5) ZEROFILL
)

mysql> desc test_int2;
+-------+--------------------------+------+-----+---------+-------+
| Field | Type                     | Null | Key | Default | Extra |
+-------+--------------------------+------+-----+---------+-------+
| f1    | int(11)                  | YES  |     | NULL    |       |
| f2    | int(5)                   | YES  |     | NULL    |       |
| f3    | int(5) unsigned zerofill | YES  |     | NULL    |       |
+-------+--------------------------+------+-----+---------+-------+
3 rows in set (0.00 sec)


INSERT INTO test_int2(f1,f2,f3) VALUES(1,123,123);
INSERT INTO test_int2(f1,f2) VALUES(123456,123456);
INSERT INTO test_int2(f1,f2,f3) VALUES(123456,123456,123456);

mysql> SELECT * FROM test_int2;
+--------+--------+--------+
| f1     | f2     | f3     |
+--------+--------+--------+
| 1      | 123    | 00123  |
| 123456 | 123456 | NULL   |
| 123456 | 123456 | 123456 |
+--------+--------+--------+
3 rows in set (0.00 sec)
```



#### 2）`UNSIGNED`

​		UNSIGNED : 无符号类型（非负）。所有的整数类型都有一个可选的属性UNSIGNED（无符号属性），无 符号整数类型的**最小取值为0**。所以，如果需要在MySQL数据库中保存非负整数值时，可以将整数类型设 置为无符号类型。

​		**<font color="green">int类型默认显示宽度为int(11)，无符号int类型默认显示宽度为int(10)。</font>**



- 举例

```mysql
CREATE TABLE test_int3(
    f1 INT UNSIGNED
);

mysql> desc test_int3;
+-------+------------------+------+-----+---------+-------+
| Field |       Type       | Null | Key | Default | Extra |
+-------+------------------+------+-----+---------+-------+
| f1    | int(10) unsigned | YES  |     | NULL    |       |
+-------+------------------+------+-----+---------+-------+
1 row in set (0.00 sec)
```





#### 3）`ZEROFILL`

​		ZEROFILL : 0填充，（**如果某列是ZEROFILL，那么MySQL会<font color="blue">自动</font>为当前列添加UNSIGNED属性**），如果指 定了ZEROFILL只是表示不够M位时，用0在左边填充，如果超过M位，**<font color="red">只要不超过数据存储范围即可。</font>** 

> ​		注意：在 int(M) 中，**M 的值跟 int(M) 所占多少存储空间并无任何关系**。 int(3)、int(4)、int(8) 在磁盘上**都是占用 4 bytes 的存储空间**。也就是说，如果声明为`int(M)`，必须和`UNSIGNED ZEROFILL`一起使用才有意义。<u>即：如果整数值没有超过M位，就用0左边填充；如果整数值超过M位，就按照实际位数存储，而无须再用字符 0 进行填充。</u>



### 2.3 适用场景

TINYINT ：一般用于枚举数据，比如系统设定取值范围很小且固定的场景。 

SMALLINT ：可以用于较小范围的统计数据，比如统计工厂的固定资产库存数量等。 

MEDIUMINT ：用于较大整数的计算，比如车站每日的客流量等。 

INT、INTEGER ：取值范围足够大，一般情况下不用考虑超限问题，用得最多。比如商品编号。 

BIGINT ：只有当你处理特别巨大的整数时才会用到。比如双十一的交易量、大型门户网站点击量、证 券公司衍生产品持仓等。



### 2.4 如何选择？

​		在评估用哪种整数类型的时候，你需要考虑 `存储空间` 和 `可靠性` 的平衡问题。一方面，用占用字节数少的整数类型可以节省存储空间；另一方面，要是为了节省存储空间， 使用的整数类型取值范围太小，一 旦遇到超出取值范围的情况，就可能引起系统错误 ，影响可靠性。 

​		举个例子，商品编号采用的数据类型是 INT。原因就在于，客户门店中流通的商品种类较多，而且，每 天都有旧商品下架，新商品上架，这样不断迭代，日积月累。 

如果使用 SMALLINT 类型，虽然占用字节数比 INT 类型的整数少，但是却不能保证数据不会超出范围 65535。相反，使用 INT，就能确保有足够大的取值范围，不用担心数据超出范围影响可靠性的问题。 

​		你要注意的是，在实际工作中，**系统故障产生的成本远远超过增加几个字段存储空间所产生的成本**。因此，我建议你**<font color="red">首先确保数据不会超过取值范围</font>**，在这个前提之下，再去考虑如何节省存储空间。



## 三、浮点类型

### 3.1 类型介绍

浮点数和定点数类型的特点是可以 处理小数 ，你可以把整数看成小数的一个特例。因此，浮点数和定点 数的使用场景，比整数大多了。 MySQL支持的浮点数类型，分别是 FLOAT、DOUBLE、REAL。

* FLOAT 表示单精度浮点数； 

* DOUBLE 表示双精度浮点数；

* `REAL默认就是 DOUBLE`。如果你把 SQL 模式设定为启用“ REAL_AS_FLOAT ”，那 么，MySQL 就认为 REAL 是 FLOAT。如果要启用“REAL_AS_FLOAT”，可以通过以下 SQL 语句实现：

    ```mysql
    SET sql_mode = “REAL_AS_FLOAT”;
    ```

    ![image-20221226095358936](C:/Users/陆俊/Desktop/无尽探索/MySQL/MySQL基础篇/MySQL基础篇.assets/image-20221226095358936.png)

    > ​		**问题1**：FLOAT 和 DOUBLE 这两种数据类型的区别是啥呢？
    >
    > ​		回答：FLOAT 占用字节数少，取值范围小；DOUBLE 占用字节数多，取值范围也大。
    >
    > ---
    >
    > ​		**问题2：为什么浮点数类型的无符号数取值范围，只相当于有符号数取值范围的一半，也就是只相当于有符号数取值范围大于等于零的部分呢**？
    >
    > ​		回答：MySQL 存储浮点数的格式为： **符号(S) 、 尾数(M) 和 阶码(E)** 。因此，无论有没有符号，MySQL 的浮点数都会存储表示符号的部分。因此， **所谓的无符号数取值范围，其实就是有符号数取值范围大于等于 零的部分**。

    

    

### 3.2 数据精度说明

对于浮点类型，在MySQL中**单精度值使用 4 个字节，双精度值使用 8 个字节。**

* MySQL允许使用 `非标准语法：FLOAT(M，D) 或 DOUBLE(M，D)` （其他数据库未必支持，因此如果涉及到数据迁移，则最好不要这么 用）。这里，M称为 精度 ，D称为 标度 。(M，D)中 M=整数位+小数位，D=小数位。 D<=M<=255，0<=D<=30。 <font color="orange">【注意：`标准语法`就和指定整形类型时一样，不需要指定任何精度范围，即不指定(M，D)。直接由计算机的默认环境决定精度。】</font>

    例如，定义为FLOAT(5，2)的一个列可以显示为-999.99-999.99。如果超过这个范围会报错。

* FLOAT和DOUBLE类型在不指定(M，D)时，默认会按照实际的精度（由实际的硬件和操作系统决定） 来显示。

* 说明：浮点类型，也可以加 UNSIGNED ，但是不会改变数据范围，例如：FLOAT(3，2) UNSIGNED仍然只能表示0-9.99的范围。还是那句话，**无论是不是被指定为无符号数，对于浮点类型而言，都需要存储表示符号的部分**。

* 不管是否显式设置了精度(M，D)，这里MySQL的处理方案如下：

    * 如果存储时，整数部分超出了范围，MySQL就会报错，不允许存这样的值
    * 如果存储时，小数点部分若超出范围，就分以下情况：
        + 若四舍五入后，整数部分没有超出范围，则只警告，但能成功操作并四舍五入删除多余 的小数位后保存。例如在FLOAT(5，2)列内插入999.009，近似结果是999.01。
        + 若四舍五入后，整数部分超出范围，则MySQL报错，并拒绝处理。如FLOAT(5，2)列内插入 999.995和-999.995都会报错。

* **从MySQL 8.0.17开始，FLOAT(M，D) 和DOUBLE(M，D)用法在官方文档中已经明确不推荐使用，将来可能被移除**。

* **关于浮点型FLOAT和DOUBLE的UNSIGNED也不推荐使用了，将来也可能被移除**。

---



- 举例

```mysql
CREATE TABLE test_double1(
    f1 FLOAT,
    f2 FLOAT(5,2),
    f3 DOUBLE,
    f4 DOUBLE(5,2)
);

mysql> desc test_double1;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| f1    | float       | YES  |     | NULL    |       |
| f2    | float(5,2)  | YES  |     | NULL    |       |
| f3    | double      | YES  |     | NULL    |       |
| f4    | double(5,2) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

INSERT INTO test_double1 VALUES(123.456,123.456,123.4567,123.45);

#Out of range value for column 'f2' at row 1
INSERT INTO test_double1 VALUES(123.456,1234.456,123.4567,123.45);
```





### 3.3 精度误差说明

​		**浮点数类型有个缺陷，就是不精准。**下面我来重点解释一下为什么 MySQL 的浮点数不够精准。比如，我 们设计一个表，有f1这个字段，插入值分别为0.47，0.44，0.19，我们期待的运行结果是：0.47 + 0.44 + 0.19 = 1.1。而使用sum之后查询：

> 
>
> - 使用`标准语法`定义浮点数类型，**存在误差**。这是底层存储的原因
>
> ```sql
> CREATE TABLE test_double2(
>     f1 DOUBLE
> );
> INSERT INTO test_double2
> VALUES(0.47)，(0.44)，(0.19);
> ```
>
> ```sql
> mysql> SELECT SUM(f1)
>        FROM test_double2;
> +--------------------+
> | SUM(f1) |
> +--------------------+
> | 1.0999999999999999 |
> +--------------------+
> 1 row in set (0.00 sec)
> ```
>
> 
>
> - 使用`非标准语法`定义浮点数据类型，在特殊情况下没有体现出误差。这是因为MySQL会进行数据范围内的四舍五入。四舍五入的结果可能**刚好**就等于没有误差的情况下的答案。
>
> ```sql
> CREATE TABLE test_double(
>     f1 DOUBLE(10， 6)
> );
> 
> INSERT INTO test_double
> VALUES(0.47)，(0.44)，(0.19);
> ```
>
> ```sql
> mysql> SELECT SUM(f1)
>        FROM test_double;
>        
> mysql>  SELECT SUM(f1)
>         FROM test_double;
> +----------+
> | SUM(f1)  |
> +----------+
> | 1.100000 |
> +----------+
> 1 row in set (0.00 sec)       
> ```
>
> 



虽然误差很小，但确实有误差。 将数据类型 改成 `FLOAT`后，再运行求和查询，得到的是， 1.0999999940395355。显然，误差更大了。

那么，为什么会存在这样的误差呢？问题还是出在 MySQL 对浮点类型数据的存储方式上。

> ​		MySQL 用 4 个字节存储 FLOAT 类型数据，用 8 个字节来存储 DOUBLE 类型数据。无论哪个，都是采用二 进制的方式来进行存储的。比如 9.625，用二进制来表达，就是 1001.101，或者表达成 1.001101×2^3。如 果尾数不是 0 或 5（比如 9.624），你就无法用一个二进制数来精确表达。进而，就只好在取值允许的范 围内进行四舍五入。
>
> ​		**凡是在编程中，用到了<u>浮点数</u>，要特别注意误差问题。**因为浮点数是不准确的，所以我们要避免使用“=”来 判断两个数是否相等。同时，在一些对精确度要求较高的项目中，千万不要使用浮点数，不然会导致结 果错误，甚至是造成不可挽回的损失。那么，MySQL 有没有精准的数据类型呢？当然有，这就是定点数 类型： DECIMAL 。



## 四、定点数类型

### 4.1 类型介绍

* MySQL中的定点数类型只有 `DECIMAL` 一种类型。

| 类型                     | 字节    | 有符号数取值范围   |
| ------------------------ | ------- | ------------------ |
| DECIMAL(M,D),DEC,NUMERIC | M+2字节 | 有效范围由M和D决定 |

> ​		**<font color="blue">使用 DECIMAL(M，D) 的方式表示高精度小数</font>**。其中，**M被称为精度，D被称为标度。**
>
> ​		 `0<=M<=65`， `0<=D<=30`，`D < M`。



* DECIMAL(M，D)的最大取值范围与DOUBLE类型一样，但是有效的数据范围是由M和D决定的。 **DECIMAL 的存储空间并不是固定的，由精度值M决定，总共占用的存储空间为M+2个字节。**也就是说，在一些对精度要求不高的场景下，比起占用同样字节长度的定点数，浮点数表达的<u>数值范围</u>可以更大一些。
* 定点数在MySQL内部是以 **<font color="green">字符串</font>** 的形式进行存储，这就决定了它一定是精准的。
* 当DECIMAL类型不指定精度和标度时，其默认为`DECIMAL(10，0)`。当数据的精度超出了定点数类型的精度范围时，则MySQL同样会进行四舍五入处理。

> #### 浮点数 vs 定点数
>
> * 浮点数相对于定点数的优点是在长度一定的情况下，浮点类型取值范围大，但是不精准，适用于需要取值范围大，又可以容忍微小误差的科学计算场景（比如计算化学、分子建模、流体动力学等）
> * 定点数类型取值范围相对小，但是精准，没有误差，适合于对精度要求极高的场景 （比如涉及金额计算的场景）

---

- 举例

```mysql
CREATE TABLE test_decimal1(
    f1 DECIMAL,     # 默认为(10, 0)
    f2 DECIMAL(5,2)
);

DESC test_decimal1;
INSERT INTO test_decimal1(f1,f2) VALUES(123.123,123.456);

#Out of range value for column 'f2' at row 1
INSERT INTO test_decimal1(f2) VALUES(1234.34);

mysql> SELECT * FROM test_decimal1;
+------+--------+
|   f1 |   f2   |
+------+--------+
| 123  | 123.46 |
+------+--------+
1 row in set (0.00 sec)
```



- 测试是否有精度误差

```mysql
CREATE TABLE test_decimal(
    f1 DECIMAL(5,2)
);
INSERT INTO test_decimal VALUES(0.47)，(0.44)，(0.19);


mysql> SELECT SUM(f1) FROM test_decimal;
+---------+
| SUM(f1) |
+---------+
|   1.10  |
+---------+
1 row in set (0.00 sec)


mysql> SELECT SUM(f1) = 1.1 FROM test_decimal;
+---------------+
| SUM(f1) = 1.1 |
+---------------+
|      1        |
+---------------+
1 row in set (0.00 sec)
```

> ​		非常精准，没有误差。



### 4.2 开发中的经验

> ​		“由于 DECIMAL 数据类型的精准性，在我们的项目中，除了极少数（比如商品编号）用到整数类型外，其他的数值都用的是 DECIMAL，原因就是这个项目所处的零售行业**要求精准**，一分钱也不能差。 ”
>
> ​																																				 ——来自某项目经理



## 五、位类型：BIT

BIT类型中存储的是**二进制值**，类似010110。

| 二进制字符串类型 | 长度 | 长度范围     | 占用空间            |
| ---------------- | ---- | ------------ | ------------------- |
| BIT(M)           | M    | 1 <= M <= 64 | 约为(M + 7)/8个字节 |

​		BIT类型，如果没有指定(M)，**默认是1位**。这个1位，表示只能存1位的二进制值。这里(M)是**<font color="red">表示二进制的位数</font>**，位数`最小值为1`，`最大值为64`。

---



- 举例

```mysql
CREATE TABLE test_bit1(
    f1 BIT,
    f2 BIT(5),
    f3 BIT(64)
);

INSERT INTO test_bit1(f1) VALUES(1);

#Data too long for column 'f1' at row 1 ：因为bit类型二进制位的默认长度为1
INSERT INTO test_bit1(f1) VALUES(2); 

INSERT INTO test_bit1(f2) VALUES(23);
```

> ​		注意：在向BIT类型的字段中插入数据时，一定要确保插入的数据在BIT类型支持的范围内。



- 使用`SELECT`命令查询`位字段`时，可以用 `BIN()` 或 `HEX()` 函数进行读取。

```mysql
# 不使用函数，直接查询。默认以【二进制】类型的形式输出数据
mysql> SELECT * FROM test_bit1;
+------------+------------+------------+
|      f1    |      f2    |      f3    |
+------------+------------+------------+
|     1      |    NULL    |     NULL   |
|    NULL    |    10111   |     NULL   |
+------------+------------+------------+
2 rows in set (0.00 sec)

---

# bin：二进制
# hex：十六进制
# oct：八进制
mysql> SELECT BIN(f2), HEX(f2) FROM test_bit1;
+---------+---------+---------+
| BIN(f2) | HEX(f2) | OCT(f2) |
+---------+---------+---------+
|   NULL  |   NULL  |   NULL  |
|  10111  |    17   |    27   |
+---------+---------+---------+
2 rows in set (0.00 sec)

---

# 十进制
# 使用 b+0 查询数据时，可以直接查询出存储的【十进制】数据的值。（相当于java中的数据类型求和规律）
mysql> SELECT f2 + 0 FROM test_bit1;
+--------+
| f2 + 0 |
+--------+
|  NULL  |
|   23   |
+--------+
2 rows in set (0.00 sec)
```



## 六、日期与时间类型

​		日期与时间是重要的信息，在我们的系统中，几乎所有的数据表都用得到。原因是<u>客户需要知道数据的时间标签，从而进行数据查询、统计和处理。</u>

​		MySQL有多种表示日期和时间的数据类型，不同的版本可能有所差异，MySQL8.0版本支持的日期和时间类型主要有：YEAR类型、TIME类型、DATE类型、DATETIME类型和TIMESTAMP类型。

* `YEAR` 类型通常用来表示年 
* `DATE` 类型通常用来表示年、月、日 
* `TIME` 类型通常用来表示时、分、秒 
* `DATETIME` 类型通常用来表示年、月、日、时、分、秒 
* `TIMESTAMP` 类型通常用来表示**带时区的年、月、日、时、分、秒**。（时间戳）

| 类型      | 名称     | 字节 | 日期格式            | 最小值                  | 最大值                 |
| --------- | -------- | ---- | ------------------- | ----------------------- | ---------------------- |
| YEAR      | 年       | 1    | YYYY或YY            | 1901                    | 2155                   |
| TIME      | 时间     | 3    | HH:MM:SS            | -838:59:59              | 838:59:59              |
| DATE      | 日期     | 3    | YYYY-MM-DD          | 1000-01-01              | 9999-12-03             |
| DATETIME  | 日期时间 | 8    | YYYY-MM-DD HH:MM:SS | 1000-01-01 00:00:00     | 9999-12-31 23:59:59    |
| TIMESTAMP | 日期时间 | 4    | YYYY-MM-DD HH:MM:SS | 1970-01-01 00:00:00 UTC | 2038-01-19 03:14:07UTC |

​		可以看到，不同数据类型表示的时间内容不同、取值范围不同，而且占用的字节数也不一样，你要根据实际需要灵活选取。

​		为什么时间类型 TIME 的取值范围不是 -23:59:59～23:59:59 呢？原因是 **MySQL 设计的 TIME 类型，不光表示一天之内的时间，而且可以用来表示一个时间间隔，这个时间间隔可以超过 24 小时。**

---



### 6.1 YEAR类型

​		YEAR类型用来表示**年份**，在所有的日期时间类型中所占用的存储空间最小，只需要 **1个字节** 的存储空间。 在MySQL中，YEAR有以下几种存储格式：

- 以4位字符串或数字格式表示YEAR类型，其格式为`YYYY`，`最小值为1901`，`最大值为2155`。
- 以2位字符串格式表示YEAR类型，`最小值为00`，`最大值为99`。
    - 当取值为01到69时，表示2001到2069
    - 当取值为70到99时，表示1970到1999
    - 当取值整数的0或00添加的话，那么是0000年
    - 当取值是日期/字符串的'0'添加的话，是2000年



​		**从MySQL 5.5.27开始，2位格式的YEAR已经不推荐使用**。YEAR默认格式就是`“YYYY”`，没必要写成YEAR(4)， 从MySQL 8.0.19开始，不推荐使用指定显示宽度的YEAR(4)数据类型。

```mysql
CREATE TABLE test_year(
    f1 YEAR,
    f2 YEAR(4)  # 不推荐使用
);

mysql> DESC test_year;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| f1    | year(4) | YES  |     | NULL    |       |
| f2    | year(4) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
2 rows in set (0.00 sec)

INSERT INTO test_year VALUES('2020','2021');

mysql>  SELECT * FROM test_year;
+------+------+
| f1   | f2   |
+------+------+
| 2020 | 2021 |
+------+------+
1 row in set (0.00 sec)

---

INSERT INTO test_year VALUES('45','71');
INSERT INTO test_year VALUES(0,'0');

mysql>  SELECT * FROM test_year;
+------+------+
| f1   | f2   |
+------+------+
| 2020 | 2021 |
| 2045 | 1971 |
| 0000 | 2000 |
+------+------+
3 rows in set (0.00 sec)
```



### 6.2 DATE类型

​		DATE类型表示日期，没有时间部分，格式为 `YYYY-MM-DD` 。其中，YYYY表示年份，MM表示月份，DD表示日期。需要 **3个字节** 的存储空间。在向DATE类型的字段插入数据时，同样需要满足一定的格式条件。

- 以 `YYYY-MM-DD` 格式或者 `YYYYMMDD` 格式表示的字符串日期，其最小取值为1000-01-01，最大取值为
    9999-12-03。YYYYMMDD格式会被转化为YYYY-MM-DD格式。【所以：请尽量写成`YYYY-MM-DD` 格式】
- 以 `YY-MM-DD` 格式或者 `YYMMDD` 格式表示的字符串日期，此格式中，年份为两位数值或字符串满足
    YEAR类型的格式条件为：当年份取值为00到69时，会被转化为2000到2069；当年份取值为70到99
    时，会被转化为1970到1999。
- 使用 `CURRENT_DATE()` 或者 `NOW()` 函数，会插入当前系统的日期。	

---



**举例**：

- 创建数据表，表中只包含一个DATE类型的字段f1

```mysql
CREATE TABLE test_date1(
    f1 DATE
);
```



- 插入数据

```mysql
# 日期上不带 '' 的写法并不推荐使用，底层仍然会存在【隐式转换】
INSERT INTO test_date1 VALUES ('2020-10-01'), ('20201001'),(20201001);

INSERT INTO test_date1 VALUES ('00-01-01'), ('000101'), ('69-10-01'), ('691001'), ('70-01-01'), ('700101'),('99-01-01'), ('990101');

INSERT INTO test_date1 VALUES (000301), (690301), (700301), (990301);

INSERT INTO test_date1 VALUES (CURRENT_DATE()), (NOW());
```



- 查询数据

```mysql
SELECT * FROM test_date1;

mysql> SELECT * FROM test_date1;
+------------+
| f1         |
+------------+
| 2020-10-01 |
| 2020-10-01 |
| 2020-10-01 |
| 2000-01-01 |
| 2000-01-01 |
| 2069-10-01 |
| 2069-10-01 |
| 1970-01-01 |
| 1970-01-01 |
| 1999-01-01 |
| 1999-01-01 |
| 2000-03-01 |
| 2069-03-01 |
| 1970-03-01 |
| 1999-03-01 |
| 2022-12-26 |
| 2022-12-26 |
+------------+
17 rows in set (0.00 sec)
```



### 6.3 TIME类型

​		TIME类型用来表示时间，即：`时分秒`。在MySQL中，需要 `3个字节` 的存储空间来存储TIME类型的数
据，可以使用“`HH:MM:SS`”格式来表示TIME类型，其中，HH表示小时，MM表示分钟，SS表示秒。

​		在MySQL中，向TIME类型的字段插入数据时，也可以使用几种不同的格式。 

- 可以使用带有冒号的字符串，比如`'D HH:MM:SS'` 、`'HH:MM:SS'`、`'HH:MM'`、`'D HH:MM'`、`'D HH'`或`'SS'`格式，都能被正确地插入TIME类型的字段中。其中**D表示天**，其**最小值为0**，**最大值为34**。如果使用带有D格式的字符串插入TIME类型的字段时，D会被转化为小时，计算格式为`D*24+HH`。当使用带有冒号并且不带D的字符串表示时间时，表示当天的时间，比如12:10表示12:10:00，而不是00:12:10。
- 可以使用不带有冒号的字符串或者数字，格式为`'HHMMSS'`或者 `HHMMSS` 。如果插入一个不合法的字符串或者数字，MySQL在存储数据时，会将其自动转化为00:00:00进行存储。比如1210，MySQL会将最右边的两位解析成秒，表示00:12:10，而不是12:10:00。 
- 使用 `CURRENT_TIME()` 或者 `NOW()` ，会插入当前系统的时间。

---



**举例**：

- 创建数据表，表中包含一个TIME类型的字段f1。

```mysql
CREATE TABLE test_time1(
    f1 TIME
);
```



- 插入数据

```mysql
INSERT INTO test_time1 VALUES('2 12:30:29'), ('12:35:29'), ('12:40'), ('2 12:40'),('1 05'), ('45');
INSERT INTO test_time1 VALUES ('123520'), (124011),(1210);
INSERT INTO test_time1 VALUES (NOW()), (CURRENT_TIME());
```



- 查询数据

```mysql
mysql> SELECT * FROM test_time1;
+----------+
|    f1    |
+----------+
| 60:30:29 |
| 12:35:29 |
| 12:40:00 |
| 60:40:00 |
| 29:00:00 |
| 00:00:45 |
| 12:35:20 |
| 12:40:11 |
| 00:12:10 |
| 12:30:09 |
| 12:30:09 |
+----------+
11 rows in set (0.00 sec)
```





### 6.4 DATETIME类型

​		DATETIME类型在所有的日期时间类型中占用的存储空间最大，总共需要 `8 个字节`的存储空间。**在格式上 为DATE类型和TIME类型的组合**，可以表示为 `YYYY-MM-DD HH:MM:SS` ，其中YYYY表示年份，MM表示月 份，DD表示日期，HH表示小时，MM表示分钟，SS表示秒。

​		在向DATETIME类型的字段插入数据时，同样需要满足一定的格式条件。

- 以 `YYYY-MM-DD HH:MM:SS` 格式或者 `YYYYMMDDHHMMSS` 格式的字符串插入DATETIME类型的字段时， **最小值为1000-01-01 00:00:00，最大值为9999-12-03 23:59:59**。
    - 以`YYYYMMDDHHMMSS`格式的数字插入字段时，会被自动转化为`YYYY-MM-DD HH:MM:SS`格式。
    - 以 `YY-MM-DD HH:MM:SS` 格式或者 `YYMMDDHHMMSS` 格式的字符串插入DATETIME类型的字段时，两位 数的年份规则符合YEAR类型的规则，00到69表示2000到2069；70到99表示1970到1999。
    - 使用函数 `CURRENT_TIMESTAMP()` 和 `NOW()` ，可以向DATETIME类型的字段插入系统的当前日期和 时间。

---



**举例**：

- 创建数据表，表中包含一个DATETIME类型的字段dt

```mysql
CREATE TABLE test_datetime1(
    dt DATETIME
);
```



- 插入数据

```mysql
INSERT INTO test_datetime1 VALUES ('2021-01-01 06:50:30'), ('20210101065030');
INSERT INTO test_datetime1 VALUES ('99-01-01 00:00:00'), ('990101000000'), ('20-01-01 00:00:00'),('200101000000');
INSERT INTO test_datetime1 VALUES (20200101000000), (200101000000), (19990101000000), (990101000000);
INSERT INTO test_datetime1 VALUES (CURRENT_TIMESTAMP()), (NOW());
```



- 查询数据

```mysql
mysql> SELECT * FROM test_datetime1;
+---------------------+
| dt                  |
+---------------------+
| 2021-01-01 06:50:30 |
| 2021-01-01 06:50:30 |
| 1999-01-01 00:00:00 |
| 1999-01-01 00:00:00 |
| 2020-01-01 00:00:00 |
| 2020-01-01 00:00:00 |
| 2020-01-01 00:00:00 |
| 2020-01-01 00:00:00 |
| 1999-01-01 00:00:00 |
| 1999-01-01 00:00:00 |
| 2022-12-26 15:20:25 |
| 2022-12-26 15:20:25 |
+---------------------+
12 rows in set (0.00 sec)
```



### 6.5 TIMESTAMP类型

​		TIMESTAMP类型也可以表示日期时间，其显示格式与DATETIME类型相同，都是 `YYYY-MM-DD HH:MM:SS` ，需要4个字节的存储空间。但是TIMESTAMP存储的时间范围比DATETIME要小很多，只能存储 “1970-01-01 00:00:01 UTC”到“2038-01-19 03:14:07 UTC”之间的时间。其中，**UTC表示世界统一时间**，也叫作世界标准时间。 

​		存储数据的时候需要对当前时间所在的时区进行转换，查询数据的时候再将时间转换回当前的时区。因此，使用TIMESTAMP存储的同一个时间值，在不同的时区查询时会显示不同的时间。 

​		向TIMESTAMP类型的字段插入数据时，当插入的数据格式满足YY-MM-DD HH:MM:SS和YYMMDDHHMMSS 时，两位数值的年份同样符合YEAR类型的规则条件，只不过表示的时间范围要小很多。 

​		如果向TIMESTAMP类型的字段插入的时间超出了TIMESTAMP类型的范围，则MySQL会抛出错误信息。

---



**举例**：

- 创建数据表，表中包含一个TIMESTAMP类型的字段ts。

```mysql
CREATE TABLE test_timestamp1(
    ts TIMESTAMP
);
```



- 插入数据：

```mysql
INSERT INTO test_timestamp1 VALUES ('1999-01-01 03:04:50'), ('19990101030405'), ('99-01-01 03:04:05'),('990101030405');

INSERT INTO test_timestamp1 VALUES ('2020@01@01@00@00@00'), ('20@01@01@00@00@00');

INSERT INTO test_timestamp1 VALUES (CURRENT_TIMESTAMP()), (NOW());

#Incorrect datetime value
INSERT INTO test_timestamp1 VALUES ('2038-01-20 03:14:07');
```



- 查询数据

```mysql
mysql> SELECT * FROM test_timestamp1;
+---------------------+
| ts                  |
+---------------------+
| 1999-01-01 03:04:50 |
| 1999-01-01 03:04:05 |
| 1999-01-01 03:04:05 |
| 1999-01-01 03:04:05 |
| 2020-01-01 00:00:00 |
| 2020-01-01 00:00:00 |
| 2022-12-26 15:26:53 |
| 2022-12-26 15:26:53 |
+---------------------+
8 rows in set (0.00 sec)
```

---



**TIMESTAMP和DATETIME的区别**：

- TIMESTAMP存储空间比较小，表示的日期时间范围也比较小
- 底层存储方式不同，TIMESTAMP底层存储的是距离`1970-1-1 0:0:0 0毫秒`的**毫秒值**。
- 两个日期比较大小或日期计算时，TIMESTAMP更方便、更快。
- TIMESTAMP和时区有关。TIMESTAMP会根据用户的时区不同，显示不同的结果。而DATETIME则只能
    反映出插入时当地的时区，其他时区的人查看数据必然会有误差的。

```mysql
CREATE TABLE temp_time(
    d1 DATETIME,
    d2 TIMESTAMP
);

---

INSERT INTO temp_time VALUES('2021-9-2 14:45:52','2021-9-2 14:45:52');
INSERT INTO temp_time VALUES(NOW(),NOW());

---

mysql> SELECT * FROM temp_time;
+---------------------+---------------------+
|         d1          |          d2         |
+---------------------+---------------------+
| 2021-09-02 14:45:52 | 2021-09-02 14:45:52 |
| 2021-11-03 17:38:17 | 2021-11-03 17:38:17 |
+---------------------+---------------------+
2 rows in set (0.00 sec)

---

#修改当前的时区
SET time_zone = '+9:00';

---

mysql> SELECT * FROM temp_time;
+---------------------+---------------------+
|         d1          |          d2         |
+---------------------+---------------------+
| 2021-09-02 14:45:52 | 2021-09-02 15:45:52 |
| 2021-11-03 17:38:17 | 2021-11-03 18:38:17 |
+---------------------+---------------------+
2 rows in set (0.00 sec)
```





### 6.6 开发中经验

​		用得最多的日期时间类型，就是 `DATETIME` 。虽然 MySQL 也支持 YEAR（年）、 TIME（时间）、 DATE（日期），以及 TIMESTAMP 类型，但是在实际项目中，尽量用 DATETIME 类型。因为这个数据类型包括了完整的日期和时间信息，取值范围也最大，使用起来比较方便。毕竟，如果日期时间信息分散在好几个字段，很不容易记，而且查询的时候，SQL 语句也会更加复杂。

​		此外，一般存**注册时间**、**商品发布时间**等，不建议使用DATETIME存储，而是使用 `时间戳` ，因为 **DATETIME虽然直观，但不便于计算**。





## 七、文本字符串类型

> ​		MySQL中，文本字符串总体上分为 CHAR 、 VARCHAR 、 TINYTEXT 、 TEXT 、 MEDIUMTEXT 、 LONGTEXT 、 ENUM 、 SET 等类型。

![image-20221226154305589](MySQL基础篇.assets\image-20221226154305589.png)



### 7.1 CHAR与VARCHAR类型

CHAR和VARCHAR类型都可以存储比较短的字符串。

| 字符串(文本)类型 |   特点   | 长度 |    长度范围     |    占用的存储空间     |
| :--------------: | :------: | :--: | :-------------: | :-------------------: |
|     CHAR(M)      | 固定长度 |  M   |  0 <= M <= 255  |        M个字节        |
|    VARCHAR(M)    | 可变长度 |  M   | 0 <= M <= 65535 | (实际长度 + 1) 个字节 |



#### 1）CHAR类型

​		CHAR(M) 类型一般需要预先定义字符串长度。如果不指定(M)，则表示**长度默认是1个字符**。 

​		如果自定义了字符串长度，但是数据的实际长度比CHAR类型声明的长度小，则会在 **右侧填充空格** 以达到指定的长度。当MySQL检索CHAR类型的数据时，CHAR类型的字段会被去除**<font color="blue">尾部的空格</font>**。 

​		定义CHAR类型字段时，声明的字段长度即为CHAR类型字段所占的存储空间的字节数。

---



- 创建数据表

```mysql
CREATE TABLE test_char1(
    c1 CHAR,     # 默认1个字符
    c2 CHAR(5)
);

DESC test_char1;
mysql> DESC test_char1;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| c1    | char(1) | YES  |     | NULL    |       |
| c2    | char(5) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```



- 测试数据

```mysql
INSERT INTO test_char1 VALUES('a','Tom');

# ERROR 1406 (22001): Data too long for column 'c1' at row 1
INSERT INTO test_char1 VALUES('aa','Tom');

---

INSERT INTO test_char1(c2) VALUES('a '); # 尾部的空格会被去除
INSERT INTO test_char1(c2) VALUES('  a'); # 字符前面的空格不会被去除
INSERT INTO test_char1(c2) VALUES('     ');  # 无论单独敲多少个空格，字符长度都是0

---

mysql> SELECT CHAR_LENGTH(c2) FROM test_char1;
+-----------------+
| CHAR_LENGTH(c2) |
+-----------------+
|               3 |
|               1 |
|               3 |
|               0 |
+-----------------+
4 rows in set (0.00 sec)
```



#### 2）VARCHAR类型

​		`VARCHAR(M)` 定义时， **必须指定`长度M`**，否则报错。 

​		MySQL4.0版本以下，varchar(20)：指的是`20字节`，如果存放UTF8汉字时，只能存6个（UTF-8编码的每个汉字3字节） ；MySQL5.0版本以上，varchar(20)：指的是`20个字符`。 ==对于UTF-8编码而言，VARCHAR的最大字符长度为：`(65535 - 1 - 2) / 3 = 21844`==。**其中，1是为`NULL`值留位，2是记录当前字符串实际占用的大小**。

​		检索VARCHAR类型的字段数据时，会**不会删除数据尾部的空格（前面的空格就更不会删除了）**。VARCHAR类型的字段所占用的存储空间为`字符串实际长度加3个字节`。**其中，1是为NULL值留位，2是存放当前字符串实际占用的大小**。



- 创建数据表

```mysql
##############################  在 UTF-8 编码的基础上建表 ############################
CREATE TABLE test_varchar1(
    NAME VARCHAR # 创建时没有指定字符长度导致错误
) DEFAULT CHARSET=utf8;

---

# ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
CREATE TABLE test_varchar2(
    # 默认为UTF-8编码，每个字符占用3个字节。则最大字符长度为：(65535 - 1 - 2) / 3 = 21844
    NAME VARCHAR(21845) # 错误。      
);

---

CREATE TABLE test_varchar3(
    # 默认为UTF-8编码，每个字符占用3个字节。则最大字符长度为：(65535 - 1 - 2) / 3 = 21844
    NAME VARCHAR(21844) # 正确。
);

---

##############################  在 latin1 编码的基础上建表 ############################
CREATE TABLE test_varchar4(
    # latin1编码，每个字符占用1个字节。则最大字符长度为：(65535 - 1 - 2) / 1 = 65532
    NAME VARCHAR(65533) # 错误。
)CHARSET=latin1;

---

CREATE TABLE test_varchar4(
    # latin1编码，每个字符占用1个字节。则最大字符长度为：(65535 - 1 - 2) / 1 = 65532
    NAME VARCHAR(65532) # 正确。
)CHARSET=latin1;
```



- 测试是否删除空格

```mysql
create table ttt(
	name varchar(12)
);

insert into ttt values('sdf  ');
insert into ttt values('  sdf  ');
insert into ttt values('     '); # 5个空格

mysql> select CHAR_LENGTH(name) from ttt;
+-------------------+
| CHAR_LENGTH(name) |
+-------------------+
|                 5 |
|                 7 |
|                 5 |
+-------------------+
3 rows in set (0.00 sec)
```





#### 3）对比 CHAR 和 VARCHAR

|    类型    |   特点   |    空间上    | 时间上 |       适用场景       |
| :--------: | :------: | :----------: | :----: | :------------------: |
|  CHAR(M)   | 固定长度 | 浪费存储空间 | 效率高 | 存储不大，速度要求高 |
| VARCHAR(M) | 可变长度 | 节省存储空间 | 效率低 |     非CHAR的情况     |



- 情况1：**存储很短的信息用`char`**。比如门牌号码101，201……这样很短的信息应该用char，因为varchar还要占2个字节用于存储当前字符串实际存储的字符长度，本来打算节约存储的，结果得不偿失。
- 情况2：**存储固定长度的字符串用`char`**。比如使用uuid作为主键，那用char会更合适，因为它固定长度。用varchar的话，它动态适配长度的特性排不上用场，还要使用2个字节那啥，得不偿失。
- 情况3：**存储会 频繁更新 的字符串用`char`**。因为varchar在每次更新之后再存储都会存在额外的长度计算开销等工作。这样，如果存储的是一个 **改变的非常频繁的字符串**，那就要有更多的精力用于计算，而这些对于char来说是不需要的。
- 情况4：具体存储引擎中的情况：
    - `MyISAM 存储引擎中的数据列`：MyISAM数据表，最好使用固定长度的`CHAR`列代替可变长度的`VARCHAR`列。这样使得整个表**静态化**，从而使**数据检索更快** ，**用空间换时间**。
    - `MEMORY 存储引擎中的数据列`：MEMORY数据表目前都统一使用**固定长度**的数据行存储，因此无论使用 CHAR或VARCHAR列都没有关系，<font color="blue">两者都是作为CHAR类型处理的</font>。
    - `InnoDB 存储引擎中的数据列`： 建议使用VARCHAR类型。因为对于InnoDB数据表，内部的行存储格式并没有区分固定长度和可变长度列（所有数据行都使用指向数据列值的头指针），而且主要影响性能的因素是数据行使用的存储总量，由于char平均占用的空间多于varchar。所以**除了简短并且固定长度的， 其他考虑varchar**。这样节省空间，对磁盘I/O和数据存储总量比较好。



### 7.2 TEXT类型

​		在MySQL中，TEXT用来保存文本类型的字符串，总共包含4种类型，分别为TINYTEXT、TEXT、 MEDIUMTEXT 和 LONGTEXT 类型。

​		在向TEXT类型的字段保存和查询数据时，系统**自动按照实际长度存储，不需要预先定义长度**。

​		每种TEXT类型保存的数据长度和所占用的存储空间不同，如下：

| 文本字符串类型 | 特点               | 长度 | 长度范围                          | 占用的存储空 间 |
| -------------- | ------------------ | ---- | --------------------------------- | --------------- |
| TINYTEXT       | 小文本、可变长度   | L    | 0 <= L <= 255                     | L + 2 个字节    |
| TEXT           | 文本、可变长度     | L    | 0 <= L <= 65535                   | L + 2 个字节    |
| MEDIUMTEXT     | 中等文本、可变长度 | L    | 0 <= L <= 16777215                | L + 3 个字节    |
| LONGTEXT       | 大文本、可变长度   | L    | 0 <= L<= 4294967295（相当于 4GB） | L + 4 个字节    |

> ​		由于实际存储的长度不确定，MySQL 不允许 TEXT 类型的字段做主键。遇到这种情况，你只能采用 CHAR(M)，或者 VARCHAR(M)。

---



**举例**：

- 创建数据表：

```mysql
CREATE TABLE test_text(
    tx TEXT     # 不用指定长度
);

INSERT INTO test_text VALUES('atguigu  ');
INSERT INTO test_text VALUES('  atguigu ');
INSERT INTO test_text VALUES('      ');   # 6个空格……

mysql> select CHAR_LENGTH(tx) from test_text;
+-----------------+
| CHAR_LENGTH(tx) |
+-----------------+
|               9 |
|              10 |
|               6 |
+-----------------+
3 rows in set (0.00 sec)
```



- 指定了TEXT的长度也不会报错，**<font color="red">但是MySQL会自动将其转换为符合数据长度的另一种TEXT类型</font>**

```mysql
CREATE TABLE test_text2(
    # TEXT(2) 被自动转换为了 tinytext 类型
    tx TEXT(2)     # 指定了也没错。但是MySQL会自动将其转换为符合数据长度的另一种TEXT类型
);

---

mysql> desc test_text2;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| tx    | tinytext | YES  |     | NULL    |       |
+-------+----------+------+-----+---------+-------+
1 row in set (0.00 sec)
```

> ​		在保存和查询数据时，并不会删除TEXT类型数据尾部的空格。（前面的空格就更不会删除了）



### 7.3 开发中经验

​		TEXT文本类型，可以存比较大的文本段，但搜索速度稍慢，因此如果不是特别大的内容，建议使用CHAR， VARCHAR来代替。还有TEXT类型不用加默认值，加了也没用。而且text和blob类型的数据删除后容易导致 “空洞”，使得**文件碎片**比较多，所以**频繁使用的表不建议包含TEXT类型字段，建议单独分出去，单独用一个表【附表】**。



## 八、ENUM类型

​		ENUM类型也叫作**枚举类型**，ENUM类型的**取值范围需要在定义字段时进行指定**。设置字段值时，ENUM 类型只允许从成员中选取单个值，不能一次选取多个值。

​		其所需要的存储空间由定义ENUM类型时指定的成员个数决定。

| 文本字符串类型 | 长度 | 长度范围        | 占用的存储空间 |
| -------------- | ---- | --------------- | -------------- |
| ENUM           | L    | 1 <= L <= 65535 | 1或2个字节     |

* 当ENUM类型包含1～255个成员时，需要1个字节的存储空间； 
* 当ENUM类型包含256～65535个成员时，需要2个字节的存储空间。 
* ENUM类型的**成员个数的上限**为`65535`个。

---



**举例**：

- 创建表如下

```mysql
CREATE TABLE test_enum(
    season ENUM('春','夏','秋','冬','unknow')
);
```



- 添加数据

```mysql
INSERT INTO test_enum VALUES('春'),('秋');

# 忽略大小写
INSERT INTO test_enum VALUES('UNKNOW');

# 允许按照 索引值 的方式插入 指定索引位置 的 枚举值。使用【数字字符串】或【数字】代表索引都行
INSERT INTO test_enum VALUES('1'),(3);  // 1 or '1' 代表索引为1的 '春'

# Data truncated for column 'season' at row 1
INSERT INTO test_enum VALUES('ab');

# 当ENUM类型的字段没有声明为NOT NULL时，插入NULL也是有效的
INSERT INTO test_enum VALUES(NULL);

--- 

mysql> select * from test_enum;
+--------+
| season |
+--------+
| 春     |
| 秋     |
| unknow |
| 春     |
| 秋     |
| NULL   |
+--------+
6 rows in set (0.00 sec)
```





## 九、SET类型

​		SET表示一个**字符串对象**，可以包含0个或多个成员，但**成员个数的上限为 64** 。设置字段值时，可以取 取值范围内的 `0 个或多个值`。

​		当SET类型包含的成员个数不同时，其所占用的**存储空间**也是不同的，具体如下：

| 成员个数范围（L表示实际成员个数） | 占用的存储空间 |
| --------------------------------- | -------------- |
| 1 <= L <= 8                       | 1个字节        |
| 9 <= L <= 16                      | 2个字节        |
| 17 <= L <= 24                     | 3个字节        |
| 25 <= L <= 32                     | 4个字节        |
| 33 <= L <= 64                     | 8个字节        |

> ​		SET类型在存储数据时成员个数越多，其占用的存储空间越大。注意：SET类型在选取成员时，可以**一次选择多个成员**，这一点与ENUM类型不同。

---



**举例1**：

- 创建表

```mysql
CREATE TABLE test_set(
    s SET ('A', 'B', 'C')
);
```



- 向表中插入数据

```mysql
INSERT INTO test_set (s) VALUES ('A'), ('A,B');

# 插入重复的SET类型成员时，MySQL会自动删除重复的成员
INSERT INTO test_set (s) VALUES ('A,B,C,A');

# 向SET类型的字段插入SET成员中不存在的值时，MySQL会抛出错误。
# 1265 - Data truncated for column 's' at row 1
INSERT INTO test_set (s) VALUES ('A,B,C,D');

mysql> SELECT * FROM test_set;
+-------+
| s     |
+-------+
| A     |
| A,B   |
| A,B,C |
+-------+
3 rows in set (0.00 sec)
```



**举例2**：

- 创建表

```mysql
CREATE TABLE temp_mul(
    gender ENUM('男','女'),
    hobby SET('吃饭','睡觉','打豆豆','写代码')
);
```



- 向表中插入数据

```mysql
INSERT INTO temp_mul VALUES('男','睡觉,打豆豆'); #成功
INSERT INTO temp_mul VALUES('男','睡觉,写代码,吃饭'); #成功

# Data truncated for column 'gender' at row 1
INSERT INTO temp_mul VALUES('男,女','睡觉,写代码'); #失败
# Data truncated for column 'gender' at row 1
INSERT INTO temp_mul VALUES('妖','睡觉,写代码');#失败


SELECT * FROM temp_mul;
mysql> SELECT * FROM temp_mul;
+--------+------------------+
| gender | hobby            |
+--------+------------------+
| 男     | 睡觉,打豆豆        |
| 男     | 吃饭,睡觉,写代码    |
+--------+------------------+
2 rows in set (0.00 sec)
```





## 十、二进制字符串类型

> ​		MySQL中的二进制字符串类型主要存储一些**二进制数据**，比如可以存储图片、音频和视频等二进制数据。 
>
> ​		MySQL中支持的二进制字符串类型主要包括BINARY、VARBINARY、TINYBLOB、BLOB、MEDIUMBLOB 和 LONGBLOB类型。



### 10.1 BINARY与VARBINARY类型

​		BINARY和VARBINARY类似于CHAR和VARCHAR，只是它们存储的数据类型是**二进制字符串**。

​		BINARY (M)为固定长度的二进制字符串，M表示最多能存储的**字节数**，取值范围是0~255个字符。如果未
指定(M)，表示只能存储 `1个字节` 。例如BINARY (8)，表示最多能存储8个字节，如果字段值不足(M)个字
节，将在右边填充'\0'以补齐指定长度。

​		VARBINARY (M)为可变长度的二进制字符串，M表示最多能存储的字节数，总字节数不能超过行的字节长
度限制`65535`，另外还要考虑额外字节开销，VARBINARY类型的数据除了存储数据本身外，还需要1或2个
字节来存储数据的字节数。VARBINARY类型 `必须指定(M)` ，否则报错。

| 二进制字符串类型 | 特点     | 值的长度             | 占用空间  |
| ---------------- | -------- | -------------------- | --------- |
| BINARY(M)        | 固定长度 | M （0 <= M <= 255）  | M个字节   |
| VARBINARY(M)     | 可变长度 | M（0 <= M <= 65535） | M+1个字节 |

---



**举例**：

- 创建表

```mysql
CREATE TABLE test_binary1(
    f1 BINARY,
    f2 BINARY(3),
    # f3 VARBINARY,
    f4 VARBINARY(10)
);
```



- 添加数据

```mysql
INSERT INTO test_binary1(f1,f2) VALUES('a','a');
INSERT INTO test_binary1(f1,f2) VALUES('尚','尚');#失败

INSERT INTO test_binary1(f2,f4) VALUES('ab','ab');
mysql> SELECT LENGTH(f2),LENGTH(f4) FROM test_binary1;
+------------+------------+
| LENGTH(f2) | LENGTH(f4) |
+------------+------------+
|     3      |    NULL    |
|     3      |     2      |
+------------+------------+
2 rows in set (0.00 sec)

```



### 10.2 BLOB类型

​		BLOB是一个 **二进制大对象** ，可以容纳可变数量的数据。
​		MySQL中的BLOB类型包括TINYBLOB、BLOB、MEDIUMBLOB和LONGBLOB 4种类型，它们可容纳值的最大长度不同。可以存储一个二进制的大对象，比如 图片 、 音频 和 视频 等。

​		需要注意的是，在实际工作中，往往不会在MySQL数据库中使用BLOB类型存储大对象数据，通常会将图
片、音频和视频文件存储到 服务器的磁盘上 ，并将图片、音频和视频的访问路径存储到MySQL中。（也就是说：数据库有能力存储这些多媒体数据，但是却用的很少。）

| 二进制字符串类型 | 值的长度 | 长度范围                          | 占用空间     |
| ---------------- | -------- | --------------------------------- | ------------ |
| TINYBLOB         | L        | 0 <= L <= 255                     | L + 1 个字节 |
| BLOB             | L        | 0 <= L <= 65535（相当于64KB）     | L + 2 个字节 |
| MEDIUMBLOB       | L        | 0 <= L <= 16777215 （相当于16MB） | L + 3 个字节 |
| LONGBLOB         | L        | 0 <= L <= 4294967295（相当于4GB） | L + 4 个字节 |

---



**举例**：

```mysql
CREATE TABLE test_blob1(
    id INT,
    img MEDIUMBLOB
);
```



### 10.3 TEXT和BLOB的使用注意事项

在使用text和blob字段类型时要注意以下几点，以便更好的发挥数据库的性能。

- BLOB和TEXT值也会引起自己的一些问题，特别是**执行了大量的删除或更新操作的时候**。删除这种值
    会在数据表中留下很大的"`空洞`"，以后填入这些"空洞"的记录可能长度不同。为了提高性能，建议定期
    使用 `OPTIMIZE TABLE` 功能对这类表进行 **碎片整理** 。
- 如果需要对大文本字段进行模糊查询，MySQL 提供了 **前缀索引** 。但是仍然要**在不必要的时候避免检**
    **索大型的BLOB或TEXT值**。例如，SELECT * 查询就不是很好的想法，除非你能够确定作为约束条件的
    WHERE子句只会找到所需要的数据行。否则，你可能毫无目的地在网络上传输大量的值。
- 把BLOB或TEXT列 **分离到单独的表** 中。在某些环境中，如果把这些数据列移动到第二张数据表中，可
    以让你把原数据表中的数据列转换为固定长度的数据行格式，那么它就是有意义的。这会 **减少主表中的**
    **碎片** ，使你得到固定长度数据行的性能优势。它还使你在主数据表上运行 SELECT * 查询的时候不会通过
    网络传输大量的BLOB或TEXT值。





## 十一、JSON 类型

​		JSON（JavaScript Object Notation）是一种轻量级的 `数据交换格式` 。简洁和清晰的层次结构使得 JSON 成 为理想的数据交换语言。它易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效 率。**JSON 可以将 JavaScript 对象中表示的一组数据转换为字符串，然后就可以在网络或者程序之间轻 松地传递这个字符串，并在需要的时候将它还原为各编程语言所支持的数据格式**。 

​		在MySQL 5.7中，就已经支持JSON数据类型。在MySQL 8.x版本中，JSON类型提供了可以进行自动验证的 JSON文档和优化的存储结构，使得在MySQL中存储和读取JSON类型的数据更加方便和高效。 

---



**举例**：

- 创建数据表，表中包含一个JSON类型的字段 js 

```mysql
CREATE TABLE test_json(
    js json
);
```



- 向表中插入JSON数据

```mysql
INSERT INTO test_json (js)
VALUES ('{"name":"songhk", "address":{"province":"beijing", "city":"beijing"}}');

mysql> select * from test_json;
+---------------------------------------------------------------------------+
| js                                                                        |
+---------------------------------------------------------------------------+
| {"name": "songhk", "address": {"city": "beijing", "province": "beijing"}} |
+---------------------------------------------------------------------------+
1 row in set (0.00 sec)
```



- 当需要检索JSON类型的字段中数据的某个具体值时，可以使用“->”和“->>”符号

```mysql
mysql> SELECT js -> '$.name' AS NAME,js -> '$.age' AS age ,js -> '$.address.province'
  AS province, js -> '$.address.city' AS city FROM test_json;
+----------+------+-----------+-----------+
| NAME     | age  | province  | city      |
+----------+------+-----------+-----------+
| "songhk" | NULL | "beijing" | "beijing" |
+----------+------+-----------+-----------+
1 row in set (0.00 sec)

---

mysql> SELECT js ->> '$.name' AS NAME,js ->> '$.age' AS age ,js ->> '$.address.province' AS province, js ->> '$.address.city' AS city FROM test_json;
+--------+------+----------+---------+
| NAME   | age  | province | city    |
+--------+------+----------+---------+
| songhk | NULL | beijing  | beijing |
+--------+------+----------+---------+
1 row in set (0.00 sec)
```





## 十二、空间类型

​		MySQL 空间类型扩展支持地理特征的生成、存储和分析。这里的地理特征表示世界上具有位置的任何东西，可以是一个实体，例如一座山；可以是空间，例如一座办公楼；也可以是一个可定义的位置，例如 一个十字路口等等。MySQL中使用 `Geometry（几何）` 来表示所有地理特征。**Geometry指一个点或点的集合，代表世界上任何具有位置的事物**。 

​		MySQL的`空间数据类型（Spatial Data Type）`对应于OpenGIS类，包括单值类型：GEOMETRY、POINT、 LINESTRING、POLYGON以及集合类型：MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、 GEOMETRYCOLLECTION 。



- `Geometry`是所有空间类型的**基类**，其他类型如POINT、LINESTRING、POLYGON都是Geometry的子类
    - `Point`：顾名思义就是点，有一个坐标值。例如POINT(121.213342 31.234532)，POINT(30 10)， 坐标值支持DECIMAL类型，经度（longitude）在前，维度（latitude）在后，用空格分隔。 
    - `LineString`：线，由一系列点连接而成。如果线从头至尾没有交叉，那就是简单的 （simple）；如果起点和终点重叠，那就是封闭的（closed）。例如LINESTRING(30 10,10 30,40 40)，点与点之间用逗号分隔，一个点中的经纬度用空格分隔，与POINT格式一致。
    - `Polygon`：多边形。可以是一个实心平面形，即没有内部边界，也可以有空洞，类似纽扣。最 简单的就是只有一个外边界的情况，例如POLYGON((0 0,10 0,10 10, 0 10))。

---



- 下面展示几种常见的几何图形元素：

![image-20221226222219141](MySQL基础篇.assets\image-20221226222219141.png)

> ​		MultiPoint、MultiLineString、MultiPolygon、GeometryCollection 这4种类型都是集合类，是多个 Point、LineString或Polygon组合而成。



- 下面展示的是多个同类或异类几何图形元素的组合：

![image-2021226222313163](MySQL基础篇.assets\image-20221226222313163.png)





## 十三、小结及选择建议

​		在定义数据类型时，如果确定是**整数** ，就用 `INT` ； 如果是**小数** ，一定用定点数类型`DECIMAL(M，D)` ； 如果是日期与时间，就用 `DATETIME` 。 

​		这样做的好处是：首先确保你的系统不会因为数据类型定义出错。不过，凡事都是有两面的，**可靠性好，并不意味着高效**。比如，TEXT 虽然使用方便，但是效率不如 CHAR(M) 和 VARCHAR(M)。

---



**阿里巴巴《Java开发手册》之MySQL数据库：**

* 任何字段如果为**非负数**，必须是 `UNSIGNED` 
* 【 强制 】小数类型为 DECIMAL，禁止使用 FLOAT 和 DOUBLE。
    * 说明：在存储的时候，FLOAT 和 DOUBLE 都存在精度损失的问题，很可能在比较值的时候，得到不正确的结果。**如果存储的数据范围超过 DECIMAL 的范围，建议将数据拆成整数和小数并分开存储**。 
* 【 强制 】如果存储的字符串长度几乎相等，就使用 CHAR 定长字符串类型。
* 【 强制 】VARCHAR 是可变长字符串，不预先分配存储空间，**长度不要超过 5000**。如果存储的字符串长度大于此值，最好定义字段类型为 TEXT，并独立出来一张附表，用主键来关联，避免影响其它字段索引效率。

