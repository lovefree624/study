# java 8 新特性
## java8 的lamdba表达式
### 以前的老的for循环
```java
public void fore(){
    for(int i = 0; i < 10 ;i++){
        system.out.println("数去结果是："+ i)；
    }
}
```
### for的增强循环
```java
public void forEach(){
    /**这是user对象的集合，遍历user**/
    for(User u : userList){
        System.out.println("输出的结果：" + u.toString());
    }
}
```
### java8 的lamdba表达式
```java
public void lamdba(){
    userList.foreach(e-(system.out.println(e)));
}
```
关键是`()->`,如果是单个参数可写可不写，如果是多个参数的话，中间用逗号隔开，`->`符号后面如果有多个程序，那么就需要添加括号。

调用对象的方法，可以使用`::`来调用方法，例如
```java
class User (
    private String name;
    private String sex;

    public String getName(){
        return this.name;
    }
)
```
我们在调用的时候可以这样调用，User :: getName()    来获取user对象的名称。
## java8 的stream表达式
### 获取流的方式
Stream 不是集合元素，它不是数据结构并不保存数据，它是有关算法和计算的，它更像一个高级版本的 Iterator。原始版本的 Iterator，用户只能显式地一个一个遍历元素并对其执行某些操作；高级版本的 Stream，用户只要给出需要对其包含的元素执行什么操作，比如 “过滤掉长度大于 10 的字符串”、“获取每个字符串的首字母”等，Stream 会隐式地在内部进行遍历，做出相应的数据转换。

Stream 就如同一个迭代器（Iterator），单向，不可往复，数据只能遍历一次，遍历过一次后即用尽了，就好比流水从面前流过，一去不复返。

而和迭代器又不同的是，Stream 可以并行化操作，迭代器只能命令式地、串行化操作。顾名思义，当使用串行方式去遍历时，每个 item 读完后再读下一个 item。而使用并行去遍历时，数据会被分成多个段，其中每一个都在不同的线程中处理，然后将结果一起输出。Stream 的并行操作依赖于 Java7 中引入的 Fork/Join 框架（JSR166y）来拆分任务和加速处理过程。
### 流的构成
当我们使用一个流的时候，通常包括三个基本步骤：

获取一个数据源（source）→ 数据转换→执行操作获取想要的结果，每次转换原有 Stream 对象不改变，返回一个新的 Stream 对象（可以有多次转换），这就允许对其操作可以像链条一样排列，变成一个管道，如下图所示。
### 集合生成流的方式
集合时我们使用Collection.stream(),Collection.parallelStream();
#### 数组生成流的方式
```java
String[] str = {"a","b","c"};
Arrays.stream(str);
Arrays.asList(str).stream();
```
#### 使用Stream生成流
Stream.of(dd);// Stream.iterate();
#### 文件生成流的方法
java.io.BufferedReader.lines()；
```java
long uniqueWords = 0; 
try(
    Stream<String> lines = Files.lines(Paths.get("data.txt"), Charset.defaultCharset())
    ){ 
uniqueWords = lines.flatMap(line -> Arrays.stream(line.split(" ")))
 .distinct() 
 .count(); 
} 
catch(IOException e){ 
}
```
#### 创建无限流
Stream API提供了两个静态方法来从函数生成流：Stream.iterate和Stream.generate。这两个操作可以创建所谓的无限流：不像从固定集合创建的流那样有固定大小的流。由iterate和generate产生的流会用给定的函数按需创建值，因此可以无穷无尽地计算下去！
使用Stream.iterate()来创建
### 流的操作类型分为两种：
Intermediate：一个流可以后面跟随零个或多个 intermediate 操作。其目的主要是打开流，做出某种程度的数据映射/过滤，然后返回一个新的流，交给下一个操作使用。这类操作都是惰性化的（lazy），就是说，仅仅调用到这类方法，并没有真正开始流的遍历。

Terminal：一个流只能有一个 terminal 操作，当这个操作执行后，流就被使用“光”了，无法再被操作。所以这必定是流的最后一个操作。Terminal 操作的执行，才会真正开始流的遍历，并且会生成一个结果，或者一个 side effect。

在对于一个 Stream 进行多次转换操作 (Intermediate 操作)，每次都对 Stream 的每个元素进行转换，而且是执行多次，这样时间复杂度就是 N（转换次数）个 for 循环里把所有操作都做掉的总和吗？其实不是这样的，转换操作都是 lazy 的，多个转换操作只会在 Terminal 操作的时候融合起来，一次循环完成。我们可以这样简单的理解，Stream 里有个操作函数的集合，每次转换操作就是把转换函数放入这个集合中，在 Terminal 操作的时候循环 Stream 对应的集合，然后对每个元素执行所有的函数。
### 流的构造与转换
下面提供最常见的几种构造 Stream 的样例。

清单 4. 构造流的几种常见方法
```java
// 1. Individual values
Stream stream = Stream.of("a", "b", "c");
// 2. Arrays
String [] strArray = new String[] {"a", "b", "c"};
stream = Stream.of(strArray);
stream = Arrays.stream(strArray);
// 3. Collections
List<String> list = Arrays.asList(strArray);
stream = list.stream();
```
需要注意的是，对于基本数值型，目前有三种对应的包装类型 Stream：

IntStream、LongStream、DoubleStream。当然我们也可以用 Stream<Integer>、Stream<Long> >、Stream<Double>，但是 boxing 和 unboxing 会很耗时，所以特别为这三种基本数值型提供了对应的 Stream。

Java 8 中还没有提供其它数值型 Stream，因为这将导致扩增的内容较多。而常规的数值型聚合运算可以通过上面三种 Stream 进行。

清单 5. 数值流的构造
```java
IntStream.of(new int[]{1, 2, 3}).forEach(System.out::println);
IntStream.range(1, 3).forEach(System.out::println);
IntStream.rangeClosed(1, 3).forEach(System.out::println);
```
清单 6. 流转换为其它数据结构
```java
// 1. Array
String[] strArray1 = stream.toArray(String[]::new);
// 2. Collection
List<String> list1 = stream.collect(Collectors.toList());
List<String> list2 = stream.collect(Collectors.toCollection(ArrayList::new));
Set set1 = stream.collect(Collectors.toSet());
Stack stack1 = stream.collect(Collectors.toCollection(Stack::new));
// 3. String
String str = stream.collect(Collectors.joining()).toString();
```
一个 Stream 只可以使用一次，上面的代码为了简洁而重复使用了数次。
### 流的操作
接下来，当把一个数据结构包装成 Stream 后，就要开始对里面的元素进行各类操作了。常见的操作可以归类如下。

Intermediate：
map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、 limit、 skip、 parallel、 sequential、 unordered

Terminal：
forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator

Short-circuiting：
anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 limit
### 举例各个关键操作的用法
#### map/flatMap
我们先来看 map。如果你熟悉 scala 这类函数式语言，对这个方法应该很了解，它的作用就是把 input Stream 的每一个元素，映射成 output Stream 的另外一个元素。

清单 7. 转换大写
```java
List<String> output = wordList.stream().
map(String::toUpperCase).
collect(Collectors.toList());
```
这段代码把所有的单词转换为大写。

清单 8. 平方数
```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4);
List<Integer> squareNums = nums.stream().
map(n -> n * n).
collect(Collectors.toList());
```
这段代码生成一个整数 list 的平方数 {1, 4, 9, 16}。

从上面例子可以看出，map 生成的是个 1:1 映射，每个输入元素，都按照规则转换成为另外一个元素。还有一些场景，是一对多映射关系的，这时需要 flatMap。

清单 9. 一对多
```java
Stream<List<Integer>> inputStream = Stream.of(
 Arrays.asList(1),
 Arrays.asList(2, 3),
 Arrays.asList(4, 5, 6)
 );
Stream<Integer> outputStream = inputStream.
flatMap((childList) -> childList.stream());
```
flatMap 把 input Stream 中的层级结构扁平化，就是将最底层元素抽出来放到一起，最终 output 的新 Stream 里面已经没有 List 了，都是直接的数字。
#### filter
filter 对原始 Stream 进行某项测试，通过测试的元素被留下来生成一个新 Stream。

清单 10. 留下偶数
```java
Integer[] sixNums = {1, 2, 3, 4, 5, 6};
Integer[] evens =
Stream.of(sixNums).filter(n -> n%2 == 0).toArray(Integer[]::new);
```
经过条件“被 2 整除”的 filter，剩下的数字为 {2, 4, 6}。

清单 11. 把单词挑出来
```java
List<String> output = reader.lines().
 flatMap(line -> Stream.of(line.split(REGEXP))).
 filter(word -> word.length() > 0).
 collect(Collectors.toList());
 ```
这段代码首先把每行的单词用 flatMap 整理到新的 Stream，然后保留长度不为 0 的，就是整篇文章中的全部单词了。
#### forEach

forEach 方法接收一个 Lambda 表达式，然后在 Stream 的每一个元素上执行该表达式。

清单 12. 打印姓名（forEach 和 pre-java8 的对比）
```java
// Java 8
roster.stream()
 .filter(p -> p.getGender() == Person.Sex.MALE)
 .forEach(p -> System.out.println(p.getName()));
// Pre-Java 8
for (Person p : roster) {
 if (p.getGender() == Person.Sex.MALE) {
 System.out.println(p.getName());
 }
}
```
对一个人员集合遍历，找出男性并打印姓名。可以看出来，forEach 是为 Lambda 而设计的，保持了最紧凑的风格。而且 Lambda 表达式本身是可以重用的，非常方便。当需要为多核系统优化时，可以 parallelStream().forEach()，只是此时原有元素的次序没法保证，并行的情况下将改变串行时操作的行为，此时 forEach 本身的实现不需要调整，而 Java8 以前的 for 循环 code 可能需要加入额外的多线程逻辑。

但一般认为，forEach 和常规 for 循环的差异不涉及到性能，它们仅仅是函数式风格与传统 Java 风格的差别。

另外一点需要注意，forEach 是 terminal 操作，因此它执行后，Stream 的元素就被“消费”掉了，你无法对一个 Stream 进行两次 terminal 运算。下面的代码是错误的：

```java
stream.forEach(element -> doOneThing(element));
stream.forEach(element -> doAnotherThing(element));
```
相反，具有相似功能的 intermediate 操作 peek 可以达到上述目的。如下是出现在该 api javadoc 上的一个示例。

清单 13. peek 对每个元素执行操作并返回一个新的 Stream
```java
Stream.of("one", "two", "three", "four")
 .filter(e -> e.length() > 3)
 .peek(e -> System.out.println("Filtered value: " + e))
 .map(String::toUpperCase)
 .peek(e -> System.out.println("Mapped value: " + e))
 .collect(Collectors.toList());
 ```
forEach 不能修改自己包含的本地变量值，也不能用 break/return 之类的关键字提前结束循环。
#### findFirst

这是一个 termimal 兼 short-circuiting 操作，它总是返回 Stream 的第一个元素，或者空。

这里比较重点的是它的返回值类型：Optional。这也是一个模仿 Scala 语言中的概念，作为一个容器，它可能含有某值，或者不包含。使用它的目的是尽可能避免 NullPointerException。

清单 14. Optional 的两个用例
```java
String strA = " abcd ", strB = null;
print(strA);
print("");
print(strB);
getLength(strA);
getLength("");
getLength(strB);
public static void print(String text) {
 // Java 8
 Optional.ofNullable(text).ifPresent(System.out::println);
 // Pre-Java 8
 if (text != null) {
 System.out.println(text);
 }
 }
public static int getLength(String text) {
 // Java 8
return Optional.ofNullable(text).map(String::length).orElse(-1);
 // Pre-Java 8
// return if (text != null) ? text.length() : -1;
 };
 ```
在更复杂的 if (xx != null) 的情况中，使用 Optional 代码的可读性更好，而且它提供的是编译时检查，能极大的降低 NPE 这种 Runtime Exception 对程序的影响，或者迫使程序员更早的在编码阶段处理空值问题，而不是留到运行时再发现和调试。

Stream 中的 findAny、max/min、reduce 等方法等返回 Optional 值。还有例如 IntStream.average() 返回 OptionalDouble 等等。
#### reduce

这个方法的主要作用是把 Stream 元素组合起来。它提供一个起始值（种子），然后依照运算规则（BinaryOperator），和前面 Stream 的第一个、第二个、第 n 个元素组合。从这个意义上说，字符串拼接、数值的 sum、min、max、average 都是特殊的 reduce。例如 Stream 的 sum 就相当于
```java
Integer sum = integers.reduce(0, (a, b) -> a+b); 或

Integer sum = integers.reduce(0, Integer::sum);
```
也有没有起始值的情况，这时会把 Stream 的前面两个元素组合起来，返回的是 Optional。

清单 15. reduce 的用例
```java
// 字符串连接，concat = "ABCD"
String concat = Stream.of("A", "B", "C", "D").reduce("", String::concat); 
// 求最小值，minValue = -3.0
double minValue = Stream.of(-1.5, 1.0, -3.0, -2.0).reduce(Double.MAX_VALUE, Double::min); 
// 求和，sumValue = 10, 有起始值
int sumValue = Stream.of(1, 2, 3, 4).reduce(0, Integer::sum);
// 求和，sumValue = 10, 无起始值
sumValue = Stream.of(1, 2, 3, 4).reduce(Integer::sum).get();
// 过滤，字符串连接，concat = "ace"
concat = Stream.of("a", "B", "c", "D", "e", "F").
 filter(x -> x.compareTo("Z") > 0).
 reduce("", String::concat);
 ```
上面代码例如第一个示例的 reduce()，第一个参数（空白字符）即为起始值，第二个参数（String::concat）为 BinaryOperator。这类有起始值的 reduce() 都返回具体的对象。而对于第四个示例没有起始值的 reduce()，由于可能没有足够的元素，返回的是 Optional，请留意这个区别。
#### limit/skip

limit 返回 Stream 的前面 n 个元素；skip 则是扔掉前 n 个元素（它是由一个叫 subStream 的方法改名而来）。

清单 16. limit 和 skip 对运行次数的影响
```java
public void testLimitAndSkip() {
 List<Person> persons = new ArrayList();
 for (int i = 1; i <= 10000; i++) {
 Person person = new Person(i, "name" + i);
 persons.add(person);
 }
List<String> personList2 = persons.stream().
map(Person::getName).limit(10).skip(3).collect(Collectors.toList());
 System.out.println(personList2);
}
private class Person {
 public int no;
 private String name;
 public Person (int no, String name) {
 this.no = no;
 this.name = name;
 }
 public String getName() {
 System.out.println(name);
 return name;
 }
}
```
输出结果为：

name1
name2
name3
name4
name5
name6
name7
name8
name9
name10
[name4, name5, name6, name7, name8, name9, name10]
这是一个有 10，000 个元素的 Stream，但在 short-circuiting 操作 limit 和 skip 的作用下，管道中 map 操作指定的 getName() 方法的执行次数为 limit 所限定的 10 次，而最终返回结果在跳过前 3 个元素后只有后面 7 个返回。

有一种情况是 limit/skip 无法达到 short-circuiting 目的的，就是把它们放在 Stream 的排序操作后，原因跟 sorted 这个 intermediate 操作有关：此时系统并不知道 Stream 排序后的次序如何，所以 sorted 中的操作看上去就像完全没有被 limit 或者 skip 一样。

清单 17. limit 和 skip 对 sorted 后的运行次数无影响
```java
List<Person> persons = new ArrayList();
 for (int i = 1; i <= 5; i++) {
 Person person = new Person(i, "name" + i);
 persons.add(person);
 }
List<Person> personList2 = persons.stream().sorted((p1, p2) -> 
p1.getName().compareTo(p2.getName())).limit(2).collect(Collectors.toList());
System.out.println(personList2);
```
上面的示例对清单 13 做了微调，首先对 5 个元素的 Stream 排序，然后进行 limit 操作。输出结果为：

name2
name1
name3
name2
name4
name3
name5
name4
[stream.StreamDW$Person@816f27d, stream.StreamDW$Person@87aac27]
即虽然最后的返回元素数量是 2，但整个管道中的 sorted 表达式执行次数没有像前面例子相应减少。

最后有一点需要注意的是，对一个 parallel 的 Steam 管道来说，如果其元素是有序的，那么 limit 操作的成本会比较大，因为它的返回对象必须是前 n 个也有一样次序的元素。取而代之的策略是取消元素间的次序，或者不要用 parallel Stream。
#### sorted

对 Stream 的排序通过 sorted 进行，它比数组的排序更强之处在于你可以首先对 Stream 进行各类 map、filter、limit、skip 甚至 distinct 来减少元素数量后，再排序，这能帮助程序明显缩短执行时间。我们对清单 14 进行优化：

清单 18. 优化：排序前进行 limit 和 skip
```java
List<Person> persons = new ArrayList();
 for (int i = 1; i <= 5; i++) {
 Person person = new Person(i, "name" + i);
 persons.add(person);
 }
List<Person> personList2 = persons.stream().limit(2).sorted((p1, p2) -> p1.getName().compareTo(p2.getName())).collect(Collectors.toList());
System.out.println(personList2);
```
结果会简单很多：
name2
name1
[stream.StreamDW$Person@6ce253f1, stream.StreamDW$Person@53d8d10a]
当然，这种优化是有 business logic 上的局限性的：即不要求排序后再取值。
#### min/max/distinct

min 和 max 的功能也可以通过对 Stream 元素先排序，再 findFirst 来实现，但前者的性能会更好，为 O(n)，而 sorted 的成本是 O(n log n)。同时它们作为特殊的 reduce 方法被独立出来也是因为求最大最小值是很常见的操作。

清单 19. 找出最长一行的长度
```java
BufferedReader br = new BufferedReader(new FileReader("c:\\SUService.log"));
int longest = br.lines().
 mapToInt(String::length).
 max().
 getAsInt();
br.close();
System.out.println(longest);
```
下面的例子则使用 distinct 来找出不重复的单词。

清单 20. 找出全文的单词，转小写，并排序
```java
List<String> words = br.lines().
 flatMap(line -> Stream.of(line.split(" "))).
 filter(word -> word.length() > 0).
 map(String::toLowerCase).
 distinct().
 sorted().
 collect(Collectors.toList());
br.close();
System.out.println(words);
```
#### Match

Stream 有三个 match 方法，从语义上说：

allMatch：Stream 中全部元素符合传入的 predicate，返回 true
anyMatch：Stream 中只要有一个元素符合传入的 predicate，返回 true
noneMatch：Stream 中没有一个元素符合传入的 predicate，返回 true
它们都不是要遍历全部元素才能返回结果。例如 allMatch 只要一个元素不满足条件，就 skip 剩下的所有元素，返回 false。对清单 13 中的 Person 类稍做修改，加入一个 age 属性和 getAge 方法。

清单 21. 使用 Match
```java
List<Person> persons = new ArrayList();
persons.add(new Person(1, "name" + 1, 10));
persons.add(new Person(2, "name" + 2, 21));
persons.add(new Person(3, "name" + 3, 34));
persons.add(new Person(4, "name" + 4, 6));
persons.add(new Person(5, "name" + 5, 55));
boolean isAllAdult = persons.stream().
 allMatch(p -> p.getAge() > 18);
System.out.println("All are adult? " + isAllAdult);
boolean isThereAnyChild = persons.stream().
 anyMatch(p -> p.getAge() < 12);
System.out.println("Any child? " + isThereAnyChild);
```
输出结果：

All are adult? false
Any child? true
进阶：自己生成流
Stream.generate

通过实现 Supplier 接口，你可以自己来控制流的生成。这种情形通常用于随机数、常量的 Stream，或者需要前后元素间维持着某种状态信息的 Stream。把 Supplier 实例传递给 Stream.generate() 生成的 Stream，默认是串行（相对 parallel 而言）但无序的（相对 ordered 而言）。由于它是无限的，在管道中，必须利用 limit 之类的操作限制 Stream 大小。

清单 22. 生成 10 个随机整数
```java
Random seed = new Random();
Supplier<Integer> random = seed::nextInt;
Stream.generate(random).limit(10).forEach(System.out::println);
//Another way
IntStream.generate(() -> (int) (System.nanoTime() % 100)).
limit(10).forEach(System.out::println);
```
Stream.generate() 还接受自己实现的 Supplier。例如在构造海量测试数据的时候，用某种自动的规则给每一个变量赋值；或者依据公式计算 Stream 的每个元素值。这些都是维持状态信息的情形。

清单 23. 自实现 Supplier
```java
Stream.generate(new PersonSupplier()).
limit(10).
forEach(p -> System.out.println(p.getName() + ", " + p.getAge()));
private class PersonSupplier implements Supplier<Person> {
 private int index = 0;
 private Random random = new Random();
 @Override
 public Person get() {
 return new Person(index++, "StormTestUser" + index, random.nextInt(100));
 }
}
```
输出结果：

StormTestUser1, 9
StormTestUser2, 12
StormTestUser3, 88
StormTestUser4, 51
StormTestUser5, 22
StormTestUser6, 28
StormTestUser7, 81
StormTestUser8, 51
StormTestUser9, 4
StormTestUser10, 76
Stream.iterate

iterate 跟 reduce 操作很像，接受一个种子值，和一个 UnaryOperator（例如 f）。然后种子值成为 Stream 的第一个元素，f(seed) 为第二个，f(f(seed)) 第三个，以此类推。

清单 24. 生成一个等差数列

```java
Stream.iterate(0, n -> n + 3).limit(10). forEach(x -> System.out.print(x + " "));
```
输出结果：

0 3 6 9 12 15 18 21 24 27
与 Stream.generate 相仿，在 iterate 时候管道必须有 limit 这样的操作来限制 Stream 大小。

进阶：用 Collectors 来进行 reduction 操作
java.util.stream.Collectors 类的主要作用就是辅助进行各类有用的 reduction 操作，例如转变输出为 Collection，把 Stream 元素进行归组。
#### groupingBy/partitioningBy

清单 25. 按照年龄归组
```java
Map<Integer, List<Person>> personGroups = Stream.generate(new PersonSupplier()).
 limit(100).
 collect(Collectors.groupingBy(Person::getAge));
Iterator it = personGroups.entrySet().iterator();
while (it.hasNext()) {
 Map.Entry<Integer, List<Person>> persons = (Map.Entry) it.next();
 System.out.println("Age " + persons.getKey() + " = " + persons.getValue().size());
}
```
上面的 code，首先生成 100 人的信息，然后按照年龄归组，相同年龄的人放到同一个 list 中，可以看到如下的输出：


Age 0 = 2
Age 1 = 2
Age 5 = 2
Age 8 = 1
Age 9 = 1
Age 11 = 2
……
清单 26. 按照未成年人和成年人归组
```java
Map<Boolean, List<Person>> children = Stream.generate(new PersonSupplier()).
 limit(100).
 collect(Collectors.partitioningBy(p -> p.getAge() < 18));
System.out.println("Children number: " + children.get(true).size());
System.out.println("Adult number: " + children.get(false).size());
```
输出结果：
Children number: 23 
Adult number: 77
在使用条件“年龄小于 18”进行分组后可以看到，不到 18 岁的未成年人是一组，成年人是另外一组。partitioningBy 其实是一种特殊的 groupingBy，它依照条件测试的是否两种结果来构造返回的数据结构，get(true) 和 get(false) 能即为全部的元素对象。
#### 数据流的操作
IntStream、LongStream、DoubleStream。当然我们也可以用 Stream<Integer>、Stream<Long> >、Stream<Double>，但是 boxing 和 unboxing 会很耗时，所以特别为这三种基本数值型提供了对应的 Stream。
##### 映射到数值流
将流转换为特化版本的常用方法是mapToInt、mapToDouble和mapToLong。这些方法和前面说的map方法的工作方式一样，只是它们返回的是一个特化流，而不是Stream<T>。
```java
int calories = menu.stream() 
                    .mapToInt(Dish::getCalories) 
                    .sum();
 ```
这个流返回的是基本类型的值，不是流，所以不能继续进行流的其他操作了。
##### 转换回对象流(boxed())
同样，一旦有了数值流，你可能会想把它转换回非特化流。例如，IntStream上的操作只能产生原始整数： IntStream 的 map 操作接受的 Lambda 必须接受 int 并返回 int （一个IntUnaryOperator）。但是你可能想要生成另一类值，比如Dish。为此，你需要访问Stream接口中定义的那些更广义的操作。要把原始流转换成一般流（每个int都会装箱成一个Integer），可以使用boxed方法，如下所示：
```java
IntStream intStream = menu.stream().mapToInt(Dish::getCalories); 
Stream<Integer> stream = intStream.boxed();
```
#####  默认值OptionalInt
求和的那个例子很容易，因为它有一个默认值：0。但是，如果你要计算IntStream中的最大元素，就得换个法子了，因为0是错误的结果。如何区分没有元素的流和最大值真的是0的流呢？
前面我们介绍了Optional类，这是一个可以表示值存在或不存在的容器。Optional可以用Integer、String等参考类型来参数化。对于三种原始流特化，也分别有一个Optional原始类型特化版本：OptionalInt、OptionalDouble和OptionalLong。
例如，要找到IntStream中的最大元素，可以调用max方法，它会返回一个OptionalInt：
```java
OptionalInt maxCalories = menu.stream() 
                                .mapToInt(Dish::getCalories) 
                                .max(); 
 ```
现在，如果没有最大值的话，你就可以显式处理OptionalInt去定义一个默认值了：
int max = maxCalories.orElse(1);
#### collect（数据收集器）
更易复合和重用。收集器非常有用，因为用它可以简洁而灵活地定义collect用来生成结果集合的标准。更具体地说，对流调用collect方法将对流中的元素触发一个归约操作（由Collector来参数化）。图6-1所示的归约操作所做的工作和代码清单6-1中的指令式代码一样。它遍历流中的每个元素，并让Collector进建立累积交易分组的Map迭代Transac￾tion的List提取Tran￾saction的货币如果分组Map中没有这种货币的条目，就创建一个将当前遍历的Transaction加入同一货币的Transac￾tion的List
图6-1 按货币对交易分组的归约过程一般来说，Collector会对元素应用一个转换函数（很多时候是不体现任何效果的恒等转换，例如toList），并将结果累积在一个数据结构中，从而产生这一过程的最终输出。
##### toList()
```java
List<Transaction> transactions = 
 transactionStream.collect(Collectors.toList());
 ```
##### counting
long howManyDishes = menu.stream().collect(Collectors.counting()); 
这还可以写得更为直接：
long howManyDishes = menu.stream().count();
##### 查找流中的最大值和最小值(max()和min())
```java
Comparator<Dish> dishCaloriesComparator = 
 Comparator.comparingInt(Dish::getCalories); 
Optional<Dish> mostCalorieDish = 
 menu.stream() 
 .collect(maxBy(dishCaloriesComparator));
 ```
##### summingInt
它可接受一个把对象映射为求和所需int的函数，并返回一个收集器；该收集器在传递给普通的collect方法后即执行我们需要的汇总操作
```java
int totalCalories = menu.stream().collect(summingInt(Dish::getCalories));
```
Collectors.summingLong和Collectors.summingDouble方法的作用完全一样，可以用于求和字段为long或double的情况。
##### averagingInt
计算数值的平均数.
double avgCalories = menu.stream().collect(averagingInt(Dish::getCalories));

到目前为止，你已经看到了如何使用收集器来给流中的元素计数，找到这些元素数值属性的最大值和最小值，以及计算其总和和平均值。不过很多时候，你可能想要得到两个或更多这样的结果，而且你希望只需一次操作就可以完成。在这种情况下，你可以使用summarizingInt工厂方法返回的收集器。例如，通过一次summarizing操作你可以就数出菜单中元素的个数，并得
到菜肴热量总和、平均值、最大值和最小值：
```java
IntSummaryStatistics menuStatistics = 
 menu.stream().collect(summarizingInt(Dish::getCalories)); 
 ```
这个收集器会把所有这些信息收集到一个叫作IntSummaryStatistics的类里，它提供了方便的取值（getter）方法来访问结果。打印menuStatisticobject会得到以下输出：
IntSummaryStatistics{count=9, sum=4300, min=120, average=477.777778, max=800} 
同样，相应的summarizingLong和summarizingDouble工厂方法有相关LongSummaryStatisticsDoubleSummaryStatistics类型，适用于收集的属性是原始类型long或double的情况。
##### joining(连接字符串)
joining工厂方法返回的收集器会把对流中每一个对象应用toString方法得到的所有字符串连接成一个字符串。这意味着你把菜单中所有菜肴的名称连接起来，如下所示：
```java
String shortMenu = menu.stream().map(Dish::getName).collect(joining());
```
请注意，joining在内部使用了StringBuilder来把生成的字符串逐个追加起来。
使用自定义的分隔符
```java 
String shortMenu = menu.stream().map(Dish::getName).collect(joining(", "));
```
##### 分组
一个常见的数据库操作是根据一个或多个属性对集合中的项目进行分组。就像前面讲到按货币对交易进行分组的例子一样，如果用指令式风格来实现的话，这个操作可能会很麻烦、啰嗦而且容易出错。但是，如果用Java 8所推崇的函数式风格来重写的话，就很容易转化为一个非常容易看懂的语句。我们来看看这个功能的第二个例子：假设你要把菜单中的菜按照类型进行分类，有肉的放一组，有鱼的放一组，其他的都放另一组。用Collectors.groupingBy工厂方法返回的收集器就可以轻松地完成这项任务，如下所示：
```java
Map<Dish.Type, List<Dish>> dishesByType = 
 menu.stream().collect(groupingBy(Dish::getType)); 
 ```
其结果是下面的Map：
{FISH=[prawns, salmon], OTHER=[french fries, rice, season fruit, pizza], MEAT=[pork, beef, chicken]} 
这里，你给groupingBy方法传递了一个Function（以方法引用的形式），它提取了流中每一道Dish的Dish.Type。我们把这个Function叫作分类函数，因为它用来把流中的元素分成不同的组。如图6-4所示，分组操作的结果是一个Map，把分组函数返回的值作为映射的键，把流中所有具有这个分类值的项目的列表作为对应的映射值。在菜单分类的例子中，键就是菜的类型，
6.3 分组 121 值就是包含所有对应类型的菜肴的列表。分类函数不一定像方法引用那样可用，因为你想用以分类的条件可能比简单的属性访问器要复杂。例如，你可能想把热量不到400卡路里的菜划分为“低热量”（diet），热量400到700卡路里的菜划为“普通”（normal），高于700卡路里的划为“高热量”（fat）。由于Dish类的作者没有把这个操作写成一个方法，你无法使用方法引用，但你可以把这个逻辑写成Lambda表达式：
```java
public enum CaloricLevel { DIET, NORMAL, FAT } 
Map<CaloricLevel, List<Dish>> dishesByCaloricLevel = menu.stream().collect( 
 groupingBy(dish -> { 
 if (dish.getCalories() <= 400) return CaloricLevel.DIET; 
 else if (dish.getCalories() <= 700) return 
 CaloricLevel.NORMAL; 
 else return CaloricLevel.FAT; 
 } )); 
 ```
现在，你已经看到了如何对菜单中的菜肴按照类型和热量进行分组，但要是想同时按照这两个标准分类怎么办呢？分组的强大之处就在于它可以有效地组合。让我们来看看怎么做。
###### 多级分组
要实现多级分组，我们可以使用一个由双参数版本的Collectors.groupingBy工厂方法创建的收集器，它除了普通的分类函数之外，还可以接受collector类型的第二个参数。那么要进行二级分组的话，我们可以把一个内层groupingBy传递给外层groupingBy，并定义一个为流中项目分类的二级标准，如代码清单6-2所示。
```java
Map<Dish.Type, Map<CaloricLevel, List<Dish>>> dishesByTypeCaloricLevel = 
menu.stream().collect( 
 groupingBy(Dish::getType, 
 groupingBy(dish -> { 
 if (dish.getCalories() <= 400) return CaloricLevel.DIET; 
 else if (dish.getCalories() <= 700) return CaloricLevel.NORMAL; 
 else return CaloricLevel.FAT; 
 } ) 
 ) 
); 
```
这个二级分组的结果就是像下面这样的两级Map：
{MEAT={DIET=[chicken], NORMAL=[beef], FAT=[pork]}, FISH={DIET=[prawns], NORMAL=[salmon]}, OTHER={DIET=[rice, seasonal fruit], NORMAL=[french fries, pizza]}} 
这里的外层Map的键就是第一级分类函数生成的值：“fish, meat, other”，而这个Map的值又是一个Map，键是二级分类函数生成的值：“normal, diet, fat”。最后，第二级map的值是流中元素构成的List，是分别应用第一级和第二级分类函数所得到的对应第一级和第二级键的值：“salmon、pizza…”这种多级分组操作可以扩展至任意层级，n级分组就会得到一个代表n级树形结构的n级Map。图6-5显示了为什么结构相当于n维表格，并强调了分组操作的分类目的。
一般来说，把groupingBy看作“桶”比较容易明白。第一个groupingBy给每个键建立了一个桶。然后再用下游的收集器去收集每个桶中的元素，以此得到n级分组。图6-5 n层嵌套映射和n维分类表之间的等价关系
###### 按子组收集数据
在上一节中，我们看到可以把第二个groupingBy收集器传递给外层收集器来实现多级分组。但进一步说，传递给第一个groupingBy的第二个收集器可以是任何类型，而不一定是另一个groupingBy。例如，要数一数菜单中每类菜有多少个，可以传递counting收集器作为groupingBy收集器的第二个参数：
```java
Map<Dish.Type, Long> typesCount = menu.stream().collect( 
 groupingBy(Dish::getType, counting())); 
 ```
其结果是下面的Map：
{MEAT=3, FISH=2, OTHER=4} 还要注意，普通的单参数groupingBy(f)（其中f是分类函数）实际上是groupingBy(f, toList())的简便写法。再举一个例子，你可以把前面用于查找菜单中热量最高的菜肴的收集器改一改，按照菜的类
型分类：
```java
Map<Dish.Type, Optional<Dish>> mostCaloricByType = 
 menu.stream() 
 .collect(groupingBy(Dish::getType, 
 maxBy(comparingInt(Dish::getCalories)))); 
```
这个分组的结果显然是一个map，以Dish的类型作为键，以包装了该类型中热量最高的Dish的Optional<Dish>作为值：{FISH=Optional[salmon], OTHER=Optional[pizza], MEAT=Optional[pork]} 

注意 这个Map中的值是Optional，因为这是maxBy工厂方法生成的收集器的类型，但实际上，如果菜单中没有某一类型的Dish，这个类型就不会对应一个Optional. empty()值，而且根本不会出现在Map的键中。groupingBy收集器只有在应用分组条件后，第一次在流中找到某个键对应的元素时才会把键加入分组Map中。这意味着Optional包装器在这里不是很有用，因为它不会仅仅因为它是归约收集器的返回类型而表达一个最终可能不存在却意外存在的值。
###### 把收集器的结果转换为另一种类型
因为分组操作的Map结果中的每个值上包装的Optional没什么用，所以你可能想要把它们去掉。要做到这一点，或者更一般地来说，把收集器返回的结果转换为另一种类型，你可以使用Collectors.collectingAndThen工厂方法返回的收集器，如下所示。代码清单6-3 查找每个子组中热量最高的Dish.
```java
Map<Dish.Type, Dish> mostCaloricByType = 
 menu.stream() 
 .collect(groupingBy(Dish::getType,
 collectingAndThen( 
 maxBy(comparingInt(Dish::getCalories)), 
 Optional::get))); 
 ```
这个工厂方法接受两个参数——要转换的收集器以及转换函数，并返回另一个收集器。这个收集器相当于旧收集器的一个包装，collect操作的最后一步就是将返回值用转换函数做一个映射。在这里，被包起来的收集器就是用maxBy建立的那个，而转换函数Optional::get则把返回的Optional中的值提取出来。前面已经说过，这个操作放在这里是安全的，因为reducing收集器永远都不会返回Optional.empty()。其结果是下面的Map：
{FISH=salmon, OTHER=pizza, MEAT=pork} 把好几个收集器嵌套起来很常见，它们之间到底发生了什么可能不那么明显。图6-6可以直观地展示它们是怎么工作的。从最外层开始逐层向里，注意以下几点。
> 收集器用虚线表示，因此groupingBy是最外层，根据菜肴的类型把菜单流分组，得到三个子流。
> groupingBy收集器包裹着collectingAndThen收集器，因此分组操作得到的每个子流
都用这第二个收集器做进一步归约。
> collectingAndThen收集器又包裹着第三个收集器maxBy。  随后由归约收集器进行子流的归约操作，然后包含它collectingAndThen收集器会对其结果应用Optional:get转换函数。
> 对三个子流分别执行这一过程并转换而得到的三个值，也就是各个类型中热量最高的Dish，将成为groupingBy收集器返回的Map中与各个分类键（Dish的类型）相关联的值。