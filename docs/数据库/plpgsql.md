# plpgsql学习笔记

## 数据类型

PostgreSQL的数据类型分为基础类型、组合类型、域类型和伪类型。

* 基础类型：是指SQL语言定义的基础类型，其底层是用C实现的，比如**int4**
* 组合类型：或者说是行类型，在用户定义一个表的时候创建的，或者用``CREATE TABLE``命令显示的创建。
* 域类型：域类型是为一个特定的基础类型定义值域用的，可以用``CREATE DOMAIN``来进行创建。
* 伪类型：伪类型是指一些特殊的类型，它不会作为表或组合类型的属性出现，但是可以作为SQL程序的声明或者是函数返回值的类型来使用。包括``any、anyelement、anyanrray、anynoarray、anyrange、cstring、internal、language_handler、fdw_handler、tsm_handler、record、trigger、event_trigger、pg_ddl_command、void、opaque``等。
* 多态类型：是指``anyelement, anyarray, anynoarray, anyenum, anyrange``等5种伪类型。在出现``anyelement``的地方不管是声明还是函数返回值都可以赋值任意类型，但是每次赋值必须是同一种类型，其他几种多态类型同理。当函数的返回值为多态类型时，在函数体内必须能够确定返回的真实数据类型。可变参数函数的参数声明可以是式态类型，通过将其最后一个参数声明为``VERIDIC anyarray``的形式实现。

## SQL函数

### 函数定义

SQL函数执行任意数量的SQL语句，并把最后一条语句的结果返回，在简单模式下，返回最后一条语句查询结果的第一行，如果想返回最后一条查询的全部结果，必须声明函数的返回类型为``SETOF sometype``，或者是``RETURNS TABLE(columns)。函数体必须是一系列以分号结束的SQL语句，最后一条SQL语句的分号可有可无。除非函数的返回声明是``void``，最后一条SQL必须是SELECT或者是带有RETURNING的INSERT, UPDATE或DELTE语句。

不返回任何值的函数其返回类型必须声明为void:

``` pgsql
CREATE FUNCTION clean_emp() RETURNS void AS '
DELETE FROM emp
    WHERE salary < 0;
' LANGUAGE SQL;

select clean_emp();
```
> 注意SQL语言定义的函数体中可以有增删查改等操作，但是不能包括创建表的操作，如果想要在函数体中创建表，则可以用PL/PgSQL实现。

上例中的函数体的定义部分是用单引号引起来的，这样如果在函数体中用到了单引号则必须加``\``用以区分，或者可以用``$$``来代替单引号。

### 函数的参数

函数的参数如果名称与列名相同的话优先表示列名，为避免这种命名冲突，在引用参数时可以用``function_name.argument_name``的形式进行引用，当然如果这种表示方法也与``table_name.column_name``冲突还是以后者为优先。

``$n``表示第几个参数，如``$1``表示第一个参数。

### 返回简单类型的函数定义
请看下例：

``` pgsql
create function tf1(accountno integer, debit numeric) returns integer as $$
    update bank
        set balance = balance - debit
        where accountno = tf1.accountno;
    select balance from bank where accountno = tf1.accountno;
$$ language SQL;
```
也可以通过UPDATE的returning语句来返回

``` pgsql
create function tf1(accountno integer, debit numeric) returns integer as $$
    update bank
        set balance = balance - debit
        where accountno = tf1.accountno
    returning balance;
$$ language SQL;
```
### 返回组合类型的函数

``` pgsql
CREATE TABLE emp (
    name        text,
    salary      numeric,
    age         integer,
    cubicle     point
);

INSERT INTO emp VALUES ('Bill', 4200, 45, '(2,1)');

CREATE FUNCTION double_salary(emp) RETURNS numeric AS $$
    SELECT $1.salary * 2 AS salary;
$$ LANGUAGE SQL;

SELECT name, double_salary(emp.*) AS dream
    FROM emp
    WHERE emp.cubicle ~= point '(2,1)';

 name | dream
------+-------
 Bill |  8400
```

## PL/pgSQL基础

用SQL语言可以实现一些基本的函数功能，但是SQL语言的函数中能够写的语句还是有很多限制，而且也没有变量、控制流等一些功能。如果想要实现更多的功能，则可以利用PostgreSQL自带的PL/pgSQL语言来实现。PostgreSQL内置了很多种语言的解释器，PL/pgSQL与Oracle的PL/SQL类似，很多语法也兼容，另外还集成了plperl、pltcl甚至plpython。

PostgreSQL9.0之后的版本自动安装了PL/pgSQL，但是这个模块是个可加载的模块，如果出于安全原因不想在服务器上运行此类脚本，也可以将这个模块移除。

对于SQL语言设计的函数，每一条SQL语句都会单独执行并返回客户端结果，这样会增加很多额外的进程间通信或网络通信开支。但是PL/pgSQL的一个语句块其内容是在服务端执行，返回最终的结果，所以会节省很多服务器与客户端的交互开支。当然，pl/pgsql保留了所有的SQL数据类型、操作、查询以及函数等。

### PL/pgSQL结构

PL/pgSQL是一种块结构的语言，函数定义必须是一个块，块定义为：

``` pgsql
[ <<label>> ]
[ DECLARE
    declarations ]
BEGIN
    statements
END [ label ];
```

label是可选的，一般用来标识一个块。所有要用到的变量都要在declare部分进行声明，变量定义的语法：
``` pgsql
name [ CONSTANT ] type [ COLLATE collation_name ] [ NOT NULL ] [ { DEFAULT | := | = } expression ];
```

一个简单的函数定义如下：
``` pgsql
create function sales_tax(subtotal real) returns real as $$
begin
    return subtotal * 0.06;
end;
$$ language plpgsql;



