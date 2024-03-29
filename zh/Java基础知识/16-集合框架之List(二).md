###16.01_集合框架(去除ArrayList中重复字符串元素方式)(掌握)
* A:案例演示
	* 需求：ArrayList去除集合中字符串的重复值(字符串的内容相同)
	* 思路：创建新集合方式

			/**
			 *  A:案例演示
			 * 需求：ArrayList去除集合中字符串的重复值(字符串的内容相同)
			 * 思路：创建新集合方式
			 */
			public static void main(String[] args) {
				ArrayList list = new ArrayList();
				list.add("a");
				list.add("a");
				list.add("b");
				list.add("b");
				list.add("b");
				list.add("c");
				list.add("c");
				list.add("c");
				list.add("c");
				
				System.out.println(list);
				ArrayList newList = getSingle(list);
				System.out.println(newList);
			}
		
			/*
			 * 去除重复
			 * 1,返回ArrayList
			 * 2,参数列表ArrayList
			 */
			public static ArrayList getSingle(ArrayList list) {
				ArrayList newList = new ArrayList();			//创建一个新集合
				Iterator it = list.iterator();					//获取迭代器
				while(it.hasNext()) {							//判断老集合中是否有元素
					String temp = (String)it.next();			//将每一个元素临时记录住
					if(!newList.contains(temp)) {				//如果新集合中不包含该元素
						newList.add(temp);						//将该元素添加到新集合中
					}
				}
				return newList;									//将新集合返回
			}
###16.02_集合框架(去除ArrayList中重复自定义对象元素)(掌握)
* A:案例演示
	* 需求：ArrayList去除集合中自定义对象元素的重复值(对象的成员变量值相同)
* B:注意事项
	* 重写equals()方法的

###16.03_集合框架(LinkedList的特有功能)(掌握)
* A:LinkedList类概述
* B:LinkedList类特有功能
	* public void addFirst(E e)及addLast(E e)
	* public E getFirst()及getLast()
	* public E removeFirst()及public E removeLast()
	* public E get(int index);

###16.04_集合框架(栈和队列数据结构)(掌握)
* 栈
	* 先进后出 
* 队列
	* 先进先出

###16.05_集合框架(用LinkedList模拟栈数据结构的集合并测试)(掌握)
* A:案例演示
	* 需求：请用LinkedList模拟栈数据结构的集合，并测试
	* 创建一个类将Linked中的方法封装
	* 
			public class Stack {
				private LinkedList list = new LinkedList();		//创建LinkedList对象
				
				public void in(Object obj) {
					list.addLast(obj);							//封装addLast()方法
				}
				
				public Object out() {
					return list.removeLast();					//封装removeLast()方法
				}
				
				public boolean isEmpty() {
					return list.isEmpty();						//封装isEmpty()方法
				}
			}
	

###16.06_集合框架(泛型概述和基本使用)(掌握)
* A:泛型概述
* B:泛型好处
	* 提高安全性(将运行期的错误转换到编译期) 
	* 省去强转的麻烦
* C:泛型基本使用
	* <>中放的必须是引用数据类型 （添加的时候可以放本类型或其子类型）
* D:泛型使用注意事项
	* 前后的泛型必须一致,或者后面的泛型可以省略不写(1.7的新特性菱形泛型)  

###16.07_集合框架(ArrayList存储字符串和自定义对象并遍历泛型版)(掌握)
* A:案例演示
	* ArrayList存储字符串并遍历泛型版

###16.08_集合框架(泛型的由来)(了解)
* A:案例演示
	* 泛型的由来:通过Object转型问题引入
	* 早期的Object类型可以接收任意的对象类型，但是在实际的使用中，会有类型转换的问题。也就存在这隐患，所以Java提供了泛型来解决这个安全问题。

###16.09_集合框架(泛型类的概述及使用)(了解)
* A:**泛型类概述<T>**
	* 把泛型定义在类上
* B:定义格式
	* public class 类名<泛型类型1,…>
* C:注意事项	
	* 泛型类型必须是引用类型
	* **自定义泛型类时，方法泛型最好与类的泛型一致，如果不一致，需要在方法上声明该泛型（例如public<T> void say(T t){}）**
* D:案例演示
	* 泛型类的使用

###16.10_集合框架(泛型方法的概述和使用)(了解)
* A:泛型方法概述
	* 把泛型定义在方法上
* B:定义格式	
	* public <泛型类型> 返回类型 方法名(泛型类型 变量名)
	* ** 注意：静态方法必须声明自己的泛型，因为静态方法是随着类的加载而加载的，而类的泛型随着对象的创建才会被初始化**
	*				
					 public static<W> void print(W w) {		//静态方法必须声明自己的泛型
						System.out.println(w);
						}
* C:案例演示
	* 泛型方法的使用

###16.11_集合框架(泛型接口的概述和使用)(了解)
* A:泛型接口概述
	* 把泛型定义在接口上
* B:定义格式	
	* public interface 接口名<泛型类型>
* C:案例演示
	* 泛型接口的使用

###16.12_集合框架(泛型高级之通配符)(了解)
* A:泛型通配符<?>
	* 任意类型，如果没有明确，那么就是Object以及任意的Java类了
	* **当右边的泛型不确定时，左边可以指定为?,保证编译能通过，但真正添加元素时要确定泛型，只写前面（菱形泛型）或前后一致**
* B:? extends E(Collections<? extends E> 类型只要是Collections集合就行，? extends E 向下限定，E及其子类)
	* 向下限定，E及其子类
* C:? super E
	* 向上限定，E及其父类


###16.13_集合框架(增强for的概述和使用)(掌握)
* A:增强for概述（JDK1.5新特性）
	* 简化数组和Collection集合的遍历
* B:格式：
* 
		for(元素数据类型 变量 : 数组或者Collection集合) {
			使用变量即可，该变量就是元素
		}
* C:案例演示
	* 数组，集合存储元素用增强for遍历
* D:好处
	* 简化遍历
	* **增强for循环底层依赖的是迭代器**


###16.14_集合框架(ArrayList存储字符串和自定义对象并遍历增强for版)(掌握)
* A:案例演示
	* ArrayList存储字符串并遍历增强for版
	* 
			ArrayList<String> list = new ArrayList<>();
			list.add("a");
			list.add("b");
			list.add("c");
			list.add("d");
			
			for(String s : list) {
				System.out.println(s);
			}
###16.15_集合框架(三种迭代的能否删除)(掌握)
* **普通for循环,可以删除,但是索引要--**
* 迭代器,可以删除,但是必须使用迭代器自身的remove方法,否则会出现并发修改异常
* 增强for循环不能删除（底层为迭代器实现）

###16.16_集合框架(静态导入的概述和使用)(掌握)
* A:静态导入概述（JDK1.5新特性）
* B:格式：
	* import static 包名….类名.方法名;
	* 可以直接导入到方法的级别（导入类中的静态方法）
* C:注意事项
	* 方法必须是静态的,如果有多个同名的静态方法，容易不知道使用谁?这个时候要使用，必须加前缀。由此可见，意义不大，所以一般不用，但是要能看懂。

###16.17_集合框架(可变参数的概述和使用)(掌握)
* A:**可变参数概述(可变参数的实质其实是一个该类型的数组)**（JDK1.5新特性）
	* 定义方法的时候不知道该定义多少个参数
* B:格式
	* 修饰符 返回值类型 方法名(数据类型…  变量名){}
* C:注意事项：
	* 这里的变量其实是一个数组
	* **如果一个方法有可变参数，并且有多个参数，那么，可变参数肯定是最后一个**

###16.18_集合框架(Arrays工具类的asList()方法的使用)(掌握)
* A:案例演示（JDK1.5新特性）
	* Arrays工具类的asList()方法的使用
	* **数组本身是属于引用数据类型的，但因为所存储的数据类型不同被分为存储基本数据类型的数组（存储值）和存储引用数据类型的数组（存储地址值）**
	* **数组转换成集合虽然不能增加或减少元素,但是可以用集合的思想操作数组,也就是说可以使用集合中的其它方法**
	*   			
	   			    String[] arr = {"a","b","c"};
		            List<String> list = Arrays.asList(arr);//将数组转换成集合
		            //list.add("d");//不能添加，会产生不能修改的异常

					//List<int[]> list = Arrays.asList(arr);			
 					//基本数据类型的数组转换成集合,会将整个数组当作一个对象转换
					
					//将数组转换成集合,数组必须是存储引用数据类型的数组
					List<Integer> list = Arrays.asList(arr);
	
* Collection中toArray(T[] a)泛型版的集合转数组
*     
                 String[] arr = list.toArray(new String[10]);		//当集合转换数组时,数组长度如果是小于等于集合的size时,转换后的数组长度等于集合的size
															//如果数组的长度大于了size,分配的数组长度就和你指定的长度一样                                        

###16.19_集合框架(集合嵌套之ArrayList嵌套ArrayList)(掌握)
* A:案例演示
	* 集合嵌套之ArrayList嵌套ArrayList

###16.20_day16总结
* 把今天的知识点总结一遍。