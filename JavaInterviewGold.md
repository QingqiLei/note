## Java  SE

### 基础

#### http 和socket

超文本传输协议, 是基于tcp /ip协议基础上的应用层协议, tcp/ip 是传输层协议, 主要解决数据如何 在网络中传输, http 是应用层协议, 主要解决如何包装数据, http规定了浏览器和服务器之间相互通信的规则, 是短连接,无状态, 一般使用session/cookie 来实现有状态,   

socket不是协议, 是一个接口 api, 是对tcp ip 的封装, 是长连接.

#### 1. java C++

1. java 为解释性语言,C++ 为编译型语言. 程序源代码经过Java 编译器编译为字节码,然后由jvm解释执行, C++ 源代码经过编译和链接后生成可执行的二进制代码.java 执行速度比C++慢,但是Java 能跨平台.
2. java 纯面向对象
3. java 没有指针,没有多重继承,不需要管理内存,不支持运算符重载,没有goto
4. 面向对象三个特点: 封装, 继承, 多态

#### 2. main

```
static public void main(String[] args)
public static final void main(String[] args)
static public synchronized void main(String[] args)
```

同一个.java 文件中可以有多个man 方法, 虽然每个类中都可以定义main 方法,但只有与文件名相同的用public 修饰的类中的main方法才被执行.

#### java 程序初始化顺序

三个原则: 静态对象,父类优先于子类,按照成员变量的定义顺序

父类静态变量, 父类静态代码块, 子类静态变量, 子类静态代码快, 父类非静态变量, 父类非静态代码块, 父类构造函数, 子类非静态变量, 子类非静态代码快, 子类构造函数.

#### java 作用域

变量有三种: 成员变量, 静态变量, 局部变量

成员变量当类被实例化的时候,成员变量就会在内存中分配空间并初始化. 静态变量不依赖于特定实例.

|           | 当前类 | 同一package | 子类 | 其他package |
| --------- | ------ | ----------- | ---- | ----------- |
| public    |        |             |      |             |
| protected |        |             |      | 不          |
| default   |        |             | 不   | 不          |
| private   |        | 不          | 不   | 不          |

#### 构造函数

1. 必须与类有相同的名字,不能有返回值(不能为void)
2. 也可以有方法和构造函数重名, 可以overload

#### 接口

1. 接口中的方法都是抽象的, 都使用 public static void 修饰.

#### char 型变量中能不能存储一个中文汉字,为什么?

可以, 因为Java 中使用的编码是unicode, , 一个char 类型占2个字节, 所以放一个中文没有问题.

#### ==和 equals 的区别?

一个是方法,一个是运算符

==: 如果是基本数据类型, 比较的是数值是否相等, 如果是引用数据类型, 比较的是对象的地址是否相等

equals: 不能用于基本数据类型, 如果没有重写, 比较的是地址, String 类型重写, 比较的是内容

#### String 判断题

```java
String s1 = "Programming"; 
String s2 = new String("Programming"); 
String s3 = "Program"; 
String s4 = "ming"; 
String s5 = "Program" + "ming"; 
String s6 = s3 + s4; 
System.out.println(s1 == s2);  //false, 
System.out.println(s1 == s5);  //true  !!!!!!!!!!!!!!!
System.out.println(s1 == s6);  //false  !!!!!!!!!!!!!!!!!
System.out.println(s1 == s6.intern());  //true
System.out.println(s2 == s2.intern());  //false  !!!!!!!!!!!!!!!!!!
```



#### 什么情况下用“+”运算符进行字符串连接比调用 StringBuffer/StringBuilder对象的 append 方法连接字符串性能更好?

`String s = "abc"; String ss = "ok"+s+"xhu"+5;`

这里虽然使用了+,但是编译时仍然将+ 转换成StringBuilder, 因此, 我们可以得出结论, 在java 中, 无论使用什么方法来字符串连接, 实际上都使用的是StringBuilder.

但是如果使用循环来连接字符串, 会有很大差别.

```java
String s = "";
Random rad = new Random();
for(int i = 0; i < 10; i ++){
    s = s + rand.nextInt(100)+" ";
}
```

这样会产生10个StringBuilder对象,优化直接使用StringBuilder.

另外, 尽量不要"+" 和StringBuilder 混着用, 这样会创建很多StringBuilder

#### 值传递和引用传递

基本数据类型: 值传递

对象: 引用传递

#### 反射

得到一个对象所属的类,获取一个类的所有成员变量和方法,在运行时创建对象,在运行时调用对象的方法,

Class a = Class.forName("Sub")

Base b = (Base)c.newInstance();

#### 创建对象的方法

new 反射 clone() 反序列化

#### package

在不同package中的类可以有相同的名字

#### 继承

子类只能继承父类非私有方法

#### 组合和继承

组合是在新类中创建原有类的对象. 组合和继承都允许在新的类中设置子对象, 组合是显式的,继承是隐式.

car 是vehicle 的一种, tire 是轮胎对象,一个car 有四个tire.

除非两个类之间是is - a 关系, 否则不要轻易使用继承. 

#### 多态

1. overload 重名方法, 不同的参数个数, 不同的参数类型,不同的参数顺序, 不能以返回值来区分
2. override 方法的覆盖, 相同的函数名,参数,返回值,抛出更多异常,-

成员变量无法实现多态

```java
class Base{
    public int i = 1;
    
}
class Derived extends Base{
    public int i = 2;
}
public class Test{
    public static void main(String[] args){
        Base b = new Derived();
        System.out.println(b.i);  // 1
    }
}
```



b.i 指的是Base 类中定义的 i

```java
class Super{
    public int f(){
        return 1;
    }
    
}
public class SubClass extends Super{
    public fload f(){
        return 2f;
    }
}
public static void main(String[] args){
    Super s = new SubClass();
    System.out.println(s.f());
}
```

这个override 编译错误,不能以返回值区分.虽然父类和子类函数有着不同的返回值,但是函数名字相同.

#### 抽象类和接口

1. 如果抽象类的子类没有为父类提供所有抽象方法的具体实现, 那么也是抽象类.
2. 接口中所有方法都是抽象的, 可以通过接口间接实现多重继承, 
3. 接口中方法只有定义, 抽象类中方法可以有实现
4. 接口 has - a  , 抽象类  is - a
5. 接口中成员变量默认为public static final, 必须赋初值, 抽象类可有有自己的成员变量.
6. 抽象类的抽象方法不能用private, static, synchronized, native等修饰不能带花括号,要带分号.
7. 接口的成员变量默认是 public static final, 接口中的方法只能用关键字public abstract 来修饰.

#### 内部类

局部内部类(类中方法中的类), 静态内部类, 成员内部类, 匿名内部类

```
class outerClass{
    static class innerClass{}
    }
class outerClass{
    class innerClass{}
}
class outerClass{
    public void memberFunction(){
        class innerClass{}
    }
}


```



静态内部类: 被声明为static 的内部类, 不依赖于外部类实例而被实例化,只能访问外部类中的静态成员和静态方法.

如果静态内部类去掉static 关键字,就变为成员内部类.

局部内部类指的是定义在一个代码块内的类,只能访问方法中定义为final 的类型的局部变量,

匿名内部类 必须继承其他类或实现其他借口.



在非静态内部类中不能定义静态成员, 

```
public class OuterClass{
    private int dl = 1;
}


class InnerClass{
    public static int methoda(){return dl;}
}
public class InnerClass{
    static int methoda() { return dl;}
}
```

静态内部类不能访问外部类的非静态成员

```
static class InnerClass{
    protected int methoda(){return dl;}
}
```

#### 获取父类的类名

this.getClass().getSuperClass().getName()

#### equals 和hashcode

如果equals 返回true, 那么他们的哈希吗是相同的, equals 方法必须满足自反性,对称性,传递性,一致性. 

而且对于任何非 null 值的引用 x,x.equals(null)必须返回 false. 实现高质量的 equals 方法的诀窍包括:1. 使用==操作符检查"参数是否为这个
对象的引用"; 2. 使用 instanceof 操作符检查"参数是否为正确的类型"; 3. 对于类中的关键属性,检查参数传入对象
的属性是否与之相匹配;4. 编写完 equals 方法后,问自己它是否满足对称性、传递性、一致性;5. 重写 equals 时
总是要重写 hashCode;6. 不要将 equals 方法参数中的 Object 对象替换为其他的类型,在重写时不要忘掉
@Override 注解。

#### super

当子类构造函数需要显示调用父类构造函数时,super() 必须为构造函数中的第一条语句.

#### 当一个对象被当作参数传递到一个方法后,此方法可改变这个对象的属性,并可返回变化后的结果,那么这里到底是值传递还是引用传递?

是值传递, java 语言的方法调用只支持参数的值传递, 当一个独享实例作为一个参数被传递到方法中时, 参数的值就是对该对象的引用, 对象的属性可以在被调用过程中被改变, 但对对象的改变是不会影响到调用者的.

#### 标识符

变量名,函数名,数组名都是标识符

字母,数字,_ $, 第一个字符不能是数字,

#### 跳出多重循环

```
out:
for(int i = 0; i < 5; i++){
    for(int j = 0; j < 5; j++){
        if(j >= 2)
        break out;
    }
}
```

#### final finally finalize

final 指的是引用的不变性,不关心指向对象内容的变化.所以被final修饰的变量必须初始化, 1. 定义时初始化,2. 初始化块中初始化,但不可在静态初始化块中初始化 3. 静态final 成员变量可以在静态初始化块中初始化, 但不可在初始化块中初始化. 4 .在类的构造器中初始化, 但静态final 成员变量不可以在构造器中初始化.当一个类被声明为final时, 不能被继承,所有方法不能被重写. 一个类不能既被声明为abstract 又被声明为final.

finally: 作为异常处理的一部分, 只能用在try/catch 语句中.

finalize 是Object 类的方法,在垃圾回收器执行时会调用被回收对象finalize 方法,可以覆盖这个方法来实现对其他资源的回收.

String 类是final 类, 不可以被继承. 对String 类型的最好的重用方式是关联关系has-a, 和依赖关系 use-a.

当一个对象

#### static

1. 为特定的数据或对象分配单一的存储空间
2. 实现某个方法或属性与类关联在一起(不和对象关联)

静态变量属于类,对于静态变量的引用有两种方式, 类.静态变量, 对象.静态变量.

static 方法,不用创建对象就能调用.

static 方法中不能使用this , super 关键字,不能调用非static方法.

static 代码块只会被执行一次.

不能在成员函数内部定义static 变量(包括静态函数内部)

#### switch

Java5以前switch中, 可以使用byte, short, char, int, 从java5开始, 可以是enum, 从java7开始,可以使用String.

可以使用int, short, byte, char String

#### clone

在实际编程过程中, 经常要遇到这种情况, 有一个对象A, 在某个时刻A中包含了一些有效值, 此时刻可能会需要一个和A完全相同新对象B,  并且此后对B任何改动都不会影响到A中的值,也就是说, A与B是两个独立的对象,但B的初始值是由A对象确定的, 在java语言中, 用简单的赋值语句是不能满足这种需求的, 要满足这种需求虽然有很多途径, ranking实现clone() 是其中最简单, 也是最高效的手段

#### new 一个对象的过程和cline 一个对象过程的区别

new 操作符本意是分配内存, 程序执行到new 操作符时, 首先去看new 操作符后面的类型, 因为知道了类型, 才能知道要分配多大的内存空间, 分配完内存之后, 在调用构造函数, 填充对象的各个域, 这一步叫做对象的初始化, 构造方法返回后, 一个对象创建完毕, 可以把他的引用发布到外部, 在外部就可以使用这个引用操纵这个对象.

clone 在第一步是和new 相似的, 都是分配内存, 调用clone 方法时, 分配的内存和原对象相同, 然后再使用原对象中对应的各个域, 填充新对象的域, 填充完成后, clone 方法返回, 一个新的相同的对象被创建, 同样可以把这个新对象的引用发布到外部

#### clone 复制对象和复制引用, 深复制和浅复制



#### java 提供了哪些基本数据类型

int 4 0

short 2 0

long 8 0l

byte 1 0

float 4 0.0f

double 8 0.0

char 2 u0000

boolean 1 false

short int long double float

byte char boolean

java 默认声明的小数是double 类型的.不是float 类型.

Math.abs(Integer.MIN_VALUE) == Integer.MIN_VALUE

#### 值传递,引用传递

```
public class Test{
    public static void changeStrinbBuffer(StringBuffer ss1, StringBuffer ss2){
        ss1.append(" world");
        ss2 = ss1;
    }
    public static void main(Stirng[] args){
        Integer a = 1;
        Integer b = a;
        b++;
        System.out.println(a);  // 1
        System.out.println(b);   // 2
        StringBuffer s1 = new StringBuffer("hello");
        StringBuffer s2 = new StringBuffer("hello");
        changeStringBuffer(s1,s2)
        System.out.println(s1 +", "+s2);  // hello world, hello 
    }
}
```

在执行完b++ 时,会创建一个新值为2 的Integer 赋值给b, b 和a 已经没有任何关系了

执行完ss2 = ss1 时,修改ss2的值对s2 毫无影响.

#### byte相加

byte short char 运算, 会把这些类型的变量强制转换为int 类型. 最后得到的值也是int 类型.

`short s1 = 1; s1 = s1 +1` 错误

`short s1 = 1; s1 = (short) (s1 + 1)` 对

`short s1 =1; s1 += 1` 对

#### round cell floor

round 四舍五入

ceil 向上取整

floor 向下取整.

#### `>>` 和`>>>`

对于正数,相同,负数,不同.

`>>`负数, 高位补1, `>>>` 正数和负数,高位都补0

char byte short 类型移位的时候,会转化为int 类型.当移动位数超过32, 没有意义, 所以java 内部自动取32的余数 a >> n 等价于 a >> (n % 32 ) 

#### 汉字

```java
public class Test{
    public static void judgeChineseCharactor(String str){
        String regEx = "[\u4e00 - \u9fa5]";
        if(str.getBytes().length == str.length())
        	System.out.println("there is no Chinese letter");
        else {
            Pattern p = Pattern.compile(regEx);
            Matcher m = p.matcher(str);
            while(m.find()){
                System.out.println(m.group(0) + " ");
            }
        }
    }
}
```

#### new String("")

new String("a") 创建了几个对象, 一个或者两个.

#### == equals hashcode

==: 基本数据类型, 比较数值是否相同, 引用类型: 比较内存地址是否相同

equals 从Object 继承过来,默认使用 ==, String 类型, 比较的是string 的内容. 

hashCode 也是从object 继承过来.

如果equals 为true, 则hashcode 一定为true

如果hashcode 不同, equals 一定不同.

```java
String s = "abc";;
String s1 = "a" + "bc";
System.out.println(s == s1); // true
```

"a"+"bc"在编译器中就被变为 "abc "了

#### 数组初始化方式

```
int[] a = new int[5];
int[] a = {1,2,3,4,5};
int[] a = new int[]{1,2,3,4,5};
```

#### finally

1 finally 在return 前执行. 

2 如果finally 中有return , 会覆盖外部的return 语句.

3 在finally 块中改变return的值对返回值没有任何影响, 而对引用类型的数据会有影响.

### 异常处理

#### Error Exception

java 提供两种异常类,分别为Error, Exception, 他们有共同的父类Throwable, 

Error 是严重的错误. outofMemoryError, ThreadDeath 

Exception 可恢复的异常.

出现运行时异常后, 系统会把异常一直往上层抛出, 直到遇到处理代码为止, 如果是多线程就用Thread.run() ,如果是单线程,就用main() 抛出.

ArithmeticException 属于运行时异常,编译器没有强制对其进行捕获并处理,编译可以通过, 如果是IOException, 属于检查异常, 编译器强制去捕获此类型的异常.

#### 异常Exception

有两种异常, 编译时异常和运行时异常(checkedexception, 和 runtimeException).

Java 认为编译时异常是可以被处理的异常, 所以java程序必须显示处理checked异常. 如果不处理, 编译不通过. 处理方法有两种: 

1. 当前方法知道怎么处理, 使用try catch  处理
2. 当前方法不知道, 在定义该方法声明抛出异常.

运行时异常: NullPointerException, ClassNotFoundExcetion, NumberFormatException, IndexOutOfBoundsException, IllegalArgumentException, ClassCastException

#### throw 和throws

throw: 

1. 用在方法体内, 表示抛出异常, 由方法体内的语句处理
2. 抛出一个异常实例, 所以执行throw 一定是抛出了某种异常

throws:

1. 用在方法声明后面, 表示如果抛出异常, 由方法的调用者进行异常处理

### 文件流

#### IO流

装饰者模式可以在运行时动态地给对象添加一些额外的职责,更具有灵活性.

 

```java
class MyOwnInputStream extends FilterInputStream{
    public MyOwnInputStream(InputStream in)
        super(in);
    public int read() throws IOException{
        int c = 0;
        if( (c = super.read()) != -1){
            if(Character.isLowerCase((char)c))
            return Character.toUpperCase((char)c);
            else if (Character.isUpperCase((char)c)
            return Character.toLowerCase((char)c);
            else return c;
        }
        else {
             return -1;
        }
                     }
}
                     
public class Test{
    public static void main(String[] args){
        int c;
        try{
            InputStream is = new MyOwnInputStream(new BufferedInputStream(new FileInputStream("test.txt")));
            while((c = is.read()) >= 0)
                System.out.prinltn((char)c);
            is.close();
        }catch(IOException e){
            System.out.println(e.getMessage());
        }
    }
}
```

常见的流有两种, 字节流和字符流, 字节流继承于 InputStream OutputStream

字符流继承于Reader, Writer

#### NIO (Nonblocking IO)

传统IO : 客户端对服务器端发起请求,accept 就会阻塞(阻塞指的是暂停一个线程的执行以等待某一条件的发生, 如某资源就绪) .如果连接成功, 数据还没有准备好, 对read的调用同样会阻塞.

#### java 程序执行过程

java 代码 -> 字节码-> 代码的装入->代码的校验->代码执行

装入代码由类装载器完成.

java 字节码的执行分为两种, 编译执行,解释执行, 编译执行: 将字节码编译成机器码,再执行机器码,

解释执行,每次解释并执行一小段代码.通常采用的是解释执行. 

#### 内存泄露

```java
Vector v = new Vector(10);
for(int i = 1; i < 10; i++){
    Object o = new Object();
    v.add(o);
}
```

#### 堆和栈

栈的存取速度更快,栈的大小和生存期必须是确定的,因此缺乏一定的灵活性.而堆可以在运行时动态分配内存,生存期不用告诉编译器.这个导致存取速度慢.

### 集合

对hashMap 中User 排序

```java
import java.util.*;

public class SortedUserHashMap {
    public static HashMap<Integer, User> sortHashMap(HashMap<Integer, User> map){
        Set<Map.Entry<Integer, User>> entrySet = map.entrySet();
        List<Map.Entry<Integer, User>> list = new ArrayList<>(entrySet);
        Collections.sort(list, new Comparator<Map.Entry<Integer, User>>() {
            @Override
            public int compare(Map.Entry<Integer, User> o1, Map.Entry<Integer, User> o2) {
                return o2.getValue().age - o1.getValue().age; // 从大到小
            }
        });
        LinkedHashMap<Integer, User> linkedHashMap = new LinkedHashMap<Integer, User>();
        for(Map.Entry<Integer, User> entry: list){
            linkedHashMap.put(entry.getKey(), entry.getValue());
        }
        return linkedHashMap;
    }
}

class User{
    public int age;
    public String name;
    public User(String name, int age){
        this.age = age;
        this.name = name;
    }
}
```



#### hashmap hashtable treemap weakhashmap

hashmap 允许键值 为null

hashtable 不允许键 或者 值 为null 

#### 并发集合

ArrayList, HashSet, HashMap 都是线程不安全的, Vector, HashTable 线程安全, 因为在核心方法上加了synchronized 关键字,

Collections 提供了相关的API, 

```
Collections.synchronizedCollection(c);
Collections.synchronizedList(list);
Collections.synchronizedMap(m);
COllections.synchronizedSet(s);
```

线程安全集合仅仅是添加了synchronized 同步锁, 严重牺牲了性能, 

ConcurrentHashMap 是线程安全的HashMap, 默认构造参数有initialCapacity, loadFactor, concurrencyLevel,  默认值分别是16, 0.75, 16,

#### hashmap

1.8:　存储的是键值对，　允许key是null, value是null, hashmap内部是数组加链表, 会根据hashcode确定数组的索引,如果发生hash冲突, 会将一个桶中的数据以链表的形式存储,如果hash冲突概率高, 会导致一个桶中链表长度过长, jdk1.8后链表长度大于8会转为红黑树, 默认, 键值对数量大于桶的个数(数组长度)* 负载因子

put: 

1. table[] 是否为空
2. 判断table[i] 是否插入过值
3. 判断链表长度是否大于8, 大于就转为红黑树
4. 判断key 是否和原有key 相同, 如果相同就覆盖原来的value, 并返回原来的value
5. 如果不同,就插入一个key.

get:

1. 判断表或key 是不是null, 如果是就返回null
2. 数组中索引第一个key 与传入的key 是否相等, 相等就返回
3. 如果不相等,判断链表是不是红黑树, 是红黑树就从树中取值,
4. 如果不是红黑树,就遍历链表取值.

#### concurrentHashMap

从1.7 到1.8版本, 由于HashEntry 从链表变成了红黑树, 时间复杂度从常数变为对数级别

jdk1.8中ConcurrentHashMap的数据结构已经接近hashmap了, 它只是增加了同步的操作来控制并发. 从jdk1.7 中reentrantLock + segment+ hashEntry 到1.8 中synchronized+cas+hashEntry + 红黑树.

1. 降低了锁的粒度
2. 使用synchronized来同步, 由于粒度的降低, 实现的复杂度也降低了.
3. 因为粒度降低了, 低粒度的加锁方式上, synchronized 并不比reentrantlock 差, 在粗粒度加锁中, reentrantlock 可能通过condition 来控制各个低粒度的边界, 更加灵活, 而在低粒度中, 这种优势就没有了.
4. 使用关键字比使用API更加自然

1.7: hashTable, 每次更新操作, 都会锁住整张表, concurrentHashMap 锁住一部分,ConcurrentHashMap的数据结构是一个segment数组和多个hashEntry组成, segment 数组的意义就是讲一个大的table 分割为多个小的table 加锁, 一个segment 元素存储hashEntry(就是下标为hash值和key + value 的数组, 一个hashEntry 可能包含多个键值对, 因为hash 碰撞, jdk1.8中会变为红黑树),  segment 默认是16, 最大65536. put: 两次hash 来定位,第一次hash定位segment, 第二次hash定位hashentry., 将数据插入到指定的链表尾端,会通过tryLock() 方法尝试去获取锁, 如果已经有线程获取该segment 锁, 那当前线程会以自旋的方式去继续的调用tryLock() 去获取锁, 超过指定次数就挂起, 等待唤醒, 

size方法: 多线程如何确定size: 不加锁, 计算3次, 如果结果一致就仍未当前没有元素加入, 计算的结果是准确的. 如果不一致, 就给每一个segment 加锁, 计算size.

1.8:　取消segments, 直接使用transient volatile hashEntry<K,V>[] table, 来保存数据, 采用table 数组元素作为锁, 从而对每行数据加锁, jdk1.8中的实现已经摒弃了segment 的概念, 而是直接用node 数组和链表和红黑树, 并发控制使用synchronized 操作, 看起来像是优化过的并线程安全的hashMap.

node继承HashMap 中的Entry 用来存放数据, hash值, 和下一个Node的引用. TreeNode继承node,treebin : 封装Treenode 的容器,

concurrentHashMap初始化操作什么也没干, 初始化放在了put 操作中.

put 流程: 

1. 如果没有初始化, 就先初始化
2. 如果没有hash冲突就直接插入
3. 如果正在扩容就先扩容
4. 如果hash 冲突, 就加锁, 锁住链表或者红黑树头结点
5. 如果该链表的数量大于8, 就先转换成红黑树(整个table 的数量小于64, 就扩容到原来的一倍, 不转为红黑树))reak, 再次进入循环,

从中发现, 并发处理中使用的是乐观锁, 当有冲突的时候才使用乐观锁, 

jdk1.8 中size 是已经计算好的, 在put方法中会调用addCount(), 

#### list map set

list 存储有顺序, 允许重复, map 没有顺序, 键不能重复, 值可以重复, set 无序, 不重复.

list 允许null

hashmap 支持null 值null 键, hashtable 不支持null 值, 不支持null 键,  linkedhashmap 支持null 值, null键, TreeMap支持null值,不支持 null 键.

### 多线程

#### monitor

Java 中每个对象都有一个监视器, 来检测并发代码的重入, 在非多线程编程时该监视器不发挥作用, 反之在synchronized 范围内, 监视器发挥作用,

wait() / notify() / notifyAll() 必须在synchronized 块中,, 这四个关键字是针对同一个监视器, 当某代码不持有监视器的所有权的时候, 会抛出java.lang.IllegalMonitorStateException, 也包括在synchronized 块中调用另一个对象的wait/ notify() 也会因为监视器不同而抛出异常

#### 三种创建线程方式

一个是重写Thread 中的run 方法, 另一种传给Thread runnable 对象, 两种方式都是调动Thread 对象的run 方法, 如果run 方法没有被覆盖, 且有runnable 对象, 那么就会调用runnable 对象的run 方法, 

也就是说, 如果重写Thread 类中的run 方法, 那么start() 会运行start0(), 会调用run 方法, 如果没有重写run方法, 但是传入runnable 对象, 那么start(), 运行start0(), 也会调用run方法, 只是在THread 类中的run方法会调用runnable (target)对象的run 方法.

```java
    /**
     * If this thread was constructed using a separate
     * {@code Runnable} run object, then that
     * {@code Runnable} object's {@code run} method is called;
     * otherwise, this method does nothing and returns.
     * <p>
     * Subclasses of {@code Thread} should override this method.
     *
     * @see     #start()
     * @see     #stop()
     * @see     #Thread(ThreadGroup, Runnable, String)
     */
    @Override
    public void run() {
        if (target != null) {
            target.run();
        }
    }
```

使用Callable, Future实现有返回结果的多线程.

#### 线程互斥和同步

1. 间接相互制约: 一个系统中的多个线程必然要共享某种系统资源, 如共享cpu, 共享io设备, 打印机, 当线程A 在使用打印机的时候, 其他线程都要等待.
2. 同步:直接相互制约, 这种制约主要是线程之间的相互合作, 如果有线程A将计算结果提供给线程B做进一步处理, 那么线程B在线程A将数据到达之前都处于阻塞状态.

**线程同步的主要任务是使并发执行的各线程之间能够有效共享资源和相互合作**

间接相互制约是互斥, 直接相互制约是同步.

#### 线程停止

由于实际的业务需要, 经常会遇到需要在特定时机终止某一线程的运行, 使其进入死亡状态, 目前最通用的做法是设置一个boolean 值, 当条件满足时, 使线程执行体快速执行完毕.

```java
public class ThreadTest {
    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread thread = new Thread(myRunnable);
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName()+" "+i);

            if(i == 30) thread.start();
            Thread.yield();
            if(i == 40) myRunnable.stopThread();
        }
    }
}

class MyRunnable implements Runnable {
    private boolean stop;

    @Override
    public void run() {
        for (int i = 0; i < 100 && !stop; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
    }

    public void stopThread() {
        this.stop = true;
    }
}
```

#### java多线程的新建, 就绪, 运行, 死亡, 阻塞状态

新建到就绪: 调用了start() 方法

就绪到运行:得到处理器资源＇

运行到就绪：　主动调用yield() 方法, 或在运行过程中失去处理器资源

阻塞: wait() synchronized() sleep() join() 

运行状态到死亡状态: 线程执行体执行完毕, 或者发生了异常.

#### java 线程阻塞的主要方法

阻塞可以释放同步锁, 也可以不释放

join(): 让一个线程等待另一个线程完成才继续执行,  在A线程执行体中调用B线程的join()方法, 则A线程被阻塞,直到B线程执行完,A才能够继续执行.

```java
package concurrent.basic.block;

public class JoinTest {
    public static void main(String[] args) {
        Thread thread = new Thread(new MyRunnablejoin());
        for(int i = 0; i < 100; i++){
            System.out.println(Thread.currentThread().getName()+" "+i);
            if(i == 30) thread.start();
            try{
                thread.join(); // main 线程要等待thread 线程执行完, 才能继续执行
            }catch (InterruptedException e){
                e.printStackTrace();
            }
        }
    }
}

 class MyRunnablejoin implements Runnable {

    @Override
    public void run() {
        for (int i = 0; i < 100; i++)
            System.out.println(Thread.currentThread().getName() + " " + i);
    }
}

```

sleep(): 让正在执行的线程暂停指定的时间, 进入阻塞状态, 在睡眠的时间段中, 线程不是处于就绪状态, 不会得到执行的机会, 即使系统的没有其他可执行的线程, 也不会执行.

后台线程:当所有前台线程进入死亡状态时候，　后台线程自动死亡．

```java
package concurrent.basic.block;

public class SleepTest {


    public static void main(String[] args) {
        Thread thread = new Thread(new MyRunnableSleep());
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread() + " " + i);
            if (i == 30) {
                thread.start();
                try {
                    Thread.sleep(10); // 让thread 在for 循环31次前开始执行
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

class MyRunnableSleep implements Runnable {

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
    }
}
```



yield(): 让出时间片, 线程进入就绪状态. 

```java
package concurrent.basic.block;

public class yieldTest {
    public static void main(String[] args){
        Thread thread1 = new Thread(new RunnableYield());
        Thread thread2 = new Thread(new RunnableYield());
        thread1.setPriority(Thread.MIN_PRIORITY);
        thread2.setPriority(Thread.MAX_PRIORITY);
        for(int i = 0; i < 100; i++){
            System.out.println(Thread.currentThread().getName()+" "+ i);
            if(i==2){
                thread1.start();
                thread2.start();
                Thread.yield();
                Thread.yield();
            }
        }
    }
}

class RunnableYield implements Runnable {

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i + " "+ Thread.currentThread().getPriority()); // 费时间
        }
    }
}
```



#### 一个例子, 同步的重要性

```java
package concurrent.example1;

public class unsafeTest {
    public static void main(String[] args) {
        Account account = new Account("12345", 1000);
        DrawMoneyRunnable drawMoneyRunnable = new DrawMoneyRunnable(account, 700);
        Thread thread1 = new Thread(drawMoneyRunnable);
        Thread thread2 = new Thread(drawMoneyRunnable);
        thread1.start();
        thread2.start();
    }

}

class DrawMoneyRunnable implements Runnable {
    private Account account;
    private double drawAmount;

    public DrawMoneyRunnable(Account account, double drawAmount) {
        super();
        this.account = account;
        this.drawAmount = drawAmount;
    }


    public void run() {
        System.out.println("现在有: "+account.getBalance());
        if (account.getBalance() >= drawAmount) {  //1

            System.out.println("取钱成功， 取出钱数为：" + drawAmount);
            double balance = account.getBalance() - drawAmount;
            account.setBalance(balance);
            System.out.println("余额为：" + balance);
        }
    }


//    @Override // 或者使用这段代码, 可能会出现更加奇特的事情(两个人取钱, 结果只扣费一次)
//    public void run() { // 两个线程并发
//        double current = account.getBalance();
//        if(current >= drawAmount){
//            System.out.println("现在有: "+current);
//            System.out.println(" 成功, 取钱: "+ drawAmount);
//            double balance = current - drawAmount;
//            account.setBalance(balance);
//            System.out.println("余额: "+balance);
//        }
//    }



}


class Account {

    private String accountNo;
    private double balance;

    public Account() {

    }

    public Account(String accountNo, double balance) {
        this.accountNo = accountNo;
        this.balance = balance;
    }

    public String getAccountNo() {
        return accountNo;
    }

    public void setAccountNo(String accountNo) {
        this.accountNo = accountNo;
    }

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

}
```

其中一种可能是, 最后余额是负数, 这是不允许的. 这样就提到线程安全, 其实指的是多线程环境下对共享资源的访问可能会引起共享资源的不一致性, 因此, 为避免线程安全问题, 应该避免多线程环境下对此共享资源的并发访问.

#### 同步

1. 方法加上synchronized, 锁对象是当前方法所在对象自身, 多线程环境下, 当执行此方法时, 首先都要获得次同步锁(同时最多只有一个线程可以获得), 如果修饰的是static 方法, 那么会锁定该类的所有实例. 只有当线程执行完同步方法后, 才会释放锁对象, 其他的线程才有可能获取此同步锁., 上个例子中, account 对象是共享资源, 只需要在run() 方法前加上synchronized就行了, 

2. 代码块加上synchronized, 解决线程安全问题其实只需要限制对共享资源访问的不确定性就行了, 使用同步方法时, 使得整个方法体都成为了同步执行状态, 会使得可能出现同步范围过大(粗粒度)的情况, 于是, 针对需要同步的代码可以使用同步代码块.

   ```
   synchronized(obj){
       
   }
   ```

   obj 是锁对象, 选择哪一个对象作为锁是至关重要的, 一般情况下, 都是选择共享资源对象作为锁对象,上个例子中, 使用account 对象作为锁对象, 也可以使用this, (这是因为创建线程使用runnable方式, 如果是直接继承Thread 方式创建线程, 使用this 对象作为同步锁其实没有起到任何作用, 因为已经是不同的对象了)

3. Lock 对象同步锁, 可以将同步锁对象和共享资源解耦, 同时又可以解决线程安全问题. 需要注意的是lock 对象需要和资源对象一一对应

   ```
   class x{
       private final Lock lock = new ReentrantLock();
       public void m(){
           lock.lock();
           // 需要进行线程安全同步的代码
           
              try {
               condition.await();
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           
           condition.signalAll();
           
           // 释放锁
           lock.unlock();
       }
   }
   ```

4. wait() notify() notifyAll(), 两个线程使用同一个runnable 对象, 不同线程执行相同的对象的方法, 这样可以使用synchronized同步, 不同线程执行相同对象的不同的方法, 可以使用wait() nofity() 同步

注意1. wait() 方法执行后, 当前线程立即进入到等到阻塞状态, 后面的代码不会执行

2 nofity() / notifyAll() , 将唤醒本线程获得的同步锁对象上的(任意一个notify() / 所有notifyAll() ) 线程对象, 但是, 此时还并没有释放同步锁对象, 也就是说, 如果notify() / notifyAll() 后面还有代码, 还会执行, 等待执行完毕才会释放同步锁对象, 这里synchronized 加载方法上, 锁的是this , 也就是这个safeAccount 对象,直到当前线程执行完毕(或者 执行 wait())才会释放同步锁对象.

3 nofity()/ notifyAll() 后面如果有sleep() , 当前线程会进入阻塞状态, 但是同步对象锁没有释放,还是直到当前线程执行完(或者 执行 wait())才会释放

4 wait() notify() notifyAll(), 是基于相同的对象锁的, 如果是不同的对象锁, 就会出错.**java.lang.IllegalMonitorStateException** 

5  当wait 线程唤醒后并执行(获得了同步对象锁)时, 接着上次执行到的wait() 方法代码后面继续向下执行, (if语句, 会执行else 中的语句)

```java
package concurrent.example1;
import java.util.Date;

public class safe1 {
    public static void main(String[] args){
        SafeAccount  safeAccount = new SafeAccount("123456",6);

        Thread drawMoneyThread  = new Thread(new SafeDrawMoneyRunnable(safeAccount, 700));


        Thread depositemoneyThraed = new Thread(new SafedepositeMoneyRunnable(safeAccount, 700));



        drawMoneyThread.start();
        depositemoneyThraed.start();

    }
}


class SafeDrawMoneyRunnable implements Runnable {
    private SafeAccount safeAccount;
    private double amount;

    public SafeDrawMoneyRunnable(SafeAccount safeAccount, double amount) {
        this.safeAccount = safeAccount;
        this.amount = amount;
    }

    @Override
    public void run() {
        for(int i = 0; i < 100; i++){

                safeAccount.draw(700,i);
        }
    }
}

class SafedepositeMoneyRunnable implements Runnable {
    private SafeAccount safeAccount;
    private double amount;

    public SafedepositeMoneyRunnable(SafeAccount safeAccount, double amount) {
        this.safeAccount = safeAccount;
        this.amount = amount;
    }

    public void run() {
        for (int i = 0; i < 100; i++) // 只是模拟, 存一百次
            safeAccount.deposite(amount, i);
    }
}


class SafeAccount {
    private String accountNo;
    private double balance;
    // 标识账户中是否已有存款
    private volatile boolean  flag = false;

    public SafeAccount() {

    }

    public SafeAccount(String accountNo, double balance) {
        this.accountNo = accountNo;
        this.balance = balance;
    }

    public String getAccountNo() {
        return accountNo;
    }

    public void setAccountNo(String accountNo) {
        this.accountNo = accountNo;
    }

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

    public synchronized void deposite(double depositeAmount, int i) {

        if (flag) {
            try {
                System.out.println(Thread.currentThread().getName() + " 开始执行wait 操作" + " i = " + i + " "+new Date(System.currentTimeMillis()));
                wait();
                System.out.println(Thread.currentThread().getName() + " wait操作 " + i + " 结束了"+ " "+new Date(System.currentTimeMillis()));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } else {
            // start to deposite
            System.out.println(Thread.currentThread().getName() + " 存款:" + depositeAmount + " -- i=" + i+ " "+new Date(System.currentTimeMillis()));
            setBalance(balance + depositeAmount);
            flag = true;
            System.out.println(Thread.currentThread().getName() + "-- 存钱 -- 执行完毕" + " -- i=" + i+ " "+new Date(System.currentTimeMillis()));
            notifyAll();
            try{
                Thread.sleep(3000);
            }catch (InterruptedException e){
                e.printStackTrace();
            }
        }
    }

    public synchronized void draw(double drawAmount, int i) {
        if (!flag) {
            try {
                System.out.println(Thread.currentThread().getName() + " 没有存款,开始要执行wait操作" + " 执行了wait操作" + " -- i=" + i+ " "+new Date(System.currentTimeMillis()));
                wait();
                System.out.println(Thread.currentThread().getName() + " 执行了wait操作" + " 执行了wait操作" + " -- i=" + i+ " "+new Date(System.currentTimeMillis()));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } else {
            // 开始取钱
            System.out.println(Thread.currentThread().getName() + " 取钱：" + drawAmount + " -- i=" + i+ " "+new Date(System.currentTimeMillis()));
            setBalance(getBalance() - drawAmount);
            flag = false;
            notifyAll();
            System.out.println(Thread.currentThread().getName() + "-- 取钱 -- 执行完毕" + " -- i=" + i+ " "+new Date(System.currentTimeMillis()));
            try{
                Thread.sleep(3000);
            }catch (InterruptedException e){
                e.printStackTrace();
            }
        }


    }
}
```

 

#### 多线程共享数据

* **多个线程行为一致, 共同操作一个数据源,**  也就是说每个线程执行的代码相同, 那么可以使用同一个runnable 对象, 
* **多个线程行为不一致, 共同操作一个数据源**, 要使用不同的runnable 对象, 比如银行存取款

**公平锁和非公平锁**

Reentrant 默认非公平锁,如果当前没有线程占有锁，当前线程直接通过cas指令占有锁，管他等待队列，就算自己排在队尾也是这样

公平锁: FIFO

#### 线程并发库 

java.util.concurrent

* 线程池
* 并发队列
* 同步器
* 并发collections

java.util.concurrent.atomic 包 (多线程的原子性操作提供的工具类)

通过如下两个方法快速理解 atomic 包的意义:
 AtomicInteger 类的 boolean compareAndSet(expectedValue, updateValue);
 AtomicIntegerArray 类的 int addAndGet(int i, int delta);
➢ 顺带解释 volatile 类型的作用,需要查看 java 语言规范。
 volatile 修饰的变量,线程在每次使用变量的时候,都会读取变量修改后的最的值。(具有可见性)
 volatile 没有原子性。

ava.util.concurrent.lock 包

➢ Lock 接口:支持那些语义不同(重入、公平等)的锁规则,可以在非阻塞式结构的上下文(包括 hand-
over-hand 和锁重排算法)中使用这些规则。主要的实现是 ReentrantLock。
➢ ReadWriteLock 接口:以类似方式定义了一些读取者可以共享而写入者独占的锁。此包只提供了一个实
现,即 ReentrantReadWriteLock,因为它适用于大部分的标准用法上下文。但程序员可以创建自己的、
适用于非标准要求的实现。
➢ Condition 接口:描述了可能会与锁有关联的条件变量。这些变量在用法上与使用 Object.wait 访问的
隐式监视器类似,但提供了更强大的功能。需要特别指出的是,单个 Lock 可能与多个 Condition 对象关
联。为了避免兼容性问题,Condition 方法的名称与对应的 Object 版本中的不同。

线程间的协作: 就是运行或者阻塞不同的线程.不同的线程之间可以用notifyAll   或者signalAll

#### executors 工厂类,四种线程池

**线程池的好处** :

1 降低资源消耗

2 提高响应速度

3 提高线程的可管理性

三张方法创建线程池

```
ExecutorService fPool = Executors.newFixedThreadPool(3);  // 固定大小
ExecutorService cPool = Executors.newCachedThreadPool();   // 缓存大小的线程池
ExecutorService sPool = Executors.newSingleThreadExecutor(); //单一线程
newScheduledThreadPool(2) //可以延迟
```

**固定大小** 

```java
private static void fixed (){
        ExecutorService pool = Executors.newFixedThreadPool(2);
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        MyThread t3 = new MyThread();
        MyThread t4 = new MyThread();
        MyThread t5 = new MyThread();

        pool.execute(t1);
        pool.execute(t2);
        pool.execute(t3);
        pool.execute(t4);
        pool.execute(t5);
        pool.shutdown();
    }
    static class MyThread implements Runnable{
        public void run(){
            System.out.println(Thread.currentThread().getName()+" running ....");
        }
    }
```

这里会输出的线程名称数量为两个.

**单线程** Executors.newSingleThreadExecutor();

 那么会输出一个线程的名称

**可变线程池**

Executors.newCachedThreadPool();, 会有5 个线程的名字出现, 说明会自动创建多个线程来处理Thread 类中的run 方法.

**延迟连接池**

 设置一个任务多长时间后运行

```java
private static void schrduled() throws ExecutionException, InterruptedException {
        ScheduledExecutorService pool = Executors.newScheduledThreadPool(2);
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        MyThread t3 = new MyThread();
        MyThread t4 = new MyThread();
        MyThread t5 = new MyThread();
        pool.execute(t1);   //要求一个runnable 对象, 然后异步执行
        Future future = pool.submit(t2);
        System.out.println(future.get());   // run方法没有返回值, 所以为null
        // call() 和run() 不同在于他有返回值
        Future future1 = pool.submit(new Callable(){
            public Object call() throws Exception{
                System.out.println("Asynchronous Callable");
                return "Callable result";
            }
        });
        System.out.println(future1.get());
        pool.schedule(t4, 2, TimeUnit.SECONDS);
        pool.schedule(t5,4, TimeUnit.SECONDS);

        pool.scheduleAtFixedRate(new Runnable() {
            @Override
            public void run() {
                System.out.println("scheduleAtFixedRate");
            }
        }, 2, 1, TimeUnit.SECONDS);

        pool.shutdown();
    }
// submit 和execute不同在于,接受参数不一样, 返回值不一样

```

execute(Runnable), 没有办法得知被执行的 Runnable 的执行结果。如果有需要的话你得使用一个

submit(): 返回一个 Future 对象。 Future 对象可以用来检查 Runnable 是否已经执行完毕。传入Runnable 返回null,传入callable返回一个Object

invokeAny(): 随机从callable 的set 中调用一个

invokeAll() : 获取一系列的Future对象

shutdown() 和shutdownNow(): shutdown只是将空闲的线程中断,之前提交的任务可以继续执行直到结束。shutdownNow 是 interrupt 所有线程, 因此大部分线程将立刻被中断。



#### ExecutorService

这是一个接口, 



```
public interface ExecutorService extends Executor
public abstract class AbstractExecutorService implements ExecutorService
public class ThreadPoolExecutor extends AbstractExecutorService

public interface ScheduledExecutorService extends ExecutorService
public class ScheduledThreadPoolExecutor  // 延迟线程池使用这个
        extends ThreadPoolExecutor
        implements ScheduledExecutorService
```

可以看出ExecutorService  上面有AbstractExecutorService, ScheduleExecutorService,  

ThreadPoolExecutor 继承AbstractExecutorService

ScheduledThreadPoolExecutor 继承ThreadPoolExecutor, 实现ScheduledExecutorService

#### 阻塞队列

BlockingQueue

, 前者使用锁实现, 后者使用CAS 非阻塞算法实现

阻塞队列: 当阻塞队列进行插入数据时, 如果队列已经满了, 线程将会阻塞等待直到队列非满, 从阻塞队列取数据时, 如果队列是空的, 那么线程将会阻塞等待队列非空, 

**一般使用LinkedList 和stack, 使用 移除poll或者检查peek, 最好先检查是否为空**

if

| 方法\处理方式 | 抛出异常 | 返回特殊值 | 一直阻塞 | 超时退出             |
| ------------- | -------- | ---------- | -------- | -------------------- |
| 插入方法      | add      | offer      | put      | offer(e, time, unit) |
| 移除方法      | remove   | poll       | take     | poll(time, unit)     |
| 检查方法      | element  | peek       | 不可用   | 不可用               |

BlockingQueue 是一个借口, 实现类:

* ArrayBlockingQueue, 有数量上限的数组,同时只有一个线程可以入队,出队
  * LInkedBlockingQueue: 如果没有上限, 使用Integer.MAX_VALUE,同时可以有两个线程入队,出队
  * PriorityBlockingQueue 同时只有一个线程可以入队,出队,可以一直put,没有上界,始终保证出队的元素是优先级最高的元素,为了避免在扩容操作时候其他线程不能进行出队操作,实现上使用了先释放锁,然后通过 cas 保证同时只有一个线程可以扩容成功。
  * SynchronousQueue 只能有一个元素,使用CAS
  * DelayQueue, 元素必须实现java.util.concurrent.Delayed

#### 非阻塞队列

在底层,非阻塞队列使用的是 CAS(compare and swap)来实现线程执行的非阻塞。

一般乐观锁的性能更好,乐观锁的竞争则只发生在最小的并发冲突处,

CAS 只是一种手段,既可以实现乐观锁,也可以实现悲观锁。

ConcurrentLinkedQueue

#### ConcurrentSkipListMap

这是TreeMap的线程安全的集合,ConcurrentSkipListMap 是通过跳表实现的,而 TreeMap 是通过红黑树实现的。时间复杂度(O(logn)),

#### atomic

非阻塞算法来实现并发控制的

**AtomicBoolean**  compareAndSet(false, true)提供了原子性操作,get(),set(), getAndSet(), compareAndSet(), 

**AtomicInteger** ++i i++都不是线程安全的,getAndSet(), getAndIncrement(), getAndDecrement(), getAndAdd(int delta)

**AtomicIntegerArray** 

**AtomicLong** 

**AtomicLongArray**







#### volatile

禁止指令重排序, 可见性, 不保证原子性, 而synchronized保证了原子性

编译器会提高程序的运行效率,将经常被访问的值缓存起来,程序重复读取这个值的时候,会在缓存中读取,不会读取原始值. 

当遇到多线程时,变量的值可能被其他线程改变了,缓存的值不会改变,造成读取的值和实际的变量值不一致.

使用volatile 修饰的值,系统每次用到都会读取原始值

多线程的内存模型：main memory（主存）、working memory（线程栈），在处理数据时，线程会把值从主存load到本地栈，完成操作后再save回去(volatile关键词的作用：每次针对该变量的操作都激发一次load and save)。

```
public class MyThread implements Runnable{
    private volatile Boolean flag;
    public void stop(){
        flag = false;
        
    }
    public void run(){
        while(flag){
            ;// do something
        }
    }
}
```

#### 

#### Semaphore

控制线程个数,在需要多线程执行的代码中加入Semaphore. 初始化为最大的线程数量

semaphore.acquire()  semaphore.release()

三个线程A,B,C, A在bc 前面执行, 可以在A执行的时候semaphore.release(2); 然后bc semaphore.acquire();

#### 多线程同步的方法

1. synchronized 方法,每个对象都有一个对象锁与之相关联, 该锁表明对象在任何时候只允许被一个线程所拥有,当一个县城调用对象的一段sybchronized代码时, 需要先获取这个锁,然后去执行相应的代码,执行完后释放锁.
2. wait() notify():  当一个线程执行synchronized 代码的时候,调用对象的wait() 方法, 释放对象锁, 进入等待状态,并且可以调用notify() 方法或者notifyAlly() 通知等在等待的线程.
3. Lock jdk 5 后提供了一个ReentrantLock 锁. await   signalAll()

#### 死锁

所谓死锁是指多个线程因竞争资源而造成的一种僵局(互相等待),若无外力作用,这些进
程都将无法向前推进。 互斥条件:不剥夺条件:请求和保持条件:循环等待条件:

#### Java 中多线程通信

1. 共享变量
2. wait/notify机制
3. BlockingQueue

#### sleep() wait()

sleep() 让线程暂停执行一段时间,时间一到,自动恢复.比如时钟1s 打印一次当前时间. 不会释放自己所占有的锁

wait() 会使当前拥有对象锁的进程等待,直到其他线程调用notify() 方法. 不过也可以指定一个时间,让他自动醒过来. 会释放自己所占有的锁.

由于sleep() 不会释放锁,更容易发生死锁,

Thread.wait() 可以设置时间

sleep(1000), 当睡眠时间结束后,线程会返回到可运行状态, 不是运行状态,还需要等待CPU 调度执行,sleep() 方法不能保证线程水边到期后开始执行.

#### synchronized lock

synchronized 使用Object 对象本身的notify wait notifyAll 调度机制, 而Lock 使用Condition 进行线程之间的调度.

1. 用法不同, synchronized 既可以加在方法上,也可以在特定代码块上,Lock需要显示指定起始位置和终止位置.
2. synchronized不会因为异常而没有释放锁,Lock 需要手动解锁,在finally 块中释放 

#### 事务处理

禁止自动提交setAutoCOmmit(false), 然后就可以把多个数据库操作的表达式作为一个事务,在操作完成后调用commit() 方法实现整体提交,如果其中一个表达式操作失败,就会抛出异常而不会调用commit() 方法.在这种情况下,就可以在异常捕获的代码块中调用rollback() 方法进行事务回滚.

1. TRANSACTION_NONE JOB 不支持事务
2. TRANSACTION_READ_UNCOMMITTED 未提交读,读脏数据
3. TRANSACTION_READ_COMMITTED  已提交读,读取未提交数据是不允许的.
4. TRANSACTION_REPEATABLE_READ  可重复读
5. TRANSACTION_SERIALIZABLE 可序列化, 

原子性: 原子性要求事务必须被完整执行

一致性: 

隔离性

持久性

#### Class.forName

作用是把类加载到JVM中,它返回一个相关的Class对象,并加载这个类,JVM会执行该类的静态代码块.

#### 数据库连接池

建立与数据库的链接是一个耗时的操作,,数据库连接个数是有数的,用户经常经理连接却忘记释放.

#### IOC

控制反转,依赖注入,是一种降低对象之间耦合关系的设计思想.

#### JVM

方法区是静态分配的,编译器将变量绑定在某个存储位置上,而且这些绑定不会在运行时改变。
常数池,源代码中的命名常量、String 常量和 static 变量保存在方法区。

Java Stack 是一个逻辑概念,特点是后进先出。

Java 堆分配(heap allocation)意味着以随意的顺序,在运行时进行存储空间分配和收回的内存管理模型

### 海量数据处理

#### Hash

hash 主要用来进行快速存取, 在O(1)时间内查找到目标元素,或者判断是否存在.

#### bit-map

找到最大值, 创建一个数组, 便利数据,把数据作为下标,在数组中找到的下标,设置为1.

#### bloom filter

m 位的数组, 定义k个hash函数,每个函数都可以将元素映射到数组中的某一位,当向集合中插入一个元素,将求出k 个hash 值,将数组中k 个hash 值下标设置为1, 当查找某个元素是否在集合中的时候,先计算k个hash 值,如果k 个hash 值下标在数组中都为1, 则有可能在集合中,如果有的不是1, 则一定不在集合中.

#### 倒排索引

主要用在 文档检索

正向索引: 存储每个文档的单词的列表

倒排索引: 单词指向了包含它的文档.

#### 外排序

当需要排序的对象很多,可以一部分一部分地调入内存处理.

#### 最大的10000个数

首先如果有很多重复的数字, 先使用hash 法, 去重复

1 构建一个最小堆, 10000个数字, 遍历完后，堆中的10000个数就是所需的最大的10000个。建堆时间复杂度是O（mlogm），算法的时间复杂度为O（nmlogm）（n为10亿，m为10000）。

2  局部淘汰, 用一个容器保存前10000个数字, 将剩余的所有数字——与容器内的最小数字相比，如果所有后续的元素都比容器内的10000个数还小，那么容器内这个10000个数就是最大10000个数。如果某一后续元素比容器内最小数字大，则删掉容器内最小元素，并将该元素插入容器，最后遍历完这1亿个数，得到的结果容器中保存的数即为最终结果了。此时的时间复杂度为O（n+m^2），其中m为容器的大小，即10000。

3 分治 , 类似于快速排序

#### 词频率 的 top k

统计搜索最热门的10个查询词, 歌曲库中统计下载最高的前10 首歌

先将数据按照Hash 方法分解为多个小数据集, 然后使用Trie 树 或者 hash 统计每个小数据集中的词频,再用小堆求出每个数据集中出现频率最高的前K 个数, 最后在所有top K 中求出最终的top K.

#### 根据机器配置选择解决方案

1 单机+单核+足够大内存 最小堆, 局部淘汰, 分治

2 单机+多核+足够大内存, 使用hash 方法分割成n个partition, 每个partition 提交给一个线程处理, 最后一个线程归并

3 单机+单核+受限内存 hash分割, 然后 使用方法1

4 多机+受限内存, 

## 数据库

#### 事务的4个基本特征

原子性, 事务中包括的操作被看做一个逻辑单元, 这个逻辑单元中的操作要么所有成功, 全部失败.

一致性: 事务开始和结束后, 数据库的完整性约束没有被破坏

隔离性: 同一时间, 只允许一个事务请求同一数据, 不同的事务之间彼此没有任何干扰

持久性: 事务完成后, 所有更新持久化.

#### 事务并发的问题

脏读: 事务A 读取到了事务B更新的数据, 然后B回滚, 导致A读到的数据是不可用的数据

不可重复读: 事务A多次读取到同一数据, 事务B对数据更新并提交, 导事务A读到的数据不一致(sql 结果发生了行内变化)

幻读: 事务A 多次执行同一个sql, 结果发生了数量变化.

脏读很好理解, 不可重复读侧重于修改, 幻读侧重于新增, 解决不可重复读只需要锁住满足条件的行, 解决幻读需要锁表

数据库隔离级别

|                  | 脏读 | 不可重复读 | 幻读 |
| ---------------- | ---- | ---------- | ---- |
| read uncommitted |      |            |      |
| read committed   | ok   |            |      |
| repeatable read  | ok   | ok         |      |
| serializable     | ok   | ok         | ok   |

mysql 默认repeatable read, 而且解决了幻读, 也就是说在一个事务中, 前后查询到的相同

在repeatable read 中, 在一个B客户端中start transaction, 然后在另一个A客户端更新balance 为1000, 在B客户端中 update account set balance = balance  -100 where name = 'li'; 结果是900.

REPEATABLE READ隔离级别保证了在同样的查询条件下，同一个事务中的两个查询。第二次读取的内容肯定包换第一次读到的内容。注：Mysql的默认隔离级别就是Repeatable read

#### 悲观锁和乐观锁

悲观锁:

读取数据时给加锁，其他事务无法改动这些数据。

改动删除数据时也要加锁，其他事务无法读取这些数据。

乐观锁: 

乐观锁，大多是基于数据版本号（ Version ）记录机制实现。何谓数据版本号？即为数据添加一个版本号标识，在基于数据库表的版本号解决方式中，通常是通过为数据库表添加一个 “version” 字段来实现。

读取出数据时，将此版本号号一同读出，之后更新时，对此版本号号加一。

此时。将提交数据的版本号数据与数据库表相应记录的当前版本号信息进行比对，假设提交的数据版本号号大于数据库表当前版本号号，则予以更新。否则觉得是过期数据。

#### 二叉树

二叉搜索树: 缺点是可能不平衡, 最坏情况是成为一个单向链表

平衡二叉搜索树: 左右子树深度之差的绝对值不超过1;

红黑树: 

AVL是严格平衡树，因此在增加或者删除节点的时候，根据不同情况，旋转的次数比红黑树要多；
红黑是弱平衡的，用非严格的平衡来换取增删节点时候旋转次数的降低；

所以简单说，搜索的次数远远大于插入和删除，那么选择AVL树，如果搜索，插入删除次数几乎差不多，应该选择RB树。

B- 树: 平衡多路搜索树:搜索从根节点开始, 对节点内的关键字序列进行二分查找, 如果命中就结束, 否则进入查询关键字所属范围的儿子节点, 重复知道对应的儿子节点是空(叶子节点)

特点: 1 关键字分布在整颗树中, 2 搜索性能等价于 二分查找



B+ 树, 一种多路搜索树, 包含根节点, 内部节点, 叶子节点, 所有非叶子节点都是索引部分, 在B+ 树中搜索, 只有到达叶子节点才命中, 性能也相当与二分查找, 叶子节点从小到大用链表链接

非叶子节点的子树和节点包含的关键字数量相同,非叶节点存储的是子树里最小的关键字。同时数据节点只存在于叶子节点中，且叶子节点间增加了横向的指针，这样顺序遍历所有数据将变得非常容易。

#### 为什么说B+ 比B 更适合文件索引和数据库索引

B+ 树磁盘读写代价更低: 因为非叶子节点没有数据信息, 所以一个内部节点所占磁盘空间更小, 一次性读入内存有更多的非叶子节点. 

查询效率更加稳定: 非叶子节点没有指向文件内容的指针, 只有叶子节点才有, 所有查询路径长度相同, 导致查询效率相当

小结:  B- 树, 多路搜索树, 每个节点存储M/2 到M 个关键字, 非叶子节点存储指向关键字范围的子节点, 所有关键字在整棵树中出现, 且只出现一次, 非叶子节点可以命中, B+ 树, 为叶子节点增加链表指针, 所有关键字都在叶子节点出现, 非叶子节点仅为索引, 存储关键字以指示搜索的方向,B+树总是到叶子节点才命中, B*树, 在B+ 树基础上, 为非叶子节点增加链表指针, 将节点最低利用率从1/2 提高到2/3. 

#### 数据库索引: 

索引文件和数据文件是分离的, 使用了B+ 树, 树的叶子节点有data 域, 保存了完整的数据记录, 真是数据库中B+ 树应该是非常扁平的, 可能树的高度为3, 每个非叶子节点存储1200 个键值(可能).

IO次数取决于b+数的高度h，

### 另外一片mysql 索引文章

#### b- 

B 是balance 的意思, b-  树是一种多路自平衡的搜索树它类似普通的平衡二叉树, 他与二叉树不同的地方在于B-树允许每个节点有更多的子节点,

特点: 

* 所有键值分布在整颗树中
* 任何一个关键字出现且只出现一个节点中
* 搜索有可能在非叶子节点结束
* 时间复杂度相当于二分查找

#### B+ 

是b- 的变体, 也是一种多路搜索树, 与b- 不同之处在于

* 所有关键字存储在叶子节点,
* 为所有叶子节点加了一个链指针

#### 为什么使用b树

索引查找过程中就要产生磁盘I/O消耗,相对于内存存取，I/O存取的消耗要高几个数量级,索引的结构组织要尽量减少查找过程中磁盘I/O的存取次数。

假设 B-Tree 的高度为 h,B-Tree中一次检索最多需要h-1次I/O（根节点常驻内存），渐进复杂度为O(h)=O(logdN)O(h)=O(logdN)。一般实际应用中，出度d是非常大的数字，通常超过100，因此h非常小（通常不超过3）。

而红黑树这种结构，h明显要深的多。由于逻辑上很近的节点（父子）物理上可能很远，无法利用局部性，所以红黑树的I/O渐进复杂度也为O(h)，效率明显比B-Tree差很多。

#### 为什么使用B+ 树

1. B+树更适合外部存储,由于内节点无 data 域,一个结点可以存储更多的内结点,每个节点能索引的范围更大更精确,也意味着 B+树单次磁盘IO的信息量大于B-树,I/O效率更高。
2. Mysql是一种关系型数据库，区间访问是常见的一种情况，B+树叶节点增加的链指针,加强了区间访问性，可使用在范围区间查询等，而B-树每个节点 key 和 data 在一起，则无法区间查找。