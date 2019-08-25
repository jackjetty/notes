* 层级结构
    * 从上到下为：数据库（Database） - 模式（Schema） - Table（表）
    * properties文件中配的地址是到数据库一级：spring.datasource.url=jdbc:postgresql://127.0.0.1:5432/postgres
    * 代码中连接成功后，如果需要使用jdbc或mybatis等方式使用sql，操作表时需要指定模式（Schema）：SELECT id, name, password FROM **test.**user
* 表名字段名尽量要避免postgres的关键字
* 定义表名、字段名时要小心**双引号**
    * 虽然管理端上看不出来，但是其实是不同的，会查不到
    * **标准的SQL是不区分大小写的，但PostgresSQL支持使用双引号来保持大小写**。但是PostgreSQL对于数据库中对象的名字允许使用支持大小写区分的定义和引用方法。方式就是在DDL中用双引号把希望支持大小的对象名括起来。
        * 比如希望创建一个叫AAA的表。如果用CREATE TABLE AAA （...）;的话，创建出来的表实际上是aaa。如果希望创建大写的AAA表的话，就需要用CREATE TABLE "AAA" (...);这种双引号的方式定义对象名。
        * 这样写的缺点是查询语句必须也使用双引号的方式引用对象名。比如SELECT * FROM "AAA"；否则PostgreSQL缺省会去找aaa这个对象，然后返回aaa不存在的错误。需要注意的是不仅仅是表可以这样定义和引用，对PostgreSQL中的任意对象（比如列名，索引名等）都有效。
        * 总结
            * 不推荐使用pgAdmin III这个工具创建数据库对象。还是应该手工编写DDL语句。
                * **如果表是通过PostgreSQL的pgAdmin III 工具创建的话，缺省是按照有双引号的方式创建对象的，所以DLL里面必须也要按照有双引号的方式使用。**但是这种写法不是标准的，如果是通过一些通用库函数访问数据库的话，会不被支持。
            * **不推荐在DDL里用双引号的方式创建区分大小写的对象。**
            * PostgreSQL给出的建议是SQL的key word用大写，其他的名称全部使用小写。
* 时间戳和时间之间的转换

```
SELECT *, to_char(to_timestamp(sent_date), 'yyyy-mm-dd hh24:mi:ss.us')
  FROM billing_report_sent_history order by usage_date desc limit 500;
```

* 自增列
    * 要不直接使用serial类型字段，要不先创建序列，然后设置字段的自增。

```
--方法一
create table test_a 
( 
  id serial, 
  name character varying(128), 
  constraint pk_test_a_id primary key( id) 
); 

--方法二
create table test_b 
( 
  id serial PRIMARY KEY, 
  name character varying(128) 
); 

--方法三
create table test_c 
( 
  id integer PRIMARY KEY, 
  name character varying(128) 
);  
NOTICE:  CREATE TABLE / PRIMARY KEY will create implicit index "test_c_pkey" for table "test_c" 
CREATE TABLE 

CREATE SEQUENCE test_c_id_seq 
    START WITH 1 
    INCREMENT BY 1 
    NO MINVALUE 
    NO MAXVALUE 
    CACHE 1; 

alter table test_c alter column id set default nextval('test_c_id_seq'); 
```