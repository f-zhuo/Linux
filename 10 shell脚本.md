shell是解释性语言，面向过程

在编写shell脚本时，因为不同shell的命令不一定相同，故要指定使用的shell，称为shebang机制，这里使用bash shell

```shell
#!/bin/bash
```

编写其他程序语言也一样

```shell
#!/usr/bin/python
```

编写hello_world.sh文件，#表示注释

```shell
# vim hello_world.sh

#!/bin/bash
echo hello world
```

执行文件的三种方法：
* `. /tmp/hello_world.sh`或者`bash /tmp/hello_world.sh`或者`source /tmp/hello_world.sh`或者` cat /tmp/hello_world.sh | bash`
* 先赋予执行文件的权限，再执行
  `chmod +x hello_world.sh`
  `cd /root/tmp`
  `./hello_world.sh`

* 放入path路径中

**检查**

检查语法错误

```shell
bash -n file
```

调试执行

```shell
bash -x file
```

查看脚本是否执行成功/上个命令执行结果是否成功

```shell
$?
```

0表示成功，非0不成功；还可以查看函数的返回结果（见下文）

# echo

直接打印或打印变量

```shell
echo a
a=1
echo $a

echo 'xxx' #直接打印xxx字符串，不进行转义和取变量
echo "xxx" #进行转义，取变量

# 转义
echo \"xxx\"

# 打印执行cmd结果
echo `cmd xxx` # echo $(cmd xxx)

# 组合
echo x{a,b,c} 
echo x{a,b,c}.{d,f}
echo x{a..z}        # a到z
echo x{1..10..2}    # 1到10，步长2
echo x{001..10..2}
```

# printf

打印函数，可以格式化输出，但不能自动换行

|  命令  |                             作用                             |
| :----: | :----------------------------------------------------------: |
| %-10s  |  输出字符串，左对齐，长度为10，不足用空格填补，超过全部显示  |
| %+10s  | 输出字符串，右对齐（默认），长度为10，不足用空格填补，超过全部显示 |
| %4.2f  |        输出浮点数，右对齐（默认），长度为4，小数点2位        |
| %-4.2f |            输出浮点数，左对齐，长度为4，小数点2位            |
|  %3d   |              输出整数，右对齐（默认），长度为3               |
|  %-3d  |                  输出整数，左对齐，长度为3                   |

```bash
printf "%-10s" "hello"

printf "%4.1" 12.34

printf "%4d" 123
```

| 转义字符 |    说明    |
| :------: | :--------: |
|    \b    |    后退    |
|    \a    |    警告    |
|    \f    |    换页    |
|    \n    |    换行    |
|    \r    |    回车    |
|    \t    | 水平制表符 |
|    \v    | 垂直制表符 |
|   `\\`   |     \      |

```bash
printf "hello,\nworld\n"
```

*awk中可使用print，不能格式化输出，但可以自动换行*

# read

输入，相当于input

```bash
read name
# lilei
echo $name
# lilei
```

# 变量

shell不需要声明变量类型，可以直接赋值，也可以将命令结果赋值，用``表示执行命令，用$引用，变量名区分大小写

```shell
name='zhangsan' # 变量名和=之间不能有空格
MyHostname=`hostname`
echo ${name} ${MyHostname} # {}作用是识别变量边界，如${host}name表示host变量和name字符串
# echo $name $MyHostname
```

*shell不支持浮点数*

*单引号中的字符会原样输出，变量是无效的；双引号中可以有变量*

命名规则

* 避免关键字
* 使用数字，字母和下划线，不能以数字开头
* 尽量采用驼峰命名法/下划线分隔

## 全局变量和局部变量

默认定义的是局部变量，定义全局变量要用export声明

```shell
name='zhangsan'
export name

# export name='zhangsan'
```

全局变量可以在子进程中使用，但在子进程中修改全局变量不会影响父进程；局部变量只在当前会话中有效

当前进程的id

```shell
echo $$
```

后台运行的最后一个进程的id

```bash
echo $!
```

显示所有变量

```shell
set
```

删除name变量

```shell
unset name
```

## 只读变量

不能修改和删除，会话结束自动删除

```shell
readonly name='zhangsan'
```

定义一次性的变量，开启一个子bash，命令执行完子bash就关闭，变量也不存在

```shell
(name='zhangsan';echo $name)
```

## 位置变量

在shell脚本中调用，命令行中传递

```shell
# cat test.sh
#!/bin/bash
echo "1st is $1"
echo "2nd is $2"
echo "filename is $0"
echo "Number is $#"
echo "All are $*"
set --
echo "truncate"
echo "$*"

#. test.sh a b
```

* `$1`：第一个变量
* `${10}`：第十个变量，$10表示第一个变量加0
* `$0`：命令本身
* `$#`：变量个数
* `$*/$@`：所有变量，前者传递给脚本的所有参数会被认为是一个字符串，后者传递的所有参数每个都认为是独立的字符串

* `set --`：清空所有位置变量

**shift**：参数左移

```shell
shift
shfit 2
```

## 字符串

* 拼接字符串

```bash
a="hello"
b="world"
echo $a","$b
```

* 获取字符串长度

```shell
echo ${#a}
```

* 截取字符串

```shell
echo ${a:1:4} # 从第2个字符串开始截取4个
```

* 查找字符串

```shell
echo `expr index "$a" e` # 在a变量中查找e
```

### 字符串运算符

|   命令    |         作用         |
| :-------: | :------------------: |
| [ -z $a ] |   a长度为0返回true   |
| [ -n $a ] |  a长度为0返回false   |
|  [ $a ]   | a为空字符串返回false |

## 数组

shell只有一维数组，用()定义，元素用空格隔开，下标从0开始

```shell
Array1=(arr1 arr2 arr3)
```

用下标赋值

```shell
Array1[0]=arr3
```

数组取值

```shell
${Aarry1[0]}

# 取出所有值
${Array1[@]}
```

获取数组长度

```shell
${#Array1[@]}

# 获取数组单个元素的长度
${#Array1[0]}
```

# 算术运算

shell默认输入的都是字符串

```shell
x=1
y=2
echo $x+$y #1+2

let sum=x+y
product=$[x*y]
minus=$((x-y))

echo $sum
echo $product
echo $minus
```

bash不支持数学运算，可以通过expr实现

```bash
echo `expr 1 + 2`
```

*expr的数字和运算符要有空格*

### 逻辑与或非

* 与：& /-a

* 或：|/-o

* 非：!

* 异或：^

* 短路与&&：a&&b，a为0，结果一定为0

* 短路或||：a||b，a为1，结果一定为1

优先级：`!` >`-a`> `-o`

逻辑关系判断：[ ]和(())

```shell
x=zhangsan
y=lisi
[ "$x" = "$y" ] && echo equal || echo no equal #[]注意空格
```

[ ]中的符号判断：

* `=` `-eq` `-ne`：判断两个数是否相等
*  `!=` ：判断两个数是否不相等
*  `-lt`：判断左边是否小于右边
*  `-le`：判断左边是否小于等于右边
*  `-gt`：判断右边是否大于左边
*  `-ge`：判断右边是否大于等于左边

(())中的逻辑判断：

* `==`判断两个数是否相等
* `!=`判断两个数是否不相等
* `>`判断左边是否大于右边
* `>=`判断左边是否大于等于右边
* `<`判断左边是否小于右边
* `<=`判断左边是否小于等于右边

*(())和[]的区别在于，前者的变量可以不加$*

文件测试符

| 参数      | 说明                           |
| --------- | ------------------------------ |
| -e 文件名 | 文件存在则为真                 |
| -r 文件名 | 如果文件存在且可读则为真       |
| -w 文件名 | 如果文件存在且可写则为真       |
| -x 文件名 | 如果文件存在且可执行则为真     |
| -d 文件名 | 如果文件存在且为目录则为真     |
| -f 文件名 | 如果文件存在且为普通文件则为真 |

# 条件语句

## if

```bash
# 模式
if then fi
```

```shell
# 脚本文件中

#!/bin/bash

if [ 2 -gt 1] then
	echo true
fi

# 命令行中
if [ 2 -gt 1];then echo true;fi

if (( 2 > 1 ));then echo true;fi
```

## if else

```bash
# 模式
if then else fi
```

```bash
# 脚本文件中

#!/bin/bash

if [ 2 -lt 1] then
	echo true
else 
	echo false
fi

# 命令行中
if [ 2 -lt 1 ];then echo true;else echo false;fi
```

## if elif else

```bash
# 模式
if then elif then else fi
```

```shell
# 脚本文件中

#!/bin/bash

if [ 2 -eq 1 ] then
	echo "2等于1"
elif [ 2 -lt 1 ] then
	echo "2小于1"
else 
	echo "2大于1"
fi

# 命令行中
if [ 2 -eq 1 ];then echo "2等于1";elif [ 2 -lt 1];then "2小于1";else echo "2大于1";fi
```

## case

```bash
# 模式
case in pattern1) ;; pattern2) ;; *) ;;esac
```

```shell
i="c";case $i in "a") echo "i=a";; "b") echo "i=b";; "c") echo "i=c";; *) echo "i=others";;esac
```

```bash
i="c";case $i in "a"|"b"|"c") echo "i在abc之间";; *) echo "i=others";;esac
```

`;;`表示的是break，case语句会逐一对每个条件比较，一旦符合就结束，后面的不会再匹配

# 循环语句

## for

```bash
# 模式
for in do done
```

```shell
for i in 1 2 3;do printf "%d\n" $i;done
```

对于in字符串，会按照原来的格式输出

```bash
for i in "Hello world!";do echo $i;done
```

无限循环

```bash 
for (( : : ))
```

## while

```bash
# 模式
while do done
```

```bash
i=0;while (( i<5 ));do echo $i;let i++;done
```

无限循环

```bash
while true
```

## until

```bash
# 模式
until do done
```

```shell
i=0;until (( i>5 ));do echo $i;let i++;done
```

*until和while的区别在于，前者是条件为真停止循环，后者是条件为真继续循环；until至少循环执行一次*

# 中断

## break

```shell
i=0;while (( i<5 ));do let i++;if (( i==3 ));then break;fi;echo $i;done 
```

跳出整个循环

## continue

```shell
i=0;while (( i<5 ));do let i++;if (( i==3 ));then continue;fi;echo $i;done 
```

跳出本次循环

# 函数

```bash
# 定义，return可选
func_name(){
	func
	return 
}

# 调用
func_name
```

```shell
#!/bin/bash

test(){
	echo "This is a test function"
	return 100
}

test

# echo "函数返回值是$?"
```

## 函数参数

采用位置参数传递和调用

```bash
#!/bin/bash

test2(){
	echo "第一个参数是$1"
	echo "第二个参数是$2"
	echo "第三个参数是$3"
	echo "参数个数是$#"
	echo "所有参数是$*"
}

test2 1 2 3
```

