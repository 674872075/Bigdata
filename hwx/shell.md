# shell学习 #
Shell是一个用c语言编写的程序，通过shell用户可以访问操作系统内核服务。它类似于DOS下的command和后来的cmd.exe。Shell即是一种命令语言，又是一种程序设计语言。

Shell script是一种为shell编写的脚本程序。Shell编程一般指shell脚本编程，不是指开发shell自身。

Shell编程跟java、php编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。

Linux的Shell种类众多，一个系统可以存在多个shell，可以通过cat /etc/shells命令查看系统中安装的shell。

Bash由于易用和免费，在日常工作中被广泛使用。同时，Bash也是大多数Linux系统默认的Shell

**注：**使用vi编辑器新建一个文件hello.sh。**扩展名并不影响脚本执行，见名知意。比如用php写shell脚本，扩展名就用.php**

##shell语法##
**注：变量和等号之间不要空格。除此之外，所有的地方都要加空格！！！**

##shell的几条说明##
1、echo  类比java中的sout(system.out.println)

2、飘号：esc键下面``。里面放的是用于执行的shell脚本

3、> --一个尖表示**覆盖**的功能。 >> --2个尖表示**追加**的功能

4、定义函数时，function 可以省略


## shell实例 ##
一键安装jdk[由于markdown中的#表示注释，所以下列代码的第1行的#号旁边加一个单引号]
    
'#!/bin/bash

tar -zxvf /export/softwares/jdk-8u141-linux-x64.tar.gz -C /export/servers/

cd /export/servers/jdk1.8.0_141
home=`pwd`

echo $home

echo "export JAVA_HOME=${home}"  >> /etc/profile
echo "export PATH=:\$PATH:\$JAVA_HOME/bin" >> /etc/profile


for m in  2 3
do
scp -r /export/servers/jdk1.8.0_141 node0$m:/export/servers/
ssh node0$m "echo 'export JAVA_HOME=/export/servers/jdk1.8.0_141' >> /etc/profile; echo 'export PATH=:\$PATH:\$JAVA_HOME/bin' >> /etc/profile"

done












`