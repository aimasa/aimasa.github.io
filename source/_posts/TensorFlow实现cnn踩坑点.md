---
title: TensorFlow实现cnn踩坑点
copyright: true
date: 2019-12-25 20:20:00
categories:
- nlp自然语言处理
- CNN语言模型
tags:
- nlp
---

因为是新手，所以跟着网上的教程用`tensorflow`框架实现了`CNN`模型，并且开始了训练。在实现过程中经历了很多的坑，今天记录一下在实现过程中遇到过的坑。

[教程博客](https://blog.csdn.net/White_Idiot/article/details/79253129)

<!--more-->

# 数据预处理

我一直觉得数据处理只是简单的对数据进行数据加载，然后清洗（去掉停用词、标点符号、转义字符--比如`\n`以及空格等冗余数据）

其实，要做的处理并不止这些。

## 标签处理

在预处理中需要把不同文本对应的标签转换为one-hot类型：

```python
def dense_to_one_hot(self, labels_dense, num_classes):    
    """Convert class labels from scalars to one-hot vectors.""" 
    num_labels = labels_dense.shape[0]    
    index_offset = np.arange(num_labels) * num_classes    
    labels_one_hot = np.zeros((num_labels, num_classes))    
    temp = index_offset + labels_dense.ravel()    
    labels_one_hot.flat[temp] = 1    
    return labels_one_hot
```

在后面隐藏层输出结果与对应正确标签计算时候是需要的。

因为后面：

```python
with tf.name_scope("output"):    
    W = tf.Variable(tf.truncated_normal([self.num_filters_total, self.num_classes]), name="W")    
    b = tf.Variable(tf.constant(0.1, shape=[self.num_classes]), name="b")       if l2_reg_lambda:        
        W_l2_loss = tf.contrib.layers.l2_regularizer(l2_reg_lambda)(W)    
        tf.add_to_collection("losses", W_l2_loss)    
        self.scores = tf.nn.xw_plus_b(self.h_drop, W, b, name="scores")             # (self.h_drop * W + b)    
        self.predictions = tf.argmax(self.scores, 1, name="predictions")           # 找出分数中的最大值，就是预测值
```

全连接输出时候，里面会通过`tf.nn.xw_plus_b()`计算得到的不同种类对应得到的`score`和以`one-hot`表示的标签下标值进行`softmax`计算误差，也就是`loss`值。

```python
losses = tf.nn.softmax_cross_entropy_with_logits_v2(self.input_y, self.scores)
# softmax_cross_entropy_with_logits_v2第一个是对应标签的labels值，第二个logits值是预测分数，会自动转为对应的标签值进行计算
```

## 文本处理

文本处理除掉上面说过的去停用词去标点符号去转义字符等，还需要把文本处理成机器可读取的模式，就是把文本变成数字。

我刚开始对这个的理解就是把文本转换为对应的词向量，再喂进模型，结果发现，并不是这样。

如果直接把词向量做词嵌入处理，这样会导致工程浩大（因为会有很多重复的词出现在文档里，在喂进模型前要把对应的词向量矩阵捣鼓出来，这样的话，就会消耗计算机大量的内存，降低速率。）所以我们会先把清洗后的文本转换成对应的文本下标的`ID`

```python
vocab_processor =learn.preprocessing.VocabularyProcessor(normal_param.max_document_length)
x = np.array(list(vocab_processor.fit_transform(x_texts)))
```

【注：`x_texts`是已经经过清洗的数据，`x`就是`x_texts`对应的下标矩阵】

然后传入`cnn`模型中`word embeding`部分，找到对应的词向量，进行后续操作。

```python
with tf.device('/cpu:0'), tf.name_scope("embedding"):    
    self.W = tf.Variable(tf.random_uniform([self.vocab_size, self.embedding_size], -1.0, 1.0))   
    # 随机化W，得到维度是self.vocab_size个self.embedding_size大小的矩阵，随机值在-1.0-1.0之间 
    self.embedding_chars = tf.nn.embedding_lookup(self.W, self.input_x)    
    # 从id(索引)找到对应的One-hot encoding    
    self.embedding_chars_expanded = tf.expand_dims(self.embedding_chars, -1)    # 增加维度
```

`W`相当于词袋模型，每个词都是个独立的个体，所以通过`tf.variable`设置随机变量，然后再根据前面处理好的下标过来找到不同的词对应的embeding。

因为是词袋模型，所以不好的地方在于，会丢失上下文的关系，造成准确度偏低的情况。

所以会有词预处理模型，比如`Word2vec`、最近特别热门的`Bert`这些模型，都会想办法获取词的上下文关系，得到对应的词向量，增加预测结果的准确性。

# 模型搭建

在搭建`CNN`模型的过程中也碰到过一些问题，因为是新手，看着论文，也不知道如何下手去搭建这个模型，所以跟着教程才写了出来，在讨论碰到的问题之前，先总结一下经验。

先不提python语法问题，python是个很好的东西，对新手来说，有很多库，上手很快，是个很优美的语言。特别是在你了解到它的面向对象的特性的时候，它的优美实现会让你瞬间爱上它，反正我还没学会python。它是面向对象，我是面向百度谷歌。

接下来要说的是tensorflow框架了，TensorFlow框架是个坑，它升级到2.0版本，和1.0版本有很多不兼容的地方，但因为是新出的2.0，普及性还不是很广，出了问题翻遍它官方的issue都不一定能找到答案，所以我配合我的cuda版本装了TensorFlow1.13.1。其实这个因果关系不是特别紧密，我只是想吐槽它升级版本顺便改了改语法，让我初学者翻了一下午的资料，才发现为啥教程上是这样写没问题，我这样写就有问题了。

回到正文。

在用代码搭建`CNN`模型中，作者用了`tf.name_scope("")`把这个实现分成了词嵌入、卷积-池化、dropout、输出、loss值计算、准确值计算这六个部分

`tf.name_scope("")`：它给我感觉就是把复杂的网络分开来，变成几个小的模块，不会因为网络复杂了，而数据混乱没有条理，导致出错。实际上和我想的差不多吧。

## loss绝对值增大

当时loss值的绝对值越来越大，并没有收敛的趋势，我找了很久的问题，在学长的帮助下才发现出在这里：

```python
losses = tf.nn.softmax_cross_entropy_with_logits_v2(self.input_y, self.scores)
```

这是第一个坑， softmax_cross_entropy_with_logits_v2第一个是对应标签的labels值，第二个logits值是预测分数，会自动转为对应的标签值进行计算出相应的loss值。

## 对tf运行的理解

```python
        self.input_x = tf.placeholder(tf.int32, [None, sequence_length], name="input_x")
        self.input_y = tf.placeholder(tf.float32, [None, num_classes], name="input_y")
```

`tf`和平常代码不一样，日常代码都是先赋值，再进行运算，而`tf`是先定义运算，再进行初始化赋值，就像上面这两行代码一样，这两行代码是预定义了`input_x`和`input_y`的值，然后再在后面

```python
step, summaries, loss, accuracy = sess.run([global_step, dev_summary_op, cnn_init.loss, cnn_init.accuracy], feed_dic)
```

把定义好`feed_dic`的值，喂进初始化后的`cnn`模型中，再返回`[global_step, dev_summary_op, cnn_init.loss, cnn_init.accuracy]`里面的值（`cnn_init`就是初始化后的`CNN`模型）

`global_step, dev_summary_op`和后面的值不一样，不是从模型里面输出的值，

## 模型存取

在程序的运行中，会因为各种问题导致程序突然中止，所以我想要运行到一定时间就自动保存一个正在学习的数据模型，再次运行时候，如果已经有保存了的模型，就加载出来。

```python
checkpoint_dir = os.path.abspath(os.path.join(out_dir, "checkpoint"))
checkpoint_prefix = os.path.join(checkpoint_dir, "model")
if not os.path.exists(checkpoint_dir):    
    os.makedirs(checkpoint_dir)
saver = tf.train.Saver(tf.global_variables())ckpt = tf.train.latest_checkpoint(checkpoint_dir)
if ckpt:    
    saver.restore(sess, ckpt)    
    print("CNN restore from the checkpoint {0}".format(ckpt))
```

继续训练后，运行了好几遍，发现并没有办法加载已有模型继续训练，究其原因，发现，问题出在`session`初始化上面。`tf.Session.run`中间的初始化参数不是和我想的那样随便加的TT。

```python
sess.run(tf.global_variables_initializer())
sess.run(tf.local_variables_initializer())
```

[参考博文](https://www.jianshu.com/p/bebcdfb74fb1)

一个是初始化全局变量，但模型中的局部变量并没有初始化，所以，就再加上`sess.run(tf.local_variables_initializer())`初始化局部变量。

# 电脑性能限制

因为实验室不是专门做深度学习这块，所以没有较好的设备去完成实验，目前实验设备有：

1.  CPU

   	Intel(R) Core(TM) i7-7700 CPU @ 3.60GHz
   	
   	基准速度:	3.60 GHz
   	插槽:	1
   	内核:	4
   	逻辑处理器:	8

2. GPU 1块

   	NVIDIA GeForce RTX 2060 SUPER
   	
   	专用 GPU 内存	8.0 GB
   	共享 GPU 内存	8.0 GB
   	GPU 内存	16.0 GB

3. 内存

   	16.0 GB

4. 磁盘 1 (D: F: G: E:)

   	WDC WD10EZEX-21M2NA0
   	
   	容量:	932 GB
   	已格式化:	932 GB
   	系统磁盘:	否
   	页面文件:	是
   	
   	读取速度	199 KB/秒
   	写入速度	41.0 KB/秒
   	活动时间	56%
   	平均响应时间	6.2 毫秒

目前硬件上面临的有几个困难：

1. GPU利用率过低，不知道如何优化
2. 读取文档的速度过慢，优化方法下面介绍

其实总结一下就是，硬件设备性能低，没法加快程序运行速度。

# 优化运行方案

因为内存的不足，所以在学长的建议下，我把我需要输入的数据进行分段处理，这样一来能快速看到效果，二来被打断的话，不需要重新加载那么庞大的数据继续跑，三来我电脑性能限制，我没法一次性跑那么多数据。其实第三点才是最重要的。

这就要来说说`python`中`yield`的语法了。

`yield`相当于`return`，不同的是，它不会结束程序的运行，而是让程序在经过yield的处理后，回到开始的循环，继续向下执行。可能这样干说不太能很好的表达，所以我写一段。

```python
def batch_iter(a, b, num_foreach):
    for i in range(num_foreach):
        c = a + b
        yield c

if __name__ == "__main__":
    batch = batch_iter(1,2，3)
    for b in batch:
        t = b
        print(t)
```

输出：

```
3
3
3
```

就是迭代器，里面`batch_iter`这个方法就是迭代时候会使用的方法，然后迭代完`c`就是输出的值。

# 结果

等我慢慢运行吧，已经两天了，还没运行完 呢，。