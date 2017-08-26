
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
```java
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
编译器在编译时擦除所有类型相关信息，所以在运行时不存在任何类型相关信息，例如`List<String>`在运行时仅用一个`List`来表示，这样做的目的是确保能和 Java 5 之前的版本开发二进制类库进行兼容，我们无法在运行时访问到类型参数，因为编译器已经把泛型类型转换成了原始类型。

3、泛型的好处其实就是约束，可以让我们编写的接口、类、方法等脱离具体类型限定而适用于更加广泛的类型，从而使代码达到低耦合、高复用、高可读、安全可靠的特点，避免主动向下转型带来的恶心可读性和隐晦的转换异常，尽可能的将类型问题暴露在 IDE 提示和编译阶段。

### **19.下面两个方法有什么区别，有什么问题？**

```java
public static <T> T get1(T t1, T t2) {  
	if(t1.compareTo(t2) >= 0);
	return t1;  
}  

public static <T extends Comparable> T get2(T t1, T t2) {
	if(t1.compareTo(t2) >= 0);  
	return t1;  
}
```
结果：

get1 方法直接编译错误，因为编译器在编译前首先进行了泛型检查和泛型擦除才编译，所以等到真正编译时 T 由于没有类型限定自动擦除为 Object 类型，所以只能调用 Object 的方法，而 Object 没有 compareTo 方法。

get2 方法添加了泛型类型限定可以正常使用，因为限定类型为 Comparable 接口，其存在 compareTo 方法，所以 t1、t2 擦除后被强转成功。所以类型限定在泛型类、泛型接口和泛型方法中都可以使用，不过不管该限定是类还是接口都使用 extends 和 & 符号，如果限定类型既有接口也有类则类必须只有一个且放在首位，如果泛型类型变量有多个限定则原始类型就用第一个边界的类型变量来替换。

### **20.下面程序的运行结果是什么？为什么？**

```java
ArrayList<String> arrayList1 = new ArrayList<String>();  
arrayList1.add("demo");  
ArrayList<Integer> arrayList2 = new ArrayList<Integer>();  
arrayList2.add(123);  
System.out.println(arrayList1.getClass() == arrayList2.getClass()); 


ArrayList<Integer> arrayList3 = new ArrayList<Integer>();  
arrayList3.add(123);  
arrayList3.getClass().getMethod("add", Object.class).invoke(arrayList3, "demo");  
for (int i=0;i<arrayList3.size();i++) {  
	System.out.println(arrayList3.get(i));  
}
```
结果：

第一个返回 true，因为 arrayList1 是`ArrayList<String>`泛型类型只能存储字符串，arrayList2 是`ArrayList<Integer>`泛型类型只能存储整形，而在编译后运行时泛型类型 String 和 Integer 都被擦除只剩下原始类型，所以都是 ArrayList 类，故相等。

第二个正常打印数字 123 和字符串 demo，因为 arrayList3 是`ArrayList<Integer>`泛型类型只能存储整形，但是编译擦除后成了 ArrayList，add 参数擦除替换为 Object，所以可以 add String 类型。

### **21.请尝试解释下面程序中每行代码执行情况及原因？**

```java
public class Test{
	public static <T> T add(T x, T y){  
        return y;
    }
  
    public static void main(String[] args) {
		int t0 = Test.add(10, 20.8);
		int t1 = Test.add(10, 20);
		Number t2 = Test.add(100, 22.2);
		Object t3 = Test.add(121, "abc");

		int t4 = Test.<Integer>add(10, 20);
		int t5 = Test.<Integer>add(100, 22.2);
		Number t6 = Test.<Number>add(121, 22.2);
    }
}  
```
结果：
```
t0 编译直接报错，add 的两个参数一个是 Integer，一个是 Float，所以取同一父类的最小级为 Number，故 T 为 Number 类型，而 t0 类型为 int，所以类型错误。
t1 执行赋值成功，add 的两个参数都是 Integer，所以 T 为 Integer 类型。
t2 执行赋值成功，add 的两个参数一个是 Integer，一个是 Float，所以取同一父类的最小级为 Number，故 T 为 Number 类型。 
t3 执行赋值成功，add 的两个参数一个是 Integer，一个是 Float，所以取同一父类的最小级为 Object，故 T 为 Object 类型。
t4 执行赋值成功，add 指定了泛型类型为 Integer，所以只能 add 为 Integer 类型或者其子类的参数。
t5 编译直接报错，add 指定了泛型类型为 Integer，所以只能 add 为 Integer 类型或者其子类的参数，不能为 Float。 
t6 执行赋值成功，add 指定了泛型类型为 Number，所以只能 add 为 Number 类型或者其子类的参数，Integer 和 Float 均为其子类，所以可以 add 成功。 
```
t0、t1、t2、t3 其实演示了调用泛型方法不指定泛型的几种情况，t4、t5、t6 演示了调用泛型方法指定泛型的情况。
在调用泛型方法的时可以指定泛型，也可以不指定泛型；在不指定泛型时泛型变量的类型为该方法中的几种类型的同一个父类的最小级（直到 Object），在指定泛型时该方法中的几种类型必须是该泛型实例类型或者其子类。

切记，java 编译器是通过先检查代码中泛型的类型，然后再进行类型擦除，再进行编译的。

### **22.请尝试解释下面程序中每行代码执行情况及原因？**

```java
ArrayList<String> arrayList1 = new ArrayList();		// 编译警告
arrayList1.add("1");	// 编译通过  
arrayList1.add(1);		// 编译错误
String str1 = arrayList1.get(0);		//返回类型就是 String  

ArrayList arrayList2 = new ArrayList<String>();  	// 编译警告
arrayList2.add("1");	// 编译通过  
arrayList2.add(1);		// 编译通过  
Object object = arrayList2.get(0);	// 返回类型就是 Object  

List<String> arrayList3 = new ArrayList<>();	//JDK7的新特性，会自动推断泛型
arrayList3.add("123");	//编译通过
arrayList3.add(123);	//编译错误
  
new ArrayList<String>().add("11");	// 编译通过
new ArrayList<String>().add(22);	// 编译错误  
String string = new ArrayList<String>().get(0); // 返回类型就是 String  

ArrayList<String> arrayList4 = new ArrayList<Object>();	// 编译错误  
ArrayList<Object> arrayList5 = new ArrayList<String>();	// 编译错误  
```

解释：

arrayList1 其实可以实现与完全使用泛型参数一样的效果，arrayList2 完全没了泛型特性，arrayList3 是 JDK 7 支持的中规中矩写法，由于类型检查就是针对引用的，与引用的对象无关，所以有了这些结果。

arrayList4 与 arrayList5 是因为泛型中参数化类型是不支持继承关系的，切记（注意与 arrayList1 和 arrayList2 对比理解）。至于不支持的原因如下：

对于 arrayList4 首先我们假设 `new ArrayList<Object>()` 申请的内存赋值给了引用类型是`ArrayList<Object>`或 ArrayList 的 temp，接着我们向 temp 中添加了几个元素（支持 add 任何类型），
然后让`ArrayList<String> arrayList4 = temp`，这时候调用 arrayList4 的 get 返回的都是 String 类型（类型检测是根据引用来决定的），锅来了，temp 中存了各种奇怪的类型啊，
arrayList4 拿回来直接用就可能出现 ClassCastException 异常啊，卧槽，Java 设计泛型的目的之一就是为了避免出现这种锅啊，所以 Java 直接不允许进行这样的引用传递，所以直接编译报错，扼杀在摇篮。

同理对于 arrayList5 来说类似 arrayList4，只不过最终 get 出来是从 String 转为 Object，不会出现 ClassCastException 异常，但是一样卧槽啊，我还得自己转，毛线，泛型出现之一也是为了解决这个锅啊，所以 Java 直接不允许进行这样的引用传递，所以直接编译报错，扼杀在摇篮。

### **23.请尝试解释下面程序中注释问题？**

```java
ArrayList<String> arrayList = new ArrayList<String>();    
if(arrayList instanceof ArrayList<String>) { //TODO }	//1.是否可以运行及结果原因
if(arrayList instanceof ArrayList<?>) { //TODO }	//2.是否可以运行及结果原因

public class Problem<T> extends Exception{ //TODO }		//3.是否可以运行及结果原因

public static <T extends Throwable> void test(Class<T> t) {  
	try{  
		//TODO 
	}catch(T e){	//4.是否可以运行及结果原因
		//TODO
	}
}

public static <T extends Throwable> void test1(T t) throws T {  
    try{  
        //TODO
    }catch(Throwable realCause){  
        t.initCause(realCause);  
        throw t;   			//5.是否可以运行及结果原因
    }
}

ArrayList listn = new ArrayList(); //已过时，取出来时需要自己强制转换，引入泛型代码之前的方式，在引入泛型后集合都已经重写以迎合泛型
```
解答：

1 编译报错，因为类型擦除之后，ArrayList<String> 只剩下原始类型，泛型信息String不存在了，所以进行类型查询的时候使用具体泛型类型是错误的。

2 可以运行，因为 java 限定了通配符这种类型查询的方式。

3 编译报错，因为不能出现继承异常的泛型类也不能捕获泛型类的异常对象，因为异常都是在运行时捕获和抛出的，而在编译的时候泛型信息全都会被擦除掉，所以多个 catcch 中可能会存在一样的原始类型捕获，这是不符合异常的语法。

4 编译报错，不可以在 catch 子句中使用泛型变量，因为泛型信息在编译的时候已经变为原始类型，也就是说上面的 T 会变为原始类型 Throwable，假设同时有多个 catch 则就不符合异常的语法。

5 可以运行，在异常声明中可以使用泛型类型变量。

### **24.请尝试解释下面程序编译到运行的现象及原因？**

```java
List<String>[] ls1 = new ArrayList<String>[10];	//1
List<String>[] ls2 = new ArrayList[10];	//2
List<?>[] lsa = new List<?>[10];	//3
```
结果：

1 编译错误。

2 正常运行，但是编译有警告。

3 正常运行。

因为 Java 规定数组的类型不可以是泛型类型变量，除非是采用通配符的方式。数组是允许把一个子类数组赋给一个父类数组变量的，譬如 Father 继承自 Son，则可以定义`Father[] son=new Son[10];`，如果 Java 允许泛型数组这会出现如下代码：
```java
List<String>[] ls1 = new ArrayList<String>[10];
Object[] oj = ls1;
```
也就是我们能在数组 ls1 里存放任何类的对象且能够通过编译，因为在编译阶段 ls1 被认为是一个Object[]，也就是 ls1 里面可以放一个 int、也可以放一个 String，当我们运行阶段取出里面的 int 并强制转换为 String 则会出现 ClassCastException，这明显违反了泛型引入的原则，所以 Java 不允许创建泛型数组。对于 2 可以编译通过但是会得到警告的解释其实是因为编译器确实不让我们实例化泛型数组，但是允许我们创建对这种数组的引用，所以我们可以创建非泛型数组转型给泛型引用。对于 3 通配符的方式，最后取出数据是要做显式的类型转换的，所以并不会存在上一个例子的问题。

提示：直接使用`ArrayList<ArrayList<String>>`最安全且有效。

### **25.请尝试解释下面程序编译到运行的现象及原因？**

```java
a1 = new T(); //1

public<T> T[] func(T[] a){  
	T[] a2 = new T[2]; //2  
	return a2;
}

public static <T extends Comparable> T[] func1(T[] a) {  
	T[] a3 == (T[]) Array.newInstance(a.getClass().getComponentType(), 2);	//3
	return a3;
}

class Bean<T super Student> { //TODO }	//4
```
解答：

1 编译时报错，不能实例化泛型类型，类型擦除会使这个操作做成 new Object()。

2 编译时报错，不能创建泛型数组，擦除会使这个方法构造一个 Object[2] 数组。

3 可以运行，可以用反射构造泛型对象和数组。

4 编译时报错，因为 Java 类型参数限定只有 extends 形式，没有 super 形式。

### **26.请尝试解释下面程序编译到运行的现象及原因？**

```java
public class Test1<T> {    
    public static T value;   //1
    public static  T test1(T param){ //2
        return null;    
    }    
}

public class Test2<T> {    
    public static <T> T test2(T param){	//3
        return null;
    }    
}

class Test<T> {  //4 这个类可以运行吗？
    public boolean equals(T value) {  
        return true;  
    }     
}
```
解答：

1 编译错误，2 编译错误，3 正常运行。

因为泛型类中的静态方法和静态变量不可以使用泛型类所声明的泛型类型参数，泛型类中的泛型参数的实例化是在定义对象的时候指定的，而静态变量和静态方法不需要使用对象来调用，对象都没创建，如何确定这个泛型参数是何种类型，所以当然是错误的；而 3 之所以成功是因为在泛型方法中使用的 T 是自己在方法中定义的 T，而不是泛型类中的 T。

4 无法编译通过，因为擦除后方法 boolean equals(T) 变成了方法 boolean equals(Object)，这与 Object.equals 方法是冲突的，除非重新命名引发错误的方法。

### **27.下面程序有什么问题，该如何修复？**

```java
public class Test {  
    public static void main(String[] args) throws Exception{  
        List<Integer> listInteger = new ArrayList<Integer>();   
        printCollection(listInteger);     
    }   
    public static void printCollection(Collection<Object> collection) {  
        for(Object obj:collection){  
            System.out.println(obj);  
        }    
    }  
}
```
解答：

语句`printCollection(listInteger);`编译报错，因为泛型的参数是没有继承关系的。
修复方式就是使用 ？通配符，`printCollection(Collection<?> collection)`，因为在方法`printCollection(Collection<?> collection)`中不可以出现与参数类型有关的方法，譬如`collection.add()`，因为程序调用这个方法的时候传入的参数不知道是什么类型的，但是可以调用与参数类型无关的方法，譬如 collection.size()。

### **28.下面程序编译运行会有什么现象？**

```java
Vector<? extends Number> x1 = new Vector<Integer>();	//正确
Vector<? extends Number> x2 = new Vector<String>();	//编译错误

Vector<? super Integer> y1 = new Vector<Number>();	//正确
Vector<? super Integer> y2 = new Vector<Byte>();	//编译错误
```
解释：

本题主要考察泛型中的`?`通配符的上下边界扩展问题。

通配符对于上边界有如下限制：`Vector<? extends 类型1> x = new Vector<类型2>();`中的类型1指定一个数据类型，则类型2就只能是类型1或者是类型1的子类。

通配符对于下边界有如下限制：`Vector<? super 类型1> x = new Vector<类型2>();`中的类型1指定一个数据类型，则类型2就只能是类型1或者是类型1的父类。

### **29.什么是泛型中的限定通配符和非限定通配符？**

解答：

限定通配符对类型进行了限制，有两种限定通配符，一种是`<? extends T>`通过确保类型必须是 T 的子类来设定类型的上界，另一种是`<? super T>`它通过确保类型必须是 T 的父类来设定类型的下界，泛型类型必须用限定内的类型来进行初始化，否则会导致编译错误。
另一方面`<?>`表示非限定通配符，因为`<?>`可以用任意类型来替代。

### **30.简单说说 Java 中`List<Object>`和原始类型`List`之间的区别？**

解答：

区别一：原始类型和带泛型参数类型`<Object>`之间的主要区别是在编译时编译器不会对原始类型进行类型安全检查，却会对带参数的类型进行检查，通过使用 Object 作为类型可以告知编译器该方法可以接受任何类型的对象（比如 String 或 Integer）。

区别二：我们可以把任何带参数的类型传递给原始类型 List，但却不能把`List<String>`传递给接受`List<Object>`的方法，因为会产生编译错误。

### **31.Java 中`List<?>`和`List<Object>`之间的区别是什么？**

解答：

这道题跟上一道题看起来很像，实质上却完全不同。`List<?>`是一个未知类型的`List`，而`List<Object>`其实是任意类型的`List`。我们可以把`List<String>`, `List<Integer>`赋值给`List<?>`，却不能把`List<String>`赋值给`List<Object>`。譬如：
```java
List<?> listOfAnyType;
List<Object> listOfObject = new ArrayList<Object>();
List<String> listOfString = new ArrayList<String>();
List<Integer> listOfInteger = new ArrayList<Integer>();
listOfAnyType = listOfString; //legal
listOfAnyType = listOfInteger; //legal
listOfObjectType = (List<Object>) listOfString; //compiler error – in-convertible types
```
所以通配符形式都可以用类型参数的形式来替代，通配符能做的用类型参数都能做。
通配符形式可以减少类型参数，形式上往往更为简单，可读性也更好，所以能用通配符的就用通配符。
如果类型参数之间有依赖关系或者返回值依赖类型参数或者需要写操作则只能用类型参数。

### **32.`List<? extends T>`和`List <? super T>`之间有什么区别？**

解答：

有时面试官会用这个问题来评估你对泛型的理解，而不是直接问你什么是限定通配符和非限定通配符，这两个`List`的声明都是限定通配符的例子，`List<? extends T>`可以接受任何继承自 T 的类型的`List`，而`List<? super T>`可以接受任何 T 的父类构成的`List`。
例如`List<? extends Number>`可以接受`List<Integer>`或`List<Float>`。Java 容器类的实现中有很多这种用法，比如 Collections 中就有如下一些方法：
```java
public static <T extends Comparable<? super T>> void sort(List<T> list)
public static <T> void sort(List<T> list, Comparator<? super T> c)
public static <T> void copy(List<? super T> dest, List<? extends T> src)
public static <T> T max(Collection<? extends T> coll, Comparator<? super T> comp)
```

### **33.`<T extends E>`和`<? extends E>`有什么关系？**

解答：

它们用的地方不一样，`<T extends E>`用于定义类型参数，声明了一个类型参数 T，可放在泛型类定义中类名后面、泛型方法返回值前面。
`<? extends E>`用于实例化类型参数，用于实例化泛型变量中的类型参数，只是这个具体类型是未知的，只知道它是 E 或 E 的某个子类型。
虽然它们不一样，但两种写法经常可以达到相同的目的，譬如：
```java
public void addAll(Bean<? extends E> c)
public <T extends E> void addAll(Bean<T> c) 
```

### **34.解释下面程序执行情况和原因？**

```java
DynamicArray<Integer> ints = new DynamicArray<>();
DynamicArray<? extends Number> numbers = ints; 
Integer a = 200;
numbers.add(a);		//这三行add现象？
numbers.add((Number)a);
numbers.add((Object)a);

public void copyTo(DynamicArray<? super E> dest){
    for(int i=0; i<size; i++){
        dest.add(get(i));	//这行add现象？
    }
}
```
解析：

上面三种 add 方法都是非法的，无论是 Integer，还是 Number 或 Object，编译器都会报错。因为`?`表示类型安全无知，`? extends Number`表示是Number的某个子类型，但不知道具体子类型，
如果允许写入，Java 就无法确保类型安全性，所以直接禁止。

最后方法的 add 是合法的，因为`<? super E>`形式与`<? extends E>`正好相反，超类型通配符表示 E 的某个父类型，有了它我们就可以更灵活的写入了。

### **35.Arraylist 的动态扩容机制是如何自动增加的？简单说说你理解的增加流程！**

解析：

当在 ArrayList 中增加一个对象时 Java 会去检查 Arraylist 以确保已存在的数组中有足够的容量来存储这个新对象，如果没有足够容量就新建一个长度更长的数组（原来的1.5倍），旧的数组就会使用 Arrays.copyOf 方法被复制到新的数组中去，现有的数组引用指向了新的数组。下面代码展示为 `Java 1.8` 中通过 `ArrayList.add` 方法添加元素时，内部会自动扩容，扩容流程如下：
```java
//确保容量够用，内部会尝试扩容，如果需要
ensureCapacityInternal(size + 1)

//在未指定容量的情况下，容量为DEFAULT_CAPACITY = 10
//并且在第一次使用时创建容器数组，在存储过一次数据后，数组的真实容量至少DEFAULT_CAPACITY
 private void ensureCapacityInternal(int minCapacity) {
    //判断当前的元素容器是否是初始的空数组
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        //如果是默认的空数组，则 minCapacity 至少为DEFAULT_CAPACITY
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }

    ensureExplicitCapacity(minCapacity);
}

//通过该方法进行真实准确扩容尝试的操作
 private void ensureExplicitCapacity(int minCapacity) {
        modCount++;//记录List的结构修改的次数

        //需要扩容
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
}

//扩容操作
 private void grow(int minCapacity) {
    //原来的容量
    int oldCapacity = elementData.length;
    
    //新的容量 = 原来的容量 + （原来的容量的一半）
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    
    //如果计算的新的容量比指定的扩容容量小，那么就使用指定的容量
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    
    //如果新的容量大于MAX_ARRAY_SIZE(Integer.MAX_VALUE - 8)
    //那么就使用hugeCapacity进行容量分配
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE : MAX_ARRAY_SIZE;
    
    //创建长度为newCapacity的数组，并复制原来的元素到新的容器，完成ArrayList的内部扩容
    elementData = Arrays.copyOf(elementData, newCapacity);
 }
```

### **36.下面这些方法可以正常运行吗？为什么？**
```java
public void remove1(ArrayList<Integer> list) {
    for(Integer a : list){
        if(a <= 10){
            list.remove(a);
        }
    }
}

public static void remove2(ArrayList<Integer> list) {
    Iterator<Integer> it = list.iterator();
    while(it.hasNext()){
        if(it.next() <= 10) {
            it.remove();
        }
    }
}

public static void remove3(ArrayList<Integer> list) {
    Iterator<Integer> it = list.iterator();
    while(it.hasNext()) {
        it.remove();    
    }
}

public static void remove4(ArrayList<Integer> list) {
    Iterator<Integer> it = list.iterator();
    while(it.hasNext()) {
        it.next();
        it.remove();
        it.remove();
    }
}
```

解析：

remove1 方法会抛出 ConcurrentModificationException 异常，这是迭代器的一个陷阱，foreach 遍历编译后实质会替换为迭代器实现（普通for循环不会抛这个异常，因为list.size方法一般不会变，所以只会漏删除），因为迭代器内部会维护一些索引位置数据，要求在迭代过程中容器不能发生结构性变化（添加、插入、删除，修改数据不算），否则这些索引位置数据就失效了，避免的方式就是使用迭代器的 remove 方法。

remove2 方法可以正常运行，无任何错误。

remove3 方法会抛出 IllegalStateException 异常，因为使用迭代器的 remove 方法前必须先调用 next 方法，next 方法会检测容器是否发生了结构性变化，然后更新 cursor 和 lastRet 值，直接不调用 next 而 remove 会导致相关值不正确。

remove4 方法会抛出 IllegalStateException 异常，理由同 remove3，remove 调用一次后 lastRet 值会重置为 -1，没有调用 next 去设置 lastRet 的情况下再直接调一次 remove 自然就状态异常了。

当然了，上面四个写法的具体官方解答可参见 ArrayList 中迭代器部分源码，如下：
```java
private class Itr implements Iterator<E> {
	int cursor;       // index of next element to return
	int lastRet = -1; // index of last element returned; -1 if no such
	int expectedModCount = modCount;

	public boolean hasNext() {
		return cursor != size;
	}

	@SuppressWarnings("unchecked")
	public E next() {
		checkForComodification();
		int i = cursor;
		if (i >= size)
			throw new NoSuchElementException();
		Object[] elementData = ArrayList.this.elementData;
		if (i >= elementData.length)
			throw new ConcurrentModificationException();
		cursor = i + 1;
		return (E) elementData[lastRet = i];
	}

	public void remove() {
		if (lastRet < 0)
			throw new IllegalStateException();
		checkForComodification();

		try {
			ArrayList.this.remove(lastRet);
			cursor = lastRet;
			lastRet = -1;
			expectedModCount = modCount;
		} catch (IndexOutOfBoundsException ex) {
			throw new ConcurrentModificationException();
		}
	}

	final void checkForComodification() {
		if (modCount != expectedModCount)
			throw new ConcurrentModificationException();
	}
}
```

### **37.简要解释下面程序的执行现象和结果？**
```java
ArrayList<Integer> list = new ArrayList<Integer>();
list.add(1);
list.add(2);
list.add(3);
Integer[] array1 = new Integer[3];
list.toArray(array1);
Integer[] array2 = list.toArray(new Integer[0]);
System.out.println(Arrays.equals(array1, array2));	//1 结果是什么？为什么？

Integer[] array = {1, 2, 3};
List<Integer> list = Arrays.asList(array);
list.add(4);	//2 结果是什么？为什么？

Integer[] array = {1, 2, 3};
List<Integer> list = new ArrayList<Integer>(Arrays.asList(array));
list.add(4);	//3 结果是什么？为什么？
```

解析：

1 输出为 true，因为 ArrayList 有两个方法可以返回数组`Object[] toArray()`和`<T> T[] toArray(T[] a)`，第一个方法返回的数组是通过 Arrays.copyOf 实现的，第二个方法如果参数数组长度足以容纳所有元素就使用参数数组，否则新建一个数组返回，所以结果为 true。

2 会抛出 UnsupportedOperationException 异常，因为 Arrays 的 asList 方法返回的是一个 Arrays 内部类的 ArrayList 对象，这个对象没有实现 add、remove 等方法，只实现了 set 等方法，所以通过 Arrays.asList 转换的列表不具备结构可变性。

3 当然可以正常运行咯，不可变结构的 Arrays 的 ArrayList 通过构造放入了真正的万能 ArrayList，自然就可以操作咯。

### **38.简单解释一下 Collection 和 Collections 的区别？**

解析：

java.util.Collection 是一个集合接口，它提供了对集合对象进行基本操作的通用接口方法，在 Java 类库中有很多具体的实现，意义是为各种具体的集合提供最大化的统一操作方式。
譬如 Collection 的实现类有 List、Set 等，List 的实现类有 LinkedList、ArrayList、Vector 等，Vector 的实现类有 Stack 等，不过切记 Map 是自立门户的，其提供了转换为 Collection 的方法，但是自己不是 Collection 的子类。
 
java.util.Collections 是一个包装类，它包含有各种有关集合操作的静态多态方法，此类构造 private 不能实例化，就像一个工具类，服务于 Java 的 Collection 框架，其提供的方法大概可以分为对容器接口对象进行操作类（查找和替换、排序和调整顺序、添加和修改）和返回一个容器接口对象类（适配器将其他类型的数据转换为容器接口对象、装饰器修饰一个给定容器接口对象增加某种性质）。

### **39.解释一下 ArrayList、Vector、Stack、LinkedList 的实现和区别及特点和适用场景？**

解析：

首先他们都是 List 家族的儿子，List 又是 Collection 的子接口，Collection 又是 Iterable 的子接口，所以他们都具备 Iterable 和 Collection 和 List 的基本特性。

ArrayList 是一个动态数组队列，随机访问效率高，随机插入、删除效率低。LinkedList 是一个双向链表，它也可以被当作堆栈、队列或双端队列进行操作，随机访问效率低，但随机插入、随机删除效率略好。Vector 是矢量队列，和 ArrayList 一样是一个动态数组，但是 Vector 是线程安全的。Stack 继承于 Vector，特性是先进后出(FILO, FirstIn Last Out)。

从线程安全角度看 Vector、Stack 是线程安全的，ArrayList、LinkedList 是非线程安全的。

从实现角度看 LinkedList 是双向链表结构，ArrayList、Vector、Stack 是内存数组结构。

从动态扩容角度看由于 ArrayList 和 Vector（Stack 继承自 Vector，只在 Vector 的基础上添加了几个 Stack 相关的方法，故之后不再对 Stack 做特别的说明）使用数组实现，当数组长度不够时，其内部会创建一个更大的数组，然后将原数组中的数据拷贝至新数组中，而 LinkedList 是双向链表结构，内存不用连续，所以用多少申请多少。

从效率方面来说 Vector、ArrayList、Stack 是基于数组实现的，是根据索引来访问元素，Vector（Stack）和 ArrayList 最大的区别就是 synchronization 同步的使用，抛开两个只在序列化过程中使用的方法不说，没有一个 ArrayList 的方法是同步的，相反，绝大多数 Vector（Stack）的方法法都是直接或者间接的同步的，因此就造成 ArrayList 比 Vector（Stack）更快些，不过在最新的 JVM 中，这两个类的速度差别是很小的，几乎可以忽略不计；而 LinkedList 是双向链表实现，根据索引访问元素时需要遍历寻找，性能略差。所以 ArrayList 适合大量随机访问，LinkList 适合频繁删除插入操作。

从差异角度看 LinkedList 还具备 Deque 双端队列的特性，其实现了 Deque 接口，Deque 继承自 Queue 队列接口，其实也挺好理解，因为 LinkedList 是的实现是双向链表结构，所以实现队列特性实在是太容易了。

### **40.简单介绍下 List 、Map、Set、Queue 的区别和关系？**

解析：

List、Set、Queue 都继承自 Collection 接口，而 Map 则不是（继承自 Object），所以容器类有两个根接口，分别是 Collection 和 Map，Collection 表示单个元素的集合，Map 表示键值对的集合。

List 的主要特点就是有序性和元素可空性，他维护了元素的特定顺序，其主要实现类有 ArrayList 和 LinkList。ArrayList 底层由数组实现，允许元素随机访问，但是向 ArrayList 列表中间插入删除元素需要移位复制速度略慢；LinkList 底层由双向链表实现，适合频繁向列表中插入删除元素，随机访问需要遍历所以速度略慢，适合当做堆栈、队列、双向队列使用。

Set 的主要特性就是唯一性和元素可空性，存入 Set 的每个元素都必须唯一，加入 Set 的元素都必须确保对象的唯一性，Set 不保证维护元素的有序性，其主要实现类有 HashSet、LinkHashSet、TreeSet。HashSet 是为快速查找元素而设计，存入 HashSet 的元素必须定义 hashCode 方法，其实质可以理解为是 HashMap 的包装类，_所以 HashSet 的值还具备可 null 性_；LinkHashSet 具备 HashSet 的查找速度且通过链表保证了元素的插入顺序（实质为 HashSet 的子类），迭代时是有序的，同理存入 LinkHashSet 的元素必须定义 hashCode 方法；TreeSet 实质是 TreeMap 的包装类，_所以 TreeSet 的值不备可 null 性_，其保证了元素的有序性，底层为红黑树结构，存入 TreeSet 的元素必须实现 Comparable 接口；不过特别注意 EnumSet 的实现和 EnumMap 没有一点关系。

Queue 的主要特性就是队列和元素不可空性，其主要的实现类有 LinkedList、PriorityQueue。LinkedList 保证了按照元素的插入顺序进行操作；PriorityQueue 按照优先级进行插入抽取操作，元素可以通过实现 Comparable 接口来保证优先顺序。Deque 是 Queue 的子接口，表示更为通用的双端队列，有明确的在头或尾进行查看、添加和删除的方法，ArrayDeque 基于循环数组实现，效率更高一些。

Map 自立门户，但是也提供了嫁接到 Collection 相关方法，其主要特性就是维护键值对关联和查找特性，其主要实现类有 HashTab、HashMap、LinkedHashMap、TreeMap。HashTab 类似 HashMap，_但是不允许键为 null 和值为 null_，比 HashMap 慢，因为为同步操作；HashMap 是基于散列列表的实现，_其键和值都可以为 null_；LinkedHashMap 类似 HashMap，_其键和值都可以为 null_，其有序性为插入顺序或者最近最少使用的次序（LRU 算法的核心就是这个），之所以能有序，是因为每个元素还加入到了一个双向链表中；TreeMap 是基于红黑树算法实现的，查看键值对时会被排序，存入的元素必须实现 Comparable 接口，_但是不允许键为 null，值可以为 null_；如果键为枚举类型可以使用专门的实现类 EnumMap，它使用效率更高的数组实现。

从数据结构角度看集合的区别有如下：

动态数组：ArrayList 内部是动态数组，HashMap 内部的链表数组也是动态扩展的，ArrayDeque 和 PriorityQueue 内部也都是动态扩展的数组。

链表：LinkedList 是用双向链表实现的，HashMap 中映射到同一个链表数组的键值对是通过单向链表链接起来的，LinkedHashMap 中每个元素还加入到了一个双向链表中以维护插入或访问顺序。

哈希表：HashMap 是用哈希表实现的，HashSet, LinkedHashSet 和 LinkedHashMap 基于 HashMap，内部当然也是哈希表。

排序二叉树：TreeMap 是用红黑树(基于排序二叉树)实现的，TreeSet 内部使用 TreeMap，当然也是红黑树，红黑树能保持元素的顺序且综合性能很高。

堆：PriorityQueue 是用堆实现的，堆逻辑上是树，物理上是动态数组，堆可以高效地解决一些其他数据结构难以解决的问题。

循环数组：ArrayDeque 是用循环数组实现的，通过对头尾变量的维护，实现了高效的队列操作。

位向量：EnumSet 是用位向量实现的，对于只有两种状态且需要进行集合运算的数据使用位向量进行表示、位运算进行处理，精简且高效。

### **41.简单说说 HashMap 的底层原理？**

答案：

当我们往 HashMap 中 put 元素时，先根据 key 的 hash 值得到这个元素在数组中的位置（即下标），然后把这个元素放到对应的位置中，如果这个元素所在的位子上已经存放有其他元素就在同一个位子上的元素以链表的形式存放，新加入的放在链头，从 HashMap 中 get 元素时先计算 key 的 hashcode，找到数组中对应位置的某一元素，然后通过 key 的 equals 方法在对应位置的链表中找到需要的元素，所以 HashMap 的数据结构是数组和链表的结合。

解析：

HashMap 底层是基于哈希表的 Map 接口的非同步实现，实际是一个链表散列数据结构（即数组和链表的结合体）。
首先由于数组存储区间是连续的，占用内存严重，故空间复杂度大，但二分查找时间复杂度小（O(1)），所以寻址容易，插入和删除困难。而链表存储区间离散，占用内存比较宽松，故空间复杂度小，但时间复杂度大（O(N)），所以寻址困难，插入和删除容易。
所以就产生了一种新的数据结构------哈希表，哈希表既满足了数据的查找方便，同时不占用太多的内容空间，使用也十分方便，哈希表有多种不同的实现方法，HashMap 采用的是链表的数组实现方式，具体如下：

首先 HashMap 里面实现了一个静态内部类 Entry(key、value、next)，HashMap 的基础就是一个 Entry[] 线性数组，Map 的内容都保存在 Entry[] 里面，而 HashMap 用的线性数组却是随机存储的原因如下：
```java
// 存储时
int hash = key.hashCode(); //每个 key 的 hash 是一个固定的 int 值 
int index = hash % Entry[].length; 
Entry[index] = value;

// 取值时
int hash = key.hashCode(); 
int index = hash % Entry[].length; 
return Entry[index];
```
当我们通过 put 向 HashMap 添加多个元素时会遇到两个 key 通过`hash % Entry[].length`计算得到相同 index 的情况，这时具有相同 index 的元素就会被放在线性数组 index 位置，然后其 next 属性指向上个同 index 的 Entry 元素形成链表结构（譬如第一个键值对 A 进来，通过计算其 key 的 hash 得到的 index = 0，记做 Entry[0] = A，接着第二个键值对 B 进来，通过计算其 index 也等于 0，这时候 B.next = A, Entry[0] = B，如果又进来 C 且 index 也等于 0 则 C.next = B, Entry[0] = C）。
当我们通过 get 从 HashMap 获取元素时首先会定位到数组元素，接着再遍历该元素处的链表获取真实元素。
当 key 为 null 时 HashMap 特殊处理总是放在 Entry[] 数组的第一个元素。
HashMap 使用 Key 对象的 hashCode() 和 equals() 方法去决定 key-value 对的索引，当我们试着从 HashMap 中获取值的时候，这些方法也会被用到，所以 equals() 和 hashCode() 的实现应该遵循以下规则：
如果`o1.equals(o2)`则`o1.hashCode() == o2.hashCode()`必须为 true，或者如果`o1.hashCode() == o2.hashCode()`则不意味着o1.equals(o2)会为true。

关于 HashMap 的 hash 函数算法巧妙之处可以参见本文链接：http://pengranxiang.iteye.com/blog/543893

### **42.简单解释一下 Comparable 和 Comparator 的区别和场景？**

解析：

Comparable 对实现它的每个类的对象进行整体排序，这个接口需要类本身去实现，若一个类实现了 Comparable 接口，实现 Comparable 接口的类的对象的 List 列表(或数组)可以通过 Collections.sort（或 Arrays.sort）进行排序，此外实现 Comparable 接口的类的对象可以用作有序映射(如TreeMap)中的键或有序集合(如TreeSet)中的元素，而不需要指定比较器，
实现 Comparable 接口必须修改自身的类（即在自身类中实现接口中相应的方法），如果我们使用的类无法修改（如SDK中一个没有实现Comparable的类），我们又想排序，就得用到 Comparator 这个接口了（策略模式）。
所以如果你正在编写一个值类，它具有非常明显的内在排序关系，比如按字母顺序、按数值顺序或者按年代顺序，那你就应该坚决考虑实现 Comparable 这个接口，
若一个类实现了 Comparable 接口就意味着该类支持排序，而 Comparator 是比较器，我们若需要控制某个类的次序，可以建立一个该类的比较器来进行排序。
Comparable 比较固定，和一个具体类相绑定，而 Comparator 比较灵活，可以被用于各个需要比较功能的类使用。

### **43.简单说说 Iterator 和 ListIterator 的区别？**

解析：

ListIterator 有 add() 方法，可以向 List 中添加对象，而 Iterator 不能。

ListIterator 和 Iterator 都有 hasNext() 和 next() 方法，可以实现顺序向后遍历，但是 ListIterator 有 hasPrevious() 和 previous() 方法，可以实现逆向（顺序向前）遍历，Iterator 就不可以。

ListIterator 可以定位当前的索引位置，通过 nextIndex() 和 previousIndex() 可以实现，Iterator 没有此功能。

都可实现删除对象，但是 ListIterator 可以实现对象的修改，通过 set() 方法可以实现，Iierator 仅能遍历，不能修改。

容器类提供的迭代器都会在迭代中间进行结构性变化检测，如果容器发生了结构性变化，就会抛出 ConcurrentModificationException，所以不能在迭代中间直接调用容器类提供的 add、remove 方法，如需添加和删除，应调用迭代器的相关方法。

### **44.请实现一个极简 LRU 算法容器？**

解析：

看起来是一道很难的题目，其实静下来你会发现想考察的其实就是 LRU 的原理和 LinkedHashMap 容器知识，当然，你要是厉害不依赖 LinkedHashMap 自己纯手写撸一个也不介意。
LinkedHashMap 支持插入顺序或者访问顺序，LRU 算法其实就要用到它访问顺序的特性，即对一个键执行 get、put 操作后其对应的键值对会移到链表末尾，所以最末尾的是最近访问的，最开始的最久没被访问的。
LRU 是一种流行的替换算法，它的全称是 Least Recently Used，最近最少使用，它的思路是最近刚被使用的很快再次被用的可能性最高，而最久没被访问的很快再次被用的可能性最低，所以被优先清理。
下面给出极简 LRU 缓存算法容器：
```java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private int maxEntries;
    
	//maxEntries 最大缓存个数
    public LRUCache(int maxEntries){
        super(16, 0.75f, true);
        this.maxEntries = maxEntries;
    }
    
	//在添加元素到 LinkedHashMap 后会调用这个方法，传递的参数是最久没被访问的键值对，如果这个方法返回 true 则这个最久的键值对就会被删除，LinkedHashMap 的实现总是返回 false，所有容量没有限制。
    @Override
    protected boolean removeEldestEntry(Entry<K, V> eldest) {
        return size() > maxEntries;
    }
}   
```

### **45.简单说说你对 Java 的 transient 关键字理解？**

解析：

我们都知道一个对象只要实现了 Serilizable 接口就可以被序列化了，java 的这种序列化模式使得我们可以不必关心具体序列化的过程，只要这个类实现了 Serilizable 接口则类的所有属性和方法都会自动序列化，而实际开发中我们常常会遇到这样的问题，这个类的有些属性需要序列化，而其他属性不需要被序列化，对于不要被序列化的属性就可以加上 transient 关键字。其主要的特性如下：

一旦变量被 transient 修饰就不再是对象持久化的一部分，该变量内容在序列化后无法获得访问；transient 关键字只能修饰变量而不能修饰方法和类（注意本地变量是不能被 transient 关键字修饰的）；变量如果是用户自定义类变量，则该类需要实现 Serializable 接口；被 transient 关键字修饰的变量不再能被序列化，一个静态变量不管是否被 transient 修饰均不能被序列化（一个静态变量不管是否被 transient 修饰均不能被序列化，反序列化后类中 static 型变量的值为当前 JVM 中对应 static 变量的值，这个值是 JVM 中的不是反序列化得出的）；在 Java 中对象的序列化可以通过实现两种接口来实现，若实现的是 Serializable 接口则所有的序列化将会自动进行，若实现的是 Externalizable 接口则没有任何东西可以自动序列化，需要在 writeExternal 方法中进行手工指定所要序列化的变量，这与是否被 transient 修饰无关。

### **46.简单谈谈你所知道的 Java IO 流特性？**

解析：

java.io 包下面的流基本都是装饰器模式的实现，提供了各种类型流操作的便携性，常见的流分类如下：
```
以二进制字节方式读写的流（字节流没有编码的概念，转换字节需要考虑编码，不能按行处理，使用不太方便）主要有：

InputStream、OutputStream: 二进制字节读写抽象类型的基类。

FileInputStream、FileOutputStream: 输入输出目标是文件的流，构造支持 File 类型和 String 文件名类型及追加覆盖模式，以 byte 或 byte 数组读写文件，FileOutputStream 没有缓冲，没有重写flush，调用 flush 没有任何效果，数据只是传递给了操作系统，但操作系统什么时候保存到硬盘上是不一定的，按字节读取效率低。

ByteArrayInputStream、ByteArrayOutputStream: 输入输出目标是字节数组的流，数组的长度是根据数据内容动态扩展的，ByteArrayOutputStream 无缓存，同理 flush 无效。

DataInputStream、DataOutputStream: 按基本类型和字符串而非只是字节读写流装饰类，FilterInputStream、FilterOutputStream 装饰基类的子类，在写入时 DataOutputStream 会将这些类型的数据转换为其对应的二进制字节，必须按照字节读取，效率较低。

BufferedInputStream、BufferedOutputStream: 对输入输出流提供缓冲功能的装饰类，BufferedInputStream 内部有个字节数组作为缓冲区，读取时先从这个缓冲区读，缓冲区读完了再调用包装的流读，BufferedOutputStream 的构造方法也有两个，默认的缓冲区大小也是 8192，它的 flush 方法会将缓冲区的内容写到包装的流中。

PipedInputStream、PipedOutputStream：分别是管道输入输出流，作用是让多线程可以通过管道进行线程间的通讯，在使用管道通信时，必须将 PipedOutputStream 和 PipedInputStream 配套使用。

PrintStream：继承于 FilterOutputStream 来装饰其它输出流的流。提供了自动 flush 和字符集设置功能。

以文本字符方式读写的流（文本文件中编码非常重要）主要有：

Reader、Writer：字符流的抽象基类。

InputStreamReader、OutputStreamWriter：适配器类，输入是 InputStream，输出是 OutputStream，将字节流转换为字符流，一个重要的参数是编码类型，如果没有传则为系统默认编码。

FileReader、FileWriter：输入输出目标是文件的字符流，InputStreamReader、OutputStreamWriter 的子类，需要注意的是 FileReader、FileWriter 不能指定编码类型，只能使用默认编码，如果需要指定编码类型可以使用 InputStreamReader、OutputStreamWriter。

CharArrayReader、CharArrayWriter: 输入输出目标是 char 动态调整数组的字符流，类似 ByteArrayInputStream、ByteArrayOutputStream。

StringReader、StringWriter：输入输出目标是 String 的字符流，与 CharArrayReader、CharArrayWriter 类似。

BufferedReader、BufferedWriter：装饰类，对输入输出流提供缓冲以及按行读写功能，FileReader、FileWriter 是没有缓冲的、也不能按行读写，所以一般应该在它们的外面包上对应的缓冲类。

PrintWriter：装饰类，可直接指定文件名作为参数、可以指定编码类型、可以自动缓冲、可以自动将多种类型转换为字符串，在输出到文件时可以优先选择该类。

PipedReader、PipedWriter：分别是字符管道输入输出流，作用是让多线程可以通过管道进行线程间的通讯，在使用管道通信时，必须将 PipedReader、PipedWriter 配套使用。
```

### **47.请简单谈谈你对 Java 的序列化与反序列化理解？**

解析：

序列化就是将对象转化为字节流，反序列化就是将字节流转化为对象，要让一个类支持序列化只要让这个类实现接口 java.io.Serializable 即可，Serializable 只是一个没有定义任何方法的标记接口，声明实现 Serializable 接口后保存读取对象就可以使用 ObjectOutputStream、ObjectInputStream 流了，ObjectOutputStream 是 OutputStream 的子类，但实现了 ObjectOutput 接口，ObjectOutput 是 DataOutput 的子接口，增加了一个 writeObject(Object obj) 方法将对象转化为字节写到流中，ObjectInputStream 是 InputStream 的子类，实现了ObjectInput 接口，ObjectInput 是 DataInput 的子接口，增加了一个 readObject() 方法从流中读取字节转为对象，序列化和反序列化的实质在于 ObjectOutputStream 的 writeObject 和 ObjectInputStream 的 readObject 方法实现，常见的 String、Date、Double、ArrayList、LinkedList、HashMap、TreeMap 等都默认实现了 Serializable。

有时候我们对象有些字段的值可能与内存位置（hashcode）、当前时间等有关，所以我们不想序列化他，因为反序列化后的值是没有意义的，或者有时候如果类中的字段表示的是类的实现细节而非逻辑信息则默认序列化也是不适合的，所以我们需要定制序列化。Java 提供的定制主要有 transient 关键字方式和实现 writeObject、readObject 方式及 Externalizable 接口 readResolve、writeReplace 方式，还可以将字段声明为 transient 后通过 writeObject、readObject 方法来自己保存该字段。

默认情况下 Java 会根据类中一系列信息自动生成一个版本号，在反序列化时如果类的定义发生了变化版本号就会变化，也就与反序列化流中的版本号不匹配导致会抛出异常，所以我们为了更好的控制和性能问题会自定义 serialVersionUID 版本号来避免类定义发生变化后反序列化版本号不匹配异常问题，如果版本号一样时流中有该字段而类定义中没有则该字段会被忽略，如果类定义中有而流中没有则该字段会被设为默认值，如果对于同名的字段类型变了则会抛出 InvalidClassException。

### **48.Java 线程优先级是怎么定义的，Java 线程有几种状态？**

解析：

Java 线程的优先级定义为从 1 到 10 的等级，默认为 5，设置和获取线程优先级的方法是 setPriority(int newPriority) 和 getPriority()，Java 的这个优先级会被映射到操作系统中线程的优先级，不过由于操作系统各不相同，不一定都是 10 个优先级，所以 Java中 不同的优先级可能会被映射到操作系统中相同的优先级，同时优先级对操作系统而言更多的是一种建议和提示而非强制，所以我们不要过于依赖优先级。

Java Thread 可以通过 State getState() 来获取线程状态，Thread.State 枚举定义了 NEW、RUNNABLE、BLOCKED、WAITING、TIMED_WAITING、TERMINATED 六种线程状态，其实真正严格意义来说线程只有就绪、阻塞、运行三种状态，Java 线程之所以有六种状态其实是站在 Thread 对象实例的角度来看待的，具体解释如下：

NEW（新建），表示线程 Thread 刚被创建，还没调用 start 方法。

RUNNABLE（运行，实质对应就绪和运行状态），表示 Thread 线程正在 JVM 中运行，也就是说处于就绪和运行状态的线程在 Thread 中都表现为 RUNNABLE。

BLOCKED（阻塞，实质对应阻塞状态），表示等待监视锁可以重新进行同步代码块中执行，此线程需要获得某个锁才能继续执行，而这个锁目前被其他线程持有，所以进入了被动的等待状态，直到抢到了那个锁才会再次进入就绪状态。处于受阻塞状态的某一线程正在等待监视器锁，以便进入一个同步的块或方法，或者在调用 wait 之后再次进入同步的块或方法。

WAITING（等待，实质对应阻塞状态），表示此线程正处于无限期的主动等待中，直到有人唤醒它它才会再次进入就绪状态。某一线程因为调用下不带超时值的 wait、不带超时值的 join、LockSupport.park 会进入等待状态，处于等待状态的线程正等待另一个线程以执行特定操作，例如已经在某一对象上调用了 Object.wait() 的线程正等待另一个线程以便在该对象上调用 Object.notify() 或 Object.notifyAll()，或者已经调用了 Thread.join() 的线程正在等待指定线程终止。

TIMED_WAITING（有限等待，实质对应阻塞状态），表示此线程正处于有限期的主动等待中，要么有人唤醒它，要么等待够了一定时间之后才会再次进入就绪状态，譬如调用带有超时的 sleep、join、wait 方法可能导致线程处于等待状态。

TERMINATED（终止），表示线程执行完毕，已经退出。

### **49.简单说说你对 Java synchronized 的理解？**

解析：

synchronized 关键字主要用来解决多线程共享内存的并发同步问题，可以用来修饰类的实例方法、静态方法、代码块；synchronized 实例方法实际保护的是同一个对象的方法调用，当为不同对象时多线程是可以同时访问同一个 synchronized 方法的；synchronized 静态方法和 synchronized 实例方法保护的是不同对象，不同的两个线程可以同时一个执行 synchronized 静态方法，另一个执行 synchronized 实例方法，因为 synchronized 静态方法保护的是 class 类对象，synchronized 实例方法保护的是 this 实例对象；synchronized 代码块同步的可以是任何对象，因为任何对象都有一个锁和等待队列。

synchronized 具备可重入性，对同一个线程在获得锁之后在调用其他需要同样锁的代码时可以直接调用，其可重入性是通过记录锁的持有线程和持有数量来实现的，调用 synchronized 代码时检查对象是否已经被锁，是则检查是否被当前线程锁定，是则计数加一，不是则加入等待队列，释放时计数减一直到为零释放锁。

synchronized 还具备内存可见性，除了实现原子操作避免竞态以外对于明显是原子操作的方法（譬如一个 boolean 状态变量 state 的 get 和 set 方法）也可以通过 synchronized 来保证并发的可见性，在释放锁时所有写入都会写回内存，而获得锁后都会从内存读取最新数据；不过对于已经是原子性的操作为了保证内存可见性而使用 synchronized 的成本会比较高，轻量级的选择应该是使用 volatile 修饰，一旦修饰 java 就会在操作对应变量时插入特殊指令保证可见性。

synchronized 是重量级锁，其语义底层是通过一个 monitor 监视器对象来完成，其实 wait、notify 等方法也依赖于 monitor 对象，所以这就是为什么只有在同步的块或者方法中才能调用 wait、notify 等方法，否则会抛出 IllegalMonitorStateException 异常的原因，监视器锁（monitor）的本质依赖于底层操作系统的互斥锁（Mutex Lock）实现，而操作系统实现线程之间的切换需要从用户态转换到核心态，这个成本非常高，状态之间的转换需要相对比较长的时间，所以这就是为什么 synchronized 效率低且重量级的原因（Java 1.6 进行了优化，但是相比其他锁机制还是略显偏重）。

synchronized 在发生异常时会自动释放线程占用的锁资源，Lock 需要在异常时主动释放，synchronized 在锁等待状态下无法响应中断而 Lock 可以。

还可以参考 JVM 官方文档：http://docs.oracle.com/javase/specs/jvms/se8/html/jvms-3.html#jvms-3.14

### **50.什么是死锁？请模拟写出一段 Java 死锁的核心代码？如何避免死锁？**

解析：

关于什么是死锁其实就是陷入互相等待谁都无法执行的一种状态，譬如有 A B 两个线程，A 持有 LockA 锁且在等待 LockB 锁，而 B 持有锁 LockB 且在等待 LockA 锁，LockA LockB 陷入互相等待导致谁都无法执行的一种现象。当发生死锁可以采用 jstack 命令进行分析，Android 上面可以采用 Android Device Monitor Thread 进行分析。

关于死锁的核心代码例子如下：
```java
public class ThreadLoop {
    private static Object mLockA = new Object();
    private static Object mLockB = new Object();

    private static void startThreadA() {
        new Thread() {
            @Override
            public void run() {
                Log.i("YYYY", "startThreadA running!");
                synchronized (mLockA) {
                    Log.i("YYYY", "startThreadA mLockA enter!");
                    ThreadLoop.sleep(200);
                    synchronized (mLockB) {
                        Log.i("YYYY", "startThreadA mLockB enter!");
                    }
                }
                Log.i("YYYY", "startThreadA over!");
            }
        }.start();
    }

    private static void startThreadB() {
        new Thread() {
            @Override
            public void run() {
                Log.i("YYYY", "startThreadB running!");
                synchronized (mLockB) {
                    Log.i("YYYY", "startThreadB mLockB enter!");
                    ThreadLoop.sleep(200);
                    synchronized (mLockA) {
                        Log.i("YYYY", "startThreadB mLockA enter!");
                    }
                }
                Log.i("YYYY", "startThreadB over!");
            }
        }.start();
    }

    private static void sleep(long time) {
        try {
            Thread.sleep(time);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void start() {
        startThreadA();
        startThreadB();
    }
}
```
运行结果可能如下：
```
startThreadA running!
startThreadA mLockA enter!
startThreadB running!
startThreadB mLockB enter!
//陷入死锁...
```

避免死锁其实主要有如下几个经验：

考虑加锁顺序：当多个线程需要相同的一些锁但每个线程又按照不同顺序加锁则很容易发生死锁（如上面死锁的例子），如果能确保所有的线程都是按照相同的顺序获得锁则发生死锁的情况就不存在了。

考虑加锁时限：可以在尝试获取锁的时候加一个超时时间，若一个线程没有在给定的时限内成功获得所有需要的锁则会进行回退并释放所有已经获得的锁，然后等待一段随机的时间再重试，这段随机的等待时间让其它线程有机会尝试获取相同的这些锁，并且让该应用在没有获得锁的时候可以继续运行。

### **51.下面程序有什么问题？该如何修改？**
```java
public class SyncCollection {
    private static void putThread(final List<String> list) {
        new Thread() {
            @Override
            public void run() {
                for (int index=0; index<1000; index++) {
                    list.add("data-"+index);
                }
            }
        }.start();
    }

    private static void loopThread(final List<String> list) {
        new Thread() {
            @Override
            public void run() {
                while (true) {
                    for (String item : list) {
                        Log.i("YYYY", "item is "+item);
                    }
                }
            }
        }.start();
    }

    public static void run() {
        final List<String> list = Collections.synchronizedList(new ArrayList<String>());
        putThread(list);
        loopThread(list);
    }
}
```

解析：

该程序运行会在 loopThread 方法的 for 循环迭代器里面抛出 ConcurrentModificationException 异常，因为在遍历容器时容器结构发生了变化（add 操作），所以会抛出该异常，如果要避免这个问题需要在遍历容器时给整个容器对象加锁，如下：
```java
......
while (true) {
        synchronized (list) {
            for (String item : list) {
                Log.i("YYYY", "item is "+item);
            }
        }
    }
}
......
```
这样就保证了 for 循环迭代的同步性，之所以只给这个方法添加 synchronized (list) 是因为 list 对象其实就是 Collections.synchronizedList 返回的同步 List 里面 add 等操作用的锁对象，所以不用给上面的 putThread 方法再添加。

### **52.简单谈谈 Java 并发协作的 wait、notify、notifyAll 等方法的特点和场景？**

解析：

首先并发协作的 wait、notify、notifyAll 等方法是定义在 java 的 Object 类中，而非 Thread，wait 有一个重载方法，参数 0 表示无限等待，更加重要的是在等待期间均可被中断抛出 InterruptedException（很重要），每个对象都有一把锁和等待队列，线程在进入 synchronized 时如果尝试获取锁失败就会把当前线程加入锁的等待队列，其实每个对象除过有用于锁的等待队列外还有一个条件队列，条件队列就是用来进行线程间的协作的，调用 wait 方法就会把当前线程放入这个条件队列并阻塞，然后等待其他线程通过 notify 或者 notifyAll 触发这个条件（自己无法触发）来执行，惟一的区别就是 notify 会从条件队列选择一个线程触发条件并且从队列移除而 notifyAll 会触发条件队列里所有等待的线程并从队列移除。

wait 和 notify、notifyAll 只能在 synchronized 函数或者对象中调用，被上锁的对象一般是多线程共享的对象，如果调用 wait 和 notify、notifyAll 方法时当前线程没有持有对象锁则会抛出 IllegalMonitorStateException 异常。切记代码执行到 synchronized 锁起来的 wait 方法时当前线程会释放对象锁，因为 wait 的具体实现过程是先把当前线程放入条件等待队列、释放对象锁、阻塞等待（线程状态变为 WAITING 或 TIMED_WATING），等待时间到了或者被其他线程 notify、notifyAll 以后从条件队列中移除然后要重新竞争对象锁，竞争到就变为 RUNNABLE 状态，否则该线程被加入对象锁队列变为 BLOCKED 状态。切记调用 notify、notifyAll 会把条件队列中等待的线程移除但是不会释放对象锁，只有在包含 notify、notifyAll 的 synchronized 方法或者代码块执行完毕才能轮到等待的线程执行。

除过我们要保证 wait 和 notify、notifyAll 应该在被 synchronized 的背景下和那个被多线程共享的对象上调用以外还要尽可能保证永远在条件循环而不是 if 语句中使用 wait，因为线程从 wait 调用中返回后不代表其等待的条件就一定成立，所以我们在使用 wait 时应该尽量使用如下模板：
```java
// The standard idiom for calling the wait method in Java 
synchronized (sharedObject) { 
    while (condition) { 
    sharedObject.wait(); 
        // (Releases lock, and reacquires on wakeup) 
    } 
    // do action based upon condition e.g. take or put into queue 
}
```
在条件循环里使用 wait 的目的是在线程被唤醒的前后都持续检查条件是否被满足，如果条件并未改变而 wait 被调用之前 notify 的唤醒通知就来了，那么这个线程并不能保证被唤醒且有可能会导致死锁问题（建立在全局项目超过两个线程以上）。譬如假设有两个生产者 A、B，一个消费者 C，在生产消费者模式中如果对生产者 A、B 不使用条件循环而简单 if 判断中调用 wait 就会出事，当空间满了后 A、B 都被 wait，当 C 取走一个数据后如果调用了 notifyAll 则 A、B 都将被唤醒，假设 A 被唤醒后往空间放入一个数据且空间满了，而此时 B 也会放置一个数据，所以发生空间炸裂错误。
（提示：如上也解答了并发的另一个面试题 ---- Java 多线程为什么使用 while 循环来调用 wait 方法？）
（其实 Thread 的 join 方法实现也是条件循环，核心代码如下：`while (isAlive()) {lock.wait(0);}`）

并发协作其实在 java.util.concurrent 包下已经提供了很多不错且高效的封装实现类了，不过我们依然可以自己使用 wait 和 notify、notifyAll 来解决生产消费者场景、并发等待等场景问题。

### **53.说说你知道的 Java 停止线程的方式及优劣点？**

解析：

关于停止 Java 线程的常见方式及优劣点主要如下：

[不推荐]使用 Thread 的 stop() 方式结束线程已经不推荐了，因为它是一种恶意的中断，一旦执行 stop 方法就会立即终止当前正在运行的线程，所以它无法保证线程逻辑是否完整（譬如线程中重要的资源清理等逻辑代码还没来得及执行就被 stop 掉了，这是非常危险的）；同时会破坏原子性操作，因为一般任何进行加锁的代码块都是为了保护数据的一致性，而调用 stop 后导致该线程所持有的所有锁被突然释放，这样就导致被保护的数据有可能呈现不一致性，其他线程在使用这些被破坏的数据时有可能导致一些很奇怪的应用程序错误。

[推荐]使用 Thread 的 interrupt() 方式中断线程是要看场景决定的，Java 的中断并不是 stop 一样强迫终止一个线程，而是一种给对应线程传递取消信号的协作机制，由对应线程决定如何及何时退出。每个线程都有一个中断标志位（Native 实现），线程的对象方法 isInterrupted() 方法就是返回该标志是否为 true；线程的静态方法 interrupted() 方法也是返回该标志是否为 true，但是它会清空标志位；调用 interrupt() 方法对线程的效果取决于线程的当前状态和在进行的 IO 操作，所以这种方式中断线程需要看场合决定。如果线程处于 RUNNABLE 状态且无 IO 操作则 interrupt() 方法只会设置线程的中断标志位而无其他任何作用，所以这种情况下我们需要在线程运行的合适位置检查中断标记来中断线程，譬如 while 循环条件为 !Thread.isInterrupted()；如果线程处于 WAITING、TIMED_WAITING 状态则 interrupt() 方法会使该线程抛出 InterruptedException 异常且在异常抛出后中断标志会被清空而不是设置，这也就是为什么我们使用 wait、join、sleep 方法时必须要处理受检查的 InterruptedException 异常原因，所以我们一般需要在捕获这种异常后进行线程的终止回收逻辑同时最好再调用一下 Thread.currentThread().interrupt() 设置下当前线程中断标志位；如果线程处于 BLOCKED 锁等待状态则 interrupt() 方法只会设置线程的中断标志位而无其他任何作用，所以 interrupt 无法让一个处于等待锁的线程真正中断，譬如当线程 A、B 共用 LockM 锁，A 如果在获取 LockM 锁后调用 B 的 interrupt() 后接着执行其他耗时操作后才释放 LockM 锁，而在 A 获得 LockM 锁的期间 B 刚好处于锁等待状态，这时即便 B 的锁代码块中显式调用了 !Thread.isInterrupted() 方法也不会中断，只能等到 B 获取锁后才可以中断；如果线程处于 NEW、TERMINATE 状态则 interrupt() 方法没有任何效果，中断标志都不会被设置，这自然是一种没有意义的操作；如果线程处于 IO 操作阻塞情况则 interrupted() 方法的效果取决于当前阻塞的 IO 类，如果类实现了 InterruptibleChannel 接口（一般都是 NIO 的通道操作有实现此接口）则可中断且使 IO 通道关闭且会收到 ClosedByInterruptException 异常且会设置中断状态，如果阻塞的 IO 类是 NIO 的 Selector 则也会被中断且设置标志，如果 IO 类操作是常见的 ServerSocket 的 accept、InputStream 的 read 等调用则只会设置线程的中断标志而不会中断。所以说如果不明白自己的线程在做什么就不要想当然的调用 interrupt()，因为不一定会终止线程，更多的是一种标志协作。

[推荐]自定义中断信号量方式，这种方式中断 RUNNABLE 状态的线程也是很常见的，使用 volatile 的原子性共享标记变量来告诉线程必须停止正在运行的任务。

[推荐]使用 java.util.concurrent 并发包下面提供的方法（很多实质还是 interrupt()），譬如 Future 的 boolean cancel(boolean mayInterruptIfRunning) 方法、ExecutorService 的 shutdown()、shutdownNow() 方法等。

### **54.简单谈谈你对 Java 虚拟机内存模型 JMM 的认识和理解及并发中的原子性、可见性、有序性的理解？**

解析：

这是一个很泛很大且很有水准的面试题，也算是对并发基础原理实质的一个深度问题，想要在面试中简短的回答好不是特别容易，本解析也仅供参考，具体理解可自己查阅其他资料。

Java 内存模型主要目标是定义程序中变量（此处变量特指实例字段、静态字段等，但不包括局部变量和函数参数，因为这两种是线程私有无法共享）在虚拟机中存储到内存与从内存读取出来的规则细节，Java 内存模型规定所有变量都存储在主内存中，每条线程还有自己的工作内存，工作内存保存了该线程使用到的变量到主内存副本拷贝，线程对变量的所有操作（读取、赋值）都必须在工作内存中进行而不能直接读写主内存中的变量，不同线程之间无法相互直接访问对方工作内存中的变量，线程间变量值的传递均需要在主内存来完成。

Java 内存模型对主内存与工作内存之间的具体交互协议定义了八种操作，具体如下：
```
lock（锁定）：作用于主内存变量，把一个变量标识为一条线程独占状态。
unlock（解锁）：作用于主内存变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。
read（读取）：作用于主内存变量，把一个变量从主内存传输到线程的工作内存中，以便随后的 load 动作使用。
load（载入）：作用于工作内存变量，把 read 操作从主内存中得到的变量值放入工作内存的变量副本中。
use（使用）：作用于工作内存变量，把工作内存中的一个变量值传递给执行引擎，每当虚拟机遇到一个需要使用变量值的字节码指令时执行此操作。
assign（赋值）：作用于工作内存变量，把一个从执行引擎接收的值赋值给工作内存的变量，每当虚拟机遇到一个需要给变量进行赋值的字节码指令时执行此操作。
store（存储）：作用于工作内存变量，把工作内存中一个变量的值传递到主内存中，以便后续 write 操作。
write（写入）：作用于主内存变量，把 store 操作从工作内存中得到的值放入主内存变量中。
```
如果要把一个变量从主内存复制到工作内存就必须按顺序执行 read 和 load 操作，从工作内存同步回主内存就必须顺序执行 store 和 write 操作，但是 JVM 只要求了操作的顺序而没有要求上述操作必须保证连续性，所以实质执行中 read 和 load 间及 store 和 write 间是可以插入其他指令的。

Java 内存模型还会对指令进行重排序操作，在执行程序时为了提高性能编译器和处理器经常会对指令进行重排序操作，重排序主要分下面几类：
```
编译器优化重排序：编译器在不改变单线程程序语义的前提下可以重新安排语句的执行顺序。
指令级并行重排序：现代处理器采用了指令级并行技术来将多条指令重叠执行，如果不存在数据依赖性处理器可以改变语句对应机器指令的执行顺序。
内存系统重排序：由于处理器使用缓存和读写缓冲区使得加载和存储操作看上去可能是在乱序执行。
```

其实 Java JMM 内存模型是围绕并发编程中原子性、可见性、有序性三个特征来建立的

（关于该问题答案还可参考：www.ifeve.com/java-memory-model-1/）

### **.谈谈 Java 的 NIO 与内存映射，线程原子性、有序性、可见性，生产消费者模式 wait、notify 和 concurrent 方式的实现，**


并发问题
http://www.importnew.com/12773.html

http://www.jfox.info/40-ge-java-ji-he-lei-mian-shi-ti-he-da-an.html
http://www.importnew.com/22083.html
http://www.importnew.com/22087.html
