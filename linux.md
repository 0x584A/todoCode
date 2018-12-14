## 命令
### xargs 批量处理shell

给其他命令传递参数的一个过滤器。

比如需要将显示的结果当作变量，在bash中时可用 `xargs` 来处理。

```bash
echo "nameXnameXnameXname" | xargs -dX

name name name name
```

高级用法：

xargs 的一个 选项 -I ，使用 -I 指定一个替换字符串{}，这个字符串在 xargs 扩展时会被替换掉，当 -I 与 xargs 结合使用，每一个参数命令都会被执行一次：

```bash
ls | awk "{print $1}" | /usr/bin/xargs -i  /usr/bin/unzip {}  -d ok
```