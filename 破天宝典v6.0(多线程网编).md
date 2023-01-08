--------------API第一天--------------

Object: 它是所有类的超类，祖宗类 
		java中所有的类都直接或间接的继承这个类
	方法(都要了解)
		public int hashCode() 返回当前对象的哈希码值，理解为对象的地址值
		public String toString() 返回当前对象中的内容, 对于Object类默认操作来说，返回的
		          包名.类名@哈希码值
		public boolean equals(Object obj) 比较两个对象内容是否相同， 
		        对于Object类默认操作来说,比较的是地址值

		public Class getClass() 该对象运行时的类   字节码文件对象

String: 字符串类(重点)
		字符串是常量；它们的值在创建之后不能更改
	
    构造方法：(掌握,重点是能知道什么意思)
		public String() 空参数构造方法
		public String(String original) 就是字符串字面值，封装成对象  
		//下面方法做为一个了解...因为会在以后IO的代码中会看到使用
  		public String(byte[] bytes) 把字节数组转换字符串
 		public String(byte[] bytes, int startIndex,  int length) 把字节数组一部分元素 转换成字符串
  		public String(char[] value) 把字符数组 转换成字符串
  		public String(char[] value, int startIndex, int count) 把字符数组一部分元素 转换成字符串
  				
 方法：
	判断功能()
		boolean equals(Object obj) 判断两个字符串中的内容是否相同
		boolean equalsIgnoreCase(String str)  判断两个字符串中的内容是否相同, 忽略大小写
		boolean contains(String str) 判断该字符串中 是否包含给定的字符串      
		boolean startsWith(String str) 判断该字符串 是否以给定的字符串开头
		boolean endsWith(String str) 判断该字符串 是否以给定的字符串结尾
		boolean isEmpty() 判断该字符串的内容是否为空的字符串  "" 

	获取功能：
		int length() 获取该字符串的长度
		char charAt(int index) 获取该字符串中指定位置上的字符 
		String substring(int start) 从指定位置开始，到末尾结束，截取该字符串，返回新字符串
		String substring(int start,int end) 从指定位置开始，到指定位置结束，截取该字符串，返回新字符串 
		//下面是关于索引的一些查询  这个不作为掌握 但是要了解字符串中有这样的功能
		int indexOf(int ch ) 获取给定的字符，在该字符串中第一次出现的位置
		int indexOf(String str) 获取给定的字符串，在该字符串中第一次出现的位置
		int indexOf(int ch,int fromIndex) 从指定位置开始，获取给定的字符，在该字符串中第一次出现的位置
		int indexOf(String str,int fromIndex) 从指定位置开始，获取给定的字符串，在该字符串中第一次出现的位置
			
	转换功能：
		byte[] getBytes() 把该字符串 转换成 字节数组
		char[] toCharArray() 把该字符串 转换成 字符数组

		//下面的方法 知道有就可以了
		String toLowerCase() 把该字符串转换成 小写字符串 
		String toUpperCase() 把该字符串转换成 大写字符串
		
		String concat(String str) 把该字符串与给定的字符串相连接，返回一个新的字符串
			
	替换功能
		String replace(char old,char new) 在该字符串中，将给定的旧字符，用新字符替换
		String replace(String old,String new) 在该字符串中， 将给定的旧字符串，用新字符串替换
	去除字符串两空格	
		String trim() 去除字符串两端空格，中间的不会去除，返回一个新字符串
	
	
StringBuffer/StringBuilder:(理解)
	构造方法：
		public StringBuffer() 创建一个字符串缓冲区，默认容量大小16
		public StringBuffer(String str) 创建一个字符串缓冲区，指定默认内容为 str 的内容，长度为 字符串str的长度 + 16
	方法：
		public StringBuffer append(String str) 在原有字符串缓冲区内容基础上，在末尾追加新数据
		public StringBuffer insert(int offset,String str) 在原有字符串缓冲区内容基础上，在指定位置插入新数据
		public StringBuffer deleteCharAt(int index) 在原有字符串缓冲区内容基础上，删除指定位置上的字符
		public StringBuffer delete(int start,int end) 在原有字符串缓冲区内容基础上，删除指定范围内的多个字符
		public StringBuffer replace(int start,int end,String str)在原有字符串缓冲区内容基础上，将指定范围内的多个字符 用给定的字符串替换
		
		-----------------------------
		public StringBuffer reverse() 将字符串缓冲区的内容 反转  "abc"----"cba"
		public String substring(int start) 从指定位置开始，到末尾结束，截取该字符串缓冲区，返回新字符串
		public String substring(int start,int end)  从指定位置开始，到指定位置结束，截取该字符串缓冲区，返回新字符串 


		+  

--------------API第二天--------------
正则表达式: 
    (1)就是符合一定规则的字符串
	(2)常见规则
		A:字符
			x 字符 x。举例："a"表示字符a
			\\ 反斜线字符。
			\n 新行（换行）符 
			\r 回车符 
			
		B:字符类
			[abc] a、b 或 c（简单类） 
			[^abc] 任何字符，除了 a、b 或 c（否定） 
			[a-zA-Z] a到 z 或 A到 Z，两头的字母包括在内（范围） 
			[0-9] 0到9的字符都包括
			
		C:预定义字符类
			. 任何字符。我的就是.字符本身，怎么表示呢? \.
			\d 数字：[0-9]
			\w 单词字符：[a-zA-Z_0-9]
				在正则表达式里面组成单词的东西必须有这些东西组成

		D:边界匹配器
			^ 行的开头 
			$ 行的结尾 
			\b 单词边界
				就是不是单词字符的地方。
				举例：hello world?haha;xixi
			
		E:Greedy 数量词 
			X? X，一次或一次也没有
			X* X，零次或多次
			X+ X，一次或多次
			X{n} X，恰好 n 次 
			X{n,} X，至少 n 次 
			X{n,m} X，至少 n 次，但是不超过 m 次 
	(3)常见功能：
		A:判断功能
			String类的
			public boolean matches(String regex) 进行字符串规则的匹配
		B:分割功能
			String类的
			public String[] split(String regex)  字符串按照某种规则进行切割
		C:替换功能
			String类的
			public String replaceAll(String regex,String replacement) 字符串按照某种规则替换小串

    (4)案例
		A:判断电话号码和邮箱
		B:按照不同的规则分割数据
		C:把论坛中的数字替换为*
    
Date/DateFormat
	(1)Date是日期类，可以精确到毫秒。
		A:构造方法
			Date()  获取当前时间
			Date(long time)  获取0点时间+毫秒值的新时间
		B:成员方法
			getTime() 获取日期对象对应的毫秒值 
			setTime(long time) 设置日期对象的时间毫秒值
		C:日期和毫秒值的相互转换
		
	案例：你来到这个世界多少天了?
	
	(2)DateFormat针对日期进行格式化和针对字符串进行解析的类，但是是抽象类，所以使用其子类SimpleDateFormat
		A:SimpleDateFormat(String pattern) 给定模式
			yyyy-MM-dd HH:mm:ss
		B:日期和字符串的转换
			a:Date -- String   日期对象转换成 字符串
				format()
				
			b:String -- Date   字符串转换成日期对象
				parse()
	
Calendar
	(1)日历类，封装了所有的日历字段值，通过统一的方法根据传入不同的日历字段可以获取值。
	(2)如何得到一个日历对象呢?
		Calendar rightNow = Calendar.getInstance();
		本质返回的是子类对象
	(3)成员方法
		A:根据日历字段得到对应的值
		B:根据日历字段和一个正负数确定是添加还是减去对应日历字段的值
		C:设置日历对象的年月日



--------------API第三天--------------	
为了让基本类型的数据进行更多的操作，Java就为每种基本类型提供了对应的包装类类型
		byte 		Byte
		short		Short
		int			Integer
		long		Long
		float		Float
		double		Double
		char		Character
		boolean		Boolean
Integer:
	构造方法：
		public Integer(int value) 把基本数据类型int值 ， 封装成对象
		public Integer(String s)  把字符串类型的数字字符，封装成对象
	方法：
	    //要知道Integer中有进制转换的方法 将来使用 去找就可以了  完全不用记忆
		public static String toBinaryString(int i) 10进制 --> 2进制
		public static String toOctalString(int i) 10进制 --> 8进制
		public static String toHexString(int i) 10进制 --> 16进制
		public static String toString(int i,int radix) 10进制 --> x进制
		public static int parseInt(String s,int radix) x进制 --> 10进制
		
		//--------------------------------------------
		public String toString() 把Integer中的数值 转换成 字符串   Integer --> String
		public static String toString(int i) 把基本数据类型 int值 转成 字符串      int --> String
		public int intValue() 把Integer中的数值 获取出来， 返回一个基本数据类型 int 值  Integer --> int
		          (我们自动拆箱中 调用的方法)
		public static int parseInt(String s) String --> int
		public static Integer valueOf(String s) String -->Integer 
	
	   JDK5的新特性
		 自动装箱	基本类型--引用类型  Integer ii = new Integer(100)
		                                Integer ii = Integer.valueOf(100);
                           自动装箱     Integer ii = 100; 
						   自动装箱中隐含 这这么一句话  Integer.valueOf(100);
		 自动拆箱	引用类型--基本类型  int i = ii.intValue();
		                   自动拆箱     int i = ii; //隐含了上面那句话
		
      再次提到 toString()和equals()都只能由对象调用 
	     如果说某一个类 直接打印对象的时候  
		                    打印的是地址值    调用的是 Object的toString() 没有重写
							打印的是对象内容  重写了toString()方法
		 如果说 对象 调用了equals方法  
		                    发现 比较的还是地址值,说明该类没有重写equals方法
							发现 比较的对象中内容,说明该类重写了equals方法

		把下面的这个代码理解即可：
			Integer i = 100;
			i += 200;
	  面试题
	    注意是在自动装箱中隐含 这这么一句话  Integer.valueOf(数值);
		-128到127之间的数据缓冲池问题
      
	  Character 比较特殊 没有parseXXX功能  
	    String 对象 
		 "a".charAt(0)   将String变成char类型
		
----------------------------------------
	
Math: 数学工具类
	public static double max(double a, double b)返回两个 double 值中较大的一个
	public static double min(double a, double b)返回两个 double 值中较小的一个
	public static double random() 返回 0.0<=x<1.0 之间的随机小数
	public static long round(double a) 四舍五入

	
System:
	方法：
		*public static void exit(int status) 
		    终止当前正在运行的 Java 虚拟机。参数用作状态码；根据惯例，非 0 的状态码表示异常终止
		*public static long currentTimeMillis()
		    返回当前系统时间的毫秒值
      public static void gc() 
            用来运行JVM中的垃圾回收器，完成内存中垃圾的清除。
      public static String getProperty(String key) 
            用来获取指定键(字符串名称)中所记录的系统属性信息
        arraycopy方法，用来实现将源数组部分元素复制到目标数组的指定位置(知道有,能简单使用就行)

数组高级以及Arrays
	(1)排序
		A:冒泡排序
			相邻元素两两比较，大的往后放，第一次完毕，最大值出现在了最大索引处。
			同理，其他的元素就可以排好。
			
			public static void bubbleSort(int[] arr) {
				for(int x=0; x<arr.length-1; x++) {
					for(int y=0; y<arr.length-1-x; y++) {
						if(arr[y] > arr[y+1]) {
							int temp = arr[y];
							arr[y] = arr[y+1];
							arr[y+1] = temp;
						}
					}
				}
			}
			
		B:选择排序
			把0索引的元素，和索引1以后的元素都进行比较，第一次完毕，最小值出现在了0索引。
			同理，其他的元素就可以排好。
			
			public static void selectSort(int[] arr) {
				for(int x=0; x<arr.length-1; x++) {
					for(int y=x+1; y<arr.length; y++) {
						if(arr[y] < arr[x]) {
							int temp = arr[x];
							arr[x] = arr[y];
							arr[y] = temp;
						}
					}
				}
			}
	(2)查找
		A:基本查找
			针对数组无序的情况
			
			public static int getIndex(int[] arr,int value) {
				int index = -1;
				
				for(int x=0; x<arr.length; x++) {
					if(arr[x] == value) {
						index = x;
						break;
					}
				}
				
				return index;
			}
		B:二分查找(折半查找)
			针对数组有序的情况
			
			public static int binarySearch(int[] arr,int value) {
				int min = 0;
				int max = arr.length-1;
				int mid = -1;
				
				while(min<=max) {

					mid = (min+max)/2;

					if(arr[mid] > value) {
						max = mid - 1;
					}else if(arr[mid] < value) {
						min = mid + 1;
					}else{
					    return mid;
					}	
					
				}
				
				return mid;
			}
	(3)Arrays 工具类
		A:是针对数组进行操作的工具类。包括排序和查找等功能。
		B:要掌握的方法
			public static void sort(数组)用来对指定数组中的元素进行排序（元素值从小到大进行排序
			public static int binarySearch(int[] arr,int key)在指定数组中，查找给定元素值出现的位置。若没有查询到，返回位置为负数
			public static String toString(数组)用来返回指定数组元素内容的字符串形式

----------------------集合第一天----------------------------
集合(Collection)(掌握)
	集合的由来?
		我们学习的是Java -- 面向对象 -- 操作很多对象 -- 存储 -- 容器 -- 数组
		而数组的长度固定，所以不适合做变化的需求，Java就提供了集合供我们使用。
	集合和数组的区别?
		A:长度区别
			数组固定
			集合可变
		B:内容区别
			数组可以是基本类型，也可以是引用类型
			集合只能是引用类型
		C:元素内容
			数组只能存储同一种类型
			集合可以存储不同类型(其实集合一般存储的也是同一种类型)
	集合的继承体系结构?
		由于需求不同，Java 就提供了不同的集合类。这多个集合类的数据结构不同，但是它们都是要提供存储和遍历功能的，
		我们把它们的共性不断的向上提取，最终就形成了集合的继承体系结构图。
		
		Collection
			|--List
				|--ArrayList
				|--Vector
				|--LinkedList
			|--Set
				|--HashSet
				|--LinkedHashSet
	Collection 的功能概述
			boolean add(Object e) 把给定的对象添加到当前集合中 
			void clear() 清空集合中所有的元素
			boolean remove(Object o) 把给定的对象在当前集合中删除
			boolean contains(Object o) 判断当前集合中是否包含给定的对象
			boolean isEmpty() 判断当前集合是否为空
			Iterator iterator() 迭代器，用来遍历集合中的元素的
			int size() 返回集合中元素的个数
			Object[] toArray() 把集合中的元素，存储到数组中
	迭代器
		A:是集合的获取元素的方式。
		B:是依赖于集合而存在的。
		C:迭代器的原理和源码。
			a:为什么定义为了一个接口而不是实现类?
			b:看了看迭代器的内部类实现。
	Collection集合的案例(遍历方式 迭代器)
		集合的操作步骤：
			A:创建集合对象
			B:创建元素对象
			C:把元素添加到集合
			D:遍历集合
	
		A:存储字符串并遍历
			import java.util.Collection;
			import java.util.ArrayList;
			import java.util.Iterator;
			
			public class CollectionDemo {
				public static void main(String[] args) {
					//创建集合对象
					Collection c = new ArrayList();
					
					//创建并添加元素
					c.add("hello");
					c.add("world");
					c.add("java");
					
					//遍历集合
					Iterator it = c.iterator();
					while(it.hasNext()) {
						String s =(String) it.next();
						System.out.println(s);
					}
				}
			}
		
		B:存储自定义对象并遍历
			public class Student {
				private String name;
				private int age;
				
				public Student(){}
				
				public Student(String name,int age) {
					this.name = name;
					this.age = age;
				}
				
				//getXxx()/setXxx()
			}
			
			import java.util.Collection;
			import java.util.ArrayList;
			import java.util.Iterator;
			
			public class StudentDemo {
				public static void main(String[] args) {
					//创建集合对象
					Collection c = new ArrayList();
					
					//创建学生对象
					Student s1 = new Student("林青霞",27);
					Student s2 = new Student("风清扬",30);
					Student s3 = new Student("刘意",30);
					Student s4 = new Student("武鑫",25);
					Student s5 = new Student("刘晓曲",16);
					
					//添加元素
					c.add(s1);
					c.add(s2);
					c.add(s3);
					c.add(s4);
					c.add(s5);
					
					//遍历集合
					Iterator it = c.iterator();
					while(it.hasNext()) {
						Student s = (Student)it.next();
						System.out.println(s.getName()+"---"+s.getAge());
					}
				}
			}
     
	泛型概述
		是一种把明确类型的工作推迟到创建对象或者调用方法的时候才去明确的特殊的类型。
	格式：
		<数据类型>
		注意：该数据类型只能是引用类型。
	好处：
		A:把运行时期的问题提前到了编译期间
		B:避免了强制类型转换
		C:优化了程序设计，解决了黄色警告线问题，让程序更安全
	泛型的前世今生
		A:泛型的由来
			Object类型作为任意类型的时候，在向下转型的时候，会隐含一个转型问题
		B:泛型类
		C:泛型方法
		D:泛型接口
		E:泛型高级通配符
			?
			? extends E
			? super E
	我们在哪里使用呢?
		一般是在集合中使用。

--------------------集合第二天---------------------------------------
集合(List)
	(1)List是Collection的子接口
		特点：有序(存储顺序和取出顺序一致)，可重复。
	(2)List的特有功能：
		A:添加功能  add(int index, Object e)：向集合指定索引处，添加指定的元素，原有元素依次后移
		B:删除功能    remove(int index)：将指定索引处的元素，从集合中删除，返回值为被删除的元素
		C:获取功能  get(int index)：获取指定索引处的元素，并返回该元素
		D:迭代器功能   listIterator()
		E:修改功能     set(int index, Object e)：将指定索引处的元素，替换成指定的元素，
		                 返回值为替换前的元素
	(3)List集合的特有遍历功能
		A:由size()和get()结合。
		B:代码演示
			//创建集合对象
			List list = new ArrayList();
			//创建并添加元素
			list.add("hello");
			list.add("world");
			list.add("java");
					
			//遍历集合
			Iterator it = list.iterator();
			while(it.hasNext()) {
				String s =(String) it.next();
				System.out.println(s);
			}
			System.out.println("----------");
			
			for(int x=0; x<list.size(); x++) {
				String s =(String) list.get(x);
				System.out.println(s);
			}
	(4)列表迭代器的特有功能；(了解)
		可以逆向遍历，但是要先正向遍历，所以无意义，基本不使用。
	(5)并发修改异常
		A:出现的现象
			迭代器遍历集合，集合修改集合元素
		B:原因
			迭代器是依赖于集合的，而集合的改变迭代器并不知道。
		C:解决方案
			a:迭代器遍历，迭代器修改(ListIterator)
				元素添加在刚才迭代的位置
			b:集合遍历，集合修改(size()和get())
				元素添加在集合的末尾
	(6)常见数据结构
		A:栈 先进后出
		B:队列 先进先出
		C:数组 查询快，增删慢
		D:链表 查询慢，增删快
	(7)List的子类特点(面试题)
		ArrayList
			底层数据结构是数组，查询快，增删慢。
			线程不安全，效率高。
		Vector
			底层数据结构是数组，查询快，增删慢。
			线程安全，效率低。
		LinkedList(特有功能 跟 头尾相关)
			底层数据结构是链表，查询慢，增删快。
			线程不安全，效率高。
			
		到底使用谁呢?看需求?
		分析：
			要安全吗?
				要：Vector(即使要，也不使用这个，后面再说)
				不要：ArrayList或者LinkedList
					查询多；ArrayList
					增删多：LinkedList
					
		什么都不知道，就用ArrayList。
 Set集合(理解)
	(1)Set集合的特点
		无序,唯一
	(2)HashSet集合(掌握)
		A:底层数据结构是哈希表(是一个元素为链表的数组)
		B:哈希表底层依赖两个方法：hashCode()和equals()
		  执行顺序：
			首先比较哈希值是否相同
				相同：继续执行equals()方法
					返回true：元素重复了，不添加
					返回false：直接把元素添加到集合
				不同：就直接把元素添加到集合
		C:如何保证元素唯一性的呢?
			由hashCode()和equals()保证的
		D:开发的时候，代码非常的简单，自动生成即可。
		E:HashSet存储字符串并遍历
		F:HashSet存储自定义对象并遍历(对象的成员变量值相同即为同一个元素)    

针对Collection集合我们到底使用谁呢?(掌握)
	  唯一吗?
		 是：Set
			HashSet
		如果你知道是Set，但是不知道是哪个Set，就用HashSet。	
		否：List
			ArrayList或者LinkedList
					查询多：ArrayList
					增删多：LinkedList
		如果你知道是List，但是不知道是哪个List，就用ArrayList。
	
	如果你知道是Collection集合，但是不知道使用谁，就用ArrayList。
	
	如果你知道用集合，就用ArrayList。
	
   在集合中常见的数据结构(掌握)
	ArrayXxx:底层数据结构是数组，查询快，增删慢
	LinkedXxx:底层数据结构是链表，查询慢，增删快
	HashXxx:底层数据结构是哈希表。依赖两个方法：hashCode()和equals()

--------------------集合第三天---------------------------------------
1:Map
	(1)将键映射到值的对象。一个映射不能包含重复的键；每个键最多只能映射到一个值。 
	(2)Map和Collection的区别?
		A:Map 存储的是键值对形式的元素，键唯一，值可以重复。夫妻对
		B:Collection 存储的是单独出现的元素，子接口Set元素唯一，子接口List元素可重复。光棍
	(3)Map接口功能概述
		A:添加功能  V put(K key, V value)  把指定的键与指定的值添加到Map集合中
		B:删除功能  V remove(Object key) 把指定的键 所对应的键值对元素 在Map集合中删除，返回被删除元素的值
		C:判断功能   boolean containsKey(Object key) 判断集合中是否包含此键
                     boolean containsValue(Object value) 判断集合中是否包含此值 
		D:获取功能  V get(Object key) 根据指定的键，在Map集合中获取对应的值
		E:长度功能  size()
	(4)Map集合的遍历
		A:键找值
			a:获取所有键的集合
			b:遍历键的集合,得到每一个键
			c:根据键到集合中去找值
		
		B:键值对对象找键和值
			a:获取所有的键值对对象的集合
			b:遍历键值对对象的集合，获取每一个键值对对象
			c:根据键值对对象去获取键和值
			
		代码体现：
			Map<String,String> hm = new HashMap<String,String>();
			
			hm.put("it002","hello");
			hm.put("it003","world");
			hm.put("it001","java");
			
			//方式1 键找值
			Set<String> set = hm.keySet();
			for(String key : set) {
				String value = hm.get(key);
				System.out.println(key+"---"+value);
			}
			
			//方式2 键值对对象找键和值
			Set<Map.Entry<String,String>> set2 = hm.entrySet();
			for(Map.Entry<String,String> me : set2) {
				String key = me.getKey();
				String value = me.getValue();
				System.out.println(key+"---"+value);
			}
	(5)HashMap集合的练习
		A:HashMap<String,String>
		B:HashMap<Integer,String>
		C:HashMap<String,Student>
		D:HashMap<Student,String>
	(5)练习
		A:统计一个字符串中每个字符出现的次数
		"aebbeda"
          可能要用到的方法  
		         V put(K key, V value)
                 boolean containsKey(Object key) 判断集合中是否包含此键
				 V get(Object key) 根据指定的键，在Map集合中获取对应的值
2:Collections
	(1)是针对集合进行操作的工具类
	(2)面试题：Collection和Collections的区别
		A:Collection 是单列集合的顶层接口，有两个子接口List和Set
		B:Collections 是针对集合进行操作的工具类，可以对集合进行排序和查找等
	(3)常见的几个小方法：
		A:public static <T> void sort(List<T> list)
		B:public static void shuffle(List<?> list)
	(4)案例
		模拟斗地主洗牌和发牌并对牌进行排序

------------------------------------------------------

异常:  相当于程序的诊断机制 
   Throwable 
       |--Error  错误  系统级别
	             癌症 艾滋病 非典
	   |--Exception 异常   
	              感冒 发烧 
	      |--RuntimeException 运行期异常  只在运行期 
		              如果发生了异常 ,运行的时候,那么根据下面的提示 去查找原因,修改代码
		  |--非 RuntimeException 编译期异常  在编译时期告诉了我们 这里可能产生异常,
		                      需要在编译的时候进行处理异常

  
    处理异常 
	   1:JVM处理  在程序执行的时候  发现了问题 (所谓的异常)  创建一个异常对象 将这个异常对象
	         进行 throw 抛出去  交给 JVM 处理 将异常对象的信息打印到控制台上,程序停止
	   2:我们一般直接处理的是编译期的异常 
	          throws 交给调用者处理
			  try..catch..捕获异常 
			       抓到了异常,try{可能出现异常的代码} catch(某个异常类 e){处理代码}
				              开发时 处理异常 是将一个编译期异常变成运行期异常 
			 finally 不管有没有异常 都会执行


   异常的细节......

   自定义异常  
      extends Exception    编译期异常 
	         RuntimeException   运行期异常
	  两个 
	     空参 
		 带参 
     
	  在 需要进行异常校验的时候  
	     比如我们的列子 在年龄赋值的时候  我们进行了 异常的处理








能够说出Map集合特点
    1:双列集合 每次存储一对元素
    2:键值对元素,一一映射 
	3:键唯一
使用Map集合添加方法保存数据
    V put(K,V);
使用”键找值”的方式遍历Map集合
     keySet找到所有键集合
     遍历键集合 找值 
使用”键值对”的方式遍历Map集合
     entrySet 
	 好多entry对象 
	 遍历得到每个entry对象
	 通过entry拿到 键 拿到值
能够使用HashMap存储自定义键值对的数据
     HashMap<Student,String> map = new HashMap<Student,String>();
能够说出可变参数的作用
     可变参数 等同于数组
	 灵活  可以接收0~n个该类型的数据  
	 原理  传递的时候将这些数据封装成数组
能够使用集合工具类
     Collections.shuffle(List集合)
	 Collections.sort(List集合)
能够使用HashMap编写斗地主洗牌发牌案例
     

--------------------------------File,递归------------------------------------
File 对应的  是 计算机中的 文件或文件夹  
 需要知道 文件或文件夹的 抽象路径
    1:构造方法  3个 
      File(String pathname) √
            根据  路径 的字符串  封装一个File对象
      File(String parent, String child) 
            根据 父路径和子路径 字符串  封装一个File对象
      File(File parent, String child) 
	        根据 父目录file对象和子路径字符串  封装成一个File对象
	  
	  绝对路径:  带盘符
	  相对路径:  不带盘符   跟项目相对的路径
	2:创建功能 
	  public boolean createNewFile() 创建新的文件
	  public boolean mkdir() 创建新的文件夹
	  public boolean mkdirs() 创建多级文件夹
	3:删除功能
	  public boolean delete() 删除比较暴力 谨慎使用 基本不用
	4:判断功能
	  public boolean isFile() 判断这个对象是否是文件   √
	  public boolean isDirectory() 判断这个对象是否是目录   √
	  public boolean exists() 判断这个对象对应的路径 是否真实存在
	5:获取功能
	  public String getName() 获取文件或文件夹名称   √
      public long length()  获取文件或文件夹大小 
	  public String getAbsolutePath() 获取绝对路径   √
	  public String getPath() 获取封装对象的 那个抽象路径
	  public long lastModified() 获取最后一次修改时间
	6:高级获取功能 
	  √ public String[] list() 获取目录下的所有文件或文件夹的名字以字符串数组形式存储
	  √ public File[] listFiles() 获取目录下 所有的文件或文件夹,它们以对象形式存储到数组中
	    public File[] listFiles(FileFilter filter) 获取目录下的所有的文件或文件夹,只将符合条件的存储到File数组中

	  递归 
	   发现 你的程序里  方法自己调用自己  
	                     递归   直接递归 间接递归
		
	   那个递归使用图解看一下  阶乘的 
	    注意事项: 
		    递归一定有出口  否则程序停不下来
			递归次数不要太多 太多就内存溢出
			构造方法  禁止使用递归

			----------------------
			IO流第二天--------------------------
文件: 
   文本文件  字符   字符流 
   其他字节码文件   字节流 
O:out  输出  write  
字节流
  OutputStream : 字节输出流抽象父类
       |--FileOutputStream  可以操作文件的  将程序中的数据写到文件File中
	     构造: FileOutputStream(File file)
		       FileOutputStream(String pathname) 如果文件不存在可以帮忙创建出来
	     写数据:
		       write重载 
			        写单个字节  一个字节一个字节
					写字节数组  
					写字节数组一部分
		 关闭流:
		      close()
I:in 输入  读  read
  InputStream : 字节输入流抽象父类 
       |--FileInputStream 可以操作文件的 字节输入流  将文件中的数据读取到内存中
	     构造: FileInputStream(File file)
		       FileInputStream(String pathname)
	     读数据
               int read() 读取一个字节  返回值是 那个字节 只不过用int接收 
			              如果读到了末尾,返回-1
			   int read(byte[] bys) 按照一个字节数组的量去读取,返回值是读取到字节的个数,
			                        如果读到末尾,返回-1
									byte[]  0, length
		       还有一个方法可以读取字节数组的一部分
		  关闭流:
		     close();
字符流:
    常见编码表(现在作为了解)
     常用的编码表   GBK  UTF-8   拉丁码表(不能识别中文) 
	                GBK:英文占一个字节  中文占两个  \r \n
					UTF-8:能用一个字节的就用一个  中文占3个
					咱们现在默认的是GBK
	编码: 将看得懂的变成看不懂的             地下党--发密文
	      字符---数字       输出流
	解码: 将看不懂的变成看的懂得             我党--解读密文
	      数字--字符        输入流

Reader 字符输入流的抽象父类
       |--FileReader 操作文件的字符输入流
         构造 FileReader(File file)
		      FileReader(String pathname)
		 读取:
		     int read() 一个字符一个字符读取 返回值是读到的字符 还是用int来接收
			            如果读不到  返回-1
			 int read(char[] chs) 使用字符数组读取 将读取到的字符放在数组中,返回值就是读到的字符个数
			 可以读取字符数组的一部分
		 关闭流
		    close()
Writer 字符输出流的抽象父类 
       |--FileWriter 操作文件的字符输出流 
	      构造 FileWriter(File file)
		       FileWriter(String pathname)
		  写
		    write重载  写单个字符
			           写字符数组
					   写字符串
					   写字符数组一部分
					   写字符串一部分
		  关闭流:
		    close()  先刷新再关闭流
			flush()  将数据刷新到文件中

读文件 写文件 
    1:创建对应的流
	2:做对应的操作
	3:关闭流
复制文件:
    1:创建输入流 跟 数据源文件关联
	2:创建输出流 跟 目标文件冠梁
	3:从数据源中读数据
	4:往目标文件写数据
	5:关闭流
        
------------------------IO流第三天--------------------------
字符流 =  字节流 + 编码表
转换流和子类区别
发现有如下继承关系：
          依赖字节流  指定编码
OutputStreamWriter:  字符流通向字节流的桥梁     看的懂变成看不懂的  编码
		|--FileWriter:
InputStreamReader:   字节流通向字符流的桥梁     看不懂的变成看的懂得 解码
	    |--FileReader;

父类和子类的功能有什么区别呢？
OutputStreamWriter和InputStreamReader是字符和字节的桥梁：也可以称之为字符转换流。
字符转换流原理：字节流+编码表。
FileWriter和FileReader：作为子类，仅作为操作字符文件的便捷类存在。
当操作的字符文件，使用的是默认编码表时可以不用父类，而直接用子类就完成操作了，简化了代码。
InputStreamReader isr = new InputStreamReader(new FileInputStream("a.txt"));//默认字符集。
InputStreamReader isr = new InputStreamReader(new FileInputStream("a.txt"),"GBK");//指定GBK字符集。
FileReader fr = new FileReader("a.txt");
这三句代码的功能是一样的，其中第三句最为便捷。

注意：一旦要指定其他编码时，绝对不能用子类，必须使用字符转换流。

什么时候用子类呢？
条件：
1、操作的是文件。2、使用默认编码。
总结：
字节--->字符 ： 看不懂的--->看的懂的。  需要读。输入流。 InputStreamReader        解码
字符--->字节 ： 看的懂的--->看不懂的。  需要写。输出流。 OutputStreamWriter       编码


缓冲流  :
在我们学习字节流与字符流的时候，大家都进行过读取文件中数据的操作，
读取数据量大的文件时，读取的速度会很慢，很影响我们程序的效率，那么，我想提高速度，怎么办？
Java中提高了一套缓冲流，它的存在，可提高IO流的读写速度
缓冲流，根据流的分类分类字节缓冲流与字符缓冲流。
缓冲流 就是 对基本的字符流或者字节流 进行了一层包装 
也就是 在构造中 BufferedXxxx(传递对应的字节流或者字符流)
                    注意 我们学习的字节流 子类 有  FileInputStream FileOutputStream 
					     我们学习的字符流 子类 有  FileReader  FileWriter
						                           InputStreamReader OutputStreamWriter

缓冲流就是加了一层包装 在调用方法上都是一样的 
只不过 在 BufferedReader 中有读取一行的方法  readLine()
       在 BufferedWriter 中有写一个换行操作的方法  newLine()


-------------流的操作规律-------------------------
IO流中对象很多，解决问题(处理设备上的数据时)到底该用哪个对象呢？　　
把IO流进行了规律的总结(四个明确)：
		明确一：要操作的数据是数据源还是数据目的。
				源： InputStream    Reader
				目的： OutputStream Writer
		先根据需求明确要读，还是要写。

		明确二：要操作的设备上的数据是字节还是文本呢？
				源：
					字节： InputStream
					文本： Reader
				目的：
					字节： OutputStream
					文本： Writer
		已经明确到了具体的体系上。

		明确三：明确数据所在的具体设备。
				源设备：
					硬盘：文件  File开头。
					内存：数组，字符串。
					键盘：System.in;
					网络：Socket
				目的设备：
					硬盘：文件  File开头。
					内存：数组，字符串。
					屏幕：System.out
					网络：Socket
		完全可以明确具体要使用哪个流对象。

		明确四：是否需要额外功能呢？
				额外功能：
					转换吗？转换流。InputStreamReader OutputStreamWriter
					高效吗？缓冲区对象。BufferedXXX

---------------------IO第四天------------------------------------------------
Properties 集合  重点
   1:Map 双列集合  Map 共性 也有  Hashtable的子类
   2:没有泛型约束  这里面的键值都是String类型
   3:一个持久化的属性集  
   4:唯一可以直接跟io交互使用的集合

   load(InputStream in)
   store(OutputStream out , String  zhushi)

   写到文件中 
   从文件读取   用的最多
   配置文件 ?  
       url=数据库地址
	   username=数据库名称
	   password=数据库密码
   在jdbc写工具类的时候 完成这样的操作

   理解
   序列化 
   反序列化 
      重点在于 知道 这俩是什么玩意儿
	  序列化 对象输出流  将对象以对象的形式写到文件中
	  反序列化 对象输入流 将文件中的数据以 对象形式 读取过来

	  如果该类对象要序列化,那么该类必须实现序列化接口

	  注意事项:
	      读取文件的那个版本号 要跟 字节码文件的版本号 一致 才能进行反序列化
		  静态的变量不会被序列化的
		  如果 成员变量不想被序列化  用瞬态关键字 修饰 transient 
  
  打印流: 了解 
     PrintStream  字节打印流 
	 PrintWriter  字符打印流
	 System.out   是字节打印流  

	 可以当做普通流使用
	 也可以 在控制台打印 

	 write  print println 区别在于 后面两个方法 可以写入任意类型的

	 --------------------------多线程---------------------------------
两个概念  
   线程 : 就是进程中的一个执行单元 也可以叫执行路径
   进程 : 正在运行的程序.当一个程序进入到内存中的时候 就有了一个进程
          进程中至少有一个线程,可以有多个线程,我们把有个线程的程序叫多线程程序

   CPU: 在某一时刻上只能执行一个路径,但是在我们看来 CPU可以执行多个线程
        因为 CPU在进行高速的切换.
    
   两种调度方式
       分时调用
	   抢占式调用  java

   主线程  JVM开启的     每个线程 都有自己的栈空间  相当于执行路径

   Thread 类  
      构造 
	     Thread()  Thread(String name)  Thread(Runnable t)  Thread(Runnable t, String name)
      静态方法 
	     static Thread currentThread() 返回正在执行的线程对象
		 static void sleep(long millis) 线程睡眠  放弃CPU执行资格和权利  醒了再去抢
	  方法:
	    getName() 获取线程名称
		void setName(String name) 设置线程名称

   开启线程的两种方式
     1:继承Thread类
          A:自定类继承Thread类
		  B:重写run方法   
		  C:创建自定义类对象
		  D:调用start()方法

	 2:实现Runnable接口
	      A:自定类实现Runnable接口
		  B:重写run方法
		  C:创建自定义类对象
		  D:创建线程对象,并将自定义类对象当做参数传递
		  E:调用start方法

    第二种更符合开发
	   1: 解决了单继承的局限性
	   2: 将线程对象和线程任务分离 解耦 降低耦合度


	   run  start  区别  
	   run 可以当做普通方法调用  跟以前的使用一样
	   start方法 是开启线程   JVM去调用了run方法执行  这个时候的run方法 线程做的任务代码

	稍微 练一下 匿名内部类的方式开启线程


	状态图  看一下  
	   新建  就绪   运行   阻塞   死亡

	

   多线程安全问题:前提
      1:多线程程序
	  2:共享一个资源
	  3:对这个资源有写的操作

   解决方式:
      1:加上同步锁  同步代码块
	    synchronized(锁对象){
		   可能出现安全问题的代码;
		 }
		锁对象: 可以是任意类型的对象
		多个线程间必须使用同一把锁
	  2:同步方法
	    普通的同步方法  this 
		权限修饰符 synchronized  返回值类型 方法名(..){
			 可能出现安全问题的代码;
		} 
		静态的同步方法  锁对象 类名.class 
		 static synchronized
	  3:Lock 锁
	      lock()
	      unlock()


------有时间补充上------
死锁

线程池的原理 :
	     因为创建和销毁线程对象消耗资源太多.为了解决这个问题
		 有了线程池 
		     想将线程池创建出来,里面配置了线程对象  也就是以后一直用这里的线程对象
			 不用再创建 和销毁   因为使用完毕 放回线程池中 
			 需要就去取.
等待唤醒机制

 ------------网络编程-------------------

ip   
端口号
UDP  
  特点: 传输快,容易丢数据
      聊天室
TCP  
  特点:  传输稳定,数据安全  
  上传,下载

UDP  TCP操作过程 知道了 

UDP 发送端和接收端  

TCP 客户端 和 服务器端


