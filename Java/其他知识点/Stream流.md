## 一、Stream流引入

 Lambda表达式，基于Lambda所带来的函数式编程，又引入了一个全新的Stream概念，用于解决集合类库既有的弊端。

 **现有一个需求：**

将list集合中姓张的元素过滤到一个新的集合中，然后将过滤出来的姓张的元素中，再过滤出来长度为3的元素，存储到一个新的集合中。

**1. 用常规方法解决需求**

```java
    // 已知的知识来解决需求
    List<String> list1 = new ArrayList<>();
    list1.add("张老三");
    list1.add("张小三");
    list1.add("李四");
    list1.add("赵五");
    list1.add("张六");
    list1.add("王八");
 
    ArrayList<String> list2 = new ArrayList<>();
    // 1.将list集合中姓张的元素过滤到一个新的集合中
    for(String name : list1){
        if(name.startsWith("张")){
            list2.add(name);
        }
    }
    ArrayList list3 = new ArrayList();
    for (String name : list2) {
        if (name.length() == 3){
            list3.add(name);
        }
    }
    System.out.println(list3);
 
    输出结果：
        [张老三, 张小三]
```

**2. 用Stream流操作集合，获取流，过滤操作，打印输出**

```java
list1.stream().filter((String name)->name.startsWith("张"))
    .filter((String name)->name.length()==3)
    .forEach((String name)->{
        System.out.println("符合条件的姓名：" + name);
    });
// 简写
list1.stream().filter(name -> name.startsWith("张"))
    .filter(name -> name.length() == 3)
    .forEach(name-> {
        System.out.println("符合条件的姓名：" + name);
    });
```

## 二、Stream流的格式

```java
Stream<T> filter(Predicate<? super T> predicate);
            -----> 参数：public interface Predicate<T>  (函数式接口)
                    ----> 抽象方法：boolean test(T t);
            -----> 参数：public interface Consumer<T>  (函数式接口)
                    ----> 抽象方法：boolean test(T t);
```

整体代码看来：流式思想 类似于 工厂车间的“流水线”

## 三、获取流

* 根据集合来获取：
  * 根据Collection获取流：
    * Collection接口中有一个stream()方法，可以获取流

```java
default Stream<E> stream()
```

1. 根据List获取流
2. 根据Set获取流
3. 根据Map获取流
   1. 根据Map集合的键来获取流
   2. 根据Map集合的值获取流
   3. 根据Map集合的键值对对象获取流

4. 根据数组获取流

**代码演示：**

1. 根据List集合获取流

```java
// 创建List集合
List<String> list = new ArrayList<>();
Stream<String> stream1 = list.stream();
```

2. 根据Set集合获取流

```java
// 创建Set集合
Set<String> set = new HashSet<>();
Stream<String> stream2 = set.stream();
```

3. 根据Map集合获取流

```java
// 创建Map集合
Map<Integer,String> map = new HashMap<>();

// 3.1根据Map集合的键获取流
Set<Integer> map1 = map.keySet();
Stream<Integer> stream3 = map1.stream();
// 3.2根据Map集合的值获取流
Collection<String> map2 = map.values();
Stream<String> stream4 = map2.stream();
// 3.3根据Map集合的键值对对象获取瑞
Set<Map.Entry<Integer, String>> map3 = map.entrySet();
Stream<Map.Entry<Integer, String>> stream5 = map3.stream();
```

4. 根据数组获取流

```java
// 根据数组获取流
String[] arr = {};
// 1
Stream<String> stream = Arrays.stream(arr);
// 2
Stream<String> stream6 = Stream.of(arr);
```

## 四、Stream流的常用方法

* 终结方法：返回值类型不再是Stream接口本身类型的方法，例如：forEach方法和count方法
* 非终结方法/延迟方法：返回值类型仍然是Stream接口自身类型的方法，除了终结方法都是延迟方法。例如：filter,limit,skip,map,concat

| 方法名称 |  方法作用  | 方法种类 | 是否支持链式调用 |
| :------: | :--------: | :------: | :--------------: |
|  count   |  统计个数  | 终结方法 |        否        |
| forEach  |  逐一处理  | 终结方法 |        否        |
|  filter  |    过滤    | 函数拼接 |        是        |
|  limit   | 取用前几个 | 函数拼接 |        是        |
|   skip   | 跳过前几个 | 函数拼接 |        是        |
|   map    |    映射    | 函数拼接 |        是        |
|  concat  |    组合    | 函数拼接 |        是        |

 **方法演示：**

### **1. count方法：**

`long count ();` 统计流中的元素，返回long类型数据

```java
List<String> list = new ArrayList<>();
list.add("张老三");
list.add("张小三");
list.add("李四");
list.add("赵五");
list.add("张六");
list.add("王八");

long count = list.stream().count();
System.out.println("集合中的元素个数是：" + count);

// 输出结果：
// 集合中的元素个数是：6
```

### **2. filter方法：**

Stream<T> filter(Predicate<? super ?> predicate); 过滤出满足条件的元素

参数Predicate：函数式接口，抽象方法：boolean test （T t)

Predicate接口：是一个判断接口

```java
// 获取stream流
Stream<String> stream = Stream.of("张老三", "张小三", "李四", "赵五", "刘六", "王七");
// 需求：过滤出姓张的元素
stream.filter((String name) -> {
                    return name.startsWith("张");
                })
                .forEach((String name) -> {
                    System.out.println("流中的元素" + name);
                });
```

### **3. forEach方法：**

`void forEach(Consumer<? super T> action)`：逐一处理流中的元素

参数 `Consumer<? super T> action`：函数式接口，只有一个抽象方法：void accept（T t)；

**注意：**

1. 此方法并不保证元素的逐一消费动作在流中是有序进行的（元素可能丢失）
2. Consumer是一个消费接口（可以获取流中的元素进行遍历操作，输出出去），可以使用Lambda表达式

```java
List<String> list = new ArrayList<>();
list.add("张老三");
list.add("张小三");
list.add("李四");
list.add("赵五");
list.add("张六");
list.add("王八");

// 函数模型：获取流 --> 注意消费流中的元素
list.stream().forEach((String name) -> {
            System.out.println(name);
        });

// 输出结果：
// 张老三
// 张小三
// 李四
// 赵五
// 张六
// 王八
```

### 4. limit方法：

`Stream<T> limit(long maxSize);`  取用前几个元素

**注意：**

参数是一个long 类型，如果流的长度大于参数，则进行截取；否则不进行操作

```java
// 获取流的长度
Stream<String> stream1 = Stream.of("张老三", "张小三", "李四", "赵五", "刘六", "王七");
// 需求：保留前三个元素
stream1.limit(3).forEach((String name) -> {
    System.out.println("流中的前三个元素是：" + name);
});

// 输出结果：
// 流中的前三个元素是：张老三
// 流中的前三个元素是：张小三
// 流中的前三个元素是：李四
```

### 5. map方法：

`Stream <R> map(Function<? super T,? exception R> mapper;`

参数Function<T,R>：函数式接口，抽象方法：R apply(T t);

Function<T,R>：其实就是一个类型转换接口（T和R的类型可以一致，也可以不一致）

```java
// 获取Stream流
Stream<String> stream1 = Stream.of("11","22","33","44","55");
// 需求：把stream1流中的元素转换为int类型
stream1.map((String s)->{
    // 将String类型的s进行转换为Integer类型的元素，并返回
    return Integer.parseInt(s); 
}).forEach((Integer i)->{
    System.out.println(i);  // 将转换后的int类型的元素逐一输出
});

// 简写
stream1.map(s -> Integer.parseInt(s))
    .forEach(System.out::pringln);

// 输出结果：
// 11
// 22
// 33
// 44
// 55
```

### 6. skip方法：

`Stream<T> skip(long n);`   跳过前几个元素

**注意：**

如果流的当前长度大于n，则跳过前n个，否则将会得到一个长度为0的空流

```java
// 获取stream流
Stream<String> stream = Stream.of("张老三", "张小三", "李四", "赵五", "刘六", "王七");

stream.skip(3).forEach((String name)->{
    System.out.println("跳过前三个，打印剩下的" + name);
});

// 输出结果：
// 跳过前三个，打印剩下的赵五
// 跳过前三个，打印剩下的刘六
// 跳过前三个，打印剩下的王七
```

### 7. concat方法：

`public static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)`

--> 合并两个流

```java
Stream<String> stream1 = Stream.of("11","22","33","44","55");
Stream<String> stream2 = Stream.of("张颜宇", "张三", "李四", "赵五", "刘六", "王七");

// 需求：合并两个流
Stream<String> stream = Stream.concat(stream1,stream2);
stream.forEach((String name)->{
	System.out.print(name);
});

// 输出结果：
// 1122334455张颜宇张三李四赵五刘六王七
```

## 五、收集Stream流

 Stream流中提供了一个方法，可以把流中的数据收集到单例集合中

<R, A> R collect(Collector<? super T, A, R> collector);   把流中的数据手机到单列集合中，返回值类型是R。R指定为什么类型，就是收集到什么类型的集合。

参数Collector<? super T, A, R>中的R类型，决定把流中的元素收集到哪个集合中，参数Collector如何得到 ？，可以使用 `java.util.stream.Collectors`工具类中的静态方法：

`public static <T> Collector<T, ?, List<T>> toList()`：转换为List集合

 `public static <T> Collector<T, ?, Set<T>> toSet()` ：转换为Set集合

```java
List<String> list2 = new ArrayList<>();
list2.add("张老三");
list2.add("张小三");
list2.add("李四");
list2.add("赵五");
list2.add("张六");
list2.add("王八");

// 需求：过滤出姓张的并且长度为3的元素
Stream<String> stream = list2.stream().filter((String name) -> {
    return name.startsWith("张");
}).filter((String name) -> {
    return name.length() == 3;
});

// stream 收集到单列集合中
List<String> list = stream.collect(Collectors.toList());
System.out.println(list);

// stream 收集到单列集合中
Set<String> set = stream.collect(Collectors.toSet());
System.out.println(set);
```