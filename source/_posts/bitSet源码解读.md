---
title: bitSet源码解读
copyright: true
date: 2019-03-14 13:49:01
categories:
- java语法
- bitSet部分源码解读
tags:
- java语法
---

因为最近在看二维码相关，在github上拉下来的代码里面有用到bitSet库，所以在试着看源码。
看了一段时间，发现就算理清了逻辑结构也不明白什么意思，所以就去网上查了一下bitSet是做什么的。

都说bitSet是用来对大量的数据进行整理，减少内存负担（我是这样理解的），bitSet是用长整型对数据进行存储，比起用二进制用int对数据进行存储的方法相比，确实可以减少许多内存【这里对内存概念不是很清楚，日后补全。

<!--more-->

# 部分源码分析

## 定义好的关键词(大概可以这样叫)

    /*
     * bitSet被打包为字的数组
     * word的大小选择完全取决于它的性能
     */
    private final static int ADDRESS_BITS_PER_WORD = 6;
    private final static int BITS_PER_WORD = 1 << ADDRESS_BITS_PER_WORD;
    private final static int BIT_INDEX_MASK = BITS_PER_WORD - 1;

    /* Used to shift left or right for a partial word mask */
    private static final long WORD_MASK = 0xffffffffffffffffL;

这是该方法中定义的参数，其中ADDRESS_BITS_PER_WORD=6是指在java中long型是占8个字节，64bit（$\,2^{6}\,=\,64byte\,$）所以对应的二进制就是6.

    private long[] words;
    private transient int wordsInUse = 0;//已使用的范围的下标
    private transient boolean sizeIsSticky = false;//表示用户是使用默认的words的大小(64bit)还是自定义

## 关于wordIndex的定义

这里是bit下标对应的word下标的计算过程：

    private static int wordIndex(int bitIndex) {
        return bitIndex >> ADDRESS_BITS_PER_WORD;
    }


## bitSet的构造函数(里面有对words这个数组的定义)

其中，bitSet的构造函数是：

    public BitSet(int nbits) {
        // nbits can't be negative; size 0 is OK
        if (nbits < 0)
            throw new NegativeArraySizeException("nbits < 0: " + nbits);

        initWords(nbits);
        sizeIsSticky = true;
    }

    private void initWords(int nbits) {
        words = new long[wordIndex(nbits-1) + 1];
    }


这是在初始化时候给bitSet中的long[] words分配大小时候，就会调用这个构造函数，但是如果初始化时候去输入long[] 这个数组的话，相当于直接定义long[] words这个数组。

    private BitSet(long[] words) {
        this.words = words;
        this.wordsInUse = words.length;//这里wordsInUse表示的是定义的数组的长度，如果没有定义长度的话，那么这个值默认为零。
        checkInvariants();
    }

然后还有无参构造，这个就是用默认的数组大小64bit

    public BitSet() {
        initWords(BITS_PER_WORD);
        sizeIsSticky = false;
    }

## bitSet的clear方法(对words这个数组进行清零)

其中wordsInUse在源码中出现频率非常高，这个参数是用来记录word数组中已经使用了的个数。

    public void clear(int bitIndex) {
        if (bitIndex < 0)
            throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

        int wordIndex = wordIndex(bitIndex);//计算这个bit下标实际上是第几个word(Long)
        if (wordIndex >= wordsInUse) //如果这个下标不在wordsInUse范围内，那么返回，因为没有必要进行别的操作了。
            return;

        words[wordIndex] &= ~(1L << bitIndex); //把words[wordIndex]中的值设置为false（就是清零）因为<<这是左移。取反后就全是零了，再进行与运算，就相当于设置该bitIndex这个位为false，也就是将该位清零。

        recalculateWordsInUse();
        checkInvariants();
    }

## 关于和clear对应的set方法(对words这个数组进行赋值)

这是set函数，就是把bitIndex的对应的位置设置为true，然后返回设置了true的地方的下标：

    public void set(int bitIndex) {
        if (bitIndex < 0)
            throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

        int wordIndex = wordIndex(bitIndex);
        expandTo(wordIndex);//判断是否需要扩容，如果需要，则进行扩容

        words[wordIndex] |= (1L << bitIndex); // 把这个bitIndex位设置为true

        checkInvariants();
    }


## 关于clear每次最后都要用到的recalculateWordsInUse()方法

更新wordsInUse，判断实际存储大小。

    private void recalculateWordsInUse() {
        //遍历words这个数组，直到找到一个是true的地方
        int i;
        for (i = wordsInUse-1; i >= 0; i--)
            if (words[i] != 0)
                break;

        wordsInUse = i+1; // 就是让wordsInUse的大小更改为实际单词存储量
    }

## clear和set方法中都会出现的checkInvariants()方法

判断这个word数组是否溢出，是否需要抛出异常。

    private void checkInvariants() {
        assert(wordsInUse == 0 || words[wordsInUse - 1] != 0);
        assert(wordsInUse >= 0 && wordsInUse <= words.length);
        assert(wordsInUse == words.length || words[wordsInUse] == 0);
    }

## 克隆方法clone()

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

[参考深克隆和浅克隆](https://aimasa.github.io/2019/03/11/%E5%AF%B9%E4%BA%8EQR%E7%94%9F%E6%88%90%E7%9A%84%E6%BA%90%E7%A0%81%E5%AD%A6%E4%B9%A0/)

## trimToSize()方法

当word的长度或者内容是自定义的情况下则调用的
    private void trimToSize() {
        if (wordsInUse != words.length) {
            words = Arrays.copyOf(words, wordsInUse);//把实际用的数据拷贝出来放进words里面
            checkInvariants();
        }
    }

## get()方法

    public boolean get(int bitIndex) {
        if (bitIndex < 0)
            throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

        checkInvariants();

        int wordIndex = wordIndex(bitIndex);
        return (wordIndex < wordsInUse)
            && ((words[wordIndex] & (1L << bitIndex)) != 0);//判断数据是否是在使用范围内，该bitIndex位是否为0.
    }

就是前面的先是判断下标是否在存储完数据使用过的范围内，如果不在就无效。再判断bitIndex对应的数据位是否是零，如果都是的话返回true。

# 参考网站

[对bitSet内存存储方法进行详细介绍，但其他的写的不太明了](http://www.voidcn.com/article/p-bqoyhfid-rv.html)

[举例说明了bitSet的用法，内容详细](https://www.cnblogs.com/larryzeal/p/7710389.html)

[对bitSet源码中的重要方法进行解读](https://www.jianshu.com/p/91d75bf588b8)

[简要介绍了bitSet里面的类](https://www.jianshu.com/p/00b38e7ec2f2)