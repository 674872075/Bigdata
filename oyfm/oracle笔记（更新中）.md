#要点
1.oracle严格区分字符串大小写，mysql不区分
2.关于null:
	1.包含null的表达都为空，空值永远不等于空值：
		nvl(A,B)是个函数，作用是如果第一个参数A为空值，则返回第二个参数B的值，否则返回第一个参数A的值。
		例：select empno,ename,sal,sal*12,comm,sal*12+nvl(comm,0)，当comm为null时候，返回0再进行四则运算
	2.如果集合中含有null，则不能使用not in，但是可以使用in
	3.在order by 中，如果需要desc的列中含有空值，则空值会排在前面，这样做不符合数据显示习惯。所以可以在语句后面加上nulls last；空值会在正常数据排序之后进行显示
		*在oracle中null值最大
	4.组函数count会自动过滤空值;可以使用nvl，将null的返回值为0就可以统计出数量(嵌套滤空函数)
3.日期格式敏感，可以通过设置日期格式进行格式的匹配
	alter session set NLS_DATE_FORMAT='yyyy-mm-dd'
4.关于使用between and:注意1.含有边界  2.小值在前，大值在后
5.当模糊搜索条件中含有% _之类的特殊含义字符，就需要使用转义字符
	例：where ename like '%\_%' escape '\'
6.使用and语句时，where A and B，其中虽然执行的结果是一样的，但是在oracle中是完全不同的两条语句
7.order by 作用于后面的所有的列，先按照第一个列排序，再后面的列
		*desc只作用于离他最近的列
8.select lower('Hello World') 转小写,upper('Hello World') 转大写,initcap('hello world') 首字母大写
9.substr(a,b,c) 从a中，第b位开始取，取c位

10--当前时间
 	select sysdate from dual;
11.--next_day
--下一个星期四 select next_day(sysdate,'星期四') from dual;
12.--coalesce 从左到右 找到第一个不为null的值
 select comm,sal,coalesce(comm,sal) "第一个不为null的值"
13.--给员工涨工资，总裁1000 经理800 其他400
	SQL> select ename,job,sal 涨前,
   case job when 'PRESIDENT' then sal+1000
 	        when 'MANAGER' then sal+800
           else sal+400
          end 涨后
14--多个列的分组
 select deptno,job,sum(sal)
   from emp
   group by deptno,job
   order by 1;
15.--多个列的分组: 先按照第一个列分组，如果相同，再第二个列分组，以此类推
--查询平均工资大于2000的部门
 select deptno,avg(sal)
  from emp
  group by deptno
  having avg(sal) > 2000;
16.--where和having的区别：where不能使用多行函数
17. /*
 希望把某些不成立的记录（40号部门），任然包含在最后的结果中 ---> 外连接
 左外连接: 当where e.deptno=d.deptno不成立的时候，等号左边的表任然被包含在最后的结果中
    写法:where e.deptno=d.deptno(+)
 右外连接: 当where e.deptno=d.deptno不成立的时候，等号右边的表任然被包含在最后的结果中
    写法:where e.deptno(+)=d.deptno
*/
	 select d.deptno 部门号,d.dname 部门名称,count(e.empno) 人数
    from emp e,dept d
    where e.deptno(+)=d.deptno
    group by d.deptno,d.dname;
18.自连接: 通过表的别名，将同一张表视为多张表
 select e.ename 员工姓名,b.ename 老板姓名
   from emp e,emp b
   where e.mgr=b.empno;
19. --自连接不适合操作大表
 --层次查询
 select level,empno,ename,mgr
   from emp
   connect by prior empno=mgr
   start with mgr is null
    order by 1;

#sql优化：
1.在sql语句中做查询语句的时候尽量避免使用*，多使用列名
2.where解析的顺序，从右往左 
3.尽量使用where
		