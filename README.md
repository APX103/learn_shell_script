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

```
egrep Linux *
```

结果如下所示：

```
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



## awk

