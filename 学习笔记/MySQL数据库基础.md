# MySQL数据库基础
  - [MySQL常用的数据类型](#mysql常用的数据类型)
  - [常用的运算符](#常用的运算符)
    - [算数运算符](#算数运算符)
    - [比较运算符](#比较运算符)
    - [逻辑运算符](#逻辑运算符)
  - [常用的函数](#常用的函数)
    - [算数运算](#算数运算)
    - [字符处理](#字符处理)
  - [CRUD操作](#crud操作)
  - [集合操作](#集合操作)
  - [约束](#约束)
  - [分组查询](#分组查询)
  - [变量声明](#变量声明)
  - [存储过程](#存储过程)
  - [触发器](#触发器)
  - [Python连接mysql](#python连接mysql)
  - [索引](#索引)
  - [如何优化MySQL](#如何优化mysql)
  - [事务](#事务)- [MySQL数据库基础](#mysql数据库基础)
  - [MySQL常用的数据类型](#mysql常用的数据类型)
  - [常用的运算符](#常用的运算符)
    - [算数运算符](#算数运算符)
    - [比较运算符](#比较运算符)
    - [逻辑运算符](#逻辑运算符)
  - [常用的函数](#常用的函数)
    - [算数运算](#算数运算)
    - [字符处理](#字符处理)
  - [CRUD操作](#crud操作)
  - [集合操作](#集合操作)
  - [约束](#约束)
  - [分组查询](#分组查询)
  - [变量声明](#变量声明)
  - [存储过程](#存储过程)
  - [触发器](#触发器)
  - [Python连接mysql](#python连接mysql)
  - [索引](#索引)
  - [如何优化MySQL](#如何优化mysql)
  - [事务](#事务)

## MySQL常用的数据类型

- int

  范围：```-2*10^32~2*10^32-1```

- bigint

  范围：```-2*10^63~2*10^63-1```

- float

  float(m, d)  其中m表示有效位（最大值为7），d表示小数位

  ```有效位就是把当前数 123456789.12 转变成科学计数法  1.23456*10^8```

- double

  double(m, d)  其中m表示有效位（最大值为15），d表示小数位

- decimal

  涉及金额存储的时候使用该类型

  - double(m, d)  其中m表示有效位（最大值为65），d表示小数位，不会产生精度问题

  - 不会产生精度问题的原因是decimal存储的形式是字符串

***

- char

固定长度的字符串，长度是255个字节

```中文字符占3~4个字节```

- varchar

  表示不定长的字符串，长度为0~65535个字符

- text

  长文本类型，最大空间占64KB

- longtext

  极大长文本类型，最大空间占4GB

***

- datetime

  不管时区是否发生变化，datetime类型不会发生更改，与存入的日期保持一致

- timestamp

  如果当前时区发生更改，那么timestamp会跟着发生更改

## 常用的运算符

### 算数运算符

- ```+```

- ```-```

- ```*```

- ```/```

- ```a div b```    表示a除以b，取商

  ```select 5 div 3```

- ```a mod b```  表示a除以b，取余

  ```select 5 div 3```

***

### 比较运算符

​	比较运算符最后的结果用0和1表示，其中1表示true，0表示false

- ```=```
  类似与python中的 ```==```（等于）
  
- !=

- ```>、>=```

- ```<、<=```

- ```between x and y、not between x and y```

- ```is null 、is not null```

  用来判断当前是否有记录

***

### 逻辑运算符

> MySQL的逻辑运算符与python的逻辑运算符一致

- AND

- OR

- NOT

- XOR

  这个异或不需要记（两个值，一个为false、另一个为true，才会返回true）

## 常用的函数

### 算数运算

- SUM

- AVG

- COUNT

  > 统计当前记录的数目

- MAX 、MIN

  > 当比较的是字符串的时候，类似与python中的排序，是按照ascii的顺序排序的。

***

### 字符处理

- CHAR_LENGTH

  > SELECT CHAR_LENGTH('test');

- FORMAT

  > SELECT FORMAT(0.333333,3);

- LEFT、RIGHT

  > SELECT LEFT('hello world', 5);	-- 从左到右顺序截取五个字符   hello
  > SELECT RIGHT('hello world', 5); -- 从右到左顺序截取五个字符   world

- TRIM

  去除字符串两端的空格

  > SELECT TRIM('    HELLO WORLD    ');  -- 去除字符串两端的空格  hello world

## CRUD操作

- Create（增、创）

  > - 创建表的时候尽量加上create_time字段，为create_time设置默认值CURRENT_TIMESTAMP
  > - 默认只有mysql5.7以上才会支持CURRENT_TIMESTAMP

  - 单条插入

    > INSERT INTO 表名(字段1,字段2...) VALUES(值1,值2);

  - 多条插入

    > INSERT INTO 表名(字段1,字段2...) VALUES(值1,值2),(值1,值2);

- Retrieve（查）

  - 获取所有数据

    > SELECT 字段1，字段2 FROM 表名;

  eg：```select * from class_01```

  - 条件查询```WHERE```

    > SELECT 字段1，字段2, ...字段n FROM 表名 WHERE 表达式;

  - 模糊匹配```LIKE```

    > - 使用关键词 LIKE
    >
    > - % 匹配**不定长**的字符
    > - _ 匹配任意**一个**字符
    > - 模糊匹配存在性能问题，表记录比较多的时候查询速度很慢

  ```mysql
  SELECT * FROM class_01 WHERE name LIKE '%z%';
  ```

  

  - 限制返回条数```LIMIT n```

    > 查询表达式 LIMIT 数量

  - 过滤重复值```DISTINCT```

    > SELECT DISTINCT 字段名1[,字段名2,...,字段名n] from 表名;

  - 排序```ORDER BY 字段 (ASC|DESC)```

    对查询的结果进行排序

    > - ASC 升序
    > - DESC 降序

- Update（更新）

  > UPDATE 表名 SET 字段1=值1， 字段2=值2 WHERE 表达式;

  ```UPDATE class_01 SET NAME='yl' WHERE id=1;```

- Delete（删除）

  > DELETE FROM 表名 WHERE 表达式;

  ```DELETE FROM class_01 WHERE NAME='wb';```

## 集合操作

![image-20221005101601930](https://s2.loli.net/2022/10/19/p19BRG7a4h2TiwU.png)

- 并集```union```

  子查询必须有相同数量的列（字段），且列的数据类型也相同

  > 查询表达式1 UNION 查询表达式2

- 交集```join|inner join```

  > SELECT * FROM (查询表达式1 AS s1 JOIN 查询表达式2 AS s2) ON s1.字段=s2.字段 

- 差集```left join、right join```

  - A对B的差集   left join

  >SELECT * FROM (查询表达式1 AS s1 LEFT JOIN 查询表达式2 AS s2) ON s1.字段=s2.字段 WHERE s2.字段 IS NULL;

- 补集

  A^B = (A∪B) - (A∩B)

  **A^B = (A-B) + (B-A)**  常用**

  >SELECT s1.字段1 FROM (查询表达式1 AS s1 LEFT JOIN 查询表达式2 AS s2) ON s1.字段=s2.字段 WHERE s2.字段 IS NULL
  >
  >**UNION**
  >
  >SELECT s1.字段1 FROM (查询表达式1 AS s1 RIGHT JOIN 查询表达式2 AS s2) ON s1.字段=s2.字段 WHERE s1.字段 IS NULL

## 约束

​	就是对表中的字段进行限定，保证数据的有效性、正确性和完整性

- 主键约束

  **tips**:查看表的主键

  > DESC 表名;

  - 什么是主键

    关系表中能**唯一标识**（不能为null且不可重复）

    > 选取和业务无关的字段，常用的逐渐就是自增id
    >
    > 数据库引擎会通过逐渐建立索引，索引是一个有序结构，我们希望当前主键的值不要改变，避免重新建立索引，影响性能

  - 添加主键

    - 建表的时候添加

    ```mysql
    -- 主键约束
    CREATE TABLE store_perf(
    id INT PRIMARY KEY AUTO_INCREMENT,
    store_name VARCHAR(255))
    ```

    - 可视化界面添加主键

  - 主键长度的限制

    767bytes，对应的长度为  764/ 4 = 191

    > bytes 表示字节，一个utf8mb4按四个字节算，所以长度是191

  - 自增键必须是主键

    **否则会报错：**
    ![image-20221005113139820](https://s2.loli.net/2022/10/19/lp8k7OT1QfFh4oM.png)

  - 联合主键

    一张表主键其实只能有一个，如果图形选择多个字段，那么就会自动创建联合主键
    - 修改主键

    ```mysql
    ALTER TABLE store_perf ADD PRIMARY KEY (`id`);
    ```
    - 移除主键

    ```mysql
    ALTER TABLE store_perf DROP PRIMARY KEY;
    ```

    - 创建联合主键

    ```mysql
    ALTER TABLE store_perf ADD PRIMARY KEY (`id`,`store_name`);
    ```

- 非空约束

  ```NOT NULL```

  > 只能约束程序层面上没有操作该表中的某个字段，不能约束人行为上对其赋值为空白字符。

- 唯一约束

  ```UNIQUE```

  > 比如统计店铺业绩，该店铺不可以重复，那就可以使用唯一约束

- 外键约束

  ```foreign key```

  - 外键的作用

    保持数据的一致性和完整性，通过外键来表达主表和从表的关系，避免冗余字段。

  - 目前外键不使用的原因

    - 性能问题

      插入数据需要校验

    - 并发问题

      在高并发的事务场景下，使用外键容易造成死锁

    - 扩展性问题

      外键相当于把对象之间的关系通过数据库来维护，如果进行分库分表，外键是不生效的。

      作数据迁移时，触发器、存储过程和外键都很难迁移，增加了维护成本。

    - 维护成本

      需要有专业DBA来维护庞大的数据库关系

- 默认约束

  ```DEFAULT```

  默认会填充当前字段。

## 分组查询

​	关键字： ```GROUP BY```

​	将数据按照某个字段进行分组，配合聚合函数```AVG COUNT MAX MIN SUM```等做统计运算

## 变量声明

- 服务器系统变量


> - 列出MySQL系统变量命令
>   SHOW VARIABLES;
>
> - 通过"@@"来调用系统变量
>   SELECT @@date_format;

- 用户变量


> - 通过“@”调用用户变量
> SELECT @yesterday;
> - 可以在赋值前调用系统变量，但是会输出空，没有意义
> - 设置和调用用户变量（比较有用）
>   SET @yesterday=SUBDATE(CURRENT_DATE, 1);
>   SELECT @yesterday;
> - 日期的格式化
>   SET @yesterday=SUBDATE(CURRENT_DATE, 1);
>   SET @yesterday=DATE_FORMAT(@yesterday, '%Y/%m/%d');
>   SELECT @yesterday;
>
> - 数值变量精度问题
>   SET @amount=0.4;
>   SET @amount=CAST(@amount AS DECIMAL(15, 2));
>   SELECT @amount;

- 局部变量
  - 不需要@前缀
  - 需要事前进行变量声明和初始化

## 存储过程

​	简单的认为是`SQL`中的函数

- 声明一个存储过程

  - 创建存储过程

    ```mysql
    DELIMITER $$
    CREATE PROCEDURE stat_store_perf(IN days INT(11))
    BEGIN
    	DECLARE t_date VARCHAR(255);
    	SET t_date=DATE_FORMAT(SUBDATE(CURRENT_DATE, days), '%Y-%m-%d');
    	SELECT COUNT(*) AS 店铺数,
    	FORMAT(AVG(amount), 2) AS 平均业绩,
    	SUM(amount) AS 总业绩,
    	MAX(amount) AS 最大业绩,
    	MIN(amount) AS 最小业绩,
    	department AS 部门
    	FROM store_perf
    	WHERE sta_date=t_date
    	GROUP BY department;
    END$$
    DELIMITER ;
    ```

    ![image-20221005223938200](https://s2.loli.net/2022/10/19/IFyNsdawcphGUn3.png)

  - 调用存储过程

    ```mysql
    call stat_store_perf(1);
    ```

    ![image-20221005224052445](https://s2.loli.net/2022/10/19/jmpBS567oJOrXfx.png)

  - 删除存储过程

    ```mysql
    DROP PROCEDURE stat_store_perf;
    ```

    

  

## 触发器

​	很存储过程一样，都是嵌入到mysql中的一段程序，区别是存储过程需要显示调用，而触发器是根据对表的相关操作自动激活执行。

- 创建触发器

  > DELIMITER $$
  >
  > CREATE TRIGGER 触发器名
  >
  > BEFORE|AFTER INSERT|UPDATE|DELETE
  >
  > ON 表明
  >
  > FOR EACH ROW
  >
  > BEGIN
  >
  > ​	IF ...
  >
  > ​	SQL语句体;
  >
  >    END IF;
  >
  > END$$
  >
  > DELIMITER ;

  ```mysql
  DELIMITER $$
  
  CREATE TRIGGER `class`.`check_depart`
  BEFORE INSERT
  ON `class`.`store_perf`
  FOR EACH ROW BEGIN
  	IF new.department NOT IN ('男装部', '女装部') THEN
  	SET new.department='unkonw';
  	END IF;
   END$$
  DELIMITER ;
  ```

- 触发器的应用

  多用来检查字段

- 查看触发器

  注意：查看触发器后面跟的是数据库的名字

  > SHOW TRIGGERS FROM 数据库名;

- 删除触发器

  > DROP TRIGGER 触发器名;

## Python连接mysql

​	参考：https://www.runoob.com/python3/python3-mysql.html

## 索引

- 创建索引

  ```mysql
  -- 创建单个索引
  CREATE INDEX store_name_index ON store_perf(store_name);
  
  -- 创建联合索引
  CREATE INDEX store_name_department_index ON store_perf(store_name, department);
  ```

- 查看索引

  ```mysql
  SHOW INDEX FROM store_perf;
  ```

- 删除索引

  ```mysql
  ALTER TABLE 表名 DROP 索引名;
  ```

- 查看当前查询语句有没有命中

  ```mysql
  EXPLAIN 查询语句
  
  eg:EXPLAIN SELECT * FROM store_perf WHERE store_name='女装99店';
  ```

  ![image-20221006163953658](https://s2.loli.net/2022/10/19/zGb5lRIZpHVWqLa.png)

- 单个字段可以命中联合索引吗？

  联合索引涉及到`左缀`查询的规则

  > 如果想命中索引，查询语句中涉及到字段必须是联合索引创建时从左到右的顺序。

  - 原理

    在`a_b_c_index`这样一个联合索引中，实质上执行中是先查询出`a`的结果集，然后再查`b`或`c`的结果集。

- 索引的实现原理

  `B+`树，一种特殊的链表，用来实现二分查找

## 如何优化MySQL

- 合理建立索引

> - 频繁作为查询条件的字段应该建立索引
>
> - 唯一性太差的字段不适合单独建立索引
> - 更新非常频繁的字段不适合建立索引
> - 避免不经过索引的操作
>   - `not in` != 等反向逻辑
>   - `between`范围查找
>   - `or`逻辑必须两边都命中索引才会走索引
>   - 联合索引不按照左缀查询规则

- 加缓存

  - 数据库缓存

    `show variables like '%query_cache%'`

  - redis做缓存

    请求 -> redis -> 未命中 -> mysql -> 返回

- 换存储介质--固态硬盘

## 事务

- 事务的四大特性`ACID`

  > - 原子性
  > - 一致性
  > - 隔离性
  > - 持久性

- 事务的提交

  ```mysql
  START TRANSACTION;
  INSERT INTO store_perf(store_name, department, amount, sta_date) VALUES('男装38店', '男装部', 123456.14, '2022-10-06'), ('女装38店', '女装部', 123456.14, '2022-10-06');
  COMMIT;
  ```

  ```python
  class DB(object):
  
      @classmethod
      def __db(self, sql=None):
          host = '192.168.72.201'  # 指定数据库之际
          user = 'root'  # 指定数据库用户
          password = '111111'  # 指定数据库密码
          dbName = 'class'  # 指定要连接的数据库
  
          db = pymysql.connect(host=host, user=user, passwd=password, db=dbName)  # 连接数据库
  
          cur = db.cursor()  # 生成游标
          flag = False  # sql执行成功与否标志
          content = None  # sql执行后返回的内容
  
          if sql:
              try:
                  cur.execute(sql)  # 执行sql
                  db.commit()  # 成功后提交事务
                  flag = True  # 修改标志flag
                  content = cur.fetchall()  # 获取返回的内容
              except:
                  db.rollback()
              finally:
                  db.close()
  
          return flag, content  # 返回
  ```

  

- 事务的回滚

  ```mysql
  ROLLBACK;
  ```

  