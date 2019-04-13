##grep命令##

 grep 命令是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并
 把匹配的行打印出来。
 格式： grep [option] pattern [file]
 可使用 —help 查看更多参数

	 ps -ef | grep sshd 查找指定 ssh 服务进程
	 ps -ef | grep sshd | grep -v grep 查找指定服务进程，排除 gerp 本身
	 ps -ef | grep sshd –c 查找指定进程个数
	 cat a.txt | grep -f b.txt 从文件中读取关键词进行搜索
	 cat a.txt | grep -f b.txt 从文件中读取关键词进行搜索
	 输出 a.txt 文件中含有从 b.txt 文件中读取出的关键词的内容行
	 cat a.txt | grep –nf b.txt 从文件中读取关键词进行搜索,显示行号
	 grep -n 'hello' a.txt  从文件中查找关键词，并显示行号
	 cat test.txt |grep ^u 找出以 u 开头的行内容
	 cat test.txt |grep ^[^u] 输出非 u 开头的行内容
	 cat test.txt |grep hat$ 输出以 hat 结尾的行内容
	 cat test.txt |grep -E "ed|at" 显示包含 ed 或者 at 字符的内容行


##find命令##

find 命令在目录结构中搜索文件，并对搜索结果执行指定的操作。
find 默认搜索当前目录及其子目录，并且不过滤任何结果（也就是返回所
有文件），将它们全都显示在屏幕上。
实际参数很多，可使用 —help 查看。

	find . -name "*.log" -ls 在当前目录查找以.log 结尾的文件， 并显示详细信息。
	find /root/ -perm 777 查找/root/目录下权限为 777 的文件
	find . -type f -name "*.log" 查找当目录，以.log 结尾的普通文件
	find . -type d | sort 查找当前所有目录并排序
	find . -size +100M 查找当前目录大于 100M 的文件

##Locate命令##

locate 让使用者可以很快速的搜寻档案系统内是否有指定的档案。其方法
是先建立一个包括系统内所有档案名称及路径的数据库。之后当寻找时就只需查
询这个数据库（ /var/lib/locatedb）。
Linux 系统自动创建这个数据库， 默认每天自动更新一次，所以使用 locate
命令查不到最新变动过的文件。为了避免这种情况，可以在使用 locate 之前，
先使用 updatedb 命令，手动更新数据库。
如果是精简版 CentOS 系统需要安装 locate 命令

    yum -y install mlocate
	updatedb 命令来创建 locate 命令依赖的数据库
	updatedb
	locate /etc/sh
	搜索 etc 目录下所有以 sh 开头的文件
	locate pwd
	查找和 pwd 相关的所有文件

##whereis命令##

whereis 命令是定位可执行文件、源代码文件、帮助文件在文件系统中的位
置。这些文件的属性应属于原始代码，二进制文件，或是帮助文件。
whereis 和下 locate 一样，会从数据库中查找数据，而不是像 find 命令那
样，通过遍历硬盘来查找

	whereis ls 将和 ls 文件相关的文件都查找出来
	ls: /bin/ls /usr/share/man/man1/ls.1.gz
	whereis -m ls 查找 ls 命令说明文档路径
	whereis -s ls 查找 ls 源文件

## which命令 ##

which 命令的作用是在 PATH 变量指定的路径中，搜索某个系统命令的位置，
并且返回第一个搜索结果。
使用 which 命令，就可以看到某个系统命令是否存在，以及执行的到底是哪
一个位置的命令。

	which pwd 查找 pwd 命令所在路径
	/bin/pwd
	which java 查找 path 中 java 的路径
	/root/apps/jdk1.8.0_65/bin/java
	which 是根据使用者所配置的 PATH 变量内的目录去搜寻可运行档， 所以，
	不同的 PATH 配置内容所找到的命令会不一样

## 用户与用户组 ##

	useradd   hadoop    #   这个就表示我们创建了一个普通用户
	passwd   hadoop    # 表示我们需要给hadoop这个普通用户分配一个密码,密码需要自己设置
	添加用户组：groupadd  storm  表示我们自己添加了一个storm的组

## su与sudo ##

- su的使用

   切换用户：linux当中可以使用su来切换不同的用户角色
   su   root   表示我们需要切换成其他用户,需要使用密码

   su 在不加任何参数，默认为切换到 root 用户，但没有转到 root 用户根目
录下； su 加参数 - ，表示默认切换到 root 用户，并转到 root 用户根目录下。

- sudo使用

	命令行输入 visudo，打开/etc/sudoers 文件
	hadoop  ALL=(ALL)       ALL
	这样普通用户就可以使用 sudo 执行 root 权限的命令了

##linux的权限管理##

chmod  改变文件的执行权限
chmod 777 abc.txt    表示给某个文件赋予所有人的所有权限

- 	chmod(change mode) 功能：变更文件或目录的权限。
- 	语法：chmod [参数] [<权限范围><符号><权限代号>]
- 	-R或--recursive 　递归处理，将指定目录下的所有文件及子目录一并处理。
- 	权限范围的表示法如下：
 
		u：User，即文件或目录的拥有者。 
		g：Group，即文件或目录的所属群组。 
		o：Other，除了文件或目录拥有者或所属群组之外，其他用户皆属于这个范围。 
		a：All，即全部的用户，包含拥有者，所属群组以及其他用户。

- 	符号：
 	
		+ 添加权限
		
- 取消权限

	    有关权限代号的部分，列表于下： 
	    	r：读取权限，数字代号为"4"。 
	    	w：写入权限，数字代号为"2"。 
	    	x：执行或切换权限，数字代号为"1"。 
	    	-：不具任何权限，数字代号为"0"。
	
	    mkdir xxx
	    ll | grep xxx
	    chmod u-rwx xxx		#取消xxx目录，用户“读写执行”权限
	    chmod g-rwx xxx 	#取消xxx目录，组“读写执行”权限
	    chmod 777 xxx		#给xxx目录添加所有权限

 **chown -R hadoop:hadoop abc.txt  改变某个文件或者文件夹的所属的用户以及用户组** 

- 第一个参数  -R   表示我们递归的进行改变
- 第二个参数 hadoop:hadoop  表示我们的用户以及用户组
- 第三个参数:我们需要改变的文件或者文件夹


##系统服务管理##

	    service iptables status  #查看防火墙状态
	    service  iptables stop  #关闭防火墙
	    service --status-all # 查看系统所有的后台服务进程
	    service sshd status # 查看指定的后台服务进程的状态
	    service sshd stop
	    service sshd start
	    service sshd restart
	    配置后台服务进程的开机自启或关闭
	    chkconfig iptables on  #配置防火墙开机开启
	    chkconfig iptables off #配置防火墙开机关闭
	    chkconfig httpd on ## 让 httpd 服务开机自启
	    chkconfig httpd off ## 让 httpd 服务开机不要自启



