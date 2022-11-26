Tmux是很强大的一个终端工具。

在接触它的时候项目需要实现自动分屏，但它的文档和使用教程都只介绍了如何用快捷键分屏操作。后面试过模拟键盘进行操作来自动分屏，但模拟的键盘很容易出现bug，所以想要实现精准的控制自动分出想要的布局，只能通过指令来实现。

这里只介绍tmux的分屏（pane）有关的指令：

左右分屏： tmux split-pane -h 对应快捷键： Ctrl+b  %

上下分屏：tmux spilt-pane -v   对应快捷键： Ctrl+b  "

查看各个分屏（pane）的编号： 快捷键： Ctrl + b  q

选择指定 pane ：确定编号后（这里假设要选择0号pane）： tmux select-pane -t 编号

> 或者 Ctrl + b 然后方向键

关闭分屏： 快捷键 Ctrl + b  x

设置pane的尺寸：resize-pane -t 编号 -x width -y width  快捷键：Ctrl+b 方向键



下面为网上能搜索到的一些基本指令：

------

vi             emacs        功能

^       M-m     反缩进

Escape     C-g     清除选定内容

Enter     M-w     复制选定内容

j       Down     光标下移

h       Left     光标左移

l       Right    光标右移

L              光标移到尾行

M       M-r     光标移到中间行

H       M-R     光标移到首行

k       Up      光标上移

d       C-u     删除整行

D       C-k     删除到行末

$       C-e     移到行尾

:       g      前往指定行

C-d      M-Down    向下滚动半屏

C-u      M-Up     向上滚动半屏

C-f      Page down  下一页

w       M-f     下一个词

p       C-y     粘贴

C-b      Page up   上一页

b       M-b     上一个词

q       Escape    退出

C-Down or J  C-Down    向下翻

C-Up or K   C-Up     向下翻

n       n      继续搜索

?       C-r     向前搜索

/       C-s     向后搜索

0       C-a     移到行首

Space     C-Space   开始选中

 C-t     字符调序



启动新会话：

tmux [new -s 会话名 -n 窗口名]

恢复会话：

tmux at [-t 会话名]

列出所有会话：

tmux ls

[关闭会话：]()

tmux kill-session -t 会话名

[关闭所有会话：]()

tmux ls | grep : | cut -d. -f1 | awk '{print substr($1, 0,

length($1)-1)}' | xargs kill



调整窗口排序

swap-window -s 3 -t 1 交换 3 号和 1 号窗口

swap-window -t 1    交换当前和 1 号窗口

move-window -t 1    移动当前窗口到 1 号



作者：万事胜意_030b
链接：https://www.jianshu.com/p/24a9d8563e1c
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。