
### **1.以下代码有问题吗，是什么问题？**
1.1
```java
List<Object> obj = new ArrayList<Long>();
obj.add("I love Android!");
```

1.2
```java
Object[] objArray = new Long[1];
objArray[0] = "I love Android!";
```

**结果：**
```
1.1 编译出错。
1.2 编译可以，运行出错。
```

**原因：**

1.1 `List\<Object\>`和`ArrayList\<Long\>`没有继承关系。

1.2 数组是在运行时类型检查。

### **2.以下代码运行结果？**
2.1
```java
Long l1 = 128L;
Long l2 = 128L;
System.out.print(l1 == l2);
System.out.print(l1 == 128L);
```

2.2
```java
Long l1 = 127L;
Long l2 = 127L;
System.out.print(l1 == l2);
System.out.print(l1 == 127L);
```

**结果：**
```
2.1 false  true。   
2.2 true  true。
```

**原因：**

2.1 Long 包装类型常量 cache 为 -128 ～ 127 之间，所以 l1 和 l2 是两个对象，== 比较的是对象的地址，所以第一个打印为 false；接着由于包装类型在表达式中且表达式中至少有一个不是包装类型，所以 Long l1== 128L 中 l1 自动拆箱比较，所以数值比较为 true。

2.2 Long 包装类型 -128 - 127 之间的值 Java 维护在一个常量池中，所以 l1 和 l2 引用同一个对象。

还可参见第 16 题解析。

### **3.程序可以编译运行吗，结果是神马？**
```java
List<String>[] list = new List<String>[3];
```

**结果：**

不可以运行，编译报错。

**原因：**

创建泛型、参数化类型或者类型参数的数组是非法的，都会导致编译错误。

考察点：Java 默认是没有泛型数组的

### **4.是否存在使 `i>j || i<=j` 结果为 false 的数吗？**
**结果：**

存在。
  
**原因：**

数值 `NaN` 代表 not a number，无法用于比较，例如即使是 `i = Double.NaN; j = i;` 最后 `i == j` 的结果依旧为 false。
  
考察点：Java NaN。

### **5.下面程序的运行结果是什么？**
```java
public class Main {
    public static void main(String[] args) {
        TxtThread txtThread = new TxtThread();

        new Thread(txtThread).start();
        new Thread(txtThread).start();
        new Thread(txtThread).start();
    }

    static class TxtThread implements Runnable {
        int num = 100;
        String str = new String();

        @Override
        public void run() {
            synchronized (str) {
                while (num > 0) {
                    try {
                        Thread.sleep(1);
                    } catch (InterruptedException ex) {
                        Logger.getLogger(TxtThread.class.getName()).log(Level.SEVERE, null, ex);
                    }
                    System.out.println(Thread.currentThread().getName() + " this is " + num--);
                }
            }
        }
    }
}
```
**结果：**
```
Thread-0 this is 100
......	
......	
Thread-0 this is 1	
```

**原因：**

三个线程共享对一个 Runnable 对象，所以同步锁中其他两个线程没有执行机会。

### **6.下面这段代码中，第几行的 fobj/sobj 符合垃圾收集器的收集标准？**
6.1
```java
 1．fobj = new Object();   
 2．fobj.Method();   
 3．fobj = new Object();   
 4．fobj.Method();   
```

6.2
```java
1．Object sobj = new Object();   
2．Object sobj = null;   
3．Object sobj = new Object();   
4．sobj = new Object();   
```
 	
6.3
```java
1．Object aobj = new Object ( ) ;   
2．Object bobj = new Object ( ) ;   
3．Object cobj = new Object ( ) ;   
4．aobj = bobj;   
5．aobj = cobj;   
6．cobj = null;   
7．aobj = null; 
```

**结果及原因：**
 
6-1、第 3 行。因为第 3 行的 fobj 被赋了新值，产生了一个新的对象，即换了一块新的内存空间，也相当于为第 1 行中的 fobj 断开了引用，这种类型的题是最简单的。 

6-2、第 2、4 行。因为第 2 行为 sobj 赋值为 null，所以在此第 1 行的 sobj 符合垃圾收集器的收集标准。而第 4 行相当于为 sobj 赋值为 null，所以在此第 3 行的 sobj 也符合垃圾收集器的收集标准。 

6-3、第 4、7 行。行 1-3 分别创建了 Object 类的三个对象aobj、bobj、cobj；行 4 此时对象 aobj 的句柄指向 bobj，原来 aojb 指向的对象已经没有任何引用或变量指向，这时，就符合回收标准了；行 5 此时对象 aobj 的句柄指向 cobj，所以该行的执行不能使 aobj 符合垃圾收集器的收集标准；行 6 此时仍没有任何一个对象符合垃圾收集器的收集标准；行 7 对象 cobj 符合了垃圾收集器的收集标准，因为 cobj 的句柄指向单一的地址空间，在第 6 行的时候 cobj 已经被赋值为 null，但由于 cobj 同时还指向了 aobj（第5行），所以此时 cobj 并不符合垃圾收集器的收集标准，而在第 7 行 aobj 所指向的地址空间也被赋予了空值 null，这就说明由 cobj 所指向的地址空间已经被完全地赋予了空值，所以此时 cobj 最终符合了垃圾收集器的收集标准，但对于 aobj 和 bobj 仍然无法判断其是否符合收集标准，总之在 Java 语言中，判断一块内存空间是否符合垃圾收集器收集的标准只有两个：给对象赋予了空值 null，以下再没有调用过；给对象赋予了新值，既重新分配了内存空间。 

### **7.以下程序运行的结果是神马？**
```java
String s1 = "abc";
StringBuffer s2 = new StringBuffer(s1);
System.out.println(s1.equals(s2));
```
**结果：**

false。

**原因:**

考 String 的 toString 方式，toString 方法进行了 instance of 判断。

### **8.下面的题目结果是什么?**
```java
public class TestFool {
    public static String foo(){
        System.out.println("Test foo called");
        return "";
    }

    public static void main(String[] args) {
        TestFool obj = null;
        System.out.println(obj.foo());
    }
}
```
**结果：**

Test foo called。

**原因:**

jvm 内存里有栈区、堆区，栈区主要用来存放基础类型数据和局部变量，堆区主要存放 new 出来的对象。在堆区又有一个叫做方法区的内存区域用来存放常量、static 变量和 static 方法，还有类的信息，static 的变量和方法不依赖对象，即使对象没有创建，在类加载的时候已经存在信息了，jvm 识别出是 static 方法就直接调用了在方法区内存里的方法，没有报空指针异常。细节参见 10 题解析。

### **9.以下继承关系，运行结果是？**
```java
public class Base {
    public Base(){
        test();
    }
    
    public void test(){
    }
}

public class Child extends Base {
    private int a = 123;
    
    public Child(){
    }
    
    public void test(){
        System.out.println(a);
    }
}

public static void main(String[] args){
    Child c = new Child();
    c.test();
}
```
**结果：**
```
0 123
```
**原因：**

Child 直接继承于 Base，默认构造函数不显示调用 super 也会直接父类的默认构造函数，所以首先调用 `Base.java-->test()` 方法。这时 Child 类还没有构造完毕，a 基本数据类型还没有赋予值，a 又为成员变量默认值为 0。
如果在父类构造方法中调用了可被重写的方法则可能会出现意想不到的效果，慎重使用，最好只调用 private 方法。

### **10.以下继承关系，运行结果是？**
```java
public class Base {
	public static String s = "static_base";
	public String m = "base";
	
	public static void staticTest(){
		System.out.println("base static: "+s);
	}
}

public class Child extends Base {
	public static String s = "child_base";
	public String m = "child";
	
	public static void staticTest(){
		System.out.println("child static: "+s);
	}
}

public static void main(String[] args) {
	Child c = new Child();
	Base b = c;
	
	System.out.println(b.s);
	System.out.println(b.m);
	b.staticTest();
	
	System.out.println(c.s);
	System.out.println(c.m);
	c.staticTest();
}
```
**结果：**
```
static_base
base
base static: static_base
child_base
child
child static: child_base
```
**原因：**

当通过 b 访问时访问的是 Base 的变量和方法，当通过 c 访问时访问的是 Child 的变量和方法，这就是所谓的静态绑定，即访问绑定到变量的静态类型，静态绑定在程序编译阶段即可决定，而动态绑定则要等到程序运行时，实例变量、静态变量、静态方法、private 方法都是静态绑定。
继承重名是允许的，重名后实质有两个变量或者方法，对于 private 变量和方法，他们只能在类内被访问，访问的也永远是当前类（即子类中访问子类的，父类中访问父类的），仅仅是名字相同而已，没有任何关系；对于 public 变量和方法要看如何访问它，在类内部访问的是当前类的，单子类可以通过 super 明确指定访问父类的，在类外则要看访问变量的静态类型，静态类型是父类则访问父类的变量和方法，静态类型是子类则访问子类的变量和方法。

### **11.如果出现以下代码，编译运行会出现什么现象？**
```java
public class Base {
	protected void protect(){
	}
	
	public void open(){        
	}
}

public class Child extends Base {
//1放开会有啥现象
//    private void protect(){
//    }
	
//2放开会有啥现象
//    protected void open(){        
//    }
//3放开会有啥现象
	public void protect(){        
	}
}
```

**结果:**
```
如果 1 放开会在编译阶段报错；
如果 2 放开会在编译阶段报错；
如果 3 放开编译运行正常；
```
**原因：**

重载是指方法名相同但参数签名不同，重写是指子类重写父类相同签名的方法，但是重写方法不能比被重写方法限制有更严格的访问级别（即可见性重写）。

### **12.以下继承方式运行时会如何打印？**
```java
public class Base {
	public static int s;
	private int a;

	static {
		System.out.println("基类静态代码块, s:" + s);
		s = 1;
	}

	{
		System.out.println("基类实例代码块, a:" + a);
		a = 1;
	}

	public Base() {
		System.out.println("基类构造方法, a:" + a);
		a = 2;
	}

	protected void step() {
		System.out.println("base s: " + s + " , a : " + a);
	}

	public void action() {
		System.out.println("start");
		step();
		System.out.println("end");
	}
}

public class Child extends Base {
	public static int s;
	private int a;
	static {
		System.out.println("子类静态代码块， s:"+s);
		s = 10;
	}

	{
		System.out.println("子类实例代码块， a:"+a);
		a = 10;
	}

	public Child(){
		System.out.println("子类构造方法，a："+a);
		a = 20;
	}

	protected  void  step(){
		System.out.println("child s: "+s+" , a: "+a);
	}
}

public static void main(String[] args) {
    System.out.println("------ new Child()");
    Child c = new Child();
    System.out.println("\n------ c.action()");
    c.action();

    Base b = c;
    System.out.println("\n-------b.action()");
    b.action();

    System.out.println("\n ------- b.s: "+b.s);
    System.out.println("\n ------- c.s: "+c.s);
}
```

**结果:**
```
------ new Child()
基类静态代码块, s:0
子类静态代码块， s:0
基类实例代码块, a:0
基类构造方法, a:1
子类实例代码块， a:0
子类构造方法，a：10

------ c.action()
start
child s: 10 , a: 20
end

-------b.action()
start
child s: 10 , a: 20
end

------- b.s: 1
------- c.s: 10
```
**原因:**

其实考察的是 Java 类加载与初始化，加载就是在第一次使用类时动态把类加载到内存，加载一个类会看其父类是否已经加载，如果没有则会先加载父类。类加载过程如下：分配内存保存类信息->给类变量赋默认值->加载父类->设置父子关系->执行类初始化代码；而类初始化过程（先执行父类再执行子类，父类执行时子类静态变量是有默认值 0 的）如下：定义静态变量时的赋值语句->静态初始化代码块；这些 OK 以后就进入实例初始化过程：定义实例变量时的赋值语句->实例初始化代码块->构造方法。
所以上面的结果基本就是这个流程，只是寻找要执行的实例方法时是从对象的实际类型信息开始查找的，找不到才会查父类；对于变量无论是类变量还是实例变量都是静态绑定访问的。

### **13.内部类你知多少？**
java 内部类分为几种，各种自己有哪些特性？
 
**结果：**

静态内部类，成员内部类，方法内部类，匿名内部类；

匿名内部类访问外部类的局部变量，局部变量需要 final 修饰，1.8 之后该关键字被省略。

方法内部类只能在定义该内部类的方法内实例化，不可以在此方法外对其实例化。

静态内部类与其他成员相似，可以用 static 修饰内部类，这样的类称为静态内部类，静态内部类与静态内部方法相似，只能访问外部类的 static 成员，不能直接访问外部类的实例变量，与实例方法，只有通过对象引用才能访问。

成员内部类没有用 static 修饰且定义在在外部类类体中。

### **14.下面程序的运行结果是什么？**
```java
public enum Size {
	SMALL, MEDIUM, LARGE
}

Size size = Size.SMALL;
System.out.println(size==Size.SMALL);
System.out.println(size.equals(Size.SMALL));
System.out.println(size==Size.MEDIUM);
System.out.println(size.toString());
System.out.println(size.name());
```

**结果：**
```
true
true
false
SMALL
SMALL
```
**原因：**

枚举可以使用 == 或者 equals 进行比较，结果都一样；枚举也是有顺序的，可以比较大小；枚举变量的 toString 方法和 name 方法返回值一样，都是其字面值；枚举的本质也是类，在编译阶段编译器自动做了很多事情，所以使用起来便捷。

### **15. 请粗略描述下面这些方法调用执行流程和最终返回值？**
```java
public static int test(){
	int ret = 0;
	try{
		return ret;
	}finally{
		ret = 2;
	}
}

public static int test(){
	int ret = 0;
	try{
		int a = 5/0;
		return ret;
	}finally{
		return 2;
	}
}

public static void test(){
	try{
		int a = 5/0;
	}finally{
		throw new RuntimeException("hello");
	}
}
```

**结果：**

1、返回 0，执行到 try 的 return ret; 语句前会先将返回值 ret 保存在一个临时变量中，然后才执行 finally 语句，最后 try 再返回那个临时变量，finally 中对 ret 的修改不会被返回。

2、返回 2，5/0 会触发 ArithmeticException 异常，但是 finally 中有 return 语句，finally 中 return 不仅会覆盖 try 和 catch 内的返回值，还会掩盖 try 和 catch 内的异常，就像异常没有发生一样，所以这个方法就会返回 2，而不再向上传递异常了。

3、运行抛出 hello 异常，因为如果 finally 中抛出了异常，则原异常就会被掩盖。

**总结：**

为避免混淆我们应该避免在 finally 中使用 return 语句或者抛出异常，如果调用的其他代码可能抛出异常，则应该捕获异常并进行处理。

### **16.java1.5开始的自动装箱和开箱机制是编译器特性还是虚拟机运行时特性？分别是怎么实现的？**
```java
Integer i1 = 100;
Integer i2 = 100;
Integer i3 = 200;
Integer i4 = 200;
System.out.println(i1 == i2);
System.out.println(i3 == i4);

Double d1 = 100.0;
Double d2 = 100.0;
Double d3 = 200.0;
Double d4 = 200.0;
System.out.println(d1 == d2);
System.out.println(d3 == d4);

Boolean b1 = false;
Boolean b2 = false;
Boolean b3 = true;
Boolean b4 = true;
System.out.println(b1 == b2);
System.out.println(b3 == b4);

Integer a = 1;
Integer b = 2;
Integer c = 3;
Integer d = 3;
Integer e = 321;
Integer f = 321;
Long g = 3L;
Long h = 2L;
System.out.println(c == d);
System.out.println(e == f);
System.out.println(c == (a + b));
System.out.println(c.equals(a + b));
System.out.println(g == (a + b));
System.out.println(g.equals(a + b));
System.out.println(g.equals(a + h));

Integer a = 444;
int b = 444;
System.out.println(a==b);
System.out.println(a.equals(b));
```

**结果：**
```
true
false
false
false
true
true
true
false
true
true
true
false
true

true
true
```
**原因:**

第一第二没什么解释的，第三句 a+b 包含运算符所以自动拆箱，所以比较值，对于 c.equals(a+b) 会先自动触发自动拆箱再触发自动装箱再比较。

java 1.5 开始的自动装箱拆箱机制其实是编译器自动完成的替换，装箱阶段自动替换为了 valueOf 方法，拆箱阶段自动替换为了 xxxValue 方法。对于 Integer 类型的 valueOf 方法参数如果是 -128~127 之间的值会直接返回内部缓存池中已经存在对象的引用，参数是其他范围值则返回新建对象；而 Double 类型与 Integer 类型一样会调用到 Double 的 valueOf 方法，但是 Double 的区别在于不管传入的参数值是多少都会 new 一个对象来表达该数值（因为在指定范围内浮点型数据个数是不确定的，整型等个数是确定的，所以可以 Cache）。
注意：Integer、Short、Byte、Character、Long 的 valueOf 方法实现类似，而 Double 和 Float 比较特殊，每次返回新包装对象。
对于两边都是包装类型的比较 == 比较的是引用，equals 比较的是值，对于两边有一边是表达式（包含算数运算）则 == 比较的是数值（自动触发拆箱过程），对于包装类型 equals 方法不会进行类型转换。

### **17.String 消消乐，下面程序的执行结果分别是什么？**
```
String stra = "ABC";
String strb = new String("ABC");
System.out.println(stra == strb);		//false
System.out.println(stra.equals(strb));		//true

String str1 = "123";
System.out.println("123" == str1.substring(0));		//true
System.out.println("23" == str1.substring(1));		//false

String str3 = new String("ijk");
String str4 = str3.substring(0);
System.out.println(str3 == str4);		//true
System.out.println((new String("ijk") == str4));		//false

String str5 = "NPM";
String str6 = "npm".toUpperCase();
System.out.println(str5 == str6);		//false
System.out.println(str5.equals(str6));		//true

String str7 = new String("TTT");
String str8 = "ttt".toUpperCase();
System.out.println(str7 == str8);		//false
System.out.println(str7.equals(str8));		//true

String str9 = "a1";
String str10 = "a" + 1;
System.out.println(str9 == str10);		//true

String str11 = "ab";
String str12 = "b";
String str13 = "a" + str12;
System.out.println(str11 == str13);		//false

String str14 = "ab";
final String str15 = "b";
String str16 = "a" + str15; 
System.out.println(str14 == str16);		//true


private static String getBB() {   
    return "b";   
}
String str17 = "ab";   
final String str18 = getBB();   
String str19 = "a" + str18;   
System.out.println(str17 == str19);		//false


String str20 = "ab";
String str21 = "a";   
String str22 = "b";   
String str23 = str21 + str22;   
System.out.println(str23 == str20);   		//false
System.out.println(str23.intern() == str20);		//true
System.out.println(str23 == str20.intern());		//false
System.out.println(str23.intern() == str20.intern());		//true
```
**结果：**
```
见题目中注释部分，基于 JDK 1.7 版本分析。
```

**原因：**

对于 stra 与 strb 来说没啥分析的，显式的创建了新对象，使用 == 总是不等，equals 被重写过，值判断。

对于 str1 的 substring 来说其实跳进去看下这个方法的实现就明白了，里面有个 index == 0 判断，等于 0 就直接返回当前对象，否则新 new 一个。

对于 str3 与 str4 来说就没啥分析的必要了，参见上面两个分析结果。

对于 str5、str6、str7、str8 来说实质都一样，toUpperCase 方法内部创建了新字符串对象。

对于 str9 与 str10 来说当两个字符串常量连接时（相加）得到的新字符串依然是字符串常量且保存在常量池中。

对于 str11 到 str13 来说当字符串常量与 String 类型变量连接时得到的新字符串不再保存在常量池中，而是在堆中新建一个 String 对象来存放，很明显常量池中要求的存放的是常量，有String类型变量当然不能存在常量池中了。

对于 str14 到 str16 来说此处是字符串常量与 String 类型常量连接，得到的新字符串依然保存在常量池中。

对于 str17 到 str19 来说`final String str18 = getBB()`其实与`final String str18 = new String(“b”)`是一样的，也就是说 return “b” 会在堆中创建一个 String 对象保存 ”b”，虽然 str18 被定义成了 final，所以可见看见，并非定义为 final 的就保存在常量池中，很明显此处 str18 常量引用的 String 对象保存在堆中，因为 getBB() 得到的 String 已经保存在堆中了，final 的 String 引用并不会改变 String 已经保存在堆中这个事实。

**str9 到 str19 深刻的说明了我们在代码中使用 String 时应该有的优化技巧，你懂得！特别说明 String 的 + 和 += 在编译后实质被自动优化为了 StringBuilder 和 append 调用，但是如果在循环等情况下调用 + 或者 += 就是在不停的 new StringBuilder 对象 append 了。**

对于 str20 到 str23 来说`str23 == str20`就是上面刚刚分析的；而对于调用 intern 方法如果字符串常量池中已经包含一个等于此 String 对象的字符串（用 equals(Object) 方法确定）则返回字符串常量池中的字符串，否则将此 String 对象添加到字符串常量池中，并返回此 String 对象的引用，所以`str23.intern() == str20`实质是常量比较返回 true，`str23 == str20.intern()`中 str23 就是上面说的堆中新对象，相当于一个新对象和一个常量比较，所以返回 false，`str23.intern() == str20.intern()` 就没啥说的了，指定相等。

要玩明白 Java String 对象的核心其实就是玩明白字符串的堆栈和常量池，虚拟机为每个被装载的类型维护一个常量池，常量池就是该类型所用常量的一个有序集合，包括直接常量（String、Integer 和 Floating Point常量）和对其他类型、字段和方法的符号引用，池中的数据项就像数组一样是通过索引访问的；由于常量池存储了相应类型所用到的所有类型、字段和方法的符号引用，所以它在 Java 程序的动态链接中起着核心的作用。


### **18.什么是 java 泛型？泛型的核心原理是什么？泛型的好处是什么？请分别简单谈谈您的理解！**

1、Java 泛型实质就是一种语法约束，泛型是Java SE 1.5的新特性，泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法的创建中，分别称为泛型类、泛型接口、泛型方法。

2、泛型的核心原理其实就是泛型的 T 类型参数的原理，Java 编译器在编译阶段会将泛型代码转换为普通的非泛型代码，实质就是擦除类型参数 T 替换为限定类型（无限定类型替换为Object）且插入必要的强制类型转换操作，所以核心其实就是 Test<String> 被擦除还原为 Test，同时 java 编译器是通过先检查代码中泛型的类型然后再进行类型擦除，最后进行编译的。

3、泛型的好处其实就是约束，可以让我们编写的接口、类、方法等脱离具体类型限定而适用于更加广泛的类型，从而使代码达到低耦合、高复用、高可读、安全可靠的特点，避免主动向下转型带来的恶心可读性和隐晦的转换异常，尽可能的将类型问题暴露在 IDE 提示和编译阶段。




