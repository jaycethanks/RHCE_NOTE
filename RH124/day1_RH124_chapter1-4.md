[TOC]



# chapter1-2:

## 开始

##### **登录本地系统**

（REHL8) 登录到本地系统，（对于安装了图形化界面的系统切换到纯命令行界面（非GNU））。 需要发送指令：

`ctrl`+`alt` + `F1` - `F6`

```bash
# 查看当前键盘系统端口
tty 
```



##### **ssh连接**

我们的上课环境，使用的：

```bash
ssh student@servera
```

实际上是以下命令的缩写：

```bash
ssh -i .ssh/lab_rsa student@servera
```

走了密钥认证，servera 到 serverb 是没有密钥认证的



##### **注销**

注销命令：

```bash
exit
```

快捷键：`ctrl`+`D`





## 基础命令

##### **获取用户基本信息**

```bash
#返回用户名
whoami
```

```bash
#返回更详细的用户信息
id username
#如果缺省username则返回当前用户信息
```

##### **获取时间**

```bash
#返回当地时间
date
#常用的选项
#返回完整的全写年月日 2020-11-16
date +%F
#返回美国格式简写年月日 11/16/20
date +%D

#返回全写的年2020
date +%Y
#返回简写的年20
date +%y

#返回月
date +%m

#返回日
date +%d

#返回时分秒
date +%H
date +%M
date +%S

```

临时修改时间：

```bash
date -s "2020-11-20 15:00"
date -s MM/DD/YY
date -s hh:mm:ss
```

简单时间计算：

```bash
echo $(date +%F)
2020-11-19

#当前时间加
echo $(date -d 30days +%F)
2020-12-19

#当前时间减
echo $(date -d -30days +%F)
2020-10-20


```



##### **修改密码**

```bash
#修改用户密码
passwd username
#username 缺省，修改当前用户密码
```

##### **查看文件信息**

```bash
file
```

Linux中，一切皆文件，使用file命令可以查看文件类型，例如：

```bash
file /etc/passwd
#将返回：
/etc/passwd: ASCII text #ASCII码文本文件

file /dev/vda
#将返回
/dev/vda: block special (252/0)#特殊块设备文件
```

##### **查看文件内容**

`cat`	: 简单输出到命令行查看

`more`	： 翻页，查找等 

`less`	：`more` 的升级版（less is more）

`tail`	: 从文档末尾开始查看内容

`head`	：从文档开头查看内容



##### **文本统计**

`wc`	命令

```bash
wc /etc/passwd
#返回
 49	119	2775 /etc/passwd	#(文件行数 单词数 字符数 文件路径)
```

```bash
wc -l /etc/passwd
#返回
49 /etc/passwd	#(返回文件行数)
```



##### **执行history返回结果中的历史命令**

```bash
!40 #将执行history命令返回的第40行历史命令
!keyword  #执行以keyword为关键字的最近历史命令
```

`ctrl` + `r` 可以搜索搜索历史命令



##### **命令行编辑实用快捷键：**

| 快捷键       | 描述                               |
| ------------ | ---------------------------------- |
| `Ctrl` + `A` | 跳到命令行开头                     |
| `Ctrl` + `E` | 跳到命令行末尾                     |
| `Ctrl` + `U` | 清除 光标 --> 命令行开头 内容      |
| `Ctrl` + `K` | 清除 光标 --> 命令行末尾 内容      |
| `Ctrl` + `←` | 跳转到前一字符串的开头             |
| `Ctrl` + `→` | 跳转到后一字符串的开头             |
| `Ctrl` + `R` | 在历史记录列表中搜索某一模式的命令 |
| `Ctrl` + `L` | 清屏 == `clear`                    |







# chapter3:从命令行管理文件

**文件系统层次结构**

**软连接**

/bin ----> /usr/bin	：可执行二进制文件，平常用户需要用到的命令

/sbin ----> /usr/bin	： 系统启动所需文件（启动文件，grub引导等等）

/usr/tmp ---> var/tmp	： 临时文件目录，默认超过30天未访问被删除

/dev	: 系统中的特殊的设备文件（磁盘块设备文件，键盘字符设备等）

/etc	: 系统中常见应用的配置文件（日常管理系统，网络等经常打交道），还有一些系统相关文件（/etc/issue	/etc/os-release）

/home	：普通用户家目录容器

/root	: 超级管理员用户家目录

/run	： 系统开机后，所有内存中的运行数据，临时不具有永久性

/sbin	: 系统管理相关的执行命令（所以需要特殊权限才能执行）

/tmp	: 用户，系统，软件，运行时产生 的临时数据，默认超过10天如果没有被访问，就会被删除 	

/usr	：类似windows的C盘 ，含有安装软件时的文档

​	/usr/lib	:库文件

​	/usr/share	: 文档

​	...

/proc	: 进程文件，系统运行时产生 的一些进程

/mnt	: 大型块设备的挂载点

/media	: 小型文件设备挂在点，例如U盘、光盘

> /mnt /media 是系统预留的目录，用不用取决于用户，用户还可以自己新建目录去挂载



> /proc /run 在系统未运行的时候是不存在的，因此又被称之为伪文件系统

图形界面中，如果插入临时的移动硬盘，将会被挂挂载在 /run/media/username/



**显示当前路径**

```bash
pwd
```



##### **返回某个文件的绝对路径**

`readlink`命令加上`-f`参数

```bash
[student@workstation yum.repos.d] readlink -f redhat.repo
#将返回
/etc/yum.repos.d/redhat.repo
```



##### **显示文件或者文件系统状态**

`stat` 命令 , 可以查看文件的详细信息，

大小，块数量，IO块，Inode，链接数，上下文，访问，修改时间

```bash
stat file
```



##### **更新文件时间**

`touch` 命令，不仅仅可以用于创建不存在的新文件。 

同时，如果该命令后面接上一个 已存在的文件或者目录， 那么将会刷新文件或者目录的状态时间，  可以用过 `stat` 命令进行查看。





##### **常用命令` ls`**

|  选项   | 含义                         |
| :-----: | ---------------------------- |
| `ls -A` | 不列出 `.` `..`              |
| `ls -a` | 列出所有文件，包括隐藏文件   |
| `ls -l` | 长格式列出文件               |
| `ls -R` | 递归列出文件                 |
| `ls -h` | 以人类可读的文件大小列出文件 |
| `ls -d` | 列出文件夹                   |
| `ls -i` | 列出文件索引，inode          |

> 以上命令会再加一个 `-l` 选项，以长格式按列列出



##### **文件管理基本命令**

复制 cp

移动 mv

删除文件 rm

删除空目录 rmdir

新建目录 mkdir

创建空白文件 touch

查看目录结构： `tree`



**创建目录 `mkdir` 常见选项**

| 选项 | 含义                    |
| ---- | ----------------------- |
| `-p` | 递归：`mkdir dir1/dir2` |
| `-m` | 权限                    |

**`cp`  命令常见选项**

| 选项 | 含义                                                         |
| :--: | ------------------------------------------------------------ |
| `-a` | 归档（只有加该选项，才能是100%复制，包括时间，权限等。其他选项都或少丢失部分信息） |
| `-R` | 递归                                                         |
| `-i` | 交互（默认，如果存在同名，是不会提示，直接覆盖，加上该选项会提示是否覆盖，可以保护意外覆盖同名文件） |
| `-p` | 保留文件附属信息（文件权限，时间戳，所有者）                 |



**`mv`命令删除文件**

`-i` 交互

`-f` 强制

`-r` 递归



**`rmdir` 删除空白文件夹**

```bash
#删除单个空白文件夹
rmdir empty_dir
#删除递归空白文件夹
rmdir -p empty_dir1/empty_dir2
```



> cp mv rm 只要不加 -i 都是危险的





##### **链接**

如果文件要存放在磁盘中，那么前提是这个磁盘有文件系统。  而磁盘在格式化文件系统的时候，会划分一数量的 inode 和 block 每一个 block中存放一个数据块， 而数据块和inode值 一 一对应。 

当我们存放一个文件到一个磁盘时，系统会在磁盘上创建一个 叫做 “元数据metadata” 的信息， 存放的是文件名和inode值的关联信息。  inode值 又会和 block隐射。  这样， 存放的文件数据就会被存放到了块中。

当我们去 读取一个文件的时候，文件系统会根据文件名，检索相应的inode值， 然后根据相应的inode值找到对应的数据块，读取出数据。 



这一整个链路，我们称为 一个 链接，（这个链接就是**硬链接**）。

> 因此只要是存放于磁盘中的数据，都必须有一条硬链接



**软链接**， 又称为符号链接， 类似windows中的快捷方式，一个软链接对应一个新的 inode值 --> 对应一个数据块。 数据块中存放了源文件的数据信息



> 如果，把**硬链接**比作一个详细的找到宝藏的藏宝图。 那么**软链接**就是一个盒子，存放了找到藏宝图的线索。 



创建硬链接：

```bash
$ ls src dest
```

创建软连接：

```bash
$ ls -s src dest
```

> :warning: 创建软连接的时候，如果在不同的目录，一定要用绝对路劲，如果在同目录下，则可以用相对路径。



**特点：**

软连接：

- 支持跨文件系统，
- 并且由于创建符号链接有一个新的inode值，因此需要消耗一个数据块，因此它是占用磁盘空间的。
- 删除源文件受影响

硬链接：

- 多个硬链接类似于创建了多个文件名，但是指向的是同一个数据块，多个硬链接的inode值是相同的，所以硬链接是不占用 磁盘空间的。 
- 删除源文件不受影响。
- 不支持跨文件系统





##### **shell字符匹配**

|    模式     | 匹配项                                         |
| :---------: | ---------------------------------------------- |
|      *      | 通配符：统配0个或者多个任意个字符              |
|     ？      | 匹配任何单个字符                               |
|  [abc...]   | 类：匹配其中任何 1 个字符                      |
|  [!abc...]  | 类取反： 不匹配其中任意字符                    |
|  [^abc...]  | 同上                                           |
| [[:alpha:]] | 任意个任何字母字符（全局匹配）                 |
| [[:lower:]] | 任意个任何小写字母字符（全局）                 |
| [[:upper:]] | ...大写                                        |
| [[:alnum:]] | ...字母、数字...                               |
| [[:punct:]] | 除了空空格和字母数字以外的任何**可打印**字符   |
| [[:digit:]] | ...数字...                                     |
| [[:space:]] | ...空百符号（制表，换行，回车，换页，空格）... |





##### **大括号扩展 {}**

```bash
touch file{1,2,3{a,b,c}}.txt
#将创建：
file1.txt
file2.txt
file3a.txt
file3b.txt
file3c.txt
```



##### **变量的扩展**

定义变量：

```bash
FIRST_NAME=Jayce
```

引用变量：

```bash
echo $FIRST_NAME
#or
echo ${FIRST_NAME}
```



##### **使用命令执行返回值**

`$(some command running here and return sth)`

```bash
$ touch  log@jayce_$(date +%F).log	
#将创建：
log@jayce_2020-11-17.log

$ echo Today is $(date +%W)th week of $(date +%Y), and $(date +%w)th day of this week.
Today is 46th week of 2020, and 2th day of this week.
```





# chapter 4 : 查看帮助

##### **1. 本地文档：GUI查看方式**

usr/share/doc

通过浏览器查看：

file:///usr/share/doc



还有一个rsyslog 文档，可能需要在仓库中下载

```bash
sudo yum list rsyslog-doc
sudo yum install rsyslog-doc
```

安装之后，可以通过浏览器查看：file:///usr/share/doc/rsyslog/html/index.html 







##### **2. man 手册**

/usr/share/man

```bash
$ man n keyword
```

*man 章节：*

 1. 可执行程序 、 shell 命令

 2. 系统调用

 3. 库调用

 4. 特殊文件（设备文件等）

 5. 文件格式，语法结构 

    ```bash
    $ man 5 passwd  #输出内容是关于文件的内容格式，含义
    ```

	6. 游戏

	7. 杂项（惯例、协议、标准）

	8. 系统命令（一般是需要特殊权限的命令）

	9. 内核相关

> :warning: 1, 5, 8 章最重要



```bash
$ man -k keyword #列举所有匹配的章节
```

```bash
$ man -K keyword #从第一张显示匹配到关键词的章节
```











##### **3. info 手册**

info手册的升级版 pinfo更友好，支持超链接跳转

```bash
$ pinfo keyword
```



##### **4. 红帽的门户文档**

本地：materials.example.com/docs/

或者：www.access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/8/