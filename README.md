# learn_shell_script

A note of my learning road map

## variables

### 变量代换

| 语法              | 说明                          |
| ----------------- | ----------------------------- |
| `${var#expr}`     | 从头匹配直到第一个 `expr`     |
| `${var##expr}`    | 从头匹配直到倒数第一个 `expr` |
| `${var%expr}`     | 从尾匹配直到第一个 `expr`     |
| `${var%%expr}`    | 从尾匹配直到倒数第一个 `expr` |
| `${var/old/new}`  | 从匹配第一个 `old`换成 `new`  |
| `${var//old/new}` | 从匹配所有 `old` 换成 `new`   |

### 变量测试

| 变量置换方式 | 变量y没有设置                  | 变量y为空值            | 变量y设置值 |
| ------------ | ------------------------------ | ---------------------- | ----------- |
| `x=${y-新值}` | x=新值                        | x 为空                 | x=$y        |
| `x=${y:-新值}` | x=新值                        | x=新值                | x=$y        |
| `x=${y+新值}` | x为空                         | x=新值                | x=新值      |
| `x=${y:+新值}` | x为空                         | x为空                 | x=新值      |
| `x=${y?新值}` | 新值输出到标准错误输出（屏幕） | x 为空                 | x=$y        |
| `x=${y:?新值}` | 新值输出到标准错误输出         | 新值输出到标准错误输出 | x=$y        |

### 计算字符串长度

| 语法                    | 说明                        |
| ----------------------- | --------------------------- |
| `${#string}`            |                             |
| `expr` length "$string" | string有空格必须加双引号`“` |

### 取子串

| 语法                                       | 说明                                     |
| ------------------------------------------ | ---------------------------------------- |
| `${string:position}`                       | 从`string`的`position`开始               |
| `${string:position:length}`                | 从`string`的`position`开始长度为`length` |
| `${string:-position}`                      | 从右开始匹配                             |
| `${string:(position)}`                     | 从左开始匹配                             |
| `${expr substr $string $position $length}` | `position` 开始长度为`length`            |

### 获取字符索引

| 语法                            | 说明 |
| ------------------------------- | ---- |
| `expr` index "$string" `substr` |      |

### 获取子串长度

| 语法                            | 说明 |
| ------------------------------- | ---- |
| `expr` match "$string" `substr` |      |

### 命令替换

```shell
用 `${command}` && `command` 直接计算出值
```

```sh
#!/bin/bash
#

index=1

for user in `cat /etc/passwd | cut -d ":" -f 1`
do
	echo "This is $index user: $user"
	index=$(($index + 1))
done
```

```sh
#!/bin/bash
#

echo "This year have passed $(date +%j) days"
echo "This year have passed $(($(date +%j)/7)) weeks"

echo "There is $((365 - $(date +%j))) days before new year"
echo "There is $(((365 - $(date +%j))/7)) weeks before new year"

```

```sh
#!/bin/bash
#

nginx_process_num=$(ps -ef | grep nginx | grep -v grep | wc -l)

if [ $nginx_process_num -eq 0 ];then
	systemctl start nginx
fi
```

### 有类型变量

**语法**

```sh
declare 与 tpyeset 等价
declare [+/-][rxi][${变量名称}＝${设置值}] 
declare -f
```

**参数说明**：

- +/- 　"-"可用来指定变量的属性，"+"则是取消变量所设的属性。
- -f 　仅显示函数。
- r 　将变量设置为只读。
- x 　指定的变量会成为环境变量，可供shell以外的程序来使用。
- i 　[设置值]可以是数值，字符串或运算式。

### 数学运算

| 运算操作符/运算命令 | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| `(( ))`             | 用于整数运算，效率很高，**推荐使用**。                       |
| `let`               | 用于整数运算，和 (()) 类似。                                 |
| `[$]`               | 用于整数运算，不如 (()) 灵活。                               |
| `expr`              | 可用于整数运算，也可以处理字符串。比较麻烦，需要注意各种细节，不推荐使用。 |
| `bc`                | `Linux`下的一个计算器程序，可以处理整数和小数。`Shell` 本身只支持整数运算，想计算小数就得使用 `bc` 这个外部的计算器。 |
| `declare -i`        | 将变量定义为整数，然后再进行数学运算时就不会被当做字符串了。功能有限，仅支持最基本的数学运算（加减乘除和取余），不支持逻辑运算、自增自减等，所以在实际开发中很少使用。 |

```sh
#!/bin/bash
# 数学运算

while true
do
	read -p "pls input a positive number: " num

	expr $num + 1 &> /dev/null

	if [ $? -eq 0 ];then
		if [ `expr $num \> 0` -eq 1 ];then
			for((i=1;i<=$num;i++))
			do
				sum=`expr $sum + $i`
			done	
			echo "1+2+3+....+$num = $sum"
			exit
		fi
	fi
	echo "error,input enlegal"
	continue
done

```

```sh
#!/bin/bash
# 浮点计算

read -p "num1: " num1
read -p "num2: " num2

num3=`echo "scale=4;$num1/$num2" | bc`


echo "$num1 / $num2 = $num3"

```



## function

### 函数定义

```sh
function name
{
	command1
	command2
	...
	commandn $1
	commando $2
	echo hello
	return 0
}
```

```sh
函数return只能返回0～255
echo则可以出任何值
```



## grep

### grep

**语法**

```
grep [-abcEFGhHilLnqrsvVwxy][-A<显示行数>][-B<显示列数>][-C<显示列数>][-d<进行动作>][-e<范本样式>][-f<范本文件>][--help][范本样式][文件或目录...]
```

**参数**：

- **-a 或 --text** : 不要忽略二进制的数据。
- **-A<显示行数> 或 --after-context=<显示行数>** : 除了显示符合范本样式的那一列之外，并显示该行之后的内容。
- **-b 或 --byte-offset** : 在显示符合样式的那一行之前，标示出该行第一个字符的编号。
- **-B<显示行数> 或 --before-context=<显示行数>** : 除了显示符合样式的那一行之外，并显示该行之前的内容。
- **-c 或 --count** : 计算符合样式的列数。
- **-C<显示行数> 或 --context=<显示行数>或-<显示行数>** : 除了显示符合样式的那一行之外，并显示该行之前后的内容。
- **-d <动作> 或 --directories=<动作>** : 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep指令将回报信息并停止动作。
- **-e<范本样式> 或 --regexp=<范本样式>** : 指定字符串做为查找文件内容的样式。
- **-E 或 --extended-regexp** : 将样式为延伸的正则表达式来使用。
- **-f<规则文件> 或 --file=<规则文件>** : 指定规则文件，其内容含有一个或多个规则样式，让grep查找符合规则条件的文件内容，格式为每行一个规则样式。
- **-F 或 --fixed-regexp** : 将样式视为固定字符串的列表。
- **-G 或 --basic-regexp** : 将样式视为普通的表示法来使用。
- **-h 或 --no-filename** : 在显示符合样式的那一行之前，不标示该行所属的文件名称。
- **-H 或 --with-filename** : 在显示符合样式的那一行之前，表示该行所属的文件名称。
- **-i 或 --ignore-case** : 忽略字符大小写的差别。
- **-l 或 --file-with-matches** : 列出文件内容符合指定的样式的文件名称。
- **-L 或 --files-without-match** : 列出文件内容不符合指定的样式的文件名称。
- **-n 或 --line-number** : 在显示符合样式的那一行之前，标示出该行的列数编号。
- **-o 或 --only-matching** : 只显示匹配PATTERN 部分。
- **-q 或 --quiet或--silent** : 不显示任何信息。
- **-r 或 --recursive** : 此参数的效果和指定"-d recurse"参数相同。
- **-s 或 --no-messages** : 不显示错误信息。
- **-v 或 --invert-match** : 显示不包含匹配文本的所有行。
- **-V 或 --version** : 显示版本信息。
- **-w 或 --word-regexp** : 只显示全字符合的列。
- **-x --line-regexp** : 只显示全列符合的列。
- **-y** : 此参数的效果和指定"-i"参数相同。

### egrep

Linux egrep命令用于在文件内查找指定的字符串。

egrep执行效果与"grep-E"相似，使用的语法及参数可参照grep指令，与grep的不同点在于解读字符串的方法。

egrep是用extended regular expression语法来解读的，而grep则用basic regular expression 语法解读，extended regular expression比basic regular expression的表达更规范。

**语法**

```
egrep [范本模式] [文件或目录] 
```

**参数说明：**

- [范本模式] ：查找的字符串规则。
- [文件或目录] ：查找的目标文件或目录。

**实例**

显示文件中符合条件的字符。例如，查找当前目录下所有文件中包含字符串"Linux"的文件，可以使用如下命令：

```sh
egrep Linux *
```

结果如下所示：

```sh
$ egrep Linux * #查找当前目录下包含字符串“Linux”的文件  
testfile:hello Linux! #以下五行为testfile 中包含Linux字符的行  
testfile:Linux is a free Unix-type operating system.  
testfile:This is a Linux testfile!  
testfile:Linux  
testfile:Linux  
testfile1:helLinux! #以下两行为testfile1中含Linux字符的行  
testfile1:This a Linux testfile!  
#以下两行为testfile_2 中包含Linux字符的行  
testfile_2:Linux is a free unix-type opterating system.  
testfile_2:Linux test  
xx00:hello Linux! #xx00包含Linux字符的行  
xx01:Linux is a free Unix-type operating system. #以下三行为xx01包含Linux字符的行  
xx01:This is a Linux testfile!  
xx01:Linux 
```

## sed

# Linux sed 命令

Linux sed 命令是利用脚本来处理文本文件。

sed 可依照脚本的指令来处理、编辑文本文件。

sed 主要用来自动编辑一个或多个文件、简化对文件的反复操作、编写转换程序等。

也可以用管道符传过来处理

**语法**

```sh
sed [-hnV][-e<script>][-f<script文件>][文本文件]
```

**参数说明**：

- -e<script>或--expression=<script> 以选项中指定的script来处理输入的文本文件。
- -f<script文件>或--file=<script文件> 以选项中指定的script文件来处理输入的文本文件。
- -h 或--help 显示帮助。
- -n 或--quiet或--silent 仅显示script处理后的结果。
- -V 或--version 显示版本信息。

**动作说明**：

- a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
- c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
- d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
- i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
- p ：打印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～
- s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是啦！

**实例**

在testfile文件的第四行后添加一行，并将结果输出到标准输出，在命令行提示符下输入如下命令：

```sh
sed -e 4a\newLine testfile 
```

首先查看testfile中的内容如下：

```sh
$ cat testfile #查看testfile 中的内容  
HELLO LINUX!  
Linux is a free unix-type opterating system.  
This is a linux testfile!  
Linux test 
```

使用sed命令后，输出结果如下：

```sh
$ sed -e 4a\newline testfile #使用sed 在第四行后添加新字符串  
HELLO LINUX! #testfile文件原有的内容  
Linux is a free unix-type opterating system.  
This is a linux testfile!  
Linux test  
newline 
```

**以行为单位的新增/删除**

将 /etc/passwd 的内容列出并且列印行号，同时，请将第 2~5 行删除！

```sh
[root@www ~]# nl /etc/passwd | sed '2,5d'
1 root:x:0:0:root:/root:/bin/bash
6 sync:x:5:0:sync:/sbin:/bin/sync
7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
.....(后面省略).....
```

sed 的动作为 '2,5d' ，那个 d 就是删除！因为 2-5 行给他删除了，所以显示的数据就没有 2-5 行罗～ 另外，注意一下，原本应该是要下达 sed -e 才对，没有 -e 也行啦！同时也要注意的是， sed 后面接的动作，请务必以 '' 两个单引号括住喔！

只要删除第 2 行

```sh
nl /etc/passwd | sed '2d' 
```

要删除第 3 到最后一行

```sh
nl /etc/passwd | sed '3,$d' 
```

在第二行后(亦即是加在第三行)加上『drink tea?』字样！

```sh
[root@www ~]# nl /etc/passwd | sed '2a drink tea'
1 root:x:0:0:root:/root:/bin/bash
2 bin:x:1:1:bin:/bin:/sbin/nologin
drink tea
3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....(后面省略).....
```

那如果是要在第二行前

```sh
nl /etc/passwd | sed '2i drink tea' 
```

如果是要增加两行以上，在第二行后面加入两行字，例如 **Drink tea or .....** 与 **drink beer?**

```sh
[root@www ~]# nl /etc/passwd | sed '2a Drink tea or ......\
> drink beer ?'
1 root:x:0:0:root:/root:/bin/bash
2 bin:x:1:1:bin:/bin:/sbin/nologin
Drink tea or ......
drink beer ?
3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....(后面省略).....
```

每一行之间都必须要以反斜杠『 \ 』来进行新行的添加喔！所以，上面的例子中，我们可以发现在第一行的最后面就有 \ 存在。

**以行为单位的替换与显示**

将第2-5行的内容取代成为『No 2-5 number』呢？

```sh
[root@www ~]# nl /etc/passwd | sed '2,5c No 2-5 number'
1 root:x:0:0:root:/root:/bin/bash
No 2-5 number
6 sync:x:5:0:sync:/sbin:/bin/sync
.....(后面省略).....
```

透过这个方法我们就能够将数据整行取代了！

仅列出 /etc/passwd 文件内的第 5-7 行

```sh
[root@www ~]# nl /etc/passwd | sed -n '5,7p'
5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
6 sync:x:5:0:sync:/sbin:/bin/sync
7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
```

可以透过这个 sed 的以行为单位的显示功能， 就能够将某一个文件内的某些行号选择出来显示。

**数据的搜寻并显示**

搜索 /etc/passwd有root关键字的行

```sh
nl /etc/passwd | sed '/root/p'
1  root:x:0:0:root:/root:/bin/bash
1  root:x:0:0:root:/root:/bin/bash
2  daemon:x:1:1:daemon:/usr/sbin:/bin/sh
3  bin:x:2:2:bin:/bin:/bin/sh
4  sys:x:3:3:sys:/dev:/bin/sh
5  sync:x:4:65534:sync:/bin:/bin/sync
....下面忽略 
```

如果root找到，除了输出所有行，还会输出匹配行。

使用-n的时候将只打印包含模板的行。

```sh
nl /etc/passwd | sed -n '/root/p'
1  root:x:0:0:root:/root:/bin/bash
```

**数据的搜寻并删除**

删除/etc/passwd所有包含root的行，其他行输出

```sh
nl /etc/passwd | sed  '/root/d'
2  daemon:x:1:1:daemon:/usr/sbin:/bin/sh
3  bin:x:2:2:bin:/bin:/bin/sh
....下面忽略
#第一行的匹配root已经删除了
```

**数据的搜寻并执行命令**

搜索/etc/passwd,找到root对应的行，执行后面花括号中的一组命令，每个命令之间用分号分隔，这里把bash替换为blueshell，再输出这行：

```sh
nl /etc/passwd | sed -n '/root/{s/bash/blueshell/;p;q}'    
1  root:x:0:0:root:/root:/bin/blueshell
```

最后的q是退出。

**数据的搜寻并替换**

除了整行的处理模式之外， sed 还可以用行为单位进行部分数据的搜寻并取代。基本上 sed 的搜寻与替代的与 vi 相当的类似！他有点像这样：

```sh
sed 's/要被取代的字串/新的字串/g'
```

先观察原始信息，利用 /sbin/ifconfig 查询 IP

```sh
[root@www ~]# /sbin/ifconfig eth0
eth0 Link encap:Ethernet HWaddr 00:90:CC:A6:34:84
inet addr:192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0
inet6 addr: fe80::290:ccff:fea6:3484/64 Scope:Link
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
.....(以下省略).....
```

本机的ip是192.168.1.100。

将 IP 前面的部分予以删除

```sh
[root@www ~]# /sbin/ifconfig eth0 | grep 'inet addr' | sed 's/^.*addr://g'
192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0
```

接下来则是删除后续的部分，亦即： 192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0

将 IP 后面的部分予以删除

```sh
[root@www ~]# /sbin/ifconfig eth0 | grep 'inet addr' | sed 's/^.*addr://g' | sed 's/Bcast.*$//g'
192.168.1.100
```

**多点编辑**

一条sed命令，删除/etc/passwd第三行到末尾的数据，并把bash替换为blueshell

```sh
nl /etc/passwd | sed -e '3,$d' -e 's/bash/blueshell/'
1  root:x:0:0:root:/root:/bin/blueshell
2  daemon:x:1:1:daemon:/usr/sbin:/bin/sh
```

-e表示多点编辑，第一个编辑命令删除/etc/passwd第三行到末尾的数据，第二条命令搜索bash替换为blueshell。

**直接修改文件内容(危险动作)**

sed 可以直接修改文件的内容，不必使用管道命令或数据流重导向！ 不过，由於这个动作会直接修改到原始的文件，所以请你千万不要随便拿系统配置来测试！ 我们还是使用文件 regular_express.txt 文件来测试看看吧！

regular_express.txt 文件内容如下：

```sh
[root@www ~]# cat regular_express.txt 
runoob.
google.
taobao.
facebook.
zhihu-
weibo-
```

利用 sed 将 regular_express.txt 内每一行结尾若为 . 则换成 !

```sh
[root@www ~]# sed -i 's/\.$/\!/g' regular_express.txt
[root@www ~]# cat regular_express.txt 
runoob!
google!
taobao!
facebook!
zhihu-
weibo-
```

:q:q

利用 sed 直接在 regular_express.txt 最后一行加入 **# This is a test**:

```sh
[root@www ~]# sed -i '$a # This is a test' regular_express.txt
[root@www ~]# cat regular_express.txt 
runoob!
google!
taobao!
facebook!
zhihu-
weibo-
# This is a test
```

由于 $ 代表的是最后一行，而 a 的动作是新增，因此该文件最后新增 **# This is a test**！

sed 的 **-i** 选项可以直接修改文件内容，这功能非常有帮助！举例来说，如果你有一个 100 万行的文件，你要在第 100 行加某些文字，此时使用 vim 可能会疯掉！因为文件太大了！那怎办？就利用 sed 啊！透过 sed 直接修改/取代的功能，你甚至不需要使用 vim 去修订！

## awk

