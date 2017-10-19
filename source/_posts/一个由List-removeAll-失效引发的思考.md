---
title: 一个由List.removeAll()失效引发的思考
date: 2017-09-11 16:59:07
tags:
---

本来以为是个错误使用的问题，稍微那么深究一下，发现脑海中，关于这个部分的知识库存已经告急了，可不能啊。
<!--more-->


# <font color= "#008000">前言：

本来以为是个错误使用的问题，稍微那么深究一下，发现脑海中，关于这个部分的知识库存已经告急了，可不能啊。  


# <font color= "#008000"> removeAll() 失效重现

今天做一个批量删除的功能，我使用了 List.removeAll()这个方法，但是该代码执行前后，被操作的列表的 size 并没由发生改变。

排查了一下，是因为两个列表中存储对象不同的原因。

为了更加清楚的理解，我写了简单的小例子，浮现了错误的场景：

实体类：   


```java
public class Bean {

    private int id;
    private String name;
    private String address;

    public Bean(int id, String name, String address) {
        this.id = id;
        this.name = name;
        this.address = address;
    }
}

```


构建场景：   

```java

     ArrayList<Bean>  allStudents = new ArrayList<>();
     ArrayList<Bean>  boyStudents = new ArrayList<>();


     for (int i = 0; i < 10 ; i++) {
         Bean  bean = new Bean(i,"name is "+i,"address is "+i);
         allStudents.add(bean);

     }


     for (int i = 0; i < 5 ; i++) {
         Bean  bean = new Bean(i,"name is "+i,"address is "+i);
         boyStudents.add(bean);

     }

      System.out.println("allStudents.size()------before-------------->"+allStudents.size());
      System.out.println("remove result : "+allStudents.removeAll(boyStudents));
      System.out.println("allStudents.size()-------after-------------->"+allStudents.size());




```

输出结果是：

```

allStudents.size()------before-------------->10
remove result : false
allStudents.size()-------after-------------->10

```


# <font color= "#008000"> 但是，换 String 对象执行 removeAll() 竟然可以成功！

因为操作对象不同，这是一个很简单的原因，但是接下来要实验的另一个小例子，绝对让你非常吃惊，我们讲Bean 替换成 String  字符串试一下。

``` java

       ArrayList<Bean>  allStudents = new ArrayList<>();
       ArrayList<Bean>  boyStudents = new ArrayList<>();
       for (int i = 0; i < 10 ; i++) {
           Bean  bean = new Bean(i,"name is "+i,"address is "+i);
           allStudents.add(bean);

       }


       for (int i = 0; i < 5 ; i++) {
           Bean  bean = new Bean(i,"name is "+i,"address is "+i);
           boyStudents.add(bean);

       }

       System.out.println("allStudents.size()------before-------------->"+allStudents.size());
       System.out.println("remove result : "+allStudents.removeAll(boyStudents));
       System.out.println("allStudents.size()-------after-------------->"+allStudents.size());




```



输出结果是 ：  

```
allStudents.size()------before-------------->10
remove result : true
allStudents.size()-------after-------------->5
```


# <font color= "#008000">揭开这一切的面纱  



从打印结果很明白的看到，removeAll() 成功执行。String也是对象，为什么会这样？代码不会说谎，我们去源码中去寻找答案。
从源码中发现，ArrayList 执行 removeAll() 方法流程如下图所示：

![removeAll流程图](http://upload-images.jianshu.io/upload_images/4373698-9440797c235eb0e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过控制变量法分析，很容易就聚焦到 `equals()`这个方法，这个方法是 Object 的方法,默认实现是比较对象在内存的地址。

```java
public boolean equals(Object obj) {
       return (this == obj);
   }

```


再看一下 String 中 equals() 方法，重写了 Object 的这个方法，不再是比较地址，而是比较字符串是否相同。

```java

public boolean equals(Object anObject) {
     if (this == anObject) {
         return true;
     }
     if (anObject instanceof String) {
         String anotherString = (String) anObject;
         int n = count;
         if (n == anotherString.count) {
             int i = 0;
             while (n-- != 0) {
                 if (charAt(i) != anotherString.charAt(i))
                         return false;
                 i++;
             }
             return true;
         }
     }
     return false;
 }


```


这样的话，引发了一个思考，也就是说，如果自定义对象，重写 equals() 中的实现，也是可以实现非相同对象的情况下，成功 removeAll()的。这里我用 上面例子的 Bean 实体类简单实验一下：

```java
public class Bean {

    private int id;
    private String name;
    private String address;

    public Bean(int id, String name, String address) {
        this.id = id;
        this.name = name;
        this.address = address;
    }


    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Bean bean = (Bean) o;

        if (id != bean.id) return false;
        if (!name.equals(bean.name)) return false;
        return address.equals(bean.address);

    }

    @Override
    public int hashCode() {
        int result = id;
        result = 31 * result + name.hashCode();
        result = 31 * result + address.hashCode();
        return result;
    }
}


```

再次执行第一个例子的程序，ArrayList<Bean> 成功 removeAll，打印信息如下:

```
allStudents.size()------before-------------->10
remove result : true
allStudents.size()-------after-------------->5
```

# <font color= "#008000">重写 equals() 方法一定要符合规范！

但是这里我们要特别注意的是，当我们重写 equals() 方法的时候，一定要遵守它的规范,否则在程序使用中，使用错误实现 equals() 的类时可能出现无法预料的问题，而且很有可能，找了很久都定位不到问题在哪！，想想都后背发冷。

以下是 Object 中 equals()的方法说明注释，绝对原汁原味！但是我相信肯定有人看了一遍还是不知道说的啥，非常正常，我看两遍也是，英语渣没办法，只能花更多时间去理解它，因为真的真重要。

``` java

/**
 * Indicates whether some other object is "equal to" this one.
 * <p>
 * The {@code equals} method implements an equivalence relation
 * on non-null object references:
 * <ul>
 * <li>It is <i>reflexive</i>: for any non-null reference value
 *     {@code x}, {@code x.equals(x)} should return
 *     {@code true}.
 * <li>It is <i>symmetric</i>: for any non-null reference values
 *     {@code x} and {@code y}, {@code x.equals(y)}
 *     should return {@code true} if and only if
 *     {@code y.equals(x)} returns {@code true}.
 * <li>It is <i>transitive</i>: for any non-null reference values
 *     {@code x}, {@code y}, and {@code z}, if
 *     {@code x.equals(y)} returns {@code true} and
 *     {@code y.equals(z)} returns {@code true}, then
 *     {@code x.equals(z)} should return {@code true}.
 * <li>It is <i>consistent</i>: for any non-null reference values
 *     {@code x} and {@code y}, multiple invocations of
 *     {@code x.equals(y)} consistently return {@code true}
 *     or consistently return {@code false}, provided no
 *     information used in {@code equals} comparisons on the
 *     objects is modified.
 * <li>For any non-null reference value {@code x},
 *     {@code x.equals(null)} should return {@code false}.
 * </ul>
 * <p>
 * The {@code equals} method for class {@code Object} implements
 * the most discriminating possible equivalence relation on objects;
 * that is, for any non-null reference values {@code x} and
 * {@code y}, this method returns {@code true} if and only
 * if {@code x} and {@code y} refer to the same object
 * ({@code x == y} has the value {@code true}).
 * <p>
 * Note that it is generally necessary to override the {@code hashCode}
 * method whenever this method is overridden, so as to maintain the
 * general contract for the {@code hashCode} method, which states
 * that equal objects must have equal hash codes.
 *
 * @param   obj   the reference object with which to compare.
 * @return  {@code true} if this object is the same as the obj
 *          argument; {@code false} otherwise.
 * @see     #hashCode()
 * @see     java.util.HashMap
 */
public boolean equals(Object obj) {
    return (this == obj);
}


```

equals 方法实现的时候必须要满足的特性：

**1.（reflexive）自反性：**

对于任何非 null 的引用值 x,x.equals(x) 必须为 true；

**2.（symmetric）对称性：**  
对于任何非 null 的引用值 x,y,当且仅当 y.equals(x) 返回 true 时，x.equals(y) 也要返回 true 。

**3.（transitive）传递性：**  
对于任何非 null 的引用值 x,y,z, 如果 x.equals(y) 返回 true，y.equals(z) 返回 true，那么 x.equals(z) 一定要返回 true。

**4.（consistent）一致性：**  
对于任何非 null 的引用值 x,y,只要 equals() 方法没有修改的前提下，多次调用 x.equals(y) 的返回结果一定是相同的。   

**5.（non-nullity）非空性**   
对于任何非 null 的引用值 x,x.equals(null) 必须返回 false。

这些特性约束我们重写 equals()的时候，写条件判断一定要谨慎，下面是提供的一个模版：

``` java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;

    (MyClass) myclass = (MyClass) o;
    if (this.xx != bean.myclass.xx) return false;
    return myclass.equals(myclass.xx);

}

```
**重要！覆盖 equals 时，一定要同时覆盖 hashCode**  

这样做的目的是保证每一个 equals()返回 true 的两个对像，要有两个相同的 hashCode 。

在上面演示的例子中，不覆盖 hashCode ，equals 方法表现的也很好，调用 List.removeAll 也能成功执行。看似是没有什么问题，但是当我们试图使用 hashMap 做存取操作的时候，就会出现问题。

```java

HashMap<Bean,String> allStudents = new HashMap<>();

for (int i = 0; i < 10 ; i++) {
    Bean  bean = new Bean(i,"name is "+i,"address is "+i);
    allStudents.put(bean,"i :"+i);

}
Bean bean = new Bean(1,"name is 1","address is 1");

System.out.println(" allStudents.get(bean)----------------------->"+ allStudents.get(bean));


```
输出结果：

Bean 中不正确覆盖 hashCode()，取不到值：

```
allStudents.get(bean)----------------------->null

```

Bean 中正确覆盖 hashCode()，能取到值：

```
allStudents.get(bean)----------------------->i :1

```

原因在于，HashMap 执行 get() 操作的时候是通过散列码，也就是对象的 HashCode 来搜索数据的。所以，当不重写 hashCode() 方法或者重写的不规范的时候，就会出现这样的问题。
使用散列码判断对象的，有 HashMap ，HashSet,HashTable 等，因此，覆盖 equals() 时，一定要同时覆盖 hashCode()。




# <font color= "#008000">快速生成euqals() and hashCode()！

看到上面的解释，估计大家都感觉覆盖这俩方法的时候都有点害怕了，但是告诉大家一个好消息，Android Studio 有这两个方法的快速生成模版，使用方法是 右键->generate->euqals() and hashCode(),也可以直接使用快捷键 command+N ->euqals() and hashCode()。

![快速生成equals和hashCode](http://upload-images.jianshu.io/upload_images/4373698-a113aa1d0cc5a626.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




# <font color= "#008000">最后


到这里，我想到，一个在面试的时候，经常被问到的 java 基础题：

> java 中 == 和 equals 和 hashCode 的区别?

我想现在，如果再被问到这个问题，我肯定可以比之前回答的要好一点了。  

java 中有两种类型，值类型和引用类型。其中，`==` 值类型和引用类型都可以运算，equals 和 hashCode 是引用类型特有的方法。

对于值类型，`==` 比较的是它们的值，对于引用类型，`==` 比较的是两者在内存中的物理地址。

equals() 是 Object 类中的方法，默认实现是使用 `==` 比较两个对象的地址，但是在一些子类，例如 String，Float,Integer 等，它们对 equals进行覆盖重写，就不再是比较两个对象的地址了。

hashCode() 也是 Object 类的一个方法。返回一个离散的 int 型整数。在集合类操作中使用，为了提高查询速度。

当覆盖 equals() 的时候，一定要同时覆盖 hashCode()，保证 x.equals(y) 返回为 true 的时候，x,y 要有相同的 HashCode 。

回答完毕。


# <font color= "#008000"> 参考资料

Effective Java 中文版第二版



# <font color= "#008000"> 最后 </font>

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注 (*^o^*)。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/836a36d6a91d859428783f8ea2ce85d7.png)

</div>
