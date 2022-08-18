# 函数式编程-Stream流

## 1.概述

### 1.1为什么学

- 能够看懂公司的代码
- 大数据量下处理集合效率高
- 代码可读性高
- 消灭嵌套地狱

### 1.2 函数式编程思想

#### 1.2.1概念

面向对象思想需要关注用什么对象完成什么事情。而函数式编程思想就类似于我们数学中的函数。它关注的是对数据进行了什么操作。

#### 1.2.2优点

- 代码简洁
- 接近自然语言，易于理解
- 易于并发编程

## 2. Lambda表达式

### 2.1概念

Lambda是 `JDK8` 中的一个语法糖。它可以对某些匿名内部类的写法进行简化。它是函数式思想的一个重要体现。让我们不用关注是什么对象。而是更关注我们对数据进行了什么操作。

### 2.2核心原则

> 可推导可省略

### 2.3基本格式

```java
(参数列表)->{代码}
```

**例：**

我们在创建线程并启动时可以使用匿名内部类的写法

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("你知道吗 我比你想象的 更想在你身边");
    }
}).start();
```

可以使用Lambda的格式对其进行修改。修改后写法：

```java
new Thread(()->{
    System.out.println("你知道吗 我比你想象的 更想在你身边");
}).start();
```

### 2.4省略规则

- 参数类型可以省略
- 方法体只有一句代码时大括号和唯一 一句代码的分号可以省略
- 方法只有一个参数时小括号可以省略
- 以上这些规则都记不住也可以省略不记（`alt` + `enter`）

## 3. Stream流

### 3.1概述

Java 8 的Stream使用的是函数式编程模式，如同它的名字一样，它可以被用来对集合或数组进行链状流式的操作。可以更方便的让我们对集合或数组操作。

### 3.2案例数据准备

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@EqualsAndHashCode
public class Author {
    private Long id;
    private String name;
    private Integer age;
    private String intro;
    private List<Book> books;
}
```

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@EqualsAndHashCode
public class Book {
    private Long id;
    private String name;
    private String category;
    private Integer score;
    private String intro;
}
```

```java
private static List<Author> getAuthors(){
    // 数据初始化
    Author author = new Author(1L, "蒙多", 33, "一个菜刀中明悟哲理的祖安人", null);
    Author author2 = new Author(2L, "亚索", 15, "面对疾风吧", null);
    Author author3 = new Author(3L, "易", 12, "你的剑就是我的剑", null);
    Author author4 = new Author(4L, "易", 12, "你的剑就是我的剑", null);

    // 书籍列表
    List<Book> books1 = new ArrayList<>();
    List<Book> books2 = new ArrayList<>();
    List<Book> books3 = new ArrayList<>();

    books1.add(new Book(1L, "西游记", "名著", 9, "师徒四人取经"));
    books1.add(new Book(2L, "红楼梦", "名著", 8, "红楼梦"));

    books2.add(new Book(3L, "水浒传", "名著", 7, "水浒传"));
    books2.add(new Book(4L, "三国演义", "名著", 8, "三国演义"));

    books3.add(new Book(5L, "骆驼祥子", "小说", 9, "骆驼祥子"));
    books3.add(new Book(6L, "孔乙己", "小说", 7, "孔乙己"));

    author.setBooks(books1);
    author2.setBooks(books2);
    author3.setBooks(books3);
    author4.setBooks(books3);

    List<Author> list = new ArrayList<>(Arrays.asList(author,author2,author3,author4));
    return list;
}
```

### 3.3快速入门

#### 3.3.1需求

我们可以调用getAuthors方法获取到作家的集合。现在需要打印所有年龄小于18的作家，并且去除重复。

#### 3.3.2实现

```java
public static void main(String[] args) {
    List<Author> authors = getAuthor();
    authors.stream()
        .distinct() // 去重
        .filter(author -> author.getAge() < 18) // 过滤年龄<18
        .forEach(author -> System.out.println(author.getName())); // 遍历打印
}
```

### 3.4常用操作

#### 3.4.1创建流

单例集合：`集合对象.stream()`

```java
List<Author> authors = getAuthor();
Stream<Author> stream = authors.stream();
```

数组：`Arrays.stream(数组)` 或者使用`Stream.of`来创建

```java
Integer[] arr = {1,2,3,4,5};
Stream<Integer> stream = Arrays.stream(arr);
Stream<Integer> stream1 = Stream.of(arr);
```

双例集合：转换成单例集合后再创建

```java
Map<String, Integer> map = new HashMap<>();
map.put("蜡笔小新", 19);
map.put("黑子", 17);
map.put("日向翔阳", 16);
Set<Map.Entry<String, Integer>> entries = map.entrySet();
Stream<Map.Entry<String, Integer>> stream = entries.stream();
```

#### 3.4.2中间操作

##### 3.4.2.1 filter

##### 3.4.2.2 map

##### 3.4.2.3 distinct

可以去除流中重复元素

> 注意：distinct方法依赖Object的equals方法来判断是否是相同对象的。所以要重写equals方法

##### 3.4.2.4 sorted

> 注意：如果调用空参的sorted方法，需要流中元素是实现了Comparable

```java
authors.stream()
    .distinct()
    .sorted((o1, o2) -> o1.getAge() - o2.getAge())
    .forEach(author -> System.out.println(author.getAge()));
```

##### 3.4.2.5 limit

##### 3.4.2.6 skip

跳过流中的前n个元素，返回剩下的元素

##### 3.4.2.7 flatMap

map只能把一个对象转换成另一个对象来作为流中的元素。而flatMap可以把一个对象转换成多个对象作为流中的元素。

```java
public static void main(String[] args) {
    List<Author> authors = getAuthor();
    authors.stream()
        .flatMap(author -> author.getBooks().stream())
        .distinct()
        .forEach(book -> System.out.println(book.getName()));
}
```

#### 3.4.3终结者操作

##### 3.4.3.1 foreach

##### 3.4.3.2 count

##### 3.4.3.3 max&min

```java
public static void main(String[] args) {
    List<Author> authors = getAuthor();
    Optional<Integer> max = authors.stream()
        .flatMap(author -> author.getBooks().stream())
        .map(book -> book.getScore())
        .distinct()
        .max((o1, o2) -> o2.compareTo(01));
    System.out.println(max.get());
}
```



##### 3.4.3.4 collect

把流转换成一个集合

```java
public static void main(String[] args) {
    List<Author> authors = getAuthor();
    List<String> collect = authors.stream()
        .map(author -> author.getName())
        .collect(Collectors.toList());
    System.out.println(collect);
}
```

```java
public static void main(String[] args) {
    List<Author> authors = getAuthor();
    Map<String, List<Book>> collect = authors.stream()
        .distinct()
        .collect(Collectors.toMap(author -> author.getName(), author -> author.getBooks()));
    System.out.println(collect);
}
```



##### 3.4.3.5查找与匹配

1. anyMatch

可以用来判断是否符合匹配条件，结果为boolean类型。如果有一个符合结果为true，都不符合则为false。

2. allMatch

可以用来判断是否符合匹配条件，结果为boolean类型。如果都符合结果为true，否则为false。

3. noMatch

可以判断流中的元素是否都不符合匹配条件。如果都不符合结果为true，否则为false。

4. findAny

获取流中任意一个元素。该方法没有办法保证获取的一定是流中的第一个元素。

5. findFirst

获取流中第一个元素

##### 3.4.3.6 reduce归并

对流中的数据按照你制定的计算方式计算出一个结果。（缩减操作）

reduce的作用是把stream中的元素给组合起来，我们可以传入一个初始值，它会按照我们的计算方式依次拿流中的元素和初始值的基础上进行计算，计算结果再和后面的元素计算。

它的内部计算方式如下：

```java
T result = identity;
for (T element : this stream)
    result = accumulator.apply(result, element)
return result;
```

reduce一个参数的重载形式内部计算

```java
boolean foundAny = false;
T result = null;
for (T element : this stream) {
    if (!foundAny) {
        foundAny = true;
        result = element;
    }
    else
        result = accumulator.apply(result, element);
}
return foundAny ? Optional.of(result) : Optional.empty();
```

```java
public static void main(String[] args) {
	List<Author> authors = getAuthor();
	Optional<Integer> reduce = authors.stream()
			.distinct()
			.map(author -> author.getAge())
			.reduce((result, element) -> result > element ? element : result);

	reduce.ifPresent(age -> System.out.println(age));
}
```

### 3.5注意事项

- 惰性求值（如果没有终结操作，没有中间操作是不会得到执行的）
- 流是一次性的（一旦一个流对象经过一个终结操作后。这个流不能再被使用
- 不会影响原数据（我们在流中可以多数据做很多处理。但是正常情况下是不会影响原来集合中的元素的。这往往也是我们期望的）

## 4. Optional

### 4.1概述

我们在编写代码的时候出现最多的就是空指针异常，所以在很多情况下我们需要做各种非空判断。

例如：

```java
Author author = new Author();
if(author!=null){
    System.out.println(author.getName());
}
```

尤其是对象中的属性还是一个对象的情况下，这种判断会更多

而过多的判断语句会让我们的代码显得臃肿不堪

所以在JDK8中引入了Optional，养成使用Optional的习惯后你可以写出更优雅的代码来避免空指针异常。

并且在很多函数式编程相关的API中也都用到了Optional，如果不会使用Optional也会对函数式编程的学习造成影响。

### 4.2使用

#### 4.2.1创建对象

Optional就好像是包装类，可以把我们的具体数据封装成Optional对象内部。然后我们去使用Optional中封装好的方法操作封装进去的数据就可以非常优雅的避免空指针异常。

我们一般使用Optional的静态方法ofNullable来把数据封装成一个Optional对象。无法传入的参数是否为null都不会出现问题。

```java
public static void main(String[] args) {
    Optional<Author> authorOptional = getAuthor();
    authorOptional.ifPresent((author -> System.out.println(author.getName())));
}

public static Optional<Author> getAuthor(){
    Author author = new Author(1L, "蒙多", 33, "一个菜刀中明悟哲理的祖安人", null);
    return Optional.ofNullable(author);
}
```

#### 4.2.2安全消费值

我们获取到一个Optional对象后肯定需要对其中的数据进行使用，这时候我们可以使用其**ifPresent**方法对来消费其中的值。

这个方法会判断其内部封装的数据是否为空，不为空时才会执行具体的消费代码。这样使用起来更加安全了。

```java
 Optional<Author> authorOptional = getAuthor();
 authorOptional.ifPresent((author -> System.out.println(author.getName())));
```

#### 4.2.3获取值

如果我们期望获取安全的获取值，我们不推荐使用get方法，而是使用以下方法：

- orElseGet

```java
Optional<Author> authorOptional = getAuthor();
Author author = authorOptional.orElseGet(() -> new Author());
```

- orElseThrow

```java
Optional<Author> authorOptional = getAuthor();
try {
    Author author = authorOptional.orElseThrow((Supplier<Throwable>) () -> new RuntimeException("数据为null"));
    System.out.println(author);
} catch (Throwable throwable) {
    throwable.printStackTrace();
}
```

#### 4.2.4过滤

我们可以使用filter方法对数据进行过滤。如果原本是有数据的，但是不符合判断，也会变成一个无数据的Optional对象。

```java
Optional<Author> authorOptional = getAuthor();
authorOptional.filter(author -> author.getAge() > 18).ifPresent(author -> author.getName());
```

#### 4.2.5判断

我们可以使用isPresent方法进行是否存在数据的判断，如果为空返回值为false，如果不为空，返回值为true。但这种方式并不能体现Optional的好处，**更推荐使用ifPresent方法**

```java
Optional<Author> authorOptional = getAuthor();
if (authorOptional.isPresent()) {
    System.out.println(authorOptional.get().getName());
}
```

#### 4.2.6数据转换

Option还提供了map可以让我们对数据进行转换，并且转换得到的数据也还是被Optional包装好的，保证了我们的使用安全。例如我们想获取作家的书籍合集

```java
Optional<Author> authorOptional = getAuthor();
authorOptional.map(author -> author.getBooks()).ifPresent(books -> System.out.println(books));
```

## 5.函数式接口

### 5.1概述

**只有一个抽象方法**的接口我们称函数接口

JDK的函数式接口都加上了**@FunctionalInterface**注解进行标识，但是无论是否加上改注解只要接口中只有一个抽象方法，都是函数式接口。

### 5.2常见函数式接口

- Consumer消费接口
- Function计算转换接口
- Predicate判断接口
- Supplier生产型接口

### 5.3常用的默认方法

- and
- or
- negate

Predicate接口中的方法，negate方法相当于是在判断添加前面加个！表示取反

## 6.方法引用

我们在使用Lambda时，如果方法体只有一个方法的调用的话（包括构造方法），我们可以用方法引用进一步简化代码。

### 6.1推荐用法

我们在使用lambda时不需要考虑什么时候用方法引用，哪种方法引用，方法引用的格式是什么，我们只需要在写完lambda方法发现方法体只有一行代码，并且是方法的调用时使用快捷键尝试是否能够转换成方法引用即可。

### 6.2基本格式

类名或者对象名::方法名

### 6.3语法详解

#### 6.3.1引用类的静态方法

其实就是引用类的静态方法

**格式**

```
类名:方法名
```

**使用前提**

如果我们在重写方法的时候，方法体**只有一行代码**，并且这行代码是调用了某个类的静态方法，并且我们把要重写的**抽象方法中所有的参数都按照顺序传入了这个静态方法中**，这个时候我们就可以引用类的静态方法。

例如：

如下代码可以用方法引用进行简化

```java
List<Author> authors = getAuthors();
        Stream<Author> authorStream = authors.stream();
        authorStream.map(author -> author.getAge())
                .map(String::valueOf);
```

#### 6.3.2引用对象的实例方法

**格式**

```
对象名::方法名
```

**使用前提**

如果我们在重写方法的时候，方法体中**只有一行代码**，并且这行代码是**调用了某个对象的成员方法**，并且我们把重写的**抽象方法中所有的参数都按照顺序传入了这个成员方法中**，这个时候我们就可以引用对象的实例方法。

例如

```java
List<Author> authors = getAuthors();
Stream<Author> authorStream = authors.stream();
StringBuilder sb = new StringBuilder();
authorStream.map(author -> author.getName()).forEach(sb::append);
```

#### 6.3.3引用类的实例方法

**格式**

```
类名::方法名
```

**使用前提**

如果我们在重写方法的时候，方法体中**只有一行代码**，并且这行代码是**调用了第一个参数的成员方法**，并且我们把**重写的抽象方法中剩余的所有的参数都按照顺序传入了这个成员方法中**，这个时候我们就可以引用类的实例方法。

例如

```java
    interface UseString {
        String use(String str, int start, int length);
    }

    public static String subAuthorName(String str, UseString useString) {
        int start = 0;
        int length = 1;
        return useString.use(str, start, length);
    }

    public static void main(String[] args) {
        subAuthorName("三更草堂", new UseString() {
            @Override
            public String use(String str, int start, int length) {
                return str.substring(start, length);
            }
        });
    }
```

优化后

```java
interface UseString {
    String use(String str, int start, int length);
}

public static String subAuthorName(String str, UseString useString) {
    int start = 0;
    int length = 1;
    return useString.use(str, start, length);
}

public static void main(String[] args) {
    subAuthorName("三更草堂", String::substring);
}
```

#### 6.3.4构造器引用

如果一个方法体中的一行代码是构造器的话可以使用构造器引用

**格式**

```
类名::new
```

**使用前提**

如果我们在重写方法的时候，方法体中**只有一行代码**，并且这行代码是**调用了某个类的构造方法**，并且我们把**重写的抽象方法中所有的参数都按照顺序传入了这个构造方法中**，这个时候我们就可以引用构造器。

例如：

```java
List<Author> authors = getAuthors();
        authors.stream()
                .map(author -> author.getName())
                .map(name->new StringBuilder(name))
                .map(sb -> sb.append("三更").toString())
                .forEach(str -> System.out.println(str));
```

优化后

```java
List<Author> authors = getAuthors();
        authors.stream()
                .map(author -> author.getName())
                .map(StringBuilder::new)
                .map(sb -> sb.append("三更").toString())
                .forEach(str -> System.out.println(str));
```



## 7.高级用法

### 基本数据类型优化

我们之前用到的很多Stream的方法由于都使用了泛型。所以涉及到的参数和返回值都是引用数据类型。

即使我们操作的是整数小数，但是实际用的都是他们的包装类。JDK5中引入的自动装箱和自动拆箱让我们在使用对应的包装类时就好像使用基本数据类型一样方便。但是你一定要知道装箱和拆箱肯定是要消耗时间的。虽然这个时间消耗很下。但是在大量的数据不断的重复装箱拆箱的时候，你就不能无视这个时间损耗了。

所以为了让我们能够对这部分的时间消耗进行优化。Stream还提供了很多专门针对基本数据类型的方法。

例如: mapTolnt,mapToLong,mapToDouble,flatMapToInt,flatMapToDouble等。

```java
List<Author> authors = getAuthors();
        authors.stream()
                .map(Author::getAge)
                .map(age -> age + 10)
                .filter(age -> age > 18)
                .map(age -> age + 2)
                .forEach(System.out::println);
        
        authors.stream()
                .mapToInt(author -> author.getAge())
                .map(age -> age + 10)
                .filter(age -> age > 18)
                .map(age -> age + 2)
                .forEach(System.out::println);
```

### 并行流

当流中有大量元素时，我们可以使用并行流去提高操作的效率。其实并行流就是把任务分配给多个线程去完全。如果我们自己去用代码实现的话其实会非常的复杂，并且要求你对并发编程有足够的理解和认识。而如果我们使用Stream的话，我们只需要修改一个方法的调用就可以使用并行流来帮我们实现，从而提高效率。

使用**parallel方法**可以把串行流转换成并行流

```java
public static void main(String[] args) {

    Stream<Integer> stream = Stream.of(1,2,3,4,5,6,7,8,9,10);
    Integer sum = stream.parallel()
        .peek(new Consumer<Integer>() {
            @Override
            public void accept(Integer num) {
                System.out.println(num + Thread.currentThread().getName());
            }
        })
        .filter(num -> num > 5)
        .reduce((result, element) -> result + element).get();
    System.out.println(sum);
}
```

也可以使用parallelStream转换成并行流

```java
 List<Author> authors = getAuthors();
        authors.parallelStream()
                .map(Author::getAge)
                .map(age -> age + 10)
                .filter(age -> age > 18)
                .map(age -> age + 2)
                .forEach(System.out::println);
```

