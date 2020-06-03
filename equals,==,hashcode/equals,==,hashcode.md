## 1.equals()的作用

equals() 的作用是 **用来判断两个对象是否相等**。

equals() 定义在JDK的Object.java中。通过判断两个对象的地址是否相等(即是否是同一个对象)来区分它们是否相等。源码如下：

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

Object.java中定义了equals()方法，这就意味着所有的Java类都实现了equals()方法，所有的类都可以通过equals()去比较两个对象是否相等。

使用默认的“**equals()**”方法，等价于“**==**”方法。

## 2. **equals() 与 == 的区别是什么？**

== : 它的作用是**判断两个对象的地址是不是相等。**即，判断两个对象是不是同一个对象。

​	 Java 是值传递，所以，对于 **==** 来说，不管是比较基本数据类型，还是引用数据类型的变量，其本质比较的都是值，只是引用类型变量存的值是对象的地址。

equals() : 它的作用也是**判断两个对象是否相等。**但它一般有两种使用情况：

* 情况1，类没有覆盖equals()方法。则通过equals()比较该类的两个对象时，等价于通过“==”比较这两个对象
* 情况2，类覆盖了equals()方法。一般，覆盖equals()方法来判断两个对象的内容相等；若它们的内容相等，则返回true(即，认为这两个对象相等)。

### 2.1. String中的==与equals

```java
/*
因为String太过常用，JAVA类库的设计者在实现时做了个小小的变化，即采用了享元模式,通过=符号进行的初始化，每当生成一个新内容的字符串时，他们都被添加到常量池中，当第二次再次生成同样内容的字符串实例时，就共享此对象，而不是创建一个新对象。
*/

public class test1 {
    public static void main(String[] args) {
        String a = new String("ab"); // new一个对象，a 为一个引用
        String b = new String("ab"); // new一个对象，b为另一个引用,对象的内容和a一样
        String aa = "ab"; // 放在常量池中
        String bb = "ab"; // 从常量池中查找
        if (aa == bb) // true
            System.out.println("aa==bb");
        if (a == b) // false，非同一对象
            System.out.println("a==b");
        if (a.equals(b)) // true
            System.out.println("a EQ b");
        if (42 == 42.0) { // true
            System.out.println("true");
        }
    }
}

//String 中的 equals 方法是被重写过的,比较的是对象的值
//String类equals()方法：
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

![01.jpg](.\01.jpg)

栈：**一些基本类型的变量和对象的引用变量都是在函数的栈内存中分配**，但对象本身不存放在栈中，而是存放在堆（new出来的对象）或者常量池中（字符串常量对象存放在常量池中。）

堆：存放new 出来的对象

常量池：存放字符串常量和基本类型常量（public static final）

## 3. hashcode()的作用

hashCode() 的作用是**获取哈希码**，也称为散列码；它实际上是返回一个int整数。这个**哈希码的作用**是确定该对象在哈希表中的索引位置。

hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode() 函数。

虽然，每个Java类都包含hashCode() 函数。但是，仅仅当创建并某个“类的散列表”时，该类的hashCode() 才有用(作用是：确定该类的每一个对象在散列表中的位置)；其它情况下(例如，创建类的单个对象，或者创建类的对象数组等等)，类的hashCode() 没有作用。

> 散列表，指的是：Java集合中本质是散列表的类，如HashMap，Hashtable，HashSet。
>
> ```markdown
> 散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码。
> 散列表的本质是通过数组实现的。当我们要获取散列表中的某个“值”时，实际上是要获取数组中的某个位置的元素。而数组的位置，就是通过“键”来获取的；更进一步说，数组的位置，是通过“键”对应的散列码计算得到的。
> ```



当向集合中插入对象时，如何判别在集合中是否已经存在该对象了？（注意：集合中不允许重复的元素存在）

也许大多数人都会想到调用equals方法来逐个进行比较，这个方法确实可行。但是如果集合中已经存在很多的数据，如果采用equals方法去逐一比较，效率必然是一个问题。

此时hashCode方法的作用就体现出来了，当集合要添加新的对象时，先调用这个对象的hashCode方法，得到对应的hashcode值，实际上在HashMap的具体实现中会用一个table保存已经存进去的对象的hashcode值，如果table中没有该hashcode值，它就可以直接存进去，不用再进行任何比较了；如果存在该hashcode值， 就调用它的equals方法与新元素进行比较，相同的话就不存了，不相同就散列其它的地址，所以这里存在一个冲突解决的问题，这样一来实际调用equals方法的次数就大大降低了。

说通俗一点：Java中的hashCode方法就是根据一定的规则将与对象相关的信息（比如对象的存储地址，对象的字段等）映射成一个数值，这个数值称作为散列值。



以HashSet为例，来深入说明hashCode()的作用。

​        假设，HashSet中已经有1000个元素。当插入第1001个元素时，需要怎么处理？因为HashSet是Set集合，它允许有重复元素。
​        “将第1001个元素逐个的和前面1000个元素进行比较”？显然，这个效率是相等低下的。散列表很好的解决了这个问题，它根据元素的散列码计算出元素在散列表中的位置，然后将元素插入该位置即可。对于相同的元素，自然是只保存了一个。
​        由此可知，若两个元素相等，它们的散列码一定相等；但反过来确不一定。在散列表中，
​                           1、如果两个对象相等，那么它们的hashCode()值一定要相同；
​                           2、如果两个对象hashCode()相等，它们并不一定相等。
​                           注意：这是在散列表中的情况。在非散列表中一定如此！

## 4. **hashCode() 和 equals() 的关系**

在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中，用到该某个类时，在这种情况下，该类的“hashCode() 和 equals() ”是有关系的：

* 如果两个对象相等，那么它们的hashCode()值一定相同。这里的相等是指，通过equals()比较两个对象时返回true。
* 如果两个对象hashCode()相等，它们并不一定相等。因为在散列表中，hashCode()相等，即两个键值对的哈希值相等。然而哈希值相等，并不一定能得出键值对相等。

**在这种情况下。若要判断两个对象是否相等，除了要覆盖equals()之外，也要覆盖hashCode()函数。否则，equals()无效。**

例如，创建Person类的HashSet集合，必须同时覆盖Person类的equals() 和 hashCode()方法。
            如果单单只是覆盖equals()方法。我们会发现，equals()方法没有达到我们想要的效果。

```java
import java.util.*;
import java.lang.Comparable;

/**
 * @desc 比较equals() 返回true 以及 返回false时， hashCode()的值。
 *
 */
public class ConflictHashCodeTest1{

    public static void main(String[] args) {
        // 新建Person对象，
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        Person p3 = new Person("aaa", 200);

        // 新建HashSet对象
        HashSet set = new HashSet();
        set.add(p1);
        set.add(p2);
        set.add(p3);

        // 比较p1 和 p2， 并打印它们的hashCode()
        //结果：p1.equals(p2) : true; p1(1169863946) p2(1690552137)
        System.out.printf("p1.equals(p2) : %s; p1(%d) p2(%d)\n", p1.equals(p2), p1.hashCode(), p2.hashCode());
        
        // 打印set
		// 结果  set:[(eee, 100), (eee, 100), (aaa, 200)]
        System.out.printf("set:%s\n", set);
        


       
    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return "("+name + ", " +age+")";
        }

        /**
         * @desc 覆盖equals方法
         */
        @Override
        public boolean equals(Object obj){
            if(obj == null){
                return false;
            }

            //如果是同一个对象返回true，反之返回false
            if(this == obj){
                return true;
            }

            //判断是否类型相同
            if(this.getClass() != obj.getClass()){
                return false;
            }

            Person person = (Person)obj;
            return name.equals(person.name) && age==person.age;
        }
    }
}
```

> 这里重写了Person的equals()。但是，很奇怪的发现：HashSet中仍然有重复元素：p1 和 p2。
>
> 这是因为虽然p1 和 p2的内容相等，但是它们的hashCode()不等；所以，HashSet在添加p1和p2的时候，认为它们不相等。



下面，我们同时覆盖equals() 和 hashCode()方法。

```java
import java.util.*;
import java.lang.Comparable;

/**
 * @desc 比较equals() 返回true 以及 返回false时， hashCode()的值。
 *
 */
public class ConflictHashCodeTest2{

    public static void main(String[] args) {
        // 新建Person对象，
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        Person p3 = new Person("aaa", 200);
        Person p4 = new Person("EEE", 100);

        // 新建HashSet对象
        HashSet set = new HashSet();
        set.add(p1);
        set.add(p2);
        set.add(p3);

        // 比较p1 和 p2， 并打印它们的hashCode()
        //结果：p1.equals(p2) : true; p1(68545) p2(68545)
        System.out.printf("p1.equals(p2) : %s; p1(%d) p2(%d)\n", p1.equals(p2), p1.hashCode(), p2.hashCode());
        
        // 比较p1 和 p4， 并打印它们的hashCode()
        //	结果   p1.equals(p4) : false; p1(68545) p4(68545)
        System.out.printf("p1.equals(p4) : %s; p1(%d) p4(%d)\n", p1.equals(p4), p1.hashCode(), p4.hashCode());
        
        // 打印set
        //	 结果  set:[aaa - 200, eee - 100]
        System.out.printf("set:%s\n", set);
        
    
	

    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return name + " - " +age;
        }

        /**
         * @desc重写hashCode
         */
        @Override
        public int hashCode(){
            int nameHash =  name.toUpperCase().hashCode();
            return nameHash ^ age;
        }

        /**
         * @desc 覆盖equals方法
         */
        @Override
        public boolean equals(Object obj){
            if(obj == null){
                return false;
            }

            //如果是同一个对象返回true，反之返回false
            if(this == obj){
                return true;
            }

            //判断是否类型相同
            if(this.getClass() != obj.getClass()){
                return false;
            }

            Person person = (Person)obj;
            return name.equals(person.name) && age==person.age;
        }
    }
}
```

