---
title: Java的知识点记录
---






### 一、小白时的记录

System.currentTimeMillis()记录时间

idea的快捷键：输入psvm快速生成main方法
输入sout生成一个输出语句
ctrl+alt+space键是内容提示即快速补全代码
java是面向对象的语言，三大特征：①封装②继承③多态
在使用私人成员变量时，建立构造方法和get或者set方法时用Alt+Fn+insert快捷键,列表第一个时构造方法
@Override用于方法重写时给一个提示这是重写的方法



### 二、基础知识

1.定义long类型的数据时要在赋值的后面加上L，例：long a=10000000000L；
  定义float类型时也是，后面要加上F。因为默认的浮点型是double，默认的整数是int；

2.标识符的注意事项：不能以数字开头，不能是关键字，区分大小写（关键字全是小写）

3.数据类型分为基本数据类型和引用数据类型，引用里面有类（class）。接口（interface），数组（[]）。
  基本数据类型又分为数值型和非数值型，数值型有整数（byte,short,int,long)，浮点数(float,double),字符（char）非数值有布尔值（boolean）

4.强制转换数据类型，用法：目标数据类型 变量名=（目标数据类型）值或变量，如int k=（int）88.88。这样出来的值是88

5.在进行算术的时候，低级的数据类型会被转换为高级的，如char，short，byte都会转换为int（int k=10+10.11这个运行是错误的）
  +号在字符串是连接符，如果出现字符串与数字相加，如果数字在后面会被直接当成字符串连接起来，如果数字在前面会先相加在连接

6.s+=20等同于s=s+20，区别在于前者包含了强制转换数据类型，如s=s+20，如果直接输出short类型会报错（因为int转为了short）但是用前者不会

7.java中有4个逻辑运算符，&（与）|（逻辑或）^(逻辑异或)即当两者相同时为true，！（逻辑非）
 &和&&的区别，&&如果是两个条件时，前者如果为假则后面直接跳过不执行，直接输出结果（所以&&和||也叫短路逻辑运算符）

8.三元运算符：格式关系表达式？表达式1：表达式2；如果表达式是true则输出表达式1的值

9.数据输入，Scanner的使用分三步，①导包：import java.util.Scanner；（导包的动作必须出现在类定义的上面）
  ②创建对象：Scanner sc =new Scanner（Syestem.in）；（只有sc是变量名，可以变，其他都不可以）
  ③接受数据：int i=sc.nextInt（）；（上面只有i是变量名可以变，其他都不能变）
  后面两个步骤可以在类里面，和c语言的scanf相似

10.continue：基于条件控制，跳过某次循环体内容的执行，继续下一次的执行
  break：基于条件控制，终止循环体内容的执行，也就是结束当前的整个循环

11.Random的作用：产生一个随机数，使用分三步，①导包import java.Random;（必须出现在类定义的上面）
  ②创建对线：Random r=new Random（）；（只允许变变量名）
  ③获取随机数：int number=nextInt（10）；（获取数据的范围[0，10），这里面只允许变变量名和范围）

12.数组
	java中的数组必须要先初始化，初始化意思就是：为数组中的数组元素分配内存空间，并为每个数组元素赋值
    动态初始化：初始化时只指定数组长度，由系统为数组分配初始值。格式：数据类型[] 便量名=new 数据类型[数组长度]
    静态初始化：初始化时指定每个数组元素的初始值，由系统决定数组长度。 格式：数据类型[] 变量名=new 数据类型[]{数据1，数据2..}
	静态的可以简写为数据类型[] 变量名={数据1，数据2....}
13.一般栈内存：存储局部变量（使用完毕会立即消失）
	堆内存：存储对象（使用完，空余时会被系统清除）     例如int[] arr = new int[3]     左边存在栈内存中，右边在堆内存中

14.获取数组的长度：
	格式：数组名.length
	数组遍历的通用格式：int[] arr={.....}； 
	for(int x=0;x<arr.length;x++){
	arr[x]
	}

15.方法定义：public static void 方法名(){}        （public static是修饰符）
   方法的返回值，这个返回值要与定义方法的数据类型一致，并且一般情况下都会有一个新的变量来接受返回的值

16.方法的注意事项：①方法不能嵌套
   ②viod表示无返回值，可以省略return，也可以单独写一个return
   ③如果不是void类型的，一般创建一个变量来接受返回值

17.方法重载：在同一个类中，方法名相同，参数不同，与返回值类型无关（java会通过参数的不同来区分方法）

18.方法参数的传递：基本类型的传递，形参的改变是不会影响实际参数的值
   引用类型的参数（数组，接口，对象），形参的改变会影响实际参数的值

19.println输出的会换行，print的不会换行

20.switch选择语句：
	格式switch（表达式）{case 常量表达式
	语句
	break;
	default:
	语句
	}

21.如果把一个对象赋值给另一个对象，即地址赋给了另一个对象，这样就可以使用其中一个对象来修改其中的属性值

22.成员变量：即类中方法外的变量
	局部变量：方法中的变量
	两个的区别，①成员在堆内存中，局部变量在栈内存中。
	②成员变量随对象的存在而存在，局部变量随方法的调用而存在，随方法的调用完毕而消失
	③成员因为在堆内存，有默认的初始化值，而局部变量则没有

23.private（主要用于封装）：他是一个权限修饰符，可以修饰成员变量和方法，作用是保护成员不被别的类使用，被他修饰的成员只能在自己的类中被访问
	针对private修饰的成员变量，如果需要被其他类使用，提供相应的操作：
	①提供"get变量名()"方法，用于获取成员变量的值，方法用public修饰
	②提供"set变量名(参数)"方法，用于设置成员变量的值，方法用public修饰（这两个方法定义在自己的类中，让别的类去调用）

24.用this修饰的变量是成员变量（条件：当出现成员变量和局部变量名字一样时）         //this用于解决局部变量隐藏成员变量
    this：代表所在类的对象引用。即方法被哪个对象调用，this就代表哪个对象

25.构造方法是一种特殊的方法，作用：创建对象    功能：主要是完成对象数据的初始化（在创建对象时可以直接调用构造方法）
	格式：修饰符  类名（参数或不写参数）{}
	注意事项：①如果没有给出构造方法，系统会给出一个默认的无参无内容的构造方法，如果定义了一个构造方法，系统将不会在给出一个默认的
	②尽量都要手写一个无参的构造方法



### 三、各种类型的使用

26.String类
	String的创建，①先创建一个char a[]字符数组，然后用String s=new String(a)去接收
	②直接进行赋值String s="abc"		//建议使用这个，这样创建的内容会在常量池中
	String对象的特点：①通过new创建的字符串对象，每一次new都会申请一个空间，虽然内容相同，但是地址不同
	②如果用直接赋值的方法，赋值了一个一样内容的字符串，则他俩时相等的（系统不会在给另一个创造空间）
	关于String：因为字符串是对象，所以他如果要比较内容的话需要一个方法来实现，方法叫equals()
	public boolean equals(Object anObject)：将此字符串与指定对象进行比较，例如：s1.equals(s2)
	遍历字符串：通用格式：for(int i=0;i<s.length(); i++){
				s.charAt(i);     //就是指定索引处的字符值，字符串的索引也是从0开始和数组一样}
	方法：substring(a,b)：截取字符串，从a到b，不包括b

27.关于==的知识：如果是基本类型的比较的是数据值，如果是引用类型的比较的是地址值
	public String[]  split(String regex)这个方法可以分隔字符串里面的内容变成一个字符串数组，以regex为分隔符(从字符串里找)

28.String是不可变的
	StringBuilder是可变的，可以使用StringBuilder的方法来添加或者反转字符串，如StringBuilder sb=new StringBuilder（）；
	sb.append（“hello”）即为sb字符串添加hello。也可以这样用sb.append("he").append("llo")
	append方法的结果是返回对象本身              也可以用reverse()方法进行字符串反转
	关于类型转换：
	String转换为StringBuilder：public StringBuilder(String s)：通过构造方法就可以实现把String转换为他	例如StringBuilder sb = new StringBuilder(s)
	StringBuilder转换为String：public String toString()：通过toString()就可以实现啦
	String对象一旦被创建就是固定不变的了，对String对象的任何改变都不影响到原对象，相关的任何change操作都会生成新的对象。
	String类是final类，也即意味着String类不能被继承，并且它的成员方法都默认为final方法。在Java中，被final修饰的类是不允许被继承的，并且该类中的成员方法都默认为final方法。

30.继承：
	格式：public class 子类名 extends 父类名()
	一般什么时候用继承：如当A类是B类的一种，或者B类是A类的一种，这时就可以用继承
	当调用子类的构造方法时会先访问父类的无参构造方法（如果没有父类的无参构造方法会报错）

31.super
	用法：用于访问父类的成员变量或方法，而this用于访问本类的成员变量或方法
	访问变量格式super.成员变量  	访问成员方法super.成员方法()

32.方法重写的注意事项：①私有方法是不能被重写（也不会被继承）
					②子类方法访问权限不能更低（public>默认>private）

33.包：
	其实就是文件夹；作用：对类进行分类管理		//一般创建包的格式com.itheima
	导包：
	用不同包下的java程序里面的成员变量或方法时需要加上包的名字，例如：com.itheima.Teacher(类名) t = new com.itheima.Teacher();
	也可以和类似导包scanner一样进行导包，例如：import com.itheima.Teacher(类名);     //这样在使用的话前面就不用加包的名字啦

34.权限修饰符:
	①权限修饰符：private<默认<protected<public
	特点：(1)在自己java程序里面4个权限都可以访问
	(2)在同一个包下面其他java程序只能访问其他三个，private不能访问
	(3)在其他包下面并且和那个java程序有关系只能访问后面两个权限的
	(4)如果在其他包下且没有关系只能访问最后一个
	②状态修饰符
	(1)final（最终态）
	被final修饰的方法是不可以被重写的
	被final修饰的成员变量就会变成常量不能再次被改变
	被final修饰的类不能被继承啦
	只要被final修饰的东西就不能再被改变，如修饰引用类型则他的地址不能在被改变
	(2)static（静态）
	他被类的所有对象共享
	可以通过类名调用，也可以通过对象调用  	例如：Student.name="he"；
	被静态修饰的成员方法只能访问静态的，非静态的都可以

35.多态中成员访问的特点：
	成员变量：编译看左边（即先看左边的类中有没有这个变量），执行看左边
	成员方法：编译看左边，执行看右边（即如果右边的类中重写了方法则会执行他的方法）
	多态的好处和弊端：
	提高了程序的扩展性，具体体现：定义方法的时候，使用父类型作为参数，在使用时，会使用具体的子类型参与操作
	多态的弊端：不能使用子类特有的功能
	可以使用向下转型来解决这个弊端，形式：子类类名  子类对象名  =  （子类类名）父类对象名
	向上转型即 父类类名  父类对象名 = new  子类类名()

36.抽象类(abstract)
	抽象方法没有方法体，如果一个类中有抽象方法，那么他必须是抽象类，在public后面加个abstract
	抽象类是不能创建对象的，他需要子类使用上转型来创建对象，并且子类必须重写父类的方法，如果是抽象子类则不用重写
	抽象类中的成员特点：
	①成员变量：可以是变量，也可以是常量
	②构造方法：有构造方法，但是不能实例化
	③成员方法：可以有抽象方法：限定子类必须完成某些动作(即重写方法)，也可以有非抽象方法：提高代码复用性

37.接口(interface)
	类和接口之间用implements实现继承，接口的实体化和抽象一样也需要借用子类，不可以直接创建对象，并且也要重写方法或者定义为抽象类
	接口中的成员变量默认是最终态（即默认是常量，可以通过接口类之间访问），接口中是没有构造方法，并且成员方法也是没有方法体
	一个类如果没有父类，默认继承自Object类

38.类和接口的关系
	类和类的关系：继承关系，只能单继承，但可以多层继承
	类和接口的关系：实现关系，可以单实现，也可以多实现，还可以在继承一个类的同时实现多个接口
	接口和接口的关系
	继承关系，可以单继承，也可以多继承
	抽象类主要是对类的抽象，包括行为和属性，接口主要是对行为的抽象

39.形参和返回值
	（1）类名作为形参和返回值
	方法的形参是类名，其实需要的是该类的对象
	方法的返回值是类名，其实返回的是该类的对象
	（2）抽象类名作为形参和返回值
	方法的形参是抽象名，其实需要的是该抽象类的子类对象
	方法的返回值是抽象类名，其实返回的是该抽象类的子类对象
	（3）接口类作为形参和返回值
	方法的形参是接口名，其实需要的是该接口的实现类对象
	方法的返回值是接口名，其实返回的是该接口类的实现类对象

40.内部类(即在一个类里面声明另一个类)
	内部类的访问特点：内部类可以直接访问外部类的成员，包括私有
	外部类要访问内部类的成员，必须创建对象
	按照内部类在类中定义位置不同，可以分为：成员内部类和局部内部类
	（1）成员内部类（即在类的成员位置）
	如何在外界创建对象去使用，格式：外部类名.内部类名  对象名=外部类对象.内部类对象	    如：Animal.Cat cat = new Animal().new Cat()
	（2）局部内部类（即在方法体中声明）
	外界如何去使用，可以现在内部类中声明一个对象，然后再声明一个外部类的对象直接调用方法即可
	（3）匿名内部类（是局部内部类的一种特殊形式）
	格式： new  类名或者接口名(){重写方法};		例如：new  inter(){pulic void show(){}}	//他整体是一个对象
	本质：是一个继承了该类或者实现了该接口的子类匿名对象
	可以通过多态的形式去使用，即他可以当作是接口或者类的一个实现类对象   	如：Inter i = new Inter(){重写方法};
	在开发中的使用可以用它从而少写一个类

41.System类
	它里面有几个有用的类字段和方法，他不能被实例化，不能被实例化即代表不能创建对象
	常用的两个方法：①System.exit(0)即到这里执行时会使java停止下面的运行
	②System.currentTimeMillis()表示时间戳即从1970年到现在的毫秒差值，可以两个之间之差用来进行计算一个程序运行所耗时间

42.Object类
	每一个类都可以将他作为超类，所有类都直接或间接的继承自该类
	①里面包含有toString()方法，建议每一个子类都重写这个方法，这个方法默认返回哈希值，如果直接输出对象则相当于调toString方法
	②equals()方法，如果直接用equals方法则比较的是两个对象的地址，需要重写以后，就可以比较内容

43.Arrays类
	Array.sort(int 数组)这个方法对数组进行排序
	他是一个工具类，他里面包含有用于操作数组的各种方法
	工具类的设计思想：构造方法用private修饰，成员用public static修饰
	①toString()方法用于返回指定数组的内容的字符串表示形式
	②sort()方法按照数字顺序排列指定的数组

44.基本数据类型的包装
	一共有8个数据类型，其中int的包装类为Integer，char的包装类为character，其他都是首字母大写
	他将基本数据类型封装成对象，方便定义方法进行操作
	①Integar类
	现在一般用Integer 对象名字=Integer.valueOf(int  i或者String s但必须是数字)
	②将int转换为String类
	第一种隐式转换，直接做字符串和int类型的相加
	第二种用String.valueOf(int i)方法转换
	③String转换为int
	第一种先用Integer类创建一个对象将字符串对象填进去，然后用public int intValue()方法直接转换
	第二种用public static int parseInt(String s)方法就行，static说明可以直接用类调用，如Integer.parseint(String s),直接用一个int类型接收就行

45.自动装箱和拆箱
	装箱：把基本数据类型转换为对应的包装类类型
	拆箱：把包装类类型转换为对应的基本数据类型
	装箱的例子：如基本用方法装的，Integer i = Integer.valueOf(填整形变量)这是基本实现，自动的化直接用Integer 名字 = 整型变量
	拆箱的例子：ii.intValue()这个方法是拆箱的方法，可以直接用 ii=ii+200就完成自动的拆箱了(前提是ii这个包装类型不能是null值，如果是引用类型最好先做判断)

46.Date类(util包)
	直接创建一个Date的对象直接输出，会输出当前的时间(因为重写toString方法啦)
	常用方法：①public long getTime()获取的是从1970年1月1号到现在的毫秒值
	②public long setTime(long time)方法用来设置时间给的是毫秒值

47.SimpleDateFormat类
	重点学习这个类的日期格式化和解析，这里面日期和时间格式由模式字符串指定其中：
	y年 M月 d日 H时 m分 s秒
	构造方法创建对象：①public  SimpleDateFormat()：构造一个SimpleDateFormat，使用默认模式和日期格式
	②public  SimpleDateFormat(String pattern)：构造一个SimpleDateFormat，使用指定的模式和日期
	格式化和解析日期：
	格式化：从Date到String	public final String format(Date date)：将日期格式和成日期/时间字符串
	解析：从String到Date  	public Date parse(String source):从给定字符串的开始解析文本生成日期

47.Calendar类
	这个类是和日历相关，这个类里面的月份是从0开始
	创造对象：Canlendar 名字 = Calendar.getInstance()
	直接输出对象里面的东西太复杂可以进行选择：用public int get(int field)进行选择
	名字.get(Calendar.YEAR)获取年份	get(Calendar.MONTH)获取月份-1	get(Calendar.DATE)获取日期
	常用方法：①void add(int field, int mount)方法       例如：c.add(Calendar.YEAR,  -3)意思为对c的日历进行减3年
	②pyblic final void set(int year, int month ,int date)设置一个日历

48.异常类
	异常体系，最大的是Throwable，他有两个子类Error和Exception，而Exception还有两个子类RuntimeException和非他之外的异常
	其中Error：出现严重问题，不需要处理
	Exception：称为异常类，他表示程序本身可以处理的问题
	RuntimeException：在编译器是不检查的，出现问题后，需要我们回来修改代码
	非EuntimeException：在编译期就必须处理，否则程序不能通过编译，更不能运行了

	解决异常类一般有两种：
	①try{可能出现异常的代码}catch(异常类名  变量名){异常后所做的处理}	这种处理完以后程序会继续运行
	建议使用：catch(Exception e){e.printStackTrace();}
	
	②格式：throws 异常类名;   这个格式是跟在方法的括号后面的(格式和继承的extends一样)，他是抛出异常，和默认一样如果出错了则后面还是不允许，还是将异常抛给了虚拟机，还是会让程序死亡
	
	③将异常抛给调用者，由调用者自己解决
	
	Throwable类的成员方法：
	①public String getMessage()返回throwable的详细消息字符串
	②public String toString()返回此可抛出的简短描述，这个方法包含了第一个输出的信息
	③public void printStackTrace()把异常的错误信息输出在控制台
	
	自定义编译时异常
	使用步骤：
	①首先要先创建一个类，让他继承自Exception，
	②然后写两个构造方法，一个无参，一个参数为(String message),并且内容为super(message)
	③然后在其他测试类出现异常的地方用throw new 自定义异常类(参数)
	并且用throws抛出
	作用：提醒强烈，一定要处理
	
	自定义允许时异常
	①首先要先创建一个类，让他继承自RuntimeException，
	②然后写两个构造方法，一个无参，一个参数为(String message),并且内容为super(message)
	③然后在其他测试类出现异常的地方用throw new 自定义异常类名(参数)
	作用：提醒不强烈，运行时才去报错
	
	throws和throw的区别：
		throws用在方法声明后面，跟的是异常类名	throw用在方法体内，跟的是异常对象名
		throws表示抛出异常，由该方法的调用者来处理	throw表示抛出异常，由方法体内的语句处理
		throws表示出现异常的一直可能性，并不一定会发生	throw执行throw一定抛出了某种异常
		throw是在方法内部直接创建了一个异常对象，并从此点抛出





### 四、集合的使用

#### (1)集合

49.Collection集合接口
	集合里面存储的只能是引用型类型
	集合类的特点：提供了一种存储空间可变的存储模型，存储的数据容量可以随时发生改变
	集合又分为单列Collection接口和Map双列接口
	其中单列中有List接口可重复，和Set接口不可重复
	List中有ArrayList类(重点)和LinkedList类等
	Set中有HashSet类和TreeSet类
	概述：JDK不提供Collection集合的接口的任何直接实现，它提供更具体的子接口(Set和List)实现
	创建Collection集合的对象：
	①多态的方式	Collection<String填类名>  c = new ArrayList<String>();
	②具体的实现类ArrayList	
	一些简单方法：
	①boolean add(E e)添加元素	E e是对象的意思
	②boolean remove(Object o)从集合中移除指定的元素
	③void clear()清空集合中的元素
	④boolean contains(Object o)判断集合中是否存在指定的元素
	⑤boolean isEmpty()判断集合是否为空
	⑥int size()集合的长度，也就说集合中元素的个数
	⑦Collections.addAll(集合名称,"添加元素",等)
	集合的遍历：
	首先用集合的iterator方法创建一个对象，例子：Iterator<String>  it = c.iterator()
	然后塔里木有两个常用方法，一个是next()用于获取迭代器的下一个元素
	一个是Boolean haxNext()用于判断迭代器是否还有元素
	可以用循环先判断是否为空，再输出it.next()，也可以将next()赋值给String的对象，方便对他进行操作

50.List集合的概述：
	有序集合(也叫序列)，用户可以精确控制列表中每个元素的插入位置，用户可以通过整数所以访问元素并搜索列表中的【元素
	与set集合不同，列表通常运行重复的元素，他继承自Collection集合
	特点：①存储和取出的元素顺序一致
	②可重复：存储的元素可以重复
	特有的方法：
	①void add(int index, E element),再此集合中的指定位置插入指定的元素
	②E remove(int index),删除指定索引的元素，返回被删除的元素
	③E set(int index,E element)修改指定索引的元素，返回被修改的元素
	④E get(int dex)返回指定索引的元素		可以用它遍历
	并发异常修改：ConcurrentModificationException
	产生原因：迭代器遍历的过程中，通过集合对象修改了集合中元素的长度，造成了迭代器获取元素中判断预期修改值和实际修改值不一致
	修改方法：用for循环，然后用集合对象做对应的操作即可

	ArrayList集合
	集合(ArrayList），集合类的特点：提供一种存储空间可变的存储模型，存储的数据容量可以发送改变
	创建一个集合对象：ArrayList<String> array = new ArrayList<String>()		//他的使用要导包
	方法：add()添加	例如array.add("hello")	add(1,"hello")在指定位置添加，在1索引处添加	//不能超过已有的元素数目加1，会报越界IndexoutofBoundException
	remove()删除	例如array.remove("hello")	array.remove(1)删除索引1的元素    //如果没有这个元素会显示false
	set()修改		例如array,set(1,"hello")
	get()获取		例如array,get(0)
	size()获取元素个数
	indexOf("内容")：取该元素的索引


51.Listlterator(列表迭代器)：
	通过List集合的listlterator()方法得到，所以说它是List集合特有的迭代器
	用于允许程序员沿任一方向遍历列表的列表迭代器，再迭代期间修改列表，并获取列表中迭代器的当前位置
	常用的方法：①E next():返回迭代中的下一个元素
	②bollean haNext()：如果迭代具有更多元素，则返回true
	③E previous()：返回列表中的上一个元素
	④boolean hasPrevious()：如果此列表迭代器再相反方向遍历列表时具有更多元素，则返回true
	⑤void add(E e)：将指定元素插入列表		他如果做前面的并发异常那里的操作，则不会报错
	增强for循环(本质时迭代器)：
	实现Collection集合的遍历和简化数组
	格式for(元素数据类型名  变量名  ： 数组或者Collection集合){//在此处使用变量即可，该变量就是元素}
	例如：int[] arr={1,2,3,4};  for(int i : arr){sout(i)}
52.List集合子类特点
	List集合常用子类：
	①ArrayList，底层数据结构是数组，查询快，增删慢
	②LinkedList，底层数据结构是链表，查询慢，增删快
	LinkedList集合特有的功能：
	①public void addFirst(E e)在该列表开头插入指定元素
	②public E getFirst()返回此列表的第一个元素
	③public E getLast()返回此列表的最后一个元素
	④public E  removeFirst()从此列表中删除并返回第一个元素
53.Set集合
	集合特点：①不包含重复元素的集合
	②不带索引的方法，因此不能使用普通的for遍历
	HashSet是他的一个子类，他对迭代的顺序不做任何保证，遍历出来会是乱序
	哈希值：是JDK根据对象的地址或者字符串或者数字算出来的int类型的数值
	Object类有一个方法可以获得对象的哈希值：public int hashCode()返回对象的哈希值		重写方法可以实现哈希值相等
	HashSet集合特点：
	①底层数据结构是哈希表
	②对集合的迭代顺序不作任何保证，也就是说不保证存储和取出的元素顺序一致
	③没有带索引的方法，所以不能使用普通的for循环
	④由于是Set集合，所以也是不包含重复元素的集合
	要保证元素唯一性，需要重写hashCode（）和equals（）
	哈希表相当于是一个链表的数组，先判断哈希值是否相等，在判断内容是否一样来确定唯一性
54.LinkedHashSet集合
	是HashSet的子类
	集合特点：①哈希表和链表实现的Set接口，具有可预测的迭代次序
	②由链表保证元素有序，也就是说元素的存储和取出顺序是一致的
	③由哈希表保证元素唯一，也就说没有重复元素
55.TreeSet集合
	间接的接口Set集合
	集合特点：①元素有序，这里的顺序不是指存储和取出的顺序，而是按照一定的规则进行排序，具体排序取决于构造方法
	TreeSet(),根据元素的自然排序进行排序
	TreeSet(Comparator comparator这个也叫做比较器),根据指定的比较器进行排序
	②也有着Set集合的特点，所以也不包含重复的
56.Comparable自然排序
	interface Comparable<T>该接口实现对它每个类的对象加一个比较排序，这个就叫做自然排序
	所以需要对自定义的类先实现该接口，并且重写方法int compareTo(T t)，如果该方法返回0，说明该元素与上一个相等，不添加进去，如果是1，说明该元素比上一个元素大，加到后面，-1则加到前面
	实现：
	如果按照年龄从小到大排序，则可以用一个值接收this.age(当前)-t.age(上一个)，最后返回这个值，如果从大到小则this和t换个位置
	当年龄相等，再比较姓名按照字母顺序排序num==0?this.name.compareTo(t.name):num; 然后用返回值接收
	字符串本来就有自然排序接口，所以可以直接调用
	TreeSet不带参是自然排序，带参是比较器

	（1）比较器Comparator的使用
		不需要实现接口，直接再集合创建那里使用带参，参数是一个newComparator<T>对象并且重写他的compare方法

57.Map集合
	Interface Map<K,V>    K:键的类型   V:值的类型
	一个键可以对应多个值，但是键必须唯一存在
	创建Map集合的对象：
	多态的实现
	具体的实现类HashMap
	方法：
	V put(K key, V value)将指定的值与该映射的值键相关联即添加方法(如果后来有个键重复了，后面这个键值会覆盖掉前面的)
	V remove(Object key):根据键删除对应的键值对元素
	void clear()：移除所有的键值对元素
	boolean containsKey(Object key)：判断集合是否包含指定的键
	boolean containsValue(Object value)：判断集合是否包含指定值
	boolean isEmpty():判断集合是否为空
	int size():集合的长度，也就说键值对的个数
	获取功能：
	V get(Object key) :根据键获取值，如果没有返回null
	Set<K> keySet():获取所有键的集合
	Collection<V> values():获取所有值的集合，返回的是集合可以用Set集合来接收
	遍历：
	①用keySet获取所有键然后根据键获取对应的值
	②Set<Map.Entry<String,String>> entrySet():获取所有键值对对象的集合,同时在Map.Entry<String,String>里面有getKey和getValue获取值或键
	用hashMap集合存储学生对象并且遍历需要重写equals和hashcode来保证键的唯一性
58.Collections类
	概述：是一个针对集合操作的工具类
	方法：(他们都是根据Comparable自然排序)
	void sort(List<T> list):将列表进行升序排序
	void reverse(List<T> list)：将列表元素进行反转排序
	void shuffle(List<T> list):将列表随机排序
	也可以和比较器Comparator相结合使用
	如sort(arraylist,new Comparator<Strudent>(){重写compare方法})
59.泛型
	概述：本质是参数化类型，顾名思义：就是将类型由原来的具体的类型参数化，然后使用或者调用传入具体的类型，可以用在类，方法和接口中
	泛型类定义格式：public class 类名<类型>：指定一种类型的格式，这里的类型可以看成是形参,类型可以是T，K，E，V等
	<类型1，类型2....>
	注意：将来具体调用时候给定的类型可以看成是实参，并且实参的类型只能是引用数据类型
	泛型方法格式：修饰符 <类型> 返回值类型 方法名(类型 变量名){}
	泛型接口格式：修饰符 interface 接口名<T>{}，他需要有个实现类，实现类也需要用泛型类

	①类型通配符

60.可变参数
	概述：就是可以有多个参数，使用时会将他们都封装到数组里面，并且可变参数只能放在后面
	格式：方法名(数据类型... 变量名)

	可变参数的使用：
	Arrays工具类有一个静态方法(不可以增删，可以修改)
	public static <T> List<T> aslist(T... a):返回由指定数组支持的固定大小的列表
	List接口中有一个静态方法(不可以增删改)
	public static <E> List<T> of(E... elements):返回包含任意数量元素的不可变列表
	Set接口中有一个静态方法(再给元素时，不能给重复的元素)
	public static <E> Set<T> of(E... elements):返回一个包含任意数量元素的不可变集合

61.Lambda表达式
	作用：简化匿名内部类的代码写法
	简化格式：(匿名内部类被重写方法的形参列表) -> {
		被重写方法的方法体代码
	}
	注意：->只是一个语法形式
		Lambda表达式只能简化函数式接口的匿名内部类的写法形式

	什么式函数式接口：
		首先必须是接口，其次接口中有且仅有一个抽象方法的形式
	
	简化规则：
	①参数类型可以省略不屑
	②如果只有一个参数，参数类型可以省略，同时()也可也省略
	③如果Lambda表达式的方法体代码只有一行代码，可以省略大括号不写，同时要省略分号！
	④如果Lambda表达式的方法体代码只有一行代码，可以省略大括号不写。此时，如果这行代码是return语句，必须省略return不写，同时也必须省略分号

62.不可变集合
	不允许添加，修改，删除等，不然会报错。
	创建方式：
	①不可变的List集合
	List<类型> lists = List.of(填数据)
	其他的集合与这个类似

#### (2)Stream流

63.Stream流
	目的：用于简化集合和数组操作的API
	集合获取stream流用：stream()，返回的是Stream<类型>流
	数组：Arrays.stream(数组)  /   Stream.of(数组)

	常用的API方法
	filter:过滤元素		例：list.stream().filter(s -> s.startsWith('张'))
	skip(long n):跳过前几个元素
	limit(long maxSize):获取前几个元素
	distinct():去除流中的重复元素。依赖hashCode和equals方法
	map：在集合元素前面都加上一个数据	   例：stream().map(s -> "黑马" + s).forEach(a -> sout(a))    forEach输出集合
	concat：合并流
	
	注意：上面的方法调用完成后的stream流可以继续使用
	流中无法直接修改集合，数组中的数据
	
	终结方法(用完不能在使用流了)
	count：统计个数		返回值为long
	forEach
	
	收集Stream流
	概念：就是把操作后stram流的结果数据转会到集合或者数组中去
	collect(Collector collector)：收集流，指定收集方法
	
	收集方法
	Collectors.toList()：收集流，返回一个list集合
	Set同上
	toArray：返回一个数组
	
	注意：流只能使用一次

64.日志
	日志常见两个接口规范：
	①Commons Logging(不好用)
	②Simple Logging Facade for java(slfj4)
	他的实现类有Logback(重点学习)

	Logback主要分为三个技术模块：
	logback-core：他为其他两个模块奠定了基础，必须有
	logback-classic：它是log4j的一个改良版本，同时他完整实现了slf4j API。
	logback-access：模块与Tomcat和jetty等Servlet容器集成，以提供HTTP访问日志功能
	
	logback快速入门
	使用步骤：
	①在新的项目里创建lib文件，然后把他们复制进去，然后右键点击add library，将他们填到这项目依赖里面
	②将logback的核心配置文件xml直接复制到src下
	③在代码中获取日志的对象
	public static final Logger LoGGER = LoggerFactory.getLogger("类名.class");
	④调用方法进行日志记录就行





### 五、IO流

65.文件操作

	File类
		File类在包java.io.File下，代表操作系统的文件对象
		它支持绝对路径，也支持相对路径
		相对路径是相对在工程的src路径下，./表示src的上一路径
		构造方法：
		public File("路径")：创建File对象(文件或文件夹)
	
		属性：
		long length()：表示文件的字节大小
		Boolean exists():判断路径是否存在
		Boolean isFile():判断是否是文件
		Boolean isDirectory():判断是否是文件夹
		getAbsolutePath():获取绝对路径
		getPath():获取文件定义的时候使用的路径
		getName()：获取文件的名称带后缀
	
		long lastModified():获取文件最后修改时间
		他需要格式化一下如：new SimpleDateFormat("yyyy/MM/dd HH:mm:ss").format(time))
	
		创建删除功能：
		boolean mkdir():创建一个一级目录
		boolean mkdirs():创建一个多级目录，用的最多
		boolean delete():删除文件或者空文件夹
	
		File类的遍历功能：
		String[] list():获取当前目录下所有的一级文件名称到一个数组中
		File[] listFiles()：获取当前目录下所有的以及文件对象到一个数组中
		注意：
		当调用的对象不存在的时候返回null
		当调用的对象是一个文件时，返回null
		当调用的对象是一个空文件夹时，返回一个长度为0的数组
	
	字符集：
	GBK中一个中文字符占2个字节，英文和数字不管咋样都是1个字节
	UTF-8编码中中文一般占3个字节
	
	String编码(文字转换为字节)
	byte[] getBytes("字符集")	#默认是UTF-8编码，负的一般是中文
	length()：输出字节长度
	Arrays.toString(byte[])：输出数组内容
	
	String解码
	new String(byte[],"字符集")
	
	IO流
	他也被称为输入，输出流，就是用来读写数据的。
	I表示input，读数据到内存，叫数据
	O表示output，写数据到磁盘，叫输出
	
	最小单位分为：字节流和字符(文本)流
	字节流有InputStream和OutputStream
	字符流有Reader字符输入流和Writer字符输出流
	注意：这四大流都是抽象类，不能直接用
	
	文件字节输入流
	FileInputStream是InputStream的实现类，多态实现
	构造方法：
		FileInputStream(File file)
		FileInputStream("路径")
	属性：
		①read()：每次读取一个字节返回其ASCII码，可以用char转，读取完毕返回-1
		读取全部的例子：int b
					   while((b=is.read()) != -1){sout((char)b}
		注意:没法读中文
		②read(byte[] buffer)：每次读取一个字节数组返回，如果没有字节可读返回-1
		例子：byte[] buffer = new byte[3];
			  int len;
			  while((len = is.read(buffer)) != -1){
			  		sout(new String(buffer,0,len));
			  }
	
		byte[] readAllBytes()：读取全部字节
		也可以直接把桶buffer数组的大小设置为文件的大小，利用file获取用int强转
	
	文件字节输出流
	
	FileOutputStream是OutputStream的实现类
	构造方法：
		FileOutputStream(File file)
		FileOutputStream("路径")
	
	属性：
		①void write(int a):写一个字节进去
		②void write(byte[] buffer，int pos，int len)：写一个字节数组进去，或者一部分进去
	
		换行是：os.write("\r\n".getBytes())
	
	注意：写数据会自动创建文件，write以后要flush()刷新数据，最后要close()关闭，write默认是覆盖写，可以加参数true代表追加写


	文件的字节流拷贝(拷贝任何文件)：
	①创建一个字节输入流管道与原文件接通
	②创建一个字节输出流管道与目标文件接通
	③定义一个字节数组转移数据


	try-catch-finally
	finally：在异常处理时提供finally块来执行所有清楚操作，比如说IO流中的释放资源
	特点：finally语句里面的一定会执行，除非JVM退出，该方法，释放资源会比较繁琐
	
	字符流的使用
	FileReader类是Reader的实现类
	构造方法：
		FileReader(File file)
		FileReader("路径")
	属性：
		int read()：读取一个字符的编号(可以用char转换)返回，读取完毕返回-1
		int read(char[] buffer)：读取一个字符数组，返回读取的字符数，如果没有可读的返回-1
		读取全部例子：char[] buffer = new char[1024]
					 int len;
					 while((len=fr.read(buffer)) != -1){
					 	String rs = new String(buffer,0,len);
					 	sout(rs)
					 }


	FileWriter是Writer的实现类
	构造方法：同上
	属性：
		write(String str,int pos,int len):写一个字符串或者一部分
		write(char[] buffer,int pos,int len):写一个字符数组或者一部分
	注意：写数据会自动创建文件，write以后要flush()刷新数据，最后要close()关闭，write默认是覆盖写，可以在构造方法处加参数true代表追加写
	
	把管道的创建写在try括号里面会自动释放资源


	缓冲流(不能多态，默认8KB缓冲)
	作用：缓冲流自带缓冲区，可以提高原始字节流，字符流读写数据的性能
	BufferedInputStream构造方法：
		BufferedInputStream(InputStream is);
	
	BufferedOutputStream构造方法同上
	
	性能分析：缓冲流字符数组复制最完美。
	
	BufferedReader
		构造方法：BufferedReader(Reader in)
		属性：readline():读取一行数据并返回，读取完毕返回null
		例子：String line;
			  while((line = br.readline()) != null){
			  	sout(line)
			  }
	
	BufferedWriter
		构造方法：BufferedReader(Writer w)
		属性：newLine()：换行


	转换流
	InputStreamReader字符输入转换流
	构造方法：
		InputStreamReader(inputStream is，String charset)：把原始的字节流按照指定编码格式转换为字符输入流
	
	OutputStreamWrite字符输出转换流
	构造方法：
		OutputStreamReader(OutputStream os，String charset)


	对象序列化
	作用：以内存为基准，把内存中的对象存储到磁盘文件中去，称为对象序列化
	使用到的流是对象字节输出流：ObjectOutputStream
	构造方法：ObjectOutputStream(OutputStream os)
	属性：Object writeObject(Object obj)
	注意：对象如果要序列化，对应的类必须要实现Serializanle序列号接口
	
	对象反序列化
	作用：以内存为基准，把存储到磁盘文件中去的对象数据恢复成内存中的对象，称为对象反序列化
	使用到的流是对象字节输出流：ObjectInputStream
		构造方法：ObjectInputStream(InputStream is)
	属性：Object readObject(Object obj)
	注意：如果某些密码啥子不想序列化，可以在定义常量前加上transient修饰的成员变量不参加序列化
	
	打印流
	作用：可以实现方便，高效的打印数据到文件中去
	可以实现打印什么就是什么，默认覆盖
	想要实现追加，还是在低级管道加true
	实现类
	PrintStream字节打印流，继承OutputStream
	构造方法：
		PrintStream(OutputStream os)
		PrintStream(File file,charset)
	属性：
		println()
	
	PrintWriter字符打印流，继承Writer
	构造方法：
		PrintWriter(OutputStream os)
	重定向
		System.setOut(printStream)：改变系统打印流改成我们自己的打印流
	
	Properties属性集对象
	他其实是一个Map集合，但是不一般不当集合用，HashMap更好用
	作用：其实他代表一个属性文件，可以把自己对象中的键值对信息存入到一个属性文件去
	属性文件：后缀是.properties结尾的文件，里面的内容都是key=value，后续做配置信息
	
	使用：直接用无参构造new一个
	方法：setPropety("key","value")：存入键值对，和put一样
		  store(OutputStream os,String comments)：保存管道，把他存到管道里，comments意思是保存的心得，相当于注释
		  store(Writer wt,String comments)：同上
		  load(FileReader fr):加载属性文件到这个properties对象中
		  String getProperty("key")：取key的值


	commons-io框架
	是一个有关IO操作的类库，提高开发效率
	主要两个类FileUtils和IOUtils
	
	FileUtils
	方法：String readFileToString(File file，String encoding)：读取文件中的数据，返回字符串
	void copyFile(File srcFile，File destFile)：复制文件
	void copyDiretoryToDirectory(File srcDir，File destDir)：复制文件夹



### 六、线程的使用

66.线程
	多线程的创建
	方法一：继承Thread类
	使用方法：
		①创建一个类继承Thread
		②重写run方法，定义线程以后要干啥
		③在主类里面new一个新线程对象(就是刚刚定义那个线程)
		④然后通用start方法启动线程(执行的还是run方法)
	注意：main方法中不要把主线程的任务设置在子线程的前面
	缺点：没法继承其他，不利于扩展

	方法二：实现Runnable接口
	使用方法：
		①定义一个线程任务类实现Runnable接口
		②重写run方法，定义任务
		③在主方法中创建一个任务对象(new 左边Runnable右边类名)
			Runnable t = new MyRunnable();
		④把任务对象交给Thread处理
			Thread t1 - new Thread(t);
	匿名内部类的形式实现创建线程：
		①Runnable target = new Runnable(){ 
			public void run(){}
			};
		②Thread t - new Thread(target);
		③t.start()
	缺点：编程多一层对象包装，如果线程有返回结果是不可以直接返回的


	方法三：JDK5.0新增：实现Callable接口
	前面两种不能返回结果
	使用方法：
		①1.得到任务对象：定义类实现Callable接口，重写V call(){}方法，封装要做的事情
		class MyCallable implements Callable<String>{
			public String call() throws Exception(){}
		}
		注意：String表示任务结束后返回的数值类型
		 2.用FutureTask把Callable对象封装成线程任务对象
		 Callable<String> call = new MyCallable();
		 FutureTask<String> f1 = new FutureTask<>(call)
		注意：FutureTask对象的作用：是Runnable的实现类，可以交给Thread，可以线程结束之后用他的get方法获取结果(get这里会等待线程执行完才会提取结果)
		②把线程任务对象交给Thread处理
		③调用Thread的start方法启动


	Thread常用方法
	getName()：获取线程名称
	setName()：改变线程名称
	static Thread currentThread()：获取当前线程名称
	static void sleep():可以睡眠多少毫秒
	
	线程同步
	作用：为了解决线程安全问题
	核心思想：
		加锁：把共享资源进行上锁，每次只能一个线程进入访问完毕以后解锁，然后其他线程才能进来
	
	加锁方式一：同步代码块
	作用：把出现问题的核心代码上锁
	格式：synchronized(同步锁名称){操作共享资源的代码}
	注意：锁对象用任意唯一的对象不好，会影响其他无关线程进行，实例方法最好用this(用共享资源作为对象)，静态方法最好用字节码(类名.class)对象作为锁对象
	
	方法二：同步方法
	格式：修饰符	synchronized 返回值类型 方法名称(形参列表){}
	注意：直接在核心代码的方法进行加锁就行，它实例默认是this对象，代码必须高度面向对象，他用的比较多
	
	方法三：Lock锁
	注意：他是一个接口，采用他的实现类ReentrantLock来构建锁对象
	例如：private final Lock lock = new ReentrantLock();
	方法：
		lock()：上锁
		unlock()：解锁，他一般放在异常的finally里面
	
	Object类唤醒和等待方法
	void wait():让当前线程等待并释放所占锁，直到另一个线程调用唤醒方法
	void notify():唤醒正在等待的单个线程
	void notifyAll():唤醒正在等待的所有线程
	注意：上述方法应该使用当前同步锁对象进行调用

67.线程池(重点)
	线程池就是一个可以复用线程的技术
	ExecutorService接口代表线程池
	获取线程池对象方式一：
		①使用ExecutorService的实现类ThreadPoolExecutor自创建一个线程池对象
		构造方法：
			ThreadPoolExecutor()

​			![](D:\Users\作业\知识点\java\线程池ThreadPool的参数.png)

​			例子：用线程池实现Runnable

![](D:\Users\作业\知识点\java\Excute的用法.png)

![](D:\Users\作业\知识点\java\线程任务拒绝时策略.png)



②使用Executors(线程池的工具类)调用方法返回不同特点的线程池对象

常用方法：

​	

![](D:\Users\作业\知识点\java\Executors得到线程池的常用方法.png)

常见问题：

​	![](D:\Users\作业\知识点\java\Executors常见问题.png)



### 七、其他

68.定时器
	实现方式一：Timer定时器(内部时一个线程)
	构造方法：Timer()：创造Timer定时器对象
	方法：void schedule(TimerTask task,long delay延时,long period周期)：开启一个定时器，按照计划处理TImerTask任务

	特点和存在问题：
		①他是单线程，处理多个任务按照顺序执行，存在延时与与设置定时器的时间有出入
		②可能因为其中的某个任务的异常使线程死掉，从而影响后续任务执行
	
	实现方式二：ScheduledExecutorService(内部是一个线程池)
	
	Executors的方法
	static scheduledExecutorService newSceduledThreadPool(int corePoolSize)：得到线程池对象，指定几个线程
	
	ScheduledExecutorService的方法：
	ScheduleFuture<?> scheduleAtFixedRate(Runnable command,long initialDelay,long period,TimeUnit unit)：周期调度方法
	优点：各个线程互不影响

69.网络编程
	IP地址：设备在网络中的地址，是唯一的标识。
	常见的分类为：IPV4(32位)和IPV6(128位16个字节)
	DNS服务器(域名解析服务器)
	IP地址形式：公网地址，私有地址(局域网)
	192.168.开头就是常见的局域网地址，范围位0.0到255.255
	常用命令：
		ipconfig:查看本机IP地址
		ping ip地址：检测网络是否连通
		本机IP:127.0.0.1或者localhost，也称为本地回环地址，只会找本机

	IP地址操作类
	InetAddress类
	方法：
		①static InetAddress getLocalHost()：返回本主机的地址对象
		②static InetAddress getByName(String host)：得到指定主机的IP地址对象，参数是域名或者IP地址
		③String getHsotName()：获取IP地址的主机名
		④String getHostAddress()：返回IP地址字符串
		⑤boolean isReachable(int timeout)：在指定毫秒内连通该IP地址对应的主机，连通返回true
	
	端口：应用程序在设备中的唯一标识
	端口号是一个16位的二进制，范围是0到65535
	端口类型：周知端口：0到1023，被某些知名应用占用(HTTp占用80，FTP占用21)
	注册端口：1024到49161，分配给某些程序(如Tomcat用8080，MySQL用3306)
	动态端口：49152到65535，是因为他不固定，是动态分配
	注意：我们自己开发的端口选用注册端口
	
	协议：数据在网络中传输的规则，常见的有UDP协议和TCP协议
	连接和通信数据的规则被称为网络通信协议
	
	TCP协议的特点
	①使用TCP协议，必须双方先建立连接，它是一种面向连接的可靠通信协议。传输前，采用“三次握手”方式建立连接，所以是可靠的。
	②在连接中可进行大数据量的传输。
	③连接、发送数据都需要确认，且传输完毕后，还需释放已建立的连接，通信效率较低。
	
	注意：在Java中使用java.net.Socket类实现通信，底层即是使用了TCP协议
	
	Socket
	构造方法：public Socket(String host, int port)：创建发送端的Socket对象与服务端连接，参数为服务端程序的ip和端口
	成员方法：
	OutputStream getOutputStream：获得字节输出流对象
	InputStream getInputStream：获得字节输入流对象
	使用步骤：①创建客户端的Socket对象，请求与服务器连接
	Socket socket = new Socket("127.0.0.1","7777")
	②从Socket通信管道中获得字节输出流，负责发送数据
	OutputStream os = socket.getOutputStream();
	PrintStream ps = new PrintStream(os);
	尽量使用打印流包装，效率更高
	③发送消息
	ps.println()	#这里不能用print，因为服务端是按行读取
	ps.flush()
	④不要关闭资源
	
	ServerSocket(服务端)
	构造方法：public ServerSocket(int port)：注册服务端端口
	成员方法：Socket accept()：等待接收客户端的socket通信连接，连接成功返回Socket对象与客户端建立
	使用步骤：①注册端口
	ServerSocket serversocket = new ServerSocket(7777)
	②调用accept方法，建立socket通信管道
	Socket socket = serversocket.accept()
	③从socket通信管道中得到一个字节输入流
	InputStream is = socket.getInputStream()
	BufferedReader br = new BufferedReader(new InputStreamReader(is))
	④按照行读取消息
	String msg
	if((msg = br.readLine()) != null){
		sout(socket.getRemoteSocketAddress())
	}
	
	注意：如果要实现多个客户端通信需要实现多线程，这个时候可以采用线程池来优化性能，使用线程池适合通信时常较短的场景，以上都是CS(服务器和客户端)结构
	
	即时通信是什么含义，要实现怎样的设计
	①即时通信，是指一个客户端的消息发出去，其他客户端可以接收到即时通信需要进行端转发的设计思想。
	②服务端需要把在线的Socket管道存储起来一旦收到一个消息要推送给其他管道
	
	BS


​	

	UDP协议特点：
	①UDP是一种无连接、不可靠传输的协议。
	②将数据源P、目的地IP和端口封装成数据包，不需要建立连接每个数据包的大小限制在64KB内
	③发送不管对方是否准备好，接收方收到也不确认，故是不可靠的可以广播发送，发送数据结束时无需释放资源，开销小，速度快。


70.Junit单元测试框架
	使用：导入jar包或者maven导入
		Assert实现类里面有许多方法
	注意：①必须是公开的，无参数，网返回值的方法
		 ②测试方法必须用Test注解标记

71.反射
	概述：反射是指对于任何一个CLass类，在运行的时候都可以直接得到这个类全部成分
	使用：
	①获取Class类的对象三种方式：
		1.Class.forName("全类名")：获取该class
		2.类名.class：获取该class
		3.对象名.getClass()：获取该Class
	②根据Class类对象获取构造器对象
		1.Constructor<?>[] getConstructors()：返回所有构造器对象的数组(只拿public)
		2.Constructor<?>[] getDeclareConstructors()：返回所有构造器对象的数组，存在就能拿
		3.Constructor<?>[] getConstructor(Class<?>... parameterTypes):返回单个public构造器对象，常用
		4.Constructor<?>[] getDeclareConstructor(Class<?>... parameterTypes):返回单个构造器对象，存在就能拿到,常用
		获取构造器对象以后调用对应方法获取值
			1.newInstance("输入参数或无")：创建一个新的对象，可能需要强转
			2.setAccessible(true)：权限被打开，可以使用私有构造器，仅限一次
	③根据Class对象获取成员变量对象
		1.Field[] getDeclaredFields()：获取全部成员变量
		2.Field getDeclaredField(String name)：获取某个成员变量
		获取成员变量后可以进行一系列操作：
			1.赋值：set(对象，值)
			2.setAccessible(true)：暴力打开权限
			2.get(对象)：取这个对象的值，可能需要强转
	④根据Class对象获取成员方法对象
		1.Method[] getMethods()：返回所有public成员方法对象的数组
		2.Method[] getDeclaredMethods()：同上返回所有的
		3.Method getMethod()：返回单个public的成员方法对象
		4.Method getDeclaredMethod(name,类型.class)：返回单个成员方法对象，存在就能拿到
		获取对象以后使用该对象进行执行此方法
			1.Object invoke(Object obj,Object... args)：运行方法 参数一：用boj对象调用该方法  参数二：调用方法的传递的参数(如果没有就不写)  返回值：方法的返回值(如果没有就不写)
		注意：方法如果没有结果回来，那么返回的是null

	反射的作用：①绕过编译阶段为集合添加数据(跳过泛型的编译约束)
					先获得集合的class，然后获得add方法的对象，然后执行方法即可
			   ②通用框架的底层原理

72.注解
	自定义注解
		格式：public @interface 注解名称{
			public 属性类型 属性名() default 默认值;
		}
		使用：需要先定义一个注解接口，然后在里面定义属性值
			  然后在类或者方法上面可以进行使用自定义注解

	注意：有默认值的时候使用的时候前面的属性名可以省略，并且若只有一个属性值也可也直接省略
	
	元注解
	概述：就是注解注解的注解(就是放在自定义注解接口上面的)
	分类：@Target：约束自定义注解智能在哪些地方使用
		  @Retention：申明注解的生命周期
	使用格式：
		@Target中可使用的值定义在ElementType枚举类中，常用值如下
			TYPE，类，接口
			FIELD,成员变量
			METHOD,成员方法
			PARAMETER,方法参数
			CONSTRUCTOR,构造器
			LOCAL_VARIABLE，局部变量
		@Retention中可使用的值定义在RetentionPolicy枚举类中，常用值如下
			SOURCE:注解只作用在源码阶段，生成的字节码文件中不存在
			CLASS:注解作用在源码阶段，字节码文件阶段，运行阶段不存在，默认值.
			RUNTIME:注解作用在源码阶段，字节码文件阶段，运行阶段（开发常用)


	注解解析
		概述：注解的操作中经常需要进行解析，注解的解析就是判断是否存在注解，存在注解就解析出内容。
	
	与注解解析相关的接口
		Annotation:注解的顶级接口，注解都是Annotation类型的对象
		AnnotatedElement:该接口定义了与注解解析相关的解析方法
	方法
		①Annotation[] getDeclaredAnnotations()：获得当前对象上使用的所有注解，返回注解数组。
		②T getDeclaredAnnotation(Class<T> annotationClass)：根据注解类型获得对应注解对象
		③boolean isAnnotationPresent(Class<Annotation> annotationClass)：判断当前对象是否使用了指定的注解，如果使用了则返回true，否则false
		注意：所有的类成分Class, Method , Field , Constructor，都实现了AnnotatedElement接口他们都拥有解析注解的能力
	使用：①先获取相应的对象
		 ②然后调用上面的方法
		 ③获取该注解的对象
	
	解析注解的技巧
		注解在哪个成分上，我们就先拿哪个成分对象。
		比如注解作用成员方法，则要获得该成员方法对应的Method对象，再来拿上面的注解
		比如注解作用在类上，则要该类的Class对象，再来拿上面的注解
		比如注解作用在成员变量上，则要获得该成员变量对应的Field对象，再来拿上面的注解

 73.代理
 	代理的代表类是java.lang.reflect.Proxy
 	方法：
 		static Object newProxyInstance(Classloder loder，Class<?> interfaces,invocationHandler h)：用于为对象产生一个代理对象返回
 		参数一：定义代理类的类加载器
 		参数二：代理类要实现的接口列表
 		参数三：将方法调用分派到的处理程序(代理对象的核心处理程序)
 	使用：
 		①必须存在接口
 		②被代理对象需要实现接口
 		③使用Proxy类提供的方法，的对象的代理对象
 	执行流程：
 		①先走向代理
 		②代理可以为方法额外做一些辅助工作。
 		③开发真正触发对象的方法的执行。
 		④回到代理中，由代理负责返回结果给方法的调用者。

 74.XML
 	概述：XML是可扩展标记语言的缩写，是一种数据表示格式，常用于传输和存储数据。
 	规范：
 		①文档的声明必须在第一行
 		②根标签有且只能有一个
 		③&lt; 小于 &gt; 大于 &amp; 和号 &apos; 单引号 &quot; 引号
 		④<![CDATA[  内容  ]]>    在里面可以随便写

 	文档约束
 	分类：DTD(约束编写格式)和schema(可以在其基础上约束数据类型，后缀必须是.xsd)
 	
 	解析XML
 	最常用的是dom4j框架
 	使用：
 		①创建一个Dom4j的解析器对象，代表了整个dom4j框架
 	    	SAXReader saxReader = new SAXReader();
 		②把XML文件加载到内存中成为一个Document文档对象
 			注意: getResourceAsStream中的/是直接去src下寻找的文件
 	        InputStream is = 类名.class.getResourceAsStream("/Contacts.xml");
 	        Document document = saxReader.read(is);
 	 	③获取根元素对象
 	 		Element root = document.getRootElement();
 		④然后调用相应方法获取想要的
 	
 	方法：
 	 	①List<Element> elements()：得到当前元素下所有子元素
 		②List<Element> elements(String name)：得到当前元素下指定名字的子元素返回集合
 		③Element element(String name)：得到当前元素下指定名字的子元素,如果有很多名字相同的返回第一个
 		④String getName()：得到元素名字
 		⑤String attributeValue(String name)：通过属性名直接得到属性值
 		⑥String elementText(子元素名)：得到指定名称的子元素的文本
 		⑦String getText()：得到文本
 	
 	xpath查找XML数据
 	使用：
 		①导jar包jaxen
 		②List<Node> nodes = document.selectNodes("写xpath语法")
 	
 		document.selectSingleNode("语法")：返回单个值



![](D:\Blog\source\_img\wuwang.png)
