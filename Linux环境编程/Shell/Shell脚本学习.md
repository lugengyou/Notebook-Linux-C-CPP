# Shell 脚本学习

1、Linux 的 shell 种类：

​	Bourne Shell -> (/usr/bin/sh或/bin/sh)

​	Bourne Again Shell -> (/bin/basj)

​	C Shell -> (/usr/bin/csh)

​	K Shell -> (/usr/bin/ksh)

​	Shell for Root -> (/sbin/sh)

 

2、执行 shell 脚本文件

chmod +x ./shell_file_name.sh # 修改文件为可执行文件权限

./shell_file_name.sh # 执行文件

 

3、Shell 变量

变量名和等号之间不能有空格

变量名的命名规则：

- 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- 中间不能有空格，可以使用下划线 **_**。
- 不能使用标点符号。
- 不能使用bash里的关键字（可用help命令查看保留关键字）。     

 

推荐给所有变量加上花括号，这是个好的编程习惯

如：${var_name}

定义标量不加 {}，使用变量加 {}

 

只读变量，在定义完变量后，使用 readonly var_name 指定只读变量

删除变量，使用 unset var_name 删除指定变量

 

变量类型：

1）局部变量，只在当前 shell 示例中有效，其他 shell 脚本不能访问

2）环境变量，所有程序都能访问

3）shell 变量，由 shell 程序设置的特殊变量，shell变量中有一部分是环境变量，一部分是局部变量

 

shell 字符串：

** shell 编程最常用最有用的数据类型，可用 ''、""、不用引号 表示

'' 单引号字符串：原样输出，变量在其中无效，但可以成对出现

"" 双引号字符串：可以有变量（用于拼接字符串），可以出现转义字符

获取字符串长度：${#str_name}

提取子字符串：${str_name:1:4}

可查找字符串：`expr index "$string" io` 反引号搜索字符 i/o 的位置

 

shell 数组：

bash 仅支持一维数组

定义：

ar_name=(val1 val2 val3…)

ar_name=(

val1

val2

val3

…  )

ar_name[0]=val1…

读取数组：

${ar_name[index]}

获取字符串长度：

${ar_name[@]}

${ar_name[*]}

${ar_name[n]}

 

shell 注释：

单行注释：以 # 开头

多行注释：

1）:

<<EOF

…

EOF

2）:

<<'

…

'

3）:

<<!

…

!

 

4、shell 传递参数

脚本获取参数格式为：$n，n 表示参数的序号

参数处理符合：

$#：传递到脚本的参数个数

$$: 脚本执行当前进程的 ID 号

$*: "$1 $2 $3…"，以字符串显示脚本传递的参数

$@: "$1" "$2"… 形式显示脚本传递的参数

$?: 显示最后命令推出的状态，0表示没有错误，其他值表示有错误

 

5、关联数组

Bash 支持关联数组，可以用任意字符串或整数作为下表访问数组

语法：

declare -A ar_name=(["ar1"]="hello world" …)

获取所有元素：

${ar_name[*]} / ${ar_name[@]}

获取数组长度：

${#ar_name[*]} / ${#ar_name[@]}

 

6、shell 基本运算符

算数运算符、关系运算符、布尔运算符、字符串运算符、文件测试运算符

expr 是一款表达式计算工具，如 2+2：`expr 2 + 2`

算术运算符：

乘号(*)前边必须加(\)，即 \*

关系运算符：

是否相等： -eq

是否不等：-ne

…

布尔运算符：

与 -a，或 -o，非 !

逻辑运算：

逻辑与：&&

逻辑或：||

字符串运算：

是否相等：=

是否不等：!=

长度是否为 0：-z

是否不为 0：-n

是否不为空：$

 

7、shell echo 命令

echo：用于输出字符串命令

显示变量：

"${var_name}"

显示换行：

"\n"

显示不换行：

"\c"

显示结果到指定文件：

echo "showed string" > file

显示命令执行结果：

echo `date`，注意是反引号，结果将显示当前日期

 

8、shell printf 命令

printf命令格式：printf format-string [参数]

如：printf "hello world\n"

printf "%-10s" 姓名，10 代表**位宽**

格式替代符：

%s %c %d %f，加位宽则左对齐，负责为右对齐

 

9、shell test 命令

test 命令用于检测某个条件是否成立，可以进行数值、字符、文件运算符三方面测试

 

10、shell 流程控制

shell 流程控制**不可为空**

if else 语法：

**if** condition

then

command

else

command

**fi**

if else-if else 语法：

**if** condition1

then

command1

**elif** condition2

then

command2

else

command3

**fi**

 

for 循环：

for var in item1 item2

do

command

**done**

 

while 语句：

while condition

do

command

done

无限循环、until 循环、case … esac、break、continue

 

12、shell 函数

定义格式：

function_name[()]

{

action;

[return int;]

}

可以带 function fun() 定义，也可以直接 fun() 定义，不带任何参数

参数返回，若不加return，则将最后一条命令运行结果作为返回

 

13、shell 输入/输出重定向

默认情况下，输入输出面向终端

重定向命令列表：

command > file：将输出重定向到 file；file会被覆盖，>> 追加方式重定向到file

command < file：将输入重定向到 file

…

 

14、shell 文件包含

shell 脚本可以包含外部脚本

语法：

. ./shell_file.sh

 