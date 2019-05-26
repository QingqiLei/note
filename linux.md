[TOC]

### 端口

netstat -anpt | grep 1000

### vim

#### 一般模式

* yy 复制当前行

y 数字   复制 数字行

* p 复制到下一行

*   dd 删除当前行

d2  删除两行

* x 删除一个字母

* yw 复制一个词

* dw 删除一个词

* shift ^ 移动到行头

* shift $ 移动到行尾

* 1 + shift + g 移动到页头

* shift g 移动到页尾

* 数字N shift g 移动到目标行

#### 编辑模式

i   a   o  

esc

#### 指令模式

:  wq! （！ 表示强制执行）

/ 查找

! 查找



#### 文件目录

* cp  cp -r source dest   (如何dest 不是目录， 也可以重命名)
* rm -rf ****
* cat　查看文件内容　cat -n  行号， cat -A
* tac 倒着显示文件内容
* more  一页一页显示（ctrl b 向上 ）   （ctrl  f 向下）（= 行号）   （f 文件名， 行号）
* less 和more 很像， 不同的是less 使用pageup pagedown 前后滚动
* head 查看头几行
* tail 查看末尾几行  （tail -n -数字  文件名） 显示末尾几行  （tail -f ） 实时追踪文档的所有更新 
* （ls -l > 文件名     把当前文件夹的信息覆盖写入log 文件中）（ll >> 文件名， 追加到文件中）
* echo （echo “hello world” > 文件名） 覆盖写  （echo "hello world" >> 文件名） 追加写
* ln -s 源文件 目标文件

### 时间

#### date

* 显示当前时间  date (date +%Y-%m-%d)    （date "+%Y-%m-%d  %H:%M:%S"）
* 显示非当前时间  date -d "next day"   "+%Y-%m-%d  %H:%M:%S"     date -d "yesterday"   date -d "next week"  date -d "next Monday"
* 设置当前时间： date -s "2017-06-30 11:45:45"
* 查看日历 cal     cal -3     cal 2017

### 用户组管理命令

* groupadd   组名
* groupdel  组名
* groupmod   (usermod -g 组名  用户)
* cat / etc/ group  查看现在有哪些组

#### 文件权限

​	-rw-r--r--  1 chauncey chauncey    220 Nov 17 13:00 .bash_logout

​	drwxr-xr-x  3 root     root       4096 Nov 17 13:00 ../

* 第一位：  -: 表示文件,    d: 文件夹
* 第2到4位 ：  用户主权限： r：读， w：写， x: 执行  -： 没有权限
* 第5 到7位： 用户组权限
* 第8 -10： 其他用户的权限
* 第十一位： 链接数， 文件嵌套了几层
* 第十二位： 文件拥有者
* 第十三位： 没有制定组名的话，默认和用户名相同
* 第十四位：　文件大小
* 第十五位：创建时间
* 第十六位：　文件名称

#### chmod

chmod:  用户主（ｏ），　用户组（ｇ）,其他用户（ｕ），所有　（ａ）

chmod u+x yi     chmod g-w yi   chmod o+w yi    chmod a+x yi    chmod 777 yi

#### chown

chown 用户名　文件

#### chgrp　

chgrp  用户组  文件

### 磁盘信息

#### fdisk -l

查看分区

#### df -h

查看磁盘

### 搜索

#### find 

find 搜索范围  匹配条件

find /home/chauncey -name *.txt

find /home/chauncey -user chauncey 

find /home/chauncey -size +10   (大于十个字节)

#### grep

ls -l | grep s   (二次匹配, 列出文件夹内名字中包含s 的所有文件) 

### 进程线程类

### ps 

ps -aux | grep 1

#### top

-d 秒数

-i 不显示任何闲置或者僵死进程

-p 指定监控进程ID来仅仅监控某个进程的状态

-s 在安全模式中运行, 去除交互命令所带来的潜在危险

操作:  P 以CPU 排序, M: 以内存排序,  N 以PID 排序, q退出

#### pstree

-u 显示进程属于谁

-p 显示进程的PID 

#### kill 

kill -9  PID 

#### netstat

netstat -anp

### 压缩

#### gzip  gunzip

只能压缩文件,不能压缩目录, 不保留原来的文件

#### zip unzip

zip  生成文件的名字  将要压缩的内容  ()

#### tar -zcvf  tar -zxvf

### crond 定时任务

45 22 * * * 每天22 点45时执行

0 15 * *１　每周一下午１５点０分

0 5 1,15 * * 每月的１号和１５号上午５点

40 4 * * 1-5　周一到周五上午４点40

*/10 4 *** 每天4点来时,每隔10分钟

0 0 1,15 * 1 周一或者1号15号 凌晨

#### crontab -e

*/1 * * * * echo "hello world" >> /home/chauncey/testlog 

### dep

sudo dpkg -i 软件名

sudo apt-get remove 软件名

将.rpm 转变为.dep

sudo alien 文件名.rpm

### rmp

* rpm -qa 列出

* rpm -ivh 安装

* rpm -e 卸载



### shell script

* 以 #!/bin/bash 开头

#### variable

echo $HOME

echo $PWD

echo $SHELL

echo $USER

#### 设置变量

ss=1000

echo $ss

unset ss

#### readonly 

readonly SS=1000

1. 变量名称可以由字母,数字,下划线组成,不能以数字开头
2. 等号两侧不能有空格
3. 变量名称一般习惯为大写
4. 双引号把空格脱意,单引号将特殊字符脱意

SJ="song\tjiang"

echo $SJ       song	jiang

SJ="song\tjiang"

echo $SJ       song\tjiang

#### 将命令的返回值赋值变量

A=`ls -la`

A=$(ls -la)



#### 参数

* $n  第n个参数
* $*  所有参数, 把所有参数看成一个整体
* $@  所有参数, 把每个参数区分对待
* $# 所有参数的个数

#### 预定义变量

* $$  当前进程的进程号 (PID)

* $! 最后一个进程的进程号 (PID)
* $? 最后一个执行的命令的返回状态, 0 :正确执行，　非0: 不正确执行

#### 运算符

* $((运算式))
* $[运算式]

A=$[4+5]    A=$[(2 + 3) * 4 ]

#### 条件判断

if [ $1 -eq "123" ]
then echo "123"
fi

elif  [ $1 -eq "456" ] then echo "456"

fi

#### for循环

```shell
for i in "$*"
do 
echo "$1"
done

for j in "$@"
do 
echo "$j"
done

s=0
for((i=1;i <= 100; i++))
do
s=$[$s+$i]
done
echo $s

```

#### while

```shell
s=0
i=0
while [ $i -le 100 ]
do 
s=$[$s+$i]
i=$[$i+1]
done
echo "$s"
```

 

#### read

```SHELL
read -t 5 -p "input your name please: " NAME
echo $NAME
```



#### basename dirname

```shell
basename /opt/shuihu/for1.sh
dirname /opt/shuihu/for1.sh
```

#### function

```shell
function sum()
{
    s=0
    s=$[ $1 + $2 ]
    echo "$s"
}

read -p "Please input the number1: " n1;
read -p "please input the number2: " n2;
sum $n1 $n2;
echo "$s"
```



### linux 优化

#### 1 开启文件系统的预读缓存可以提高读取速度

`sudo blockdev --setra 32768 /dev/sda`

#### 2 关闭进程睡眠池

` sudo sysctl -w vm.swappiness=0`

#### 3 调整ulimit 上线, 

打开进程数, 打开最大文件数

`sudo vim  /etc/security/limits.conf`  文件数

追加 

```
*                soft    nofile          1024000
*                hard    nofile          1024000
Hive             -       nofile          1024000
hive             -       nproc           1024000 
```

sudo vim  /etc/security/limits.d/20-nproc.conf 修改用户打开进程数限制

```
#*          soft    nproc     4096
#root       soft    nproc     unlimited
*          soft    nproc     40960
root       soft    nproc     unlimited
```

#### 4 集群内部时间统一

#### 5 不升级系统





