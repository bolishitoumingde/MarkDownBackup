### String、StringBuffer、StringBuilder的区别

1. String 是不可变的，如果尝试去修改，会生成一个新的字符串对象，StringBuffer和StringBuilder是可变的。
2. StringBuffer 是线程安全的，StringBuilder是线程不安全的，所以单线程下StringBuilder效率更高

```java
public static void main(String[] args) {
        String s = "abc";

        StringBuffer buffer = new StringBuffer(s);
        buffer.append("d");
        System.out.println(buffer);

        StringBuilder builder = new StringBuilder(s);
        builder.append("d");
        System.out.println(builder);
        
    }
```

StringBuffer是线程安全的

```java
@Override
    @IntrinsicCandidate
    public synchronized StringBuffer append(String str) {
        toStringCache = null;
        super.append(str);
        return this;
    }
```

![image-20220821181941589](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image-20220821181941589.5yv1wv1mekw0.webp)

### String.intern

```java
String s1 = new String("abc");

String s2 = s1.intern();
String s3 = s1.intern();
System.out.println(s1 == s2); // false
System.out.println(s2 == s3); // true
```

1. `new String("abc")`创建了两个对象，首先创建`abc`并放在常量池中，然后创建一个String对象，指向常量池中的`abc`再使变量`s1`指向该String对象。
2. `intern`首先在常量池总寻找`abc`，找到则直接返回该字符串的引用，未找到则在常量池新建该字符串，并返回该字符串的引用。

