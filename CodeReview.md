# CodeReview笔记

## php

### 代码执行函数

* `eval()`

> `mixed eval ( string $code )` : 把字符串 code 作为PHP代码执行。

很多webshell都基于eval来执行，如一句话木马： `<?php @eval($_POST['cmd']);?>`

* `assert()`

> `bool assert(mixed $assertion [,string $descrition])` : 检查一个断言是否为FALSE.(把字符串$assertion作为PHP代码执行)。

很多webshell都基于assert来执行，如一句话木马： `<?php @assert($_POST['cmd']);?>`

* `preg_replace()`

> `mixed preg_replace ( mixed $pattern , mixed $replacement , mixed $subject [, int $limit = -1 [, int &$count ]] )` : 搜索subject中匹配pattern的部分， 以replacement进行替换。

`php -r "echo preg_replace('/test/e',phpinfo(),'jjj test');"`

其中的 $replacement 可控的情况下，能够被攻击。

* `create_function()`

> `string create_function ( string $args , string $code )` : 创建一个匿名函数，并返回独一无二的函数名。

`$newfunc = create_function('$v','return system($v);');`

其中，`$newfunc('whoami');` 就相当于 `system('whoami');`

* `call_user_func() && call_user_func_array()`

> `mixed call_user_func(callable $callback [,mixed $parameter [, mixed $...]])` : 第一个参数 callback 是被调用的回调函数，其余参数是回调函数的参数。
>
> `mixed call_user_func_array(callable $callback,array $param_arr)` : 把第一个参数作为回调函数调用，把参数数组作为回调函数的参数传入。

* `shell_exec()`

>  通过 shell 环境执行命令，并且将完整的输出以字符串的方式返回。

### 包含函数

* `require`

* `include`

* `require_once`

* `include_once`


### 命令执行函数

常见的命令执行函数有：

* `exec()` -- 执行一个外部程序

* `passthru()` -- 执行外部程序并显示原始输出

* `proc_exec()` -- 通过shell环境执行命令，并且将完整的输出以字符串的方法返回

* `system()` -- 执行外部程序，并且显示输出

* `popen()` -- 通过popen()的参数传递一条命令，并对popen()所打开的文件进行执行。

### 文件操作

* `copy()` -- 拷贝文件

* `file_get_contents()` -- 将整个文件读入一个字符串

* `file_put_contents()` -- 将一个字符串写入文件

* `file()` -- 将整个文件读入一个数组中

* `fopen()` -- 打开文件或者URL

* `move_uploaded_file()` -- 将上传的文件移动到新位置

* `readfile()` -- 输出文件内容

* `rename()` --重命名文件或者目录

* `rmdir()` -- 删除目录

* `unlink()` & `delete()` -- 删除文件

### 特殊函数

* `phpfinfo()`

输出包含路径、编译选项、扩展、版本、服务器信息和环境等信息

* `dl()` 

载入指定参数 string 路径的PHP扩展。

* `parse_str()`

> `void parse_str ( string $encoded_string [, array &$result ] )`

如果 encoded_string 是 URL 传递入的查询字符串（query string），则将它解析为变量并设置到当前作用域（如果提供了 result 则会设置到该数组里 ）。

* `mb_parse_str()`

解析 GET/POST/COOKIE 数据并设置全局变量

* `get_defined_vars()`

返回一个包含所有已定义变量的列表多维数组，这些变量包括环境变量，服务器变量和用户定义的变量。

* `get_defined_constants()`

返回所有当前已定义的常量名和值。包括define()函数所创建的，也包含所有扩展创建的。

* `get_defined_functions()`

返回所有已经定义的函数列表。

* `get_included_files()`

返回所有被include,include_once,require,require_once的文件名。

* `serialize()` 和 `unserialize()`

用于序列号和反序列化，配合 `__construct()`、`__destruct()`、`__wakeup()` 会触发漏洞。

* `scandir()` 

列出指定路径中的文件和目录，用于信息收集。

### 接收全局变量

* `$_GET`

* `$_POST`

* `$_COOKIE`

* `$_REQUEST`

* `$_FILES`

* `$_SERVER`

* `$HTTP_GET_VARS`

* `$HTTP_POST_VARS`

* `$HTTP_COOKIE_VARS`

* `$HTTP_REQUEST_VARS`

* `$HTTP_POST_FILES`

* `$HTTP_SERVER_VARS`

* `$HTTP_RAW_POST_DATA`

* `$argc`

* `$argv`


### 持的协议和封装协议

PHP 带有很多内置 URL 风格的封装协议，可用于类似 fopen()、 copy()、 file_exists() 和 filesize() 的文件系统函数。 除了这些封装协议，还能通过 stream_wrapper_register() 来注册自定义的封装协议。

```
file:// — 访问本地文件系统
http:// — 访问 HTTP(s) 网址
ftp:// — 访问 FTP(s) URLs
php:// — 访问各个输入/输出流（I/O streams）
zlib:// — 压缩流
data:// — 数据（RFC 2397）
glob:// — 查找匹配的文件路径模式
phar:// — PHP 归档
ssh2:// — Secure Shell 2
rar:// — RAR
ogg:// — 音频流
expect:// — 处理交互式的流
```

### filter_var函数缺陷

[filter_var函数缺陷](https://github.com/jiangsir404/Audit-Learning/blob/master/filter_var%E5%87%BD%E6%95%B0%E7%BC%BA%E9%99%B7.md)

## java

### xxe

常见漏洞出现函数：

```
javax.xml.parsers.DocumentBuilderFactory;
javax.xml.parsers.SAXParser
javax.xml.transform.TransformerFactory
javax.xml.validation.Validator
javax.xml.validation.SchemaFactory
javax.xml.transform.sax.SAXTransformerFactory
javax.xml.transform.sax.SAXSource
org.xml.sax.XMLReader
DocumentHelper.parseText
DocumentBuilder
org.xml.sax.helpers.XMLReaderFactory
org.dom4j.io.SAXReader
org.jdom.input.SAXBuilder
org.jdom2.input.SAXBuilder
javax.xml.bind.Unmarshaller
javax.xml.xpath.XpathExpression
javax.xml.stream.XMLStreamReader
org.apache.commons.digester3.Digester
rg.xml.sax.SAXParseExceptionpublicId
```