### Linux

------

#### 常用命令记录

###### 1、linux防火墙命令

```shell
firewall-cmd --zone=public --list-ports #查看开放端口

firewall-cmd --zone=public --add-port=8080/tcp --permanent  # 开放8080端口

firewall-cmd --zone=public --remove-port=8080/tcp --permanent #关闭8080端口

firewall-cmd --reload  # 配置立即生效

systemctl status firewalld #查看firewalld状态
```

###### 2、ll命令详解

```shell
[root@VM-0-7-centos home]# ll
total 12
drwxr-xr-x 3 root         root         4096 Dec 16 13:51 docker
-rw-r--r-- 1 root         root           10 Dec 20 16:51 hello.txt
drwx------ 4 wangfeixiang wangfeixiang 4096 Dec 20 16:59 wangfeixiang

第1位确定文件类型（d,-,l,c,b)
	-:普通文件 d:目录 l:软链接 c:字符设备  b:快文件，硬盘
```

###### 3、scp命令

```shell
scp .\SpringBootDemo-0.0.1-SNAPSHOT.jar root@175.24.114.222:/root/java
root@175.24.114.222's password:
SpringBootDemo-0.0.1-SNAPSHOT.jar                                         100%   20MB 759.9KB/s   00:27
```

###### 4、Linux中nohup和&的用法和区别

1.不使用nohup和&   (Ctrl+C即可停止程序)

```shell
[root@VM-0-7-centos java]# java -jar app.jar 
```

2.使用&  （Ctrl+C不能停止程序，关闭当前session可停止程序）

```shell
[root@VM-0-7-centos java]# java -jar app.jar &
[1] 11095
```

3.使用nohup （Ctrl+C可停止程序，关闭当前session不能停止程序）

```shell
[root@VM-0-7-centos java]# nohup java -jar app.jar 
nohup: ignoring input and appending output to ‘nohup.out’
```

 4.同时使用&和nohup同时使用 (Ctrl+C和关闭当前session都不能停止程序，停止程序只能kill进程)

```shell
[root@VM-0-7-centos java]# nohup java -jar app.jar &
[1] 13589
[root@VM-0-7-centos java]# nohup: ignoring input and appending output to ‘nohup.out’
```

###### 5、端口占用解决

```shell
//linux
[root@VM-0-7-centos java]# lsof -i:8080
COMMAND   PID USER   FD   TYPE    DEVICE SIZE/OFF NODE NAME
java    29854 root   15u  IPv6 167005873      0t0  TCP *:webcache (LISTEN)
//windows
PS C:\Users\fei> netstat -ano |findstr 8080
  TCP    0.0.0.0:8080           0.0.0.0:0              LISTENING       3828
  TCP    2.0.1.5:62003          100.224.32.121:8080    ESTABLISHED     5424
  TCP    2.0.1.5:65021          100.224.32.121:8080    ESTABLISHED     5424
  TCP    192.168.31.90:55777    59.37.96.203:8080      ESTABLISHED     15772
  TCP    192.168.31.90:58742    61.151.165.0:8080      ESTABLISHED     12892
  TCP    [::]:8080              [::]:0                 LISTENING       3828
//关闭进程taskkill /f /t /im "进程id或者进程名称"
```

nohup java -jar -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8862 -Xms2048M -Xmx2048M /home/cfs-dev/dev/cfs/target/app.jar > /home/cfs-dev/dev/logs/xd-cfs-custom.log 2>&1 &

###### 6、定时任务

crontab

- -e 编辑 crontab 定时任务

- -l 查询 crontab 任务

- -r 删除当前用户所有的 crontab 任务

  

- 

#### shell编程

###### 1、执行脚本

- sh/bash+脚本的路径
- 输入脚本路径执行脚本（必须具有可执行权限 chmod +x）

###### 2、变量

- **$n** （功能描述：n 为数字，$0 代表该脚本名称，$1-$9 代表第一到第九个参数，十以上的参数，十以上的参数需要用大括号包含，如${10}）

  ```shell
  [root@wangfeixiang www]# cat hello.sh 
  echo "hello, $1"
  [root@wangfeixiang www]# ./hello.sh world
  hello, world
  [root@wangfeixiang www]# 
  ```

- **$#** （功能描述：获取所有输入参数个数，常用于循环,判断参数的个数是否正确以及加强脚本的健壮性）

  ```shell
  [root@wangfeixiang www]# cat hello.sh 
  #!/bin/bash
  echo "hello, $1"
  echo $#
  [root@wangfeixiang www]# ./hello.sh wang fei
  hello, wang
  2
  ```

- **$*** （功能描述：这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体）

  **$@** （功能描述：这个变量也代表命令行中所有的参数，不过$@把每个参数区分对待）

- **$？** （功能描述：最后一次执行的命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；如果这个变量的值为非 0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确了。）

  ```shell
  [root@wangfeixiang www]# echo $?
  0
  [root@wangfeixiang www]# hello.sh
  -bash: hello.sh: command not found
  [root@wangfeixiang www]# echo $?
  127
  ```

###### 3、运算符

​	“$((运算式))” 或 “$[运算式]”

```shell
[root@wangfeixiang www]# echo $[2*7]
14
[root@wangfeixiang www]# echo $((2*7))
14
```

###### 4、条件判断

- 基本语法 

  - test condition 

  - [ condition ]**（注意 condition 前后要有空格）**

  ```java
  //echo $? 输出0表示为真，1为假
  [root@wangfeixiang www]# a=hello
  [root@wangfeixiang www]# test $a = hello
  [root@wangfeixiang www]# echo $?
  0
  [root@wangfeixiang www]# test $a = wang
  [root@wangfeixiang www]# echo $?
  1
  [root@wangfeixiang www]# [ $a = hello ]
  [root@wangfeixiang www]# echo $?
  0
  //等于号前后也要有空格
  [root@wangfeixiang www]# [ $a=wang ]
  [root@wangfeixiang www]# echo $?
  0
  ```

- 常用判断条件

  - 两个整数之间比较

    | 表达式 |           含义            |
    | :----: | :-----------------------: |
    |  -eq   |       等于（equal）       |
    |  -ne   |    不等于（not equal）    |
    |  -lt   |     小于（less than）     |
    |  -le   |  小于等于（less equal）   |
    |  -gt   |   大于（greater than）    |
    |  -ge   | 大于等于（greater equal） |

     **注：如果是字符串之间的比较 ，用等号“=”判断相等；用“!=”判断不等。**

  - 按照文件权限进行判断

    -  -r 有读的权限（read） 
    -  -w 有写的权限（write）
    -  -x 有执行的权限（execute） 

  - 按照文件类型进行判断

    -  -e 文件存在（existence）
    -  -f 文件存在并且是一个常规的文件（file） 
    -  -d 文件存在并且是一个目录（directory）

    ```shell
    [root@wangfeixiang www]# ll
    total 1049616
    -rwxr-xr-x 1 root root         37 Apr 13 09:24 hello.sh
    -rw-r--r-- 1 root root 1074790400 Aug 30  2021 swap
    -rw-r--r-- 1 root root          6 Apr 13 10:13 test
    drwxr-xr-x 3 root root       4096 Dec 16 13:01 wwwroot
    [root@wangfeixiang www]# [ -x test ]
    [root@wangfeixiang www]# echo $?
    1
    [root@wangfeixiang www]# [ -e test ]
    [root@wangfeixiang www]# echo $?
    0
    ```

  - 多条件判断

    多条件判断（&& 表示前一条命令执行成功时，才执行后一条命令，|| 表示上一条命令执行失败后，才执行下一条命令）

    ```shell
    [root@wangfeixiang www]# [ hello ] && echo OK || echo notOK
    OK
    [root@wangfeixiang www]# [ ] && echo OK || echo notOK
    notOK
    ```

###### 5、流程控制

- if 判断

  - 单分支

    ```
    if [ 条件判断式 ];then
    	程序
    fi
    ```

    或

    ```
    if [ 条件判断式 ]
    then
    	程序
    fi
    ```

    ```
    [root@wangfeixiang www]# a=13
    [root@wangfeixiang www]# if [ $a -eq 13 ]; then echo "a=13"; fi
    a=13
    [root@wangfeixiang www]# cat hello.sh 
    #!/bin/bash
    if [ $1 = hello ]
    then
    echo "$1=hello"
    fi
    [root@wangfeixiang www]# ./hello.sh hello
    hello=hello
    ```

    **条件判断优化**  **if [ $1 = hello ] 替换成 if [ "$1"x = "hello"x ]**  没有输入参数是不会报错

    **多条件写法**

    ```
    [root@wangfeixiang www]# echo $a
    13
    [root@wangfeixiang www]# if [ $a -gt 10 ] && [ $a -lt 20 ]; then echo "OK"; fi
    OK
    [root@wangfeixiang www]# if [ $a -gt 10 -a $a -lt 20 ]; then echo "OK"; fi
    OK
    ```

  - 多分支

    ```
    if [ 条件判断式 ]
    then
    	程序
    elif [ 条件判断式 ]
    then
    	程序
    else
    	程序
    fi
    ```

    ```shell
    [root@wangfeixiang www]# cat hello.sh 
    #!/bin/bash
    if [ $1 -lt 10 ]
    then
    	echo "小"
    elif [ $1 -lt 20 ]
    then 
    	echo "中"
    else
     	echo "大"
    fi
    [root@wangfeixiang www]# ./hello.sh 15
    中
    [root@wangfeixiang www]# ./hello.sh 25
    大
    [root@wangfeixiang www]# ./hello.sh 5
    小
    ```

    

- case语句

  ```
  case $变量名 in
  "值 1"）
  如果变量的值等于值 1，则执行程序 1
  ;;
  "值 2"）
  如果变量的值等于值 2，则执行程序 2
  ;;
  …省略其他分支…
  *）
  如果变量的值都不是以上的值，则执行此程序
  ;;
  esac
  ```

  ```
  [root@wangfeixiang www]# cat hello.sh 
  #!/bin/bash
  case $1 in
  1)
  	echo one
  ;;
  2)
  	echo two
  ;;
  *)
  	echo x
  ;;
  esac
  [root@wangfeixiang www]# ./hello.sh 4
  x
  [root@wangfeixiang www]# ./hello.sh 2
  two
  [root@wangfeixiang www]#
  ```

- for循环

  - 语法1

    ```
    for (( 初始值;循环控制条件;变量变化 ))
    do
    程序
    done
    ```

    ```
    [root@wangfeixiang www]# cat hello.sh 
    #!/bin/bash
    for (( i=1; i<=$1; i++))
    do
    	sum=$[ $sum + $i ]
    done
    echo $sum
    [root@wangfeixiang www]# ./hello.sh 100
    5050
    ```

  - 语法2

    ```she
    for 变量 in 值 1 值 2 值 3…
    do
    程序
    done
    ```

    ```
    [root@wangfeixiang www]# cat hello.sh 
    #!/bin/bash
    for para in $@
    do
    	echo $para
    done
    [root@wangfeixiang www]# ./hello.sh a b c d
    a
    b
    c
    d
    
    ```

- while循环

  ```
  while [ 条件判断式 ]
  do
  程序
  done
  ```

  ```
  [root@wangfeixiang www]# cat hello.sh 
  #!/bin/bash
  sum=0
  i=1
  while [ $i -le 100 ]
  do
  sum=$[$sum+$i]
  i=$[$i+1]
  done
  echo $sum
  [root@wangfeixiang www]# ./hello.sh 100
  5050
  ```

###### 6、read读取控制台输入

read (选项) (参数) 

①选项： -p：指定读取值时的提示符；

​				 -t：指定读取值时等待的时间（秒）如果-t 不加表示一直等待

②参数 变量：指定读取值的变量名

```
[root@wangfeixiang www]# cat hello.sh 
#!/bin/bash
read -t 7 -p "Enter your name:" name
echo $name
[root@wangfeixiang www]# ./hello.sh 
Enter your name:wangfeidxiang
wangfeidxiang
```

###### 7、函数

- 系统函数

  - basename

    basename [string / pathname] [suffix] 

    ```
    [root@wangfeixiang www]# cat hello.sh 
    #!/bin/bash
    echo $(basename $0)
    [root@wangfeixiang www]# ./hello.sh 
    hello.sh
    ```

  - dirname

    ```
    [root@wangfeixiang www]# dirname $(pwd)
    /
    [root@wangfeixiang www]# pwd
    /www
    ```

- 自定义函数

  - 语法

    ```
    [ function ] funname[()]
    {
    	Action;
    	[return int;]
    }
    ```

    （1）必须在调用函数地方之前，先声明函数，shell 脚本是逐行运行。不会像其它语言一样先编译。 

    （2）函数返回值，只能通过$?系统变量获得，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。return 后跟数值 n(0-255)

    ```
    [root@wangfeixiang www]# cat hello.sh 
    #!/bin/bash
    function sum()
    {
    	s=$[$1+$2]
    	echo $s
    }
    a=$(sum $1 $2)
    echo "结果:"$a
    [root@wangfeixiang www]# ./hello.sh 3 4
    结果:7
    ```

###### 8、正则表达式



###### 9、文本处理工具

- cut

  - -f 列号，提取第几列

  - -d 分隔符，按照指定分隔符分割列，默认是制表符“\t”

  - -c 按字符进行切割 后加加 n 表示取第几列 比如-c 1

    ```
    [root@wangfeixiang hello]# cat /etc/passwd | grep bash
    root:x:0:0:root:/root:/bin/bash
    wangfeixiang:x:1002:1002::/home/wangfeixiang:/bin/bash
    [root@wangfeixiang hello]# cat /etc/passwd | grep bash | cut -d ":" -f 1
    root
    wangfeixiang
    ```

- awk

  - -F 指定输入文件分隔符

  - -v 赋值一个用户定义变量

    ```
    [root@wangfeixiang java]# ll
    total 21080
    -rw-r--r-- 1 root root   140725 Dec 29 09:43 app.log
    -rw-r--r-- 1 root root     6843 Mar 16 17:13 app.tar.gz
    -rw-r--r-- 1 root root      121 Dec 28 17:08 DockerFile
    -rw-r--r-- 1 root root 21413915 Dec 21 14:01 hello.jar
    -rwxr-xr-x 1 root root      250 Dec 28 17:48 hello.sh
    drwxr-xr-x 5 root root     4096 Mar 17 14:12 SpringBootDemo
    [root@wangfeixiang java]# ll |awk '/app.log/ {print $5}'
    140725
    ```

    



###### 综合应用案例

1. 日志归档

   需求：实现一个每天对指定目录归档备份的脚本，输入一个目录名称（末尾不带/），将目录下所有文件按天归档保存，并将归档日期附加在归档文件名上。

   ```shell
   #!/bin/bash
   # 首先判断输入参数个数是否为 1
   if [ $# -ne 1 ]
   then
   	echo "参数个数错误！应该输入一个参数，作为归档目录名"
   exit
   fi
   # 从参数中获取目录名称
   if [ -d $1 ]
   then
   	echo
   else
   	echo
   	echo "目录不存在！"
   	echo
   exit
   fi
   DIR_NAME=$(basename $1)
   DIR_PATH=$(cd $(dirname $1); pwd)
   # 获取当前日期
   DATE=$(date +%y%m%d)
   # 定义生成的归档文件名称
   FILE=archive_${DIR_NAME}_$DATE.tar.gz
   DEST=/www/archive/$FILE
   # 开始归档目录文件
   echo "开始归档..."
   echo
   tar -czf $DEST $DIR_PATH/$DIR_NAME
   if [ $? -eq 0 ]
   then
   	echo
   	echo "归档成功！"
   	echo "归档文件为：$DEST"
   	echo
   else
   	echo "归档出现问题！"
   	echo
   fi
   exit
   ```

   
