# mysql注入速查表

## 常用函数

`@@hostname`  主机名称

`@@datadir`  数据库所在路径

`@@version_compile_os`  查看操作系统版本

`database()`  当前数据库名称

`user()`  数据库用户名

`version()`  当前版本

`order by x / gruop by x`  判断列数

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

