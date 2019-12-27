---
title: 对于QR生成的java源码学习(一):关于java语法内容
copyright: true
date: 2019-03-11 19:53:20
categories:
- 对于QR生成的java源码学习
- java语法
tags:
- java语法
mathjax: true
---

java的一些基本语法在这里，然后QR生成的源码结构非常清晰，值得一读，所以在这里会零零散散记录一些关于源码里面的一些语法的笔记。

<!--more-->

# Object 与 Objects 的区别

Object 是 Java 中所有类的基类，位于java.lang包。

Objects 是 Object 的工具类，位于java.util包。它从jdk1.7开始才出现，被final修饰不能被继承，拥有私有的构造函数。它由一些静态的实用方法组成，这些方法是null-save（空指针安全的）或null-tolerant（容忍空指针的），用于计算对象的hashcode、返回对象的字符串表示形式、比较两个对象。

    Objects.requireNonNull(text);

其中源码是这样写的：
    
    public static <T> T requireNonNull(T obj)

T：obj的相关类型

obj：要检查是否为空的参数

return：如果obj不为空就返回obj，如果是空就返回NullPointerException（空指针异常）

# 关于Matcher

源码是这样的：
    NUMERIC_REGEX.matcher(text).matches()

先把text创建一个匹配此模式的给定输入的匹配器。返回的值再去与NUMERIC_REGEX这个模式(源码中定义的final字段)进行匹配


    public Matcher matcher(CharSequence input)
    Pattern.matcher(CharSequence input)

input：需要被转换为匹配模式的字符串

return：返回这个Pattern的新匹配器

    public boolean matches()

return：当且仅当整个区域序列匹配此匹配器的模式时才返回true

# bitSet

一个long长64bit，所以

    private final static int ADDRESS_BITS_PER_WORD = 6;
    
    private static int wordIndex(int bitIndex) {
        return bitIndex >> ADDRESS_BITS_PER_WORD;
    }

其中是在计算bitIndex个bit对应的是第几个long

# assert

assert()对括号中的条件进行判定，如果条件为真则往下继续运行，条件为假则打印完错误信息然后程序停止运行。

# native

native是c++开发时候用的，java开发是不用它的，它是用来调用操作系统的一些函数的，然而操作系统的函数就是由c++写的，是没有办法看到它的源码的，java对它只能进行调用。是因为这些函数的实现体在DLL中，JDK的源代码中并不包含。

因为native是底层实现的，所以它的速度非常快。

# cloneable接口和Serializable接口

扩展
Java语言提供的Cloneable接口和Serializable接口的代码非常简单，它们都是空接口，这种空接口也称为标识接口，标识接口中没有任何方法的定义，其作用是告诉JRE这些接口的实现类是否具有某个功能，如是否支持克隆、是否支持序列化等。----摘自[论java中的浅克隆和深克隆](https://www.cnblogs.com/Qian123/p/5710533.html)

# 关于序列化

序列化简单来说就保存对象在内存中的状态,也可以说是实例化变量。这是Java提供的用来保存 Object state，一种保存对象状态的机制。只有实现了serializable接口的类的对象才能被实例化。-----摘自[序列化是什么](https://blog.csdn.net/u010486679/article/details/81562344)

然后我谷歌了一会，理解的意思是，序列化就是把实例化的对象状态用二进制保存到一个文件之类的地方，用的时候再取出来。当然，这个接口只是一种申明，说在这里我要实现对某个实例对象进行序列化，而不包含去序列化东西的一个方法，序列化需要自己去实现。在[bitSet](https://aimasa.github.io/2019/03/14/bitSet%E6%BA%90%E7%A0%81%E8%A7%A3%E8%AF%BB/)里面就implement了Serializable这个接口。

或者又如以下的代码：

    import java.io.*;   
      
    public class Box implements Serializable{   
        private int width;   
        private int height;   
    public void setWidth(int width){   
        this.width = width;   
    }   
    public void setHeight(int height){   
        this.height = height;   
    }   
    public static void main(String[] args){   
        Box myBox = new Box();   
        myBox.setWidth(50);   
        myBox.setHeight(30);   
      
        try{   
            FileOutputStream fs = new FileOutputStream("foo.ser");   
            ObjectOutputStream os = new ObjectOutputStream(fs);   
            os.writeObject(myBox);   
            os.close();   
        }catch(Exception ex){   
            ex.printStackTrace();   
        }   
    }   
      
    }   

如果去掉implements Serializable的话，那么下面的writeObject会报错。

6、相关注意事项 

> * 当一个父类实现序列化，子类自动实现序列化，不需要显式实现Serializable接口； 
* 当一个对象的实例变量引用其他对象，序列化该对象时也把引用对象进行序列化； 
* 并非所有的对象都可以序列化，,至于为什么不可以，有很多原因了,比如： 
 *  安全方面的原因，比如一个对象拥有private，public等field，对于一个要传输的对象，比如写到文件，或者进行rmi传输 等等，在序列化进行传输的过程中，这个对象的private等域是不受保护的。 
  * 资源分配方面的原因，比如socket，thread类，如果可以序列化，进行传输或者保存，也无法对他们进行重新的资源分 配，而且，也是没有必要这样实现。 

# 关于克隆clone()方法

	public QrSegment(Mode md, int numCh, BitBuffer data) {//numCh=想在二维码中展示的字的长度。
		mode = Objects.requireNonNull(md);
		Objects.requireNonNull(data);
		if (numCh < 0)
			throw new IllegalArgumentException("Invalid value");
		numChars = numCh;
		this.data = data.clone();  // 做一个完整的副本（final data）
	}

这个clone()方法因为data是BitBuffer这个类，所以调用了BitBuffer这个类里面写的的clone()方法

	public BitBuffer clone() {
		try {
			BitBuffer result = (BitBuffer)super.clone();//创建并返回这个对象的一个副本，“副本”的准确含义可能依赖于对象的类。
			result.data = (BitSet)result.data.clone();//对BitBuffer这个对象里面成员变量再做一次克隆（到BitSet类中的clone()这个方法去了）
			return result;
		} catch (CloneNotSupportedException e) {
			throw new AssertionError(e);
		}
	}

为了进行深度克隆，第一次调用的clone()方法时java的Object这个对象的类的克隆，那个属于浅克隆。
这个克隆方法是用来创建并返回这个对象的一个副本，“副本”的准确含义可能依赖于对象的类。

    类Object：
    
    protected native Object clone() throws CloneNotSupportedException;

浅克隆方法中，如果克隆对象的成员变量是值类型，那么就会把值原原本本复制一份出来，但是如果成员变量是值引用类型，那么复制出来的也会是地址信息，而引用类型的成员对象并没有复制。所以会对引用类型的成员对象再去做一次克隆，让这个复制出来的东西是可以独立于那个克隆对象的东西。

    类BitSet：
    
    public Object clone() {
        if (! sizeIsSticky)
            trimToSize();
    
        try {
            BitSet result = (BitSet) super.clone();
            result.words = words.clone();
            result.checkInvariants();
            return result;
        } catch (CloneNotSupportedException e) {
            throw new InternalError(e);
        }
    }

其中result.data指向的是BitSet这个被实例化过一个类，所以去对BitSet data进行了一次克隆，然后data的里面有words的引用，所以再对这个值做一次clone().

# 关于在一段数据后循环添加0xEC和0x11这两个数

	for (int padByte = 0xEC; bb.bitLength() < dataCapacityBits; padByte ^= 0xEC ^ 0x11)
		bb.appendBits(padByte, 8);

其中0xEC和0x11是8bit8bit循环添加在bb里面的
通过padByte ^= 0xEC ^ 0x11异或，来控制每次添加的8bit，先0xEC然后0x11这样去填充数据编码部分，直到值填满（为什么不能16bit一起填进去？如果16bit的话就会导致填到最后悔有溢出的情况。）


# 关于QrSegment这个类

QrSegment这个类被定义后包括的函数

	public final Mode mode;
	
	/** The length of this segment's unencoded data. Measured in characters for
	 * numeric/alphanumeric/kanji mode, bytes for byte mode, and 0 for ECI mode.
	 * Always zero or positive. Not the same as the data's bit length. */
	public final int numChars;
	
	// The data bits of this segment. Not null. Accessed through getData().
	final BitBuffer data;


# 参考资料

[关于Object和Objects的区别](https://www.cnblogs.com/quiet-snowy-day/p/6387321.html)

[关于bitSet的源码解读](https://www.jianshu.com/p/91d75bf588b8)

[关于native的用法](https://blog.csdn.net/youjianbo_han_87/article/details/2586375)

[关于序列化的详细说明](https://lixh1986.iteye.com/blog/1767076)