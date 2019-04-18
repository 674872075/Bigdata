###14.01_常见对象(正则表达式的概述和简单使用)
* A:正则表达式
	* 是指一个用来描述或者匹配一系列符合某个语法规则的字符串的单个字符串。其实就是一种规则。有自己特殊的应用。
	* 作用:比如注册邮箱,邮箱有用户名和密码,一般会对其限制长度,这个限制长度的事情就是正则表达式做的
* B:案例演示
	* 需求：校验qq号码.
		* 1:要求必须是5-15位数字
		* 2:0不能开头
		* 3:必须都是数字
		
	* a:非正则表达式实现
	* b:正则表达式实现

###14.02_常见对象(字符类演示)
* A:字符类
	* [abc] a、b 或 c（简单类） 
	* [^abc] 任何字符，除了 a、b 或 c（否定） 
	* [a-zA-Z] a到 z 或 A到 Z，两头的字母包括在内（范围） 并集 &&符号代表交集
	* [0-9] 0到9的字符都包括
	* **[]代表单个字符‘\’代表转义字符**
###14.03_常见对象(预定义字符类演示)
* A:预定义字符类
	* . 任何字符。
	* \d 数字：[0-9]
	* \w 单词字符：[a-zA-Z_0-9]
###14.04_常见对象(数量词)
* A:Greedy 数量词 
	* X? X，零次或一次(一次或一次也没有)
	* X* X，零次或多次
	* X+ X，一次或多次
	* X{n} X，恰好 n 次 
	* X{n,} X，至少 n 次 
	* X{n,m} X，至少 n 次，但是不超过 m 次 

###14.05_常见对象(正则表达式的分割功能)
* A:正则表达式的分割功能
	* String类的功能：public String[] split(String regex)
* B:案例演示
	* 正则表达式的分割功能

###14.06_常见对象(把给定字符串中的数字排序)
* A:案例演示
	* 需求：我有如下一个字符串:”91 27 46 38 50”，请写代码实现最终输出结果是：”27 38 46 50 91”

###14.07_常见对象(正则表达式的替换功能)
* A:正则表达式的替换功能
	* String类的功能：public String replaceAll(String regex,String replacement)
* B:案例演示
	* 正则表达式的替换功能

###14.08_常见对象(正则表达式的分组功能)
* A:正则表达式的分组功能
	* 捕获组可以通过从左到右计算其开括号来编号。例如，在表达式 ((A)(B(C))) 中，存在四个这样的组： 
* 
		1     ((A)(B(C))) 
		2     (A 
		3     (B(C)) 
		4     (C) 
	
		组零始终代表整个表达式。
B:案例演示
	a:切割
		需求：请按照叠词切割： "sdqqfgkkkhjppppkl";
	b:替换
		需求：我我....我...我.要...要要...要学....学学..学.编..编编.编.程.程.程..程
		将字符串还原成:“我要学编程”。
###14.09_常见对象(Pattern和Matcher的概述)
* A:Pattern和Matcher的概述
* B:模式和匹配器的典型调用顺序
	* 通过JDK提供的API，查看Pattern类的说明

	* 典型的调用顺序是 
	* Pattern p = Pattern.compile("a*b");
	* Matcher m = p.matcher("aaaaab");
	* boolean b = m.matches();

###14.10_常见对象(正则表达式的获取功能)
* A:正则表达式的获取功能
	* Pattern和Matcher的结合使用
* B:案例演示
	* 需求：把一个字符串中的手机号码获取出来


###14.11_常见对象(Math类概述和方法使用)
* A:Math类概述
	* Math 类包含用于执行基本数学运算的方法，如初等指数、对数、平方根和三角函数。 
* B:成员方法
	* public static int abs(int a) 			返回绝对值
	* public static double ceil(double a) 	向上取整,结果是一个double值
	* public static double floor(double a)  向下取整,结果是一个double值
	* public static int max(int a,int b) 	返回两数中的最大值
	* public static int min(int a,int b) 	返回两数中的最小值
	* public static double pow(double a,double b)     返回第一个参数的第二个参数次幂的值
	* public static double random()  		返回带正号的 double 值，该值大于等于 0.0 且小于 1.0。
	* public static int round(float a)  	返回最接近参数的 long。(四舍五入)
	* public static double sqrt(double a) 	开平方

###14.12_常见对象(Random类的概述和方法使用)
* A:Random类的概述
	* 此类用于产生随机数如果用相同的种子创建两个 Random 实例，
	* 则对每个实例进行相同的方法调用序列，它们将生成并返回相同的数字序列。
* B:构造方法
	* public Random()
	* public Random(long seed)
* C:成员方法
	* public int nextInt()
	* public int nextInt(int n)(重点掌握)

###14.13_常见对象(System类的概述和方法使用)
* A:System类的概述
	* System 类包含一些有用的类字段和方法。它不能被实例化。 
* B:成员方法
	* public static void gc()  运行垃圾回收器。
	* public static void exit(int status) 终止当前正在运行的 Java 虚拟机。
	* public static long currentTimeMillis()  当前时间与协调世界时 1970 年 1 月 1 日午夜之间的时间差（以毫秒为单位测量）。
	* pubiic static void arraycopy(Object src, int  srcPos, Object dest, int destPos, int length)  从指定源数组中复制一个数组，复制从指定的位置开始，到目标数组的指定位置结束。
* C:案例演示
	* System类的成员方法使用

###14.14_常见对象(BigInteger类的概述和方法使用)
* A:BigInteger的概述
	* 可以让超过Integer范围内的数据进行运算
* B:构造方法
	* public BigInteger(String val)
* C:成员方法
	* public BigInteger add(BigInteger val)  加法运算
	* public BigInteger subtract(BigInteger val)  减法运算
	* public BigInteger multiply(BigInteger val)  乘法运算
	* public BigInteger divide(BigInteger val)    除法运算
	* public BigInteger[] divideAndRemainder(BigInteger val)   返回包含 (this / val) 后跟 (this % val) 的两个 BigInteger 的数组。
   
###14.15_常见对象(BigDecimal类的概述和方法使用)
* A:BigDecimal的概述
	* 由于在运算的时候，float类型和double很容易丢失精度，演示案例。
	* 所以，为了能精确的表示、计算浮点数，Java提供了BigDecimal

	* 不可变的、任意精度的有符号十进制数。
* B:构造方法
	* public BigDecimal(String val)
* C:成员方法
	* public BigDecimal add(BigDecimal augend)
	* public BigDecimal subtract(BigDecimal subtrahend)
	* public BigDecimal multiply(BigDecimal multiplicand)
	* public BigDecimal divide(BigDecimal divisor)
* D:案例演示
	* BigDecimal类的构造方法和成员方法使用

###14.16_常见对象(Date类的概述和方法使用)(掌握)
* A:Date类的概述
	* 类 Date 表示特定的瞬间，精确到毫秒。 
* B:构造方法
	* public Date()
	* public Date(long date)
* C:成员方法
	* public long getTime()
	* public void setTime(long time)

###14.17_常见对象(SimpleDateFormat类实现日期和字符串的相互转换)(掌握)
* A:DateFormat类的概述
	* DateFormat 是日期/时间格式化子类的抽象类，它以与语言无关的方式格式化并解析日期或时间。是抽象类，所以使用其子类SimpleDateFormat
* B:SimpleDateFormat构造方法
	* public SimpleDateFormat()
	* public SimpleDateFormat(String pattern)
* C:成员方法
	* public final String format(Date date)
	* public Date parse(String source)


###14.18_常见对象(你来到这个世界多少天案例)(掌握)
* A:案例演示
	* 需求：算一下你来到这个世界多少天?

###14.19_常见对象(Calendar类的概述和获取日期的方法)(掌握)
* A:Calendar类的概述
	* Calendar 类是一个抽象类，它为特定瞬间与一组诸如 YEAR、MONTH、DAY_OF_MONTH、HOUR 等日历字段之间的转换提供了一些方法，并为操作日历字段（例如获得下星期的日期）提供了一些方法。
* B:成员方法
	* public static Calendar getInstance()
	* public int get(int field)


###14.20_常见对象(Calendar类的add()和set()方法)(掌握)
* A:成员方法
	* public void add(int field,int amount)
	* public final void set(int year,int month,int date)
* B:案例演示
	* Calendar类的成员方法使用

###14.21_常见对象(如何获取任意年份是平年还是闰年)(掌握)
* A:案例演示
	* 需求：键盘录入任意一个年份，判断该年是闰年还是平年

###14.22_day14总结
* 把今天的知识点总结一遍。