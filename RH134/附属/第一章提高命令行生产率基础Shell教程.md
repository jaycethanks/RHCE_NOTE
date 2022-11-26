[TOC]

---

## SHELL

### 一、引入

#### 基本介绍：

- Shell 用C 语言编写，即是一种命令语言，又是一种程序设计语言
- shell脚本执行需要指定解释程序，Linux中Shell 的种类繁多，用的比较多的有：
  - Bourne Shell (/usr/bin/sh 或者 /bin/sh)
  - Bourne Again Shell ( /bin/bash )
  - C Shell (/usr/bin/csh)

#### 第一个shell脚本

```bash
#!/bin/bash
echo "Hello World!"
```

**`#!`** 是一个约定的标记，告诉系统这个脚本需要什么解释器来执行。读作 Sharp Bang => Sha-bang

#### 运行shell的两种办法

##### 1. 作为可执行程序

```bash
chmod +x test.sh
./test.sh
```

##### 2. 作为解释器参数

```bash
bash test.sh
/bin/sh test.sh
/bin/php test.php
/bin/python3 test.py
```



#### Shell 注释

##### 单行注释：

以 **#** 开头的行就是注释，会被解释器忽略。通过每一行加一个 # 号设置多行注释，像这样：

```bash
#--------------------------------------------
# 这是一个注释
# author：菜鸟教程
# site：www.runoob.com
# slogan：学的不仅是技术，更是梦想！
#--------------------------------------------
##### 用户配置区 开始 #####
#
#
# 这里可以添加脚本描述信息
# 
#
##### 用户配置区 结束  #####
```

> 如果在开发过程中，遇到大段的代码需要临时注释起来，过一会儿又取消注释，怎么办呢？
>
> 每一行加个#符号太费力了，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，这块代码就不会执行，达到了和注释一样的效果。

##### 多行注释：

```bash
:<<EOF
注释内容...
注释内容...
注释内容...
EOF

#EOF 也可以使用其他符号:

:<<'
注释内容...
注释内容...
注释内容...
'

:<<!
注释内容...
注释内容...
注释内容...
!
```



通过每一行加一个 **#** 号设置多行注释，像这样：



### 二、 变量

#### 定义变量

定义变量的时候，遵循以下格式：

```bash
VARIABLE_NAME=VARIABLE_VALUE
# " = " 两端不可以有空格
```

除了显示地直接复制，还可以用语句给变量赋值，如：

```bash
for file in `ls /etc`
或者
for file in $(ls /etc)
```



#### 以用户输入作为变量

read 命令，用于从标准输入（键盘输入）中读取一行。 并把每个字段的值指定为shell变量

script.sh

```bash
#!/bin/bash
echo "what's you name?"
read name
echo "Hello ${name} ,Good Morning!"
```

```bash
jayce@DESKTOP-JASQLDM:~/test$ ./script.sh
what's you name?
jayce
Hello jayce ,Good Morning!
```



#### 引用变量

```bash
name=jayce
echo $name
echo ${name}
```

>变量名外面的花括号是可选的，加花括号是为了帮助解释器识别变量的边界，比如下面这种情况：
>
>```bash
>for skill in Ada Coffe Action Java;do
>	echo "I am good at ${skill}Script"
>done
>```
>
>当和字符串写在一起的时候，如果不给skill变量加花括号，写成 echo "I am good at $skillScript", 解释器就会把$skillScript 当成一个变量，其值为空。 推荐给所有变量加上花括号，这是个好的编程习惯。

#### 只读变量

使用`readonly` 命令，可以将变量定义为只读变量，只读变量不能够被修改。

```bash
#!/bin/bash
myUrl="https://www.google.com"
readonly myUrl
myUrl="https://www.baidu.com"
```

```bash
/bin/sh : NAME: This variable is read only
```

#### 删除变量

使用`unset`命令可以删除变量

```bash
unset variable_name
```

> :warning: `unset` 命令不能删除只读变量

#### 变量类型

在运行shll的时候，会存在三种变量：

- **局部变量 ：**局部变量在脚本或者命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
- **环境变量：**所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行，必要的时候shell脚本也可以定义环境变量。
- **shell 变量：**shell 变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一不分是局部变量， 这些变量保证了shell的正常运行。 

#### Shell 字符串

##### shell 字符串定义

Shell字符串的定义，可以使用单引号，也可以使用双引号，还可以不用引号。

```bash
str='this is a string'
```

```bash
you_name='jayce'
str="Hello! I'm ${my_name} \n"
echo -e $str
```

> 特别要注意，在Shell中，单引号和双引号有以下特点：
>
> 单引号：
>
> - 单引号中所有的字符都会原样输出，不能再单引号字符串中引用变量。
> - 单引号字符串中不能出现单独一的的单引号（转义也不行）。但是可成对出现，作为字符串拼接使用。
>
> 双引号：
>
> - 双引号里面可以有变量
> - 双引号可以出现转义字符

##### 获取字符串的长度

```bash
string="abcd"
echo ${#string}
4
```

##### 提取子字符串

```bash
string="runoob is a great site"
echo ${string:1:4}
unoo
```

##### 查找子字符串

```bash
string="runoob is a great site"
echo `expr index "$string" io` #在$string中查找 "i" 或者 "o" 的索引位置
```



### 三、 传递参数

在Shell脚本执行的时候，可以向脚本传递参数，例如：./test.sh par1 par2 ; 而在脚本内部，获取参数，采用**$** 和自然数 组合的成的变量的方式获取到第n个参数。 $n

例如：

script.sh

```bash
#!/bin/bash
echo $1
echo $2
echo $3
```

```bash
./script.sh hello world jayce
hello
world
jayce
```

除了**`$n` **变量， 还有很多特殊变量，有着特殊的作用:

| 参数处理 | 说明                                                         |
| :------: | ------------------------------------------------------------ |
|   `$0`   | 当前脚本的文件路径和文件名                                   |
|   `$#`   | 传递到脚本的参数个数                                         |
|   `$$`   | 脚本运行的当前进程的ID号码                                   |
|   `$!`   | 后台运行的最后一个进程ID号码                                 |
|   `$*`   | 以一个单字符串显示所有向脚本传递的参数。<br/>如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。 |
|   `$@`   | 与$*相同，但是使用时加引号，并在引号中返回每个参数。<br/>如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。 |

> $* 与 $@ 区别：
>
> - 相同点：都是引用所有参数。
> - 不同点：只有在双引号中体现出来。假设在脚本运行时写了三个参数 1、2、3，，则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）。



### 四、 数组

Bash Shell 仅支持一维数组。

##### 数组定义：

```bash
array_name=(value1 value2 ... valuen) 
```

同普通变量的定义一样，`=` 两边不可以有空格，数组值使用 "()" 括起来，值与值之间的分隔符为空格。

除了直接全部赋值一组值给一个数组变量，还可以使用下标单个的为指定的索引位置赋值：

```bash
array_name[0]=value0
array_name[1]=value1
array_name[2]=value2
```

##### 读取数组中的单个元素

```bash
${array_name[index]}
```

script.sh

```bash
my_array=(A B "C" D)

echo "${my_array[0]}"
echo "${my_array[1]}"
echo "${my_array[2]}"
echo "${my_array[3]}"
```

```bash
./script.sh
A
B
C
D
```

##### 读取数组中的所有元素

使用`@` 或者 `*` 可以获取数组中的所有元素，例如：

script.sh

```bash
#!/bin/bash
my_array=(A B C D)

echo "数组的元素为： ${my_array[@]}"
echo "数组的元素为： ${my_array[*]}"
```

```bash
./script.sh
数组的元素为： A B C D
数组的元素为： A B C D
```

##### 获取数组的长度

```bash
#!/bin/bash
my_array=(A B C D)

echo "数组的元素为： ${#my_array[@]}"
echo "数组的元素为： ${#my_array[*]}"
```

> :warning: 获取数组的长度，和获取字符串的字符长度有些类似，区别在于，获取数组的长度，是在获取到数组所有元素的基础上，再去获取长度的。  



### 五、 运算符

Shell 支持多种运算符：

> :star: 以下所有提到的运算符，使用时，都应该在运算符的两端有空格。 例如
>
> ```bash
> test $num1 == $num2;echo $?
> ```
>

- 算数运算符
- 关系运算符
- 布尔运算符
- 字符串运算符
- 文件测试运算符

原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。expr 是一款表达式计算工具，使用它能完成表达式的求值操作。例如，两个数相加(**注意使用的是反引号 *`* 而不是单引号 '**)：

```bash
#!/bin/bash
val=`expr 2 + 2`
echo "两数之和为 ： $val"
```

```bash
两数之和为 ：4
```

> 两点注意：
>
> - 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
> - 完整的表达式要被 **` `** 包含，注意这个字符不是常用的单引号，在 Esc 键下边。

##### 算数运算符

| 运算符 | 说明   |
| ------ | ------ |
| +      | 加     |
| -      | 减     |
| *      | 乘     |
| /      | 除     |
| %      | 取余   |
| =      | 赋值   |
| ==     | 相等   |
| !=     | 不相等 |



##### 关系运算符

| 运算符 | 说明                         |
| ------ | ---------------------------- |
| -eq    | 等于（equal）                |
| -ne    | 不等（not equal）            |
| -gt    | 大于（greater than）         |
| -lt    | 小于（less than）            |
| -ge    | 大于等于（greater or equal） |
| -le    | 小于等于（less or equal）    |

expr 是一款表达式计算工具，使用它能完成表达式的求值操作。

例如，两个数相加(**注意使用的是反引号 \**`\** 而不是单引号 \**'\****)：



##### 逻辑运算符

| 运算符 | 说明 |
| ------ | ---- |
| &&     | and  |
| \|\|   | or   |



**布尔运算符**


| 运算符 | 说明         |
| ------ | ------------ |
| ！     | 非运算       |
| -o     | 或运算（or） |
| -a     | 与运算 (and) |



#####  字符串运算符

| 运算符 | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| =      | 检测两个字符串是否相等，相等返回true :warning: 就是单个`=`没有错 |
| !=     | 检测两个字符串是否不相等，不相等返回true                     |
| -z     | 检测字符串长度是否为0，为0返回true                           |
| -n     | 将测字符串长度是否不为0，不为0返回true                       |
| $      | 检测字符串是否为空，不为空返回true                           |



##### 文件测试运算符

| 操作符  | 说明                                                |
| ------- | --------------------------------------------------- |
| -b file | 是否块设备 （block device）                         |
| -c file | 是否字符设备                                        |
| -d file | 是否目录（directory）                               |
| -f file | 是否普通文件（既不是目录，也不是设备文件） （file） |
| -g file | 是否设置了SGID 位                                   |
| -k file | 是否设置了黏着位 （Sticky Bit）                     |
| -p file | 是否有名管道 （pipe）                               |
| -u file | 是否设置了SUID                                      |
| -r file | 是否可读 read                                       |
| -w file | 是否可写 write                                      |
| -x file | 是否可执行 excute                                   |
| -s file | 文件是否为空                                        |
| -e file | 检测文件（ 包括目录）是否存在                       |
|         |                                                     |
| -S      | 判断文件是否套接字 （socket）                       |
| -L      | 检测文件是否存在并是一个符号链接                    |

```shell
#!/bin/bash
# author:菜鸟教程
# url:www.runoob.com

file="/var/www/runoob/test.sh"
if [ -r $file ]
then
   echo "文件可读"
else
   echo "文件不可读"
fi
if [ -w $file ]
then
   echo "文件可写"
else
   echo "文件不可写"
fi
if [ -x $file ]
then
   echo "文件可执行"
else
   echo "文件不可执行"
fi
if [ -f $file ]
then
   echo "文件为普通文件"
else
   echo "文件为特殊文件"
fi
if [ -d $file ]
then
   echo "文件是个目录"
else
   echo "文件不是个目录"
fi
if [ -s $file ]
then
   echo "文件不为空"
else
   echo "文件为空"
fi
if [ -e $file ]
then
   echo "文件存在"
else
   echo "文件不存在"
fi
```





### 六、 echo

##### 显示普通字符串

```shell
echo "It is a test"
It is a test
```

##### 显示转义字符

```shell
echo "\"It is a test\""
"It is a test"
```

##### 显示变量

```bash
name=jayce;echo "Thanks $name"
Thanks jayce
```

##### 开启转义

```shell
#!/bin/sh
echo -e "OK! \c" #-e 开启转义 \c 表示不换行
echo "It is a test"

#输出： Ok! It is a test

echo -e "Ok! \nIt is a test" # \n 表示换行
```

```bash
jayce@DESKTOP-JASQLDM:~/test$ echo -e "ok! \c" ; echo "it is a test"
ok! it is a test
jayce@DESKTOP-JASQLDM:~/test$ echo "ok!" ; echo "it is a test"
ok!
it is a test
jayce@DESKTOP-JASQLDM:~/test$ echo -e "try to so something \n@jayce"
try to so something
@jayce
jayce@DESKTOP-JASQLDM:~/test$
```

##### 原样输出字符串，不进行转义或取变量(用单引号)

```bash
echo '$name\"'
$name\"
```

##### 显示命令执行结果

```bash
echo `date`
Tue Dec 1 11:27:56 CST 2020
```



### 七、 printf

printf 命令模仿 C 程序库（library）里的 printf() 程序。可以通过`printf`来格式化输出。

**语法：**

```bash
printf format-string [arguments...]
```

- format-string ：为格式控制字符串
- argument ： 为参数列表

```bash
$ echo "Hello, Shell"			
Hello, Shell
$ printf "Hello, Shell\n"			#printf 不会自动换行
Hello, Shell
$
```

```bash
#!/bin/bash
 
printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg  
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234 
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543 
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876 

#执行结果
姓名     性别   体重kg
郭靖     男      66.12
杨过     男      48.65
郭芙     女      47.99
```

> %s %c %d %f都是格式替代符
>
> `%-10s` 指一个宽度为10个字符（-表示左对齐，没有则表示右对齐），任何字符都会被显示在10个字符宽的字符内，如果不足则自动以空格填充，超过也会将内容全部显示出来。
>
> `%-4.2f` 指格式化为小数，其中.2指保留2位小数。


> **%d %s %c %f** 格式替代符详解:
>**d: Decimal 十进制整数** -- 对应位置参数必须是十进制整数，否则报错！
> **s: String 字符串** -- 对应位置参数必须是字符串或者字符型，否则报错！
> **c: Char 字符** -- 对应位置参数必须是字符串或者字符型，否则报错！
> **f: Float 浮点** -- 对应位置参数必须是数字型，否则报错！
> 
>**f: Float 浮点** -- 对应位置参数必须是数字型，否则报错！
> 
>如：其中最后一个参数是 "def"，%c 自动截取字符串的第一个字符作为结果输出。
> 
>```bash
> $  printf "%d %s %c\n" 1 "abc" "def"
>1 abc d
> ```
>

#### printf的转义序列

| 序列  | 说明                                                         |
| :---- | :----------------------------------------------------------- |
| \a    | 警告字符，通常为ASCII的BEL字符                               |
| \b    | 后退                                                         |
| \c    | 抑制（不显示）输出结果中任何结尾的换行字符（只在%b格式指示符控制下的参数字符串中有效），而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略 |
| \f    | 换页（formfeed）                                             |
| \n    | 换行                                                         |
| \r    | 回车（Carriage return）                                      |
| \t    | 水平制表符                                                   |
| \v    | 垂直制表符                                                   |
| \\    | 一个字面上的反斜杠字符                                       |
| \ddd  | 表示1到3位数八进制值的字符。仅在格式字符串中有效             |
| \0ddd | 表示1到3位的八进制值字符                                     |





### 八、 test

shell 中的 `test` 命令用于检查某个条件是否成立，它可以进行**数值**、**字符**和**文件**三个方面的测试。

#### H4 - 字符串测试

```bash
#bash
str1="hello"
str2="hello"
test $str1 = $str2;echo$? #返回0 代表真
test $str1 == $str2;echo$? #返回0 代表真
```

> :warning:注意，在判断字符串的时候，可以使用**字符串运算符**或者**算数**运算符。但是不可以使用**关系**运算符。
>
> 例如以下用例是错误的：
>
> ```bash
> test $str1 -eq $str2;echo $?
> -bash: test: hello: integer expression expected 
> 2
> ```

#### H4 - 数值测试

数值测试的时候，可以使用**算数**运算符或者**关系**运算符。还可以用**字符串运算符**

```bash
num1=100
num2=100
test $num1 = $num2; echo $? #返回0
test $num1 == $num2; echo $? #返回0
test $num1 -eq $num2; echo $? #返回0
```

#### H4 - 文件测试

| 参数      | 说明                                 |
| :-------- | :----------------------------------- |
| -e 文件名 | 如果文件存在则为真                   |
| -r 文件名 | 如果文件存在且可读则为真             |
| -w 文件名 | 如果文件存在且可写则为真             |
| -x 文件名 | 如果文件存在且可执行则为真           |
| -s 文件名 | 如果文件存在且至少有一个字符则为真   |
| -d 文件名 | 如果文件存在且为目录则为真           |
| -f 文件名 | 如果文件存在且为普通文件则为真       |
| -c 文件名 | 如果文件存在且为字符型特殊文件则为真 |
| -b 文件名 | 如果文件存在且为块特殊文件则为真     |

```bash
cd /bin
if test -e ./bash
then
    echo '文件已存在!'
else
    echo '文件不存在!'
fi
```



> 另外，Shell 还提供了与( -a )、或( -o )、非( ! )三个逻辑操作符用于将测试条件连接起来，其优先级为： **!** 最高， **-a** 次之， **-o** 最低。例如：
>
> ```shell
> cd /bin
> if test -e ./notFile -o -e ./bash
> then
>     echo '至少有一个文件存在!'
> else
>     echo '两个文件都不存在'
> fi
> ```
>
> 





### 九、 流程控制

#### H4 - 条件语句


```shell
#脚本中
if condition
then
	command1
	command2
	...
	commandN
elif condition2
then
	command1
	command2
	...
	commandN
else
	command1
	command2
	...
	commandN
	
fi
```


```bash
#bash下,一行 if else 的写法
if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true";fi
```

> :star: **有两点极其要注意：**
>
> - 不同于大多数的编程语法，在else没有执行语句的时候可以留空：
>
>   ```bash
>   if(condition){
>   	dosomething here
>   }else{}
>   ```
>
>   在shell脚本中是不被允许的。如果<span style="color:red">没有else逻辑，就不要写</span>
>
> - shell是严格缩进的语法，条件判断时，condition 写在 "`[]`" 中。 且<span style="color:red">**内部两端一定要有空格**</span>。如：` if [ $num1 == $num2 ]; then echo $?;fi`, 且，<span style="color:red">**在运算符两端也要有空格**</span>。

#### 【附】H4 - 算数表达式arithmetic expansion

- 在执行数值运算的时候，应该放在 <span style="color:red">`$[compute here]`</span>或者<span style="color:red">`$((compute here))`</span>,这样的结构中，<span style="color:red">**注意没有空格**</span>。这两种表达式一样的。 

  ```bash
  num1=$[2*3]
  num2=$[1+5]
  if test $[num1] -eq $[num2]
  ```

- 前面说了引用变量除了使用 `$variable_name`，`${variable_name}`。 如果变量部分是数值计算式变量，其实也可以用<span style="color:red">`$[variable_name]`</span>。  但是不推荐，用前两种方式就可以了。 

  实验：

  ```bash
  $ var1="Hello World!"
  $ echo "$var1 ${var1} $[var1]"
  -bash: Hello World!: syntax error in expression (error token is "World!")
  $ echo "$var1 ${var1}"
  Hello World! Hello World!
  ```

  ```bash
  $ num8=$[2*3*4*5]
  $ echo "$num8 ${num8} $[num8]"
  120 120 120
  ```

  可以发现，变量为字符串的时候，并不是适用 `$[variable_name]` 这种方式去引用变量。 



额外示例：批量ping主机

```bash
#!/bin/bash
for i in {1..20};do
        if ping -c 1 192.168.2.$i >/dev/null;then
                echo "192.168.2.$i is running"
        else
                echo "192.168.2.$i is down"
        fi
done 
```







#### H4 - for循环语句

```bash
for VAR in ITEM1 ITEM2 ... ITEMN
do
	command1
	command2
	...
	commandN
done
```

```bash
#写在hash中的一行
for VAR in ITEM1 ITEM2 ... ITEMN; do command1;command2... done;
```

> in列表可以包含替换、字符串和文件名。命令执行的返回列表。

```bash
#!/bin/bash
for item in {1,2,3,a,b,c}
do
        echo $item
done
#输出
1
2
3
a
b
c
```

```shell
#!/bin/bash
for file in `ls ./`
do
        echo $file
done

#输出
index.js
script.sh
testscript.sh
```

#### H4 -while 循环

```bash
while condition
do
    command
done
```

```bash
#!/bin/bash
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
```

while 循环可以用于读取键盘信息：

```bash
#!/bin/bash
echo 'Please input your password to continue...'
while read INPUT
do
        inputstring="$INPUT"
        if [ ${#inputstring} == 0 ]
        then
                echo 'You hava not input anything'
        elif [ $INPUT != 'jaycethanks' ]
        then
                echo 'Sorry ! Something Wrong with your input'
        else
                echo 'continue...have a good day!'
                exit
        fi
done
```

```bash
jayce@DESKTOP-JASQLDM:~/test$ ./testscript.sh
Please input your password to continue...
jayce
Sorry ! Something Wrong with your input

You hava not input anything
jaycethanks
continue...have a good day!
```

#### H4 - 无限循环

```bash
while :
do 
	command
done
```

 或者

```bash
while true
do 
	command
done
```

或者

```bash
for (( ; ; ))
```



#### H4 - until 循环

until 循环执行一系列命令直至条件为 true 时停止。
until 循环与 while 循环在处理方式上刚好相反。
一般 while 循环优于 until 循环，但在某些时候—也只是极少数情况下，until 循环更加有用。

一般 while 循环优于 until 循环，但在某些时候—也只是极少数情况下，until 循环更加有用。

until 语法格式:

```bash
until condition
do
    command
done
```

condition 一般为条件表达式，如果返回值为 false，则继续执行循环体内的语句，否则跳出循环。以下实例我们使用 until 命令来输出 0 ~ 9 的数字：

```bash
#!/bin/bash
a=0
until [ ! $a -lt 10 ]
do
   echo $a
   a=`expr $a + 1`
done
```



以下实例我们使用 until 命令来输出 0 ~ 9 的数字：

until 语法格式:

#### H4 - case 语句

Shell case语句为多选择语句。可以用case语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令。case语句格式如下：

```bash
case 值 in
模式1）
	command1
	command2
	...
	commandN
	;;
模式2）
	command1
	command2
	...
	commandN
	;;
esac
```

```bash
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```

#### H4 - 跳出循环

**break命令**

break命令允许跳出所有循环（终止执行后面的所有循环）。

下面的例子中，脚本进入死循环直至用户输入数字大于5。要跳出这个循环，返回到shell提示符下，需要使用break命令。

```bash
#!/bin/bash
while :
do
    echo -n "输入 1 到 5 之间的数字:"
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的! 游戏结束"
            break
        ;;
    esac
done
```

### continue

continue命令与break命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。

对上面的例子进行修改

```bash
#!/bin/bash
while :
do
    echo -n "输入 1 到 5 之间的数字: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的!"
            continue
            echo "游戏结束"
        ;;
    esac
done
```



### 十、 函数

#### H4 - 函数定义

Linux shell 可以用户定义函数，然后再shell脚本中可以随便调用。

```bash
[function] funcname [()]
{
	action;
	[return int;]
}
```

> - 1、可以带function fun() 定义，也可以直接fun() 定义,不带任何参数。
> - 2、参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255

```bash
#基本示例
demoFun(){
    echo "这是我的第一个 shell 函数!"
}
echo "-----函数开始执行-----"
demoFun
echo "-----函数执行完毕-----"
```

```bash
#return 示例
funWithReturn(){
    echo "这个函数会对输入的两个数字进行相加运算..."
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "输入的两个数字之和为 $? !"
```

#### H4 - 函数参数

```BASH
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73

#输出
第一个参数为 1 !
第二个参数为 2 !
第十个参数为 10 !
第十个参数为 34 !
第十一个参数为 73 !
参数总数有 11 个!
作为一个字符串输出所有参数 1 2 3 4 5 6 7 8 9 34 73 !
```

| 参数处理 | 说明                                                         |
| :------- | :----------------------------------------------------------- |
| $#       | 传递到脚本或函数的参数个数                                   |
| $*       | 以一个单字符串显示所有向脚本传递的参数                       |
| $$       | 脚本运行的当前进程ID号                                       |
| $!       | 后台运行的最后一个进程的ID号                                 |
| $@       | 与$*相同，但是使用时加引号，并在引号中返回每个参数。         |
| $-       | 显示Shell使用的当前选项，与set命令功能相同。                 |
| $?       | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。 |



### 十一、 输入/输出重定向（略）

### 十二、文件包含

和其他语言一眼给，Shell也可以包含外部脚本。这样可以很方便的封装一些公用的代码作为一个独立的文件。 语法格式如下：

```bash
. filename   # 注意点号(.)和文件名中间有一空格

或

source filename
```

### 实例

创建两个 shell 脚本文件。

test1.sh 代码如下：

```
#!/bin/bash
# author:菜鸟教程
# url:www.runoob.com

url="http://www.runoob.com"
```

test2.sh 代码如下：

```
#!/bin/bash
# author:菜鸟教程
# url:www.runoob.com

#使用 . 号来引用test1.sh 文件
. ./test1.sh

# 或者使用以下包含文件代码
# source ./test1.sh

echo "菜鸟教程官网地址：$url"
```

接下来，我们为 test2.sh 添加可执行权限并执行：

```
$ chmod +x test2.sh 
$ ./test2.sh 
菜鸟教程官网地址：http://www.runoob.com
```

> **注：**被包含的文件 test1.sh 不需要可执行权限。