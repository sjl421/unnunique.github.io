## lambda 表达式（又被称为"闭包"或者"匿名方法"）
```
例子：
(int x, int y) -> x + y
() -> 42
(String s) -> { System.out.println(s); }

语法：
参数列表 +  “->”  + 函数体
参数列表可有可无，
函数体可以是表达式或者语句块。
    ruturn 语句把控制权交给匿名方法的调用者
    break 和continute，只可以在循环中使用
```
##### 函数式接口：只拥有一个方法的接口，称为函数式接口：比如Runnable, Comparator。（即SAM类型，但抽象方法类型）
``` 
lamda 表达式基于如下原有的Java SE 7 中的函数式接口,可以直接用lambda 表达式。
java.lang.Runnable
java.util.concurrent.Callable
java.security.PrivilegedAction
java.util.Comparator
java.io.FileFilter
java.beans.PropertyChangeListener

除此之外java 8 中新增加了java.util.function的包，它里面包含了常用的函数式接口。
Predicate<T>——接收T对象并返回boolean
Consumer<T>——接收T对象，不返回值
Function<T, R>——接收T对象，返回R对象
Supplier<T>——提供T对象（例如工厂），不接收值
UnaryOperator<T>——接收T对象，返回T对象
BinaryOperator<T>——接收两个T对象，返回T对象
除了上面的这些基本的函数式接口，我们还提供了一些针对原始类型（Primitive type）的特化
（Specialization）函数式接口，例如IntSupplier和LongBinaryOperator。
（我们只为int、long和double提供了特化函数式接口，如果需要使用其它
原始类型则需要进行类型转换）同样的我们也提供了一些针对多个参数的函数式接口，
例如BiFunction<T, U, R>，它接收T对象和U对象，返回R对象。

表达式函数体适合小型lambda表达式，它消除了return关键字，使得语法更加简洁。
lambda表达式也会经常出现在嵌套环境中，比如说作为方法的参数。
为了使lambda表达式在这些场景下尽可能简洁，我们去除了不必要的分隔符。
不过在某些情况下我们也可以把它分为多行，
然后用括号包起来，就像其它普通表达式一样。
下面是一些出现在语句中的lambda表达式：
FileFilter java = (File f) -> f.getName().endsWith("*.java");

String user = doPrivileged(() -> System.getProperty("user.name"));

new Thread(() -> {
  connectToService();
  sendNotification();
}).start();
```
### 4. 目标类型（Target typing）
```
需要注意的是，函数式接口的名称并不是lambda表达式的一部分。那么问题来了，
对于给定的lambda表达式，它的类型是什么？答案是：它的类型是由其上下文
推导而来。例如，下面代码中的lambda表达式类型是ActionListener：
ActionListener l = (ActionEvent e) -> ui.dazzle(e.getModifiers());
这就意味着同样的lambda表达式在不同上下文里可以拥有不同的类型：

Callable<String> c = () -> "done";  
PrivilegedAction<String> a = () -> "done";
第一个lambda表达式() -> "done"是Callable的实例，而第二个lambda表达式则是
PrivilegedAction的实例。

编译器负责推导lambda表达式的类型。它利用lambda表达式所在上下文所期待
的类型进行推导，这个被期待的类型被称为目标类型。
lambda表达式只能出现在目标类型为函数式接口的上下文中。

当然，lambda表达式对目标类型也是有要求的。
编译器会检查lambda表达式的类型和目标类型的方法签名
（method signature）是否一致。
当且仅当下面所有条件均满足时，lambda表达式才可以被赋给目标类型T：

T是一个函数式接口
lambda表达式的参数和T的方法参数在数量和类型上一一对应
lambda表达式的返回值和T的方法返回值相兼容（Compatible）
lambda表达式内所抛出的异常和T的方法throws类型相兼容
由于目标类型（函数式接口）已经“知道”lambda表达式的形式参数
（Formal parameter）类型，所以我们没有必要把已知类型再重复一遍。
也就是说，lambda表达式的参数类型可以从目标类型中得出：

Comparator<String> c = (s1, s2) -> s1.compareToIgnoreCase(s2);
在上面的例子里，编译器可以推导出s1和s2的类型是String。
此外，当lambda的参数只有一个而且它的类型可以被推导得知时，
该参数列表外面的括号可以被省略：

FileFilter java = f -> f.getName().endsWith(".java");

button.addActionListener(e -> ui.dazzle(e.getModifiers()));
这些改进进一步展示了我们的设计目标：“不要把高度问题转化成宽度问题。”
我们希望语法元素能够尽可能的少，以便代码的读者能够直达lambda表达式的核心部分。

lambda表达式并不是第一个拥有上下文相关类型的Java表达式：
泛型方法调用和“菱形”构造器调用也通过目标类型来进行类型推导：

List<String> ls = Collections.emptyList();
List<Integer> li = Collections.emptyList();

Map<String, Integer> m1 = new HashMap<>();
Map<Integer, String> m2 = new HashMap<>();

```
### 5. 目标类型的上下文（Contexts for target typing）
```
之前我们提到lambda表达式智能出现在拥有目标类型的上下文中。
下面给出了这些带有目标类型的上下文：

变量声明
赋值
返回语句
数组初始化器
方法和构造方法的参数
lambda表达式函数体
条件表达式（? :）
转型（Cast）表达式
在前三个上下文（变量声明、赋值和返回语句）里，目标类型即是被赋值或被返回的类型：

Comparator<String> c;
c = (String s1, String s2) -> s1.compareToIgnoreCase(s2);

public Runnable toDoLater() {
  return () -> {
    System.out.println("later");
  }
}
数组初始化器和赋值类似，只是这里的“变量”变成了数组元素，
而类型是从数组类型中推导得知：

filterFiles(new FileFilter[] {
              f -> f.exists(), f -> f.canRead(), f -> f.getName().startsWith("q")
            });
方法参数的类型推导要相对复杂些：目标类型的确认会涉及到其它两个语言特性
：重载解析（Overload resolution）和参数类型推导（Type argument inference）。

重载解析会为一个给定的方法调用（method invocation）寻找最合适的方法声明
（method declaration）。由于不同的声明具有不同的签名，当lambda表达式作为方
法参数时，重载解析就会影响到lambda表达式的目标类型。编译器会通过它所得
之的信息来做出决定。如果lambda表达式具有显式类型（参数类型被显式指定），
编译器就可以直接 使用lambda表达式的返回类型；如果lambda表达式具有隐式类
型（参数类型被推导而知），重载解析则会忽略lambda表达式函数体而只依赖
lambda表达式参数的数量。

如果在解析方法声明时存在二义性（ambiguous），我们就需要利用转型
（cast）或显式lambda表达式来提供更多的类型信息。如果lambda表达式
的返回类型依赖于其参数的类型，那么lambda表达式函数体有可能可以
给编译器提供额外的信息，以便其推导参数类型。

List<Person> ps = ...
Stream<String> names = ps.stream().map(p -> p.getName());
在上面的代码中，ps的类型是List<Person>，所以ps.stream()的返
回类型是Stream<Person>。map()方法接收一个类型为Function<T, R>的函数
式接口，这里T的类型即是Stream元素的类型，也就是Person，而R的类型未知。
由于在重载解析之后lambda表达式的目标类型仍然未知，
我们就需要推导R的类型：通过对lambda表达式函数体进行类型检查，
我们发现函数体返回String，因此R的类型是String，因而map()返回Stream<String>
。绝大多数情况下编译器都能解析出正确的类型，但如果碰到无法解析的情况，我们则需要：

使用显式lambda表达式（为参数p提供显式类型）以提供额外的类型信息
把lambda表达式转型为Function<Person, String>
为泛型参数R提供一个实际类型。（.<String>map(p -> p.getName())）
lambda表达式本身也可以为它自己的函数体提供目标类型，也就是说lambda表
达式可以通过外部目标类型推导出其内部的返回类型，这意味着我们可以方便
的编写一个返回函数的函数：

Supplier<Runnable> c = () -> () -> { System.out.println("hi"); };
类似的，条件表达式可以把目标类型“分发”给其子表达式：

Callable<Integer> c = flag ? (() -> 23) : (() -> 42);
最后，转型表达式（Cast expression）可以显式提供lambda表达式的类型
，这个特性在无法确认目标类型时非常有用：

// Object o = () -> { System.out.println("hi"); }; 这段代码是非法的
Object o = (Runnable) () -> { System.out.println("hi"); };
除此之外，当重载的方法都拥有函数式接口时，转型可以帮助解决重载解析时出现的二义性。

目标类型这个概念不仅仅适用于lambda表达式，泛型方法调用和“菱形”
构造方法调用也可以从目标类型中受益，下面的代码在Java SE 7是非法的，
但在Java SE 8中是合法的：

List<String> ls = Collections.checkedList(new ArrayList<>(), String.class);

Set<Integer> si = flag ? Collections.singleton(23) : Collections.emptySet();
```
### 6. 词法作用域（Lexical scoping）
```
在内部类中使用变量名（以及this）非常容易出错。内部类中通过继
承得到的成员（包括来自Object的方法）可能会把外部类的成员掩盖
（shadow），此外未限定（unqualified）的this引用会指向内部类自己而非外部类。

相对于内部类，lambda表达式的语义就十分简单：它不会从超类
（supertype）中继承任何变量名，也不会引入一个新的作用域。
lambda表达式基于词法作用域，也就是说lambda表达式函数体里面
的变量和它外部环境的变量具有相同的语义（也包括lambda表达式的形式参
数）。此外，'this'关键字及其引用在lambda表达式内部和外部也拥有相同的语义。

为了进一步说明词法作用域的优点，请参考下面的代码，它会把"Hello, world!"打印两遍：

public class Hello {
  Runnable r1 = () -> { System.out.println(this); }
  Runnable r2 = () -> { System.out.println(toString()); }

  public String toString() {  return "Hello, world"; }

  public static void main(String... args) {
    new Hello().r1.run();
    new Hello().r2.run();
  }
}
与之相类似的内部类实现则会打印出类似Hello$1@5b89a773
和Hello$2@537a7706之类的字符串，这往往会使开发者大吃一惊。

基于词法作用域的理念，lambda表达式不可以掩盖任何其所在上下文中
的局部变量，它的行为和那些拥有参数的控制流结构（例如for循环和catch从句）一致。

个人补充：这个说法很拗口，所以我在这里加一个例子以演示词法作用域：

int i = 0;
int sum = 0;
for (int i = 1; i < 10; i += 1) { //这里会出现编译错误，因为i
已经在for循环外部声明过了
  sum += i;
}
```
### 7. 变量捕获（Variable capture）

```
在Java SE 7中，编译器对内部类中引用的外部变量（即捕获的变量）要求非
常严格：如果捕获的变量没有被声明为final就会产生一个编译错误。我们现
在放宽了这个限制——对于lambda表达式和内部类，我们允许在其中捕获那
些符合有效只读（Effectively final）的局部变量。

简单的说，如果一个局部变量在初始化后从未被修改过，那么它就符合有效
只读的要求，换句话说，加上final后也不会导致编译错误的局部变量就是有效只读变量。

Callable<String> helloCallable(String name) {
  String hello = "Hello";
  return () -> (hello + ", " + name);
}
对this的引用，以及通过this对未限定字段的引用和未限定方法的
调用在本质上都属于使用final局部变量。包含此类引用的lambda表
达式相当于捕获了this实例。在其它情况下，lambda对象不会保留任何对this的引用。

这个特性对内存管理是一件好事：内部类实例会一直保留一个对其外
部类实例的强引用，而那些没有捕获外部类成员的lambda表达式则不
会保留对外部类实例的引用。要知道内部类的这个特性往往会造成内存泄露。

尽管我们放宽了对捕获变量的语法限制，但试图修改捕获变量的行为仍
然会被禁止，比如下面这个例子就是非法的：

int sum = 0;
list.forEach(e -> { sum += e.size(); });
为什么要禁止这种行为呢？因为这样的lambda表达式很容易引起race condition。
除非我们能够强制（最好是在编译时）这样的函数不能离开其当前线程，
但如果这么做了可能会导致更多的问题。简而言之，lambda表达式对值封闭，对变量开放。

个人补充：lambda表达式对值封闭，对变量开放的原文是
：lambda expressions close over values, not variables，
我在这里增加一个例子以说明这个特性：

int sum = 0;
list.forEach(e -> { sum += e.size(); }); // Illegal, close over values

List<Integer> aList = new List<>();
list.forEach(e -> { aList.add(e); }); // Legal, open over variables
lambda表达式不支持修改捕获变量的另一个原因是
我们可以使用更好的方式来实现同样的效果：
使用规约（reduction）。java.util.stream包提供了
各种通用的和专用的规约操作（例如sum、min和max），
就上面的例子而言，我们可以使用规约操作
（在串行和并行下都是安全的）来代替forEach：

int sum = list.stream()
              .mapToInt(e -> e.size())
              .sum();
sum()等价于下面的规约操作：

int sum = list.stream()
              .mapToInt(e -> e.size())
              .reduce(0 , (x, y) -> x + y);
规约需要一个初始值（以防输入为空）和一个操作符（
在这里是加号），然后用下面的表达式计算结果：

0 + list[0] + list[1] + list[2] + ...
规约也可以完成其它操作，比如求最小值、最大值和
乘积等等。如果操作符具有可结合性（associative），那
么规约操作就可以容易的被并行化。所以，与其支持一个本
质上是并行而且容易导致race condition的操作，我们选择在
库中提供一个更加并行友好且不容易出错的方式来进行累积（accumulation）。
```
### 8. 方法引用（Method references）
```
lambda表达式允许我们定义一个匿名方法，并允许我们以函数式
接口的方式使用它。我们也希望能够在已有的方法上实现同样的特性。

方法引用和lambda表达式拥有相同的特性（例如，它们都需要一
个目标类型，并需要被转化为函数式接口的实例），不过我们并不需
要为方法引用提供方法体，我们可以直接通过方法名称引用已有方法。

以下面的代码为例，假设我们要按照name或age为Person数组进行排序：

class Person {
  private final String name;
  private final int age;

  public int getAge() { return age; }
  public String getName() {return name; }
  ...
}

Person[] people = ...
Comparator<Person> byName = Comparator.comparing(p -> p.getName());
Arrays.sort(people, byName);
在这里我们可以用方法引用代替lambda表达式：

Comparator<Person> byName = Comparator.comparing(Person::getName);
这里的Person::getName可以被看作为lambda表达式的简写形式。
尽管方法引用不一定（比如在这个例子里）会把语法变的更紧凑，
但它拥有更明确的语义——如果我们想要调用的方法拥有一个名字，
我们就可以通过它的名字直接调用它。

因为函数式接口的方法参数对应于隐式方法调用时的参数，所以被
引用方法签名可以通过放宽类型，装箱以及组织到参数数组中的方
式对其参数进行操作，就像在调用实际方法一样：

Consumer<Integer> b1 = System::exit;    // void exit(int status)
Consumer<String[]> b2 = Arrays:sort;    // void sort(Object[] a)
Consumer<String> b3 = MyProgram::main;  // void main(String... args)
Runnable r = Myprogram::mapToInt        // void main(String... args)
```
###9. 方法引用的种类（Kinds of method references）
```
方法引用有很多种，它们的语法如下：

静态方法引用：ClassName::methodName
实例上的实例方法引用：instanceReference::methodName
超类上的实例方法引用：super::methodName
类型上的实例方法引用：ClassName::methodName
构造方法引用：Class::new
数组构造方法引用：TypeName[]::new
对于静态方法引用，我们需要在类名和方法名之间加入::分隔符，例如Integer::sum。

对于具体对象上的实例方法引用，我们则需要在对象名和方法名之间加入分隔符：

Set<String> knownNames = ...
Predicate<String> isKnown = knownNames::contains;
这里的隐式lambda表达式（也就是实例方法引用）会从knownNames中
捕获String对象，而它的方法体则会通过Set.contains使用该String对象。

有了实例方法引用，在不同函数式接口之间进行类型转换就变的很方便：

Callable<Path> c = ...
Privileged<Path> a = c::call;
引用任意对象的实例方法则需要在实例方法名称和其所属类型名称间加上分隔符：

Function<String, String> upperfier = String::toUpperCase;
这里的隐式lambda表达式（即String::toUpperCase实例方法引用）
有一个String参数，这个参数会被toUpperCase方法使用。

如果类型的实例方法是泛型的，那么我们就需要在::分隔符前提供类
型参数，或者（多数情况下）利用目标类型推导出其类型。

需要注意的是，静态方法引用和类型上的实例方法引用拥有一样的语法。
编译器会根据实际情况做出决定。

一般我们不需要指定方法引用中的参数类型，因为编译器往往可以推导
出结果，但如果需要我们也可以显式在::分隔符之前提供参数类型信息。

和静态方法引用类似，构造方法也可以通过new关键字被直接引用：

SocketImplFactory factory = MySocketImpl::new;
如果类型拥有多个构造方法，那么我们就会通过目标类型的方法参
数来选择最佳匹配，这里的选择过程和调用构造方法时的选择过程是一样的。

如果待实例化的类型是泛型的，那么我们可以在类型名称之后提供
类型参数，否则编译器则会依照"菱形"构造方法调用时的方式进行推导。

数组的构造方法引用的语法则比较特殊，为了便于理解，你可以假
想存在一个接收int参数的数组构造方法。参考下面的代码：

IntFunction<int[]> arrayMaker = int[]::new;
int[] array = arrayMaker.apply(10) // 创建数组 int[10]
```
### 10. 默认方法和静态接口方法（Default and static interface methods）
```
lambda表达式和方法引用大大提升了Java的表达能力（expressiveness）
，不过为了使把代码即数据（code-as-data）变的更加容易，
我们需要把这些特性融入到已有的库之中，以便开发者使用。

Java SE 7时代为一个已有的类库增加功能是非常困难的。具体的说
，接口在发布之后就已经被定型，除非我们能够一次性更新所有该接
口的实现，否则向接口添加方法就会破坏现有的接口实现。默认方法
（之前被称为虚拟扩展方法或守护方法）的目标即是解决这个问题，
使得接口在发布之后仍能被逐步演化。

这里给出一个例子，我们需要在标准集合API中增加针对lambda的方法。
例如removeAll方法应该被泛化为接收一个函数式接口Predicate，
但这个新的方法应该被放在哪里呢？我们无法直接在Collection接口
上新增方法——不然就会破坏现有的Collection实现。
我们倒是可以在Collections工具类中增加对应的静态方法，
但这样就会把这个方法置于“二等公民”的境地。

默认方法利用面向对象的方式向接口增加新的行为。
它是一种新的方法：接口方法可以是抽象的或是默认的
。默认方法拥有其默认实现，实现接口的类型通过继承得到该默认实现
（如果类型没有覆盖该默认实现）。此外，默认方法不是抽象方法，
所以我们可以放心的向函数式接口里增加默认方法，而不用担心函数
式接口的单抽象方法限制。

下面的例子展示了如何向Iterator接口增加默认方法skip：

interface Iterator<E> {
  boolean hasNext();
  E next();
  void remove();

  default void skip(int i) {
    for ( ; i > 0 && hasNext(); i -= 1) next();
  }
}
根据上面的Iterator定义，所有实现Iterator的类型都会自动继承skip方法。
在使用者的眼里，skip不过是接口新增的一个虚拟方法。
在没有覆盖skip方法的Iterator子类实例上调用skip会执行skip的默认实现：
调用hasNext和next若干次。子类可以通过覆盖skip来提供更好的实现
——比如直接移动游标（cursor），或是提供为操作提供原子性（Atomicity）等。

当接口继承其它接口时，我们既可以为它所继承而来的抽象方法提供一
个默认实现，也可以为它继承而来的默认方法提供一个新的实现，
还可以把它继承而来的默认方法重新抽象化。

除了默认方法，Java SE 8还在允许在接口中定义静态方法。这
使得我们可以从接口直接调用和它相关的辅助方法（Helper method），
而不是从其它的类中调用（之前这样的类往往以对应接口的复数命名
，例如Collections）。比如，我们一般需要使用静态辅助方法生成实现Comparat
or的比较器，在Java SE 8中我们可以直接把该静态方法定义在Comparator接口中：

public static <T, U extends Comparable<? super U>>
    Comparator<T> comparing(Function<T, U> keyExtractor) {
  return (c1, c2) -> keyExtractor.apply(c1).compareTo(keyExtractor.apply(c2));
}
```

###11. 继承默认方法（Inheritance of default methods）
```
和其它方法一样，默认方法也可以被继承，大多数情况下这种继承行为和我们
所期待的一致。不过，当类型或者接口的超类拥有多个具有相同签名的方法时，
我们就需要一套规则来解决这个冲突：

类的方法（class method）声明优先于接口默认方法。无论该方法是具体的还是抽象的。
被其它类型所覆盖的方法会被忽略。这条规则适用于超类型共享一个公共祖先的情况。
为了演示第二条规则，我们假设Collection和List接口均提供了removeAll的默
认实现，然后Queue继承并覆盖了Collection中的默认方法。在下面的implement
从句中，List中的方法声明会优先于Queue中的方法声明：

class LinkedList<E> implements List<E>, Queue<E> { ... }
当两个独立的默认方法相冲突或是默认方法和抽象方法相冲突时会产生编译错误
。这时程序员需要显式覆盖超类方法。一般来说我们会定义一个默认方法，然后
在其中显式选择超类方法：

interface Robot implements Artist, Gun {
  default void draw() { Artist.super.draw(); }
}
super前面的类型必须是有定义或继承默认方法的类型。这种方法调用并不只限
于消除命名冲突——我们也可以在其它场景中使用它。

最后，接口在inherits和extends从句中的声明顺序和它们被实现的顺序无关。
```
###12. 融会贯通（Putting it together）
```
我们在设计lambda时的一个重要目标就是新增的语言特性和库特性
能够无缝结合（designed to work together）。接下来，我们通过一
个实际例子（按照姓对名字列表进行排序）来演示这一点：

比如说下面的代码：

List<Person> people = ...
Collections.sort(people, new Comparator<Person>() {
  public int compare(Person x, Person y) {
    return x.getLastName().compareTo(y.getLastName());
  }
})
冗余代码实在太多了！

有了lambda表达式，我们可以去掉冗余的匿名类：

Collections.sort(people,
                 (Person x, Person y) -> x.getLastName().
                 compareTo(y.getLastName()));
尽管代码简洁了很多，但它的抽象程度依然很差：开发者仍然需要进
行实际的比较操作（而且如果比较的值是原始类型那么情况会更糟），
所以我们要借助Comparator里的comparing方法实现比较操作：

Collections.sort(people, Comparator.comparing((Person p) -> p.getLastName()));
在类型推导和静态导入的帮助下，我们可以进一步简化上面的代码：

Collections.sort(people, comparing(p -> p.getLastName()));
我们注意到这里的lambda表达式实际上是getLastName的代理（forwarder），
于是我们可以用方法引用代替它：

Collections.sort(people, comparing(Person::getLastName));
最后，使用Collections.sort这样的辅助方法并不是一个好主意：
它不但使代码变的冗余，也无法为实现List接口的数据结构提供特定
（specialized）的高效实现，而且由于Collections.sort方法不属于List接口，
用户在阅读List接口的文档时不会察觉在另外的Collections类中还有
一个针对List接口的排序（sort()）方法。

默认方法可以有效的解决这个问题，我们为List增加默认方法sort()，
然后就可以这样调用：

people.sort(comparing(Person::getLastName));;
此外，如果我们为Comparator接口增加一个默认方法reversed()（
产生一个逆序比较器），我们就可以非常容易的在前面代码的基础上实现降序排序。

people.sort(comparing(Person::getLastName).reversed());;
```
###13. 小结（Summary）
```
Java SE 8提供的新语言特性并不算多——lambda表达式，方法引用，
默认方法和静态接口方法，以及范围更广的类型推导。但是把它们结合在一起之后，
开发者可以编写出更加清晰简洁的代码，类库编写者可以编写更加强大易用的并行类库。
```
## 方法引用和构造方法引用
## 扩展目标类型和类型推倒
## 接口中的默认方法和静态方法



