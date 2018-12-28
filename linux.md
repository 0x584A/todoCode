# Linux 使用笔记

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

### 二进制形式输出文件信息

`hexdump xxxx.exe -C` 或者 `xxd xxxx.exe`

或者工具：`hexedit`

### 递归搜索删除文件

`find "${@:-.}" -type f -name "__MACOSX" -delete`

`find "${@:-.}" -type f -name .DS_Store -delete`

### shell简单实例

> 识别用户输入参数执行进入不同操作

```bash
#!/bin/bash
while [ -n "$1" ]
do
    case "$1" in
    1)
        echo "参数1";;
    2)
        echo "参数2";;
    3)
        echo "参数3";;
    *)
        echo "[Error] 参数: $1 无选项!";;
    esac
    shift
done
```

> 运行前端对应分支

```bash
#!/bin/bash

FE_PWD="/opt/wwwroot/tob/web/fe"
DEV_1=$1

gitName=`git symbolic-ref --short -q HEAD`
cd $FE_PWD

echo '当前分支：' $gitName
echo ''

if [ -z $DEV_1 ]; then
  echo '[!] Error! NUll DEV_NAME'
  exit
elif [ -n $DEV_1 ]; then
  cd $FE_PWD && yarn start $DEV_1
fi
```

> 自动登录脚本

```
#!/usr/bin/expect
spawn /usr/bin/ssh root@127.x.x.x
expect "*password:" // 识别关键字
send "123456\r" // 填入密码
expect eof // 结束
```

### wrk压测

`$ wrk -t2 -c20 -d30s --latency  http://swoolelive.com/`

使用2个线程运行30秒, 20个http并发

参数：

```
-c, --connections: 总的http并发数
-d, --duration:    持续压测时间, 比如: 2s, 2m, 2h
-t, --threads:     总线程数
-s, --script:      使用lua脚本
-H, --header:      添加http header, 比如. "User-Agent: wrk"
    --latency:     在控制台打印出延迟统计情况
    --timeout:     http超时时间
```

返回：

```
Running 30s test @ http://swoolelive.com/
  2 threads(线程) and 20 connections(连接)
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency(平均延时)   264.99ms  303.12ms   1.99s    90.01%
    Req/Sec(每秒平均)    51.52     29.52   180.00     66.61%
  2959 requests in 30.08s, 13.70MB read
  Socket errors: connect 0, read 0, write 0, timeout 5(超时)
Requests/sec:     98.38(每秒处理QPS)
Transfer/sec:    466.42KB(秒读取)
```

lua脚本参考：[scripts](https://github.com/wg/wrk/tree/master/scripts)

---------------------------------------------------

## 问题

### 1. 如何查看端口被哪个进程占用？

* lsof -i:端口号
* netstat -tunlp|grep 端口号
* netstat -antup
* netstat -antpx
* netstat -tulpn

### 2. 查看当前用户及登录情况

* `w` 查看所有登录用户
* `who` 查看自己的登录信息
* `whoami` 我是谁？
* `pkill -kill -t pts/0` 踢出登录用户，`pts/0` 为 `w` 显示的 `TTY` 列

### 3. 查看最近登录情况

`last、lastlog`

### 4. 查看系统信息

`uname -a`

### 5. 搜索文件及字符串

* 搜文件

`find . -iname '*linux*'`

递归目录下找到体积最大的文件

`find . -name "*.txt" -ls | sort -n -k7 | tail -n 1`

* 搜字符串

```
grep [选项] <字符串> <源文件>
-n 显示行号
-c 共匹配多少行
-i 忽略大小写
-v 排除指定字符串
-E 正则
-B 查找指定字符串后，显示指定行
-r 递归
```


### 6. 文件类型

```
-rw-r--r--.
第一位 "-" 是文件类型(-文件 d目录 |软连接文件)
rw-  r--  r---
u所有者 g所有组 o其他人 r读 w写 x执行
.代表CAL权限
r => 4 读
w => 2 写
x => 1 执行
读写 => 4+2 => 6
读执行 => 4+1 => 5
读写执行 => 4+2+1 => 7
```

### 7. 重要的文件

* 查看用户组
    * `cat /etc/group`  组名:组密码:组ID:组用户
* 查看用户组密码
    * `cat /etc/gshadow`  组名称:组密码
* 查看用户信息
    * `cat /etc/passwd`  用户名:用户密码:用户ID:所属组ID:用户备注信息:用户家目录:Shell目录
* 查看用户密码
    * `cat /etc/shadow`  用户名:用户密码

    
### 8. 怎么操作用户

> 用户的增删改

* 添加用户
    * `useradd [参数] 用户名`
        * `[-g (添加用户名指定组id [指定组 用户名])] `
        * `[-d (添加时指定用户家)]`
* 修改用户
    * `usermod [参数] 用户名`
        * `[-l (修改用户名 [新 旧])]`
        * `[-d (修改用户家目录)]`
        * `[-L 锁定帐号 -U 解锁帐号]`
        * `[-c (修改备注信息)]`
> 实例

建立一个新用户账户，并设置ID：`useradd test001 -u 544`

修改newuser的用户名为newuser1：`usermod -l newuser1 newuser`

锁定账号newuser1：`usermod -L newuser1`

**需要说明的是，设定ID值时尽量要大于500，以免冲突。因为Linux安装后会建立一些特殊用户，一般0到499之间的值留给bin、mail这样的系统账号。**

### 9. 定时执行脚本

```
crontab [指令]
-e 自动进入进入vi编辑模式
-r 删除定时任务
-l 查询定时定时任务

语法：* * * * * command
顺序：分 时 日 月 周 要执行的命令
```

每天的7点24分已root身份执行：

`24 7 * * * root run-parts /etc/cron.daily`

### 10. 查看指定时间段内的nginx日志

```
awk '$4>"[09/Sep/2016:14:34:47"&&$4<"[09/Sep/2016:14:38:47"' ngx_access.log
awk '$4>"[09/Sep/2016:14:08:00"&&$4<"[09/Sep/2016:14:14:00"' ngx_access.log
```

### 11. 查询ssh爆破日志

`grep "Failed password " /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr | more`

### 12. 压缩和解压

* 打包压缩

```
tar zcvf /dir/file.tar.gz /dir/file   打包后，以gzip压缩
tar --exclude /dir1/file1 zcvf file.tar.gz /dir1/* /dir2   打包压缩dir1和dir2中所有文件，除了/dir1/下的file1文件
```

* 解压

```
tar zxvf backup.tar
```

### 13. 管理服务

`systemctl is-enabled servicename.service` 查询服务是否开机启动

`systemctl enable *.service` 开机运行服务 

`systemctl disable *.service` 取消开机运行 

`systemctl start *.service` 启动服务 

`systemctl stop *.service` 停止服务 

`systemctl restart *.service` 重启服务 

`systemctl reload *.service` 重新加载服务配置文件 

`systemctl status *.service` 查询服务运行状态 

`systemctl --failed` 显示启动失败的服务

### 14. git[fatal: 拒绝合并无关的历史]

首先将远程仓库和本地仓库关联起来：

`git branch --set-upstream-to=origin/master master`

然后使用git pull整合远程仓库和本地仓库，

`git pull --allow-unrelated-histories` (忽略版本不同造成的影响)

### 15. 提取图片中的经纬度

```
$ exiftool -n -gpslatitude -gpslongitude IMG_3545.JPG

GPS Latitude : 42.3176472222222
GPS Longitude : -83.7321083333333
```

### 16. docker简单负载均衡

[利用nginx和docker实现一个简易的负载均衡](https://www.cnblogs.com/pangziyibudong/p/6211921.html)

[使用nginx+docker配置https负载均衡](https://www.cnblogs.com/gaoxu387/p/7993671.html)

```
* 在docker中从源中拉一下nginx的官方镜像，docker pull nginx，留着备用

* 在本地目录新建两个文件夹，我这里的新建在/mydata/test1，/mydata/test2/

* 分别在两个test文件夹中新建index.html来标志，在第一个html中输出this is nginx1，在第二个html中输出this is nginx2，

* 通过docker运行两个nginx服务器的容器，并分别将静态目录映射到我们刚刚创建的目录中

[root@catchtouch test2]# docker run --name nginx-test -d -p 8080:80 -v /mydata/test1:/usr/share/nginx/html nginx #第一个，将8080端口映射到容器中的80端口
[root@catchtouch test2]# docker run --name nginx-test1 -d -p 8081:80 -v /mydata/test2:/usr/share/nginx/html nginx #第二个，将容器中的8081端口映射到容器中的80端口

* 修改宿主机中的nginx的配置文件

在http{}中添加如下代码

upstream myweb { #myproject为自定义名字
　　ip_hash; #同一个ip一定时间内负载到一台机器
　　server 127.0.0.1:8080 weight=1 max_fails=2 fail_timeout=2; #weight越大，权重越高，被分配的几率越大
　　server 127.0.0.1:8081 weight=1 max_fails=2 fail_timeout=1; #我全部在本机，因此用了本地的ip，只要相应换成对应的ip或者域名即可
}

* 修改default.conf

server {
    listen 80 default_server;
    server_name  _;

    location / {　　
          #如果服务器要获取客户端真实IP，可以用下三句设置主机头和客户端真实地址
          #proxy_set_header Host $host;
          #proxy_set_header X-Real-IP $remote_addr;
          #proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    
          root   /usr/share/nginx/html;
          index  index.html index.htm;
        　proxy_pass http://myweb;  #myweb为之前在nginx.conf中upstream后定义的名字
     }
 
```

### 17. swoole安装错误

> `configure: WARNING: unrecognized options: --enable-async-redis`

执行 `php --ri=swoole` ，查看 `async_redis` 是否是 `enabled`  状态。
