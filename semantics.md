

#  语义

## 语义相关的英文词汇
semantics ： 语义学

semantic atom  : 语义原子

semantic componentlal analysis : 语义成分分析

semantic field : 语义场

convolutional neural network ：卷积神经网络

## 语义的范畴

## 语义分析有哪些
### 文本语义分析
文本处理的基本方法构成了语义分析的基础

一个文本串除了分词，还需要做词性标注，命名实体识别，新词发现等。通常有两种方案，一种是pipeline approaches，就是先分词，再做词性标注；另一种是joint approaches，就是把这些任务用一个模型来完成。
    
#### 文本基本处理

##### 1.1中文分词

* 基于字符串匹配的分词方法。此方法按照不同的扫描方式，逐个查找词库进行分词。根据扫描方式可细分为：正向最大匹配，反向最大匹配，双向最大匹配，最小切分(即最短路径)；总之就是各种不同的启发规则。
* 全切分方法。它首先切分出与词库匹配的所有可能的词，再运用统计语言模型决定最优的切分结果。它的优点在于可以解决分词中的歧义问题。对于文本串“南京市长江大桥”，首先进行词条检索(一般用Trie存储)，找到匹配的所有词条（南京，市，长江，大桥，南京市，长江大桥，市长，江大桥，江大，桥），以词网格(word lattices)形式表示，接着做路径搜索，基于统计语言模型找到最优路径，最后可能还需要命名实体识别。下图中“南京市 长江 大桥”的语言模型得分，即P(南京市，长江，大桥)最高，则为最优切分。
* 由字构词的分词方法。可以理解为字的分类问题，也就是自然语言处理中的sequence labeling问题，通常做法里利用HMM，MAXENT，MEMM，CRF等预测文本串每个字的tag，譬如B，E，I，S，这四个tag分别表示：beginning, inside, ending, single，也就是一个词的开始，中间，结束，以及单个字的词。 例如“南京市长江大桥”的标注结果可能为：“南(B)京(I)市(E)长(B)江(E)大(B)桥(E)”。由于CRF既可以像最大熵模型一样加各种领域feature，又避免了HMM的齐次马尔科夫假设，所以基于CRF的分词目前是效果最好的。除了HMM，CRF等模型，分词也可以基于深度学习方法来做。

参考文献：[斯坦福课程-语言模型] (http://52opencourse.com/111/%E6%96%AF%E5%9D%A6%E7%A6%8F%E5%A4%A7%E5%AD%A6%E8%87%AA%E7%84%B6%E8%AF%AD%E8%A8%80%E5%A4%84%E7%90%86%E7%AC%AC%E5%9B%9B%E8%AF%BE-%E8%AF%AD%E8%A8%80%E6%A8%A1%E5%9E%8B%EF%BC%88language-modeling%EF%BC%89)

##### 1.2Term Weighting
对文本分词后，接下来需要对分词后的每个term计算一个权重，重要的term应该给与更高的权重。Term weighting在文本检索，文本相关性，核心词提取等任务中都有重要作用。

利用有监督机器学习方法来预测weight。这里类似于机器学习的分类任务，对于文本串的每个term，预测一个[0,1]的得分，得分越大则term重要性越高。既然是有监督学习，那么就需要训练数据。如果采用人工标注的话，极大耗费人力，所以可以采用训练数据自提取的方法，利用程序从搜索日志里自动挖掘。从海量日志数据里提取隐含的用户对于term重要性的标注，得到的训练数据将综合亿级用户的“标注结果”，覆盖面更广，且来自于真实搜索数据，训练结果与标注的目标集分布接近，训练数据更精确。

* 从搜索session数据里提取训练数据，用户在一个检索会话中的检索核心意图是不变的，提取出核心意图所对应的term，其重要性就高。
* 从历史短串关系资源库里提取训练数据，短串扩展关系中，一个term出现的次数越多，则越重要。
* 从搜索广告点击日志里提取训练数据，query与bidword共有term的点击率越高，它在query中的重要程度就越高。

参考文献：[Deep learning for Chinese word segmentation and POS tagging]( http://www.aclweb.org/anthology/D13-1061)

[Max-margin tensor neural network for chinese word segmentation]( http://aclweb.org/anthology/P14-1028)

[Conditional Random Fields: Probabilistic Models for Segmenting and Labeling Sequence Data](http://repository.upenn.edu/cgi/viewcontent.cgi?article=1162&context=cis_papers)

[Conditional Random Fields: Probabilistic Models for Segmenting and Labeling Sequence Data](http://repository.upenn.edu/cgi/viewcontent.cgi?article=1162&context=cis_papers)

[Chinese Segmentation and New Word Detection using Conditional Random Fields](http://scholarworks.umass.edu/cgi/viewcontent.cgi?article=1091&context=cs_faculty_pubs)

### 对于不同层次的处理对象，都需要语义分析：
 - 对于“词”：
   - 词义消歧：判断一个词是不是多义词，对多义词不同含义的表示和判别，结合上下文判断多义词在当前环境下的意思。（方法：根据词典释义、基于语义定义、利用语料库）
 - 对于“句子”：
   - 语义角色标注：识别句子中各词的作用，特别是围绕句中的“动词”识别句中相关成分（谓词-论元结构）。（方法：利用语料库）
 - 对于“篇章”：
   - 指代消解：确定文章中的代词是指的哪个名词。
   - 篇章语义整合：把篇章片段的语义正确整合，得到篇章的全局意图。（方法：基于句法，利用语料库）
 - 可以看到语义分析常常利用语料库（例如pdtb这样的带有标注的熟语料库），使用包括统计、机器学习等方法。

## 语义如何表达

### 文本表示
#### 1. 传统向量空间模型
(1) 主要步骤
* 将文本的基本语言单位（字、词、词组、短语）抽取，组成特征项，用tn表示
* 将tn按在文本中的重要性给出权重wn
* 将文本抽象为（t1,w1,t2,w2,……,tn,wn）简化为（w1,w2,……,wn）即为文本的向量 空间模型。

(2) 权值wn计算
* 布尔：取值1/0表示该特征是否在文本中出现；
* 词频（TF）：wn用特征在文档中出现的频数表示；
* 文档频率(IDF)：通过该特征词在所有文档中的出现频率来分配权重；
* TF/IDF权值：将前两种词的特征相乘得到权重值。

传统向量空间模型有明显的缺点：
* 基于关键字的文档处理方法，依据的是词频信息，两个文档的相似度取决于共同词汇的数量，无法分辨自然语言的语义模糊性。
* 假设词与词之间是相互独立的，一个关键字唯一代表一个概念或语义单元，而实际情况是文档存在很多的一词多义和同义词现象，因此这种假设很难满足实际情况。
* 文档中词与词往往存在一定的关联性，信息检索的本质就是语义的检索，孤立的用关键字来表示文档内容，通过简单的词汇模式匹配进行检索，忽视上下文语境的影响作用，会影响到信息检索的结果的查准率和查全率。

#### 2.	one-hot词向量
NLP 中最直观，也是到目前为止最常用的词表示方法是 One-hot Representation，这种方法把每个词表示为一个很长的向量。这个向量的维度是词表大小，其中绝大多数元素为 0，只有一个维度的值为 1，这个维度就代表了当前的词。

举个栗子，
“话筒”表示为 [0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 ...]

“麦克”表示为 [0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 ...]

每个词都是茫茫 0 海中的一个 1。

这种 One-hot Representation 如果采用稀疏方式存储，会是非常的简洁：也就是给每个词分配一个数字 ID。比如刚才的例子中，话筒记为 3，麦克记为 8（假设从 0 开始记）。如果要编程实现的话，用 Hash 表给每个词分配一个编号就可以了。这么简洁的表示方法配合上最大熵、SVM、CRF 等等算法已经很好地完成了 NLP 领域的各种主流任务。

当然这种表示方法也存在一个重要的问题就是“词汇鸿沟”现象：任意两个词之间都是孤立的。光从这两个向量中看不出两个词是否有关系，哪怕是话筒和麦克这样的同义词也不能幸免于难。

#### 3. Distributed representation 词向量
现在俗称的词向量主要指Distributioned Representation，是是一种低维实数向量。这种向量一般长成这个样子：[0.792, −0.177, −0.107, 0.109, −0.542, ...]。维度以 50 维和 100 维比较常见。这种向量的表示不是唯一的。

Distributed representation 最大的贡献就是让相关或者相似的词，在距离上更接近了。向量的距离可以用最传统的欧氏距离来衡量，也可以用 cos 夹角来衡量。用这种方式表示的向量，“麦克”和“话筒”的距离会远远小于“麦克”和“天气”。可能理想情况下“麦克”和“话筒”的表示应该是完全一样的，但是由于有些人会把英文名“迈克”也写成“麦克”，导致“麦克”一词带上了一些人名的语义，因此不会和“话筒”完全一致。

Word2vec是比较常用的一种，其基本思想是通过训练将每个词映射成 K 维实数向量（K 一般为模型中的超参数），通过词之间的距离（比如 cosine 相似度、欧氏距离等）来判断它们之间的语义相似度.其采用一个三层的神经网络 ，输入层-隐层-输出层。有个核心的技术是 根据词频用Huffman编码 ，使得所有词频相似的词隐藏层激活的内容基本一致，出现频率越高的词语，他们激活的隐藏层数目越少，这样有效的降低了计算的复杂度。而Word2vec大受欢迎的一个原因正是其高效性，Mikolov 在论文中指出，一个优化的单机版本一天可训练上千亿词。这个三层神经网络本身是对语言模型进行建模，但也同时获得一种单词在向量空间上的表示 ，而这个副作用才是Word2vec的真正目标。
  
Word2Vec实际上是两种不同的方法：Continuous Bag of Words (CBOW) 和 Skip-gram。CBOW的目标是根据上下文来预测当前词语的概率。Skip-gram刚好相反：根据当前词语来预测上下文的概率。这两种方法都利用人工神经网络作为它们的分类算法。起初，每个单词都是一个随机 N 维向量。经过训练之后，该算法利用 CBOW 或者 Skip-gram 的方法获得了每个单词的最优向量。

#### 4. 文档向量
即使上述模型对词向量进行平均处理，我们仍然忽略了单词之间的排列顺序对情感分析的影响。即上述的word2vec只是基于词的维度进行"语义分析"的，而并不具有上下文的"语义分析"能力。作为一个处理可变长度文本的总结性方法，Quoc Le 和 Tomas Mikolov 提出了 Doc2Vec方法。除了增加一个段落向量以外，这个方法几乎等同于 Word2Vec。和 Word2Vec 一样，该模型也存在两种方法：Distributed Memory(DM) 和 Distributed Bag of Words(DBOW)。DM 试图在给定上下文和段落向量的情况下预测单词的概率。在一个句子或者文档的训练过程中，段落 ID 保持不变，共享着同一个段落向量。DBOW 则在仅给定段落向量的情况下预测段落中一组随机单词的概率。

word2vec&doc2vec具体训练过程参考：[文本深度表示模型——word2vec&doc2vec词向量模型](http://www.wtoutiao.com/p/238FL4B.html)

其它参考文献：

* 文本表示+向量表示 http://blog.sina.com.cn/s/blog_795b865e0102v984.html
* Deep Learning in NLP （一）词向量和语言模型 http://blog.csdn.net/zhoubl668/article/details/23271225 

#语言模型

语言模型是用来计算一个句子产生概率的概率模型，即P(w_1,w_2,w_3…w_m)，m表示词的总个数。根据贝叶斯公式：P(w_1,w_2,w_3 … w_m) = P(w_1)P(w_2|w_1)P(w_3|w_1,w_2) … P(w_m|w_1,w_2 … w_{m-1})。

最简单的语言模型是N-Gram，它利用马尔科夫假设，认为句子中每个单词只与其前n–1个单词有关，即假设产生w_m这个词的条件概率只依赖于前n–1个词，则有P(w_m|w_1,w_2…w_{m-1}) = P(w_m|w_{m-n+1},w_{m-n+2} … w_{m-1})。其中n越大，模型可区别性越强，n越小，模型可靠性越高。

N-Gram语言模型简单有效，但是它只考虑了词的位置关系，没有考虑词之间的相似度，词语法和词语义，并且还存在数据稀疏的问题，所以后来，又逐渐提出更多的语言模型，例如Class-based ngram model，topic-based ngram model，cache-based ngram model，skipping ngram model，指数语言模型（最大熵模型，条件随机域模型）等。

最近，随着深度学习的兴起，神经网络语言模型也变得火热。用神经网络训练语言模型的经典之作，要数Bengio等人发表的《A Neural Probabilistic Language Model》，它也是基于N-Gram的，首先将每个单词w_{m-n+1},w_{m-n+2} … w_{m-1}映射到词向量空间，再把各个单词的词向量组合成一个更大的向量作为神经网络输入，输出是P(w_m)。本文将此模型简称为ffnnlm（Feed-forward Neural Net Language Model）。ffnnlm解决了传统n-gram的两个缺陷：(1)词语之间的相似性可以通过词向量来体现；(2)自带平滑功能。

从最新文献看，目前state-of-the-art语言模型应该是基于循环神经网络(recurrent neural network)的语言模型，简称rnnlm。循环神经网络相比于传统前馈神经网络，其特点是：可以存在有向环，将上一次的输出作为本次的输入。而rnnlm和ffnnlm的最大区别是：ffnnmm要求输入的上下文是固定长度的，也就是说n-gram中的 n 要求是个固定值，而rnnlm不限制上下文的长度，可以真正充分地利用所有上文信息来预测下一个词，本次预测的中间隐层信息可以在下一次预测里循环使用。

基于RNN的language model利用BPTT(BackPropagation through time)算法比较难于训练，原因就是深度神经网络里比较普遍的vanishing gradient问题（在RNN里，梯度计算随时间成指数倍增长或衰减，称之为Exponential Error Decay）。所以后来又提出基于LSTM(Long short term memory)的language model，LSTM也是一种RNN网络。LSTM通过网络结构的修改，从而避免vanishing gradient问题。
