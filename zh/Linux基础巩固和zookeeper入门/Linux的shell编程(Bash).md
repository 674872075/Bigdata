##入门示例##
- 使用 vi 编辑器新建一个文件 hello.sh。 扩展名并不影响脚本执行，见名知
意

	    #!/bin/bash
	    echo "Hello World !"
	    #!是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用
	    哪一种 Shell。
	    echo 命令用于向窗口输出文本。

- 执行方式
	
		①chmod -r 777 hello.sh 
		./hello.sh

		②sh hello.sh

## shell变量 ##

**变量＝值，如： your_name="itcast.cn"**

**注意：变量名和等号之间不能有空格，同时，变量名的命名须遵循如下规则：**

- 首个字符必须为字母（ a-z， A-Z）
- 中间不能有空格，可以使用下划线（ _）
- 不能使用标点符号
- 不能使用 bash 里的关键字（可用 help 命令查看保留关键字）

**变量使用：**

		使用一个定义过的变量，只要在变量名前面加 $ 即可。
		your_name="itcast.cn"
		echo $your_name
		echo ${your_name}
		花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界。
		已定义的变量，可以被重新定义。
		使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。
		使用 unset 命令可以删除变量。 不能删除只读变量。
		readonly variable_name
		unset variable_name

## 变量类型 ##

**局部变量 ：**

- 局部变量在脚本或命令中定义，仅在当前 shell 实例中有效，其
- 他 shell 启动的程序不能访问局部变量。

**环境变量 ：**

- 所有的程序，包括 shell 启动的程序，都能访问环境变量，有些程
- 序需要环境变量来保证其正常运行。 可以用过 set 命令查看当前环境变量。

**shell 变量 ：**

- shell 变量是由 shell 程序设置的特殊变量。 shell 变量中有一
- 部分是环境变量，有一部分是局部变量，这些变量保证了 shell 的正常运行
- 参数传递
- 在执行 Shell 脚本时， 可以向脚本传递参数。
- 脚本内获取参数的格式为： $n。 n 代表一个数字， 1 为执行脚本的第一个参
- 数， 2 为执行脚本的第二个参数，以此类推…… $0 表示当前脚本名称。

**特殊字符**

		$# 	传递到脚本的参数个数
		$* 	以一个单字符串显示所有向脚本传递的参数。
		$$ 	脚本运行的当前进程 ID 号
		$! 	后台运行的最后一个进程的 ID 号
		$@ 	与$*相同，但是使用时加引号，并在引号中返回每个参数。
		$? 	显示最后命令的退出状态。 0 表示没有错误，其他任何值表明有错误。

		例子：
		#!/bin/bash
		echo "第一个参数为： $1";
		echo "参数个数为： $#";
		echo "传递的参数作为一个字符串显示： $*";
		执行脚本： ./test.sh 1 2 3

**命令$*和$@区别**

		相同点： 都表示传递给脚本的所有参数。
		不同点：
		不被" "包含时， $*和$@都以$1 $2… $n 的形式组成参数列表。
		被" "包含时， "$*" 会将所有的参数作为一个整体，以"$1 $2 … $n"
		的形式组成一个整串； "$@" 会将各个参数分开，以"$1" "$2" … "$n" 的
		形式组成一个参数列表。

## Shell 运算符 ##

		Shell 和其他编程语音一样，支持包括：算术、关系、 布尔、字符串等运
		算符。 原生 bash 不支持简单的数学运算，但是可以通过其他命令来实现，例如
		expr。 expr 是一款表达式计算工具，使用它能完成表达式的求值操作。例如加，减，乘，除等操作
		注意：表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2。
		完整的表达式要被 ` ` 包含，注意不是单引号，在 Esc 键下边。
		例如，以下表示一些数据的运算
 		#!/bin/bash
		echo "hello world"
		a=4
		b=20

**加法运算**

		echo `expr $a + $b`

**减法运算**

		echo `expr $b - $a`

**乘法运算，注意*号前面需要反斜杠**

		echo `expr $a \* $b`

**除法运算**

		echo `expr $b / $a`

		注意：
		此外，还可以通过(())、 $[]进行算术运算。
		count=1
		((count++))
		echo $count
		a=$((1+2))
		a=$[1+2]

## 4.3、流程控制 ##

		if  else  语句
		if condition1
		then
		command1
		elif condition2
		then
		command2
		else
		commandN
		fi

## 条件表达式写法 ##

		EQ 就是 EQUAL等于
		NQ 就是 NOT EQUAL不等于 
		GT 就是 GREATER THAN大于　 
		LT 就是 LESS THAN小于 
		GE 就是 GREATER THAN OR EQUAL 大于等于 
		LE 就是 LESS THAN OR EQUAL 小于等于

		#!/bin/bash
		
		a=10
		b=20
		
		if [ $a -eq $b ]
		then
		   echo "$a -eq $b : a 等于 b"
		else
		   echo "$a -eq $b: a 不等于 b"
		fi
		if [ $a -ne $b ]
		then
		   echo "$a -ne $b: a 不等于 b"
		else
		   echo "$a -ne $b : a 等于 b"
		fi
		if [ $a -gt $b ]
		then
		   echo "$a -gt $b: a 大于 b"
		else
		   echo "$a -gt $b: a 不大于 b"
		fi
		if [ $a -lt $b ]
		then
		   echo "$a -lt $b: a 小于 b"
		else
		   echo "$a -lt $b: a 不小于 b"
		fi
		if [ $a -ge $b ]
		then
		   echo "$a -ge $b: a 大于或等于 b"
		else
		   echo "$a -ge $b: a 小于 b"
		fi
		if [ $a -le $b ]
		then
		   echo "$a -le $b: a 小于或等于 b"
		else
		   echo "$a -le $b: a 大于 b"
		fi
		
## for循环 ##

**方式一**

		for N in 1 2 3
		do
		echo $N
		done
		或
		for N in 1 2 3; do echo $N; done
		或
		for N in {1..3}; do echo $N; done

**方式二**

		for ((i = 0; i <= 5; i++))
		do
		echo "welcome $i times"
		done
		或
		for ((i = 0; i <= 5; i++)); do echo "welcome $i times"; done
		
## 需求：使用for循环打印数字以及当前系统所有进程 ##

		#!/bin/bash
		#循环打印 1 2 3这三个数字
		for N in 1 2 3
		do
		 echo $N
		done
		a=1
		b=2
		c=3
		for N in  $a $b $c
		do
		 echo $N
		done

## 打印当前系统所有进程 ##

		for N in `ps -ef`
		do 
		 echo $N
		done
		
## while语法 ##

**方式一**

		while expression
		do
		command
		…
		done

**方式二：**

		#!/bin/bash
		i=1
		while (( i <= 3))
		do 
		 let i++
		 echo $i
		done
		
		#echo $i
		let 命令是 BASH 中用于计算的工具，用于执行一个或多个表达式，变量
		计算中不需要加上 $ 来表示变量。 自加操作： let no++ 自减操作： let no—
		
**方式三： 无限循环**

		while true
		do
		command
		done
		case语句
		case 值 in
		模式 1)
		command1
		command2
		...
		commandN
		;;
		模式 2）
		command1
		command2
		...
		commandN
		;;
		esac


## 使用case语句实现交互会话 ##

		#!/bin/bash
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


## 4.4、函数使用 ##

**所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell 解释器首次发现它时，才可以使用。 调用函数仅使用其函数名即可。**

		[ function ] funname [()]
		{
		action;
		[return int;]
		} 
		、可以带 function fun()定义，也可以直接 fun() 定义,不带任何参数。
		2、参数返回，可以显示加 return ，如果不加，将以最后一条命令运行结
		果，作为返回值。 return 后跟数值 n(0-255)。
		
**函数参数**

		在 Shell 中，调用函数时可以向其传递参数。在函数体内部， 通过 $n 的形
		式来获取参数的值，例如， $1 表示第一个参数， $2 表示第二个参数...
		注意， 当 n>=10 时，需要使用${n}来获取参数。
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
		
**定义我们自己的函数并在shell脚本当中调用函数**

		#!/bin/bash
		hello(){
		 echo "hello world"
		 echo "第一个参数为 $1"
		 echo "第二个参数为 $2"
		}
		hello abc 123
