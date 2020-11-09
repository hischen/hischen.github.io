---
layout: post
title: "互联网新闻「情感分析」"
subtitle: "Emotional Analysis of Internet News"
date:   2019-12-12 12:00:00
author: "hischen"
header-img: "img/post-bg-rwd.jpg"
header-mask: 0.3
catalog:    true
tags:
  - CCF大数据与计算智能大赛
  - CCF Big Data & Computing Intelligence Contest
  - CCF BDCI
  - 互联网新闻情感分析
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

　　最近几个月参加了CCF大数据与计算智能大赛（CCF Big Data & Computing Intelligence Contest，简称CCF BDCI）分赛道里的 [「互联网新闻情感分析」](https://www.datafountain.cn/competitions/350)比赛（Emotional Analysis of Internet News），获得了初赛(14/2745),复赛(7/2745)的成绩。我的队员有来自北京语言大学的张海鸥同学和来自上海交通大学的彭启明和徐传华同学。

## 赛题任务

　　随着各种社交平台的兴起，网络上用户的生成内容越来越多，产生大量的文本信息，如新闻、微博、博客等，面对如此庞大且富有情绪表达的文本信息，完全可以考虑通过探索他们潜在的价值为人们服务。因此近年来情绪分析受到计算机语言学领域研究者们的密切关注，成为一项进本的热点研究任务。

　　本赛题目标为在庞大的数据集中精准的区分文本的情感极性，情感分为正中负三类。面对浩如烟海的新闻信息，精确识别蕴藏在其中的情感倾向，对舆情有效监控、预警及疏导，对舆情生态系统的良性发展有着重要的意义。

　　参赛者需要对我们提供的新闻数据进行情感极性分类，其中正面情绪对应0，中性情绪对应1以及负面情绪对应2。根据我们提供的训练数据，通过您的算法或模型判断出测试集中新闻的情感极性。

## 评分标准

　　本赛题采用Macro-F1值进行评价。详细评分算法如下：   


$$精确率=\frac{TP}{TP+FP}$$  


$$召回率=\frac{TP}{TP+FN}$$

  
$$F1分数=\frac{2*精确率*召回率}{精确率+召回率}$$    

　

　　其中，TP是真样例，FP是假阳例，FN是假阴例，通过以上公式得到该类f1值，将每一类f1值求平均，即得到Macro-F1值。
## 数据来源

数据多来自新闻网、微信、博客、贴吧等，因此数据中多包含非正式文本。

## 文本长度

将每行title和content拼接在一起，最长的长度为33772个字，最短的只有4个字。大部分文本长度都集中在5000字以下，且接近5000字都很少。

## 数据的label平衡情况

样本显著不平衡，label为"1"的数量最多，大约占49.7%;label为"2"的大约占40%；label为"0"的最少，大约占10.4%。

## 模型

　　我们采用的都是基于预训练语言模型的方法，尝试过的预训练语言模型有BERT、BERT-wwm、ERNIE、XLNet、RoBERTa。具体使用的代码是基于[Baseline](https://github.com/guoday/CCF-BDCI-Sentiment-Analysis-Baseline)上进行修改的。在该Baseline中，将文本截成k段，分别输入BERT等语言模型，然后再将cls的输出，即pooled_output的输出用双向GRU拼接起来，然后再做分类。  
　　将数据分为5个fold，然后训练五个模型，输出五个分类概率，再取平均得到最后的预测标签。  
　　经过对比发现，单模型表现最出色的预训练语言模型是[RoBERTa-wwm-ext-large](https://github.com/ymcui/Chinese-BERT-wwm)。


## 我们尝试过的方法

1.参考[BertHAN](https://github.com/songyingxin/Bert-TextClassification/blob/master/BertHAN/BertHAN.py),我们将预训练语言模型的cls的输出pooled_output输出做最大池化以后，再进行分类，效果没有提高。

2.参考[BertRCNN](https://github.com/songyingxin/Bert-TextClassification/blob/master/BertRCNN/BertRCNN.py),我们将预训练语言模型的最后一层输出送入LSTM，得到的输出再与原始输出进行拼接，再经过一个非线性激活函数（tanh），再进行最大池化，最后再进行分类，发现效果也没有提高。

3.参考CCL 2019 best paper [How to Fine-Tune BERT for Text Classification?](https://arxiv.org/pdf/1905.05583.pdf)这篇论文，我们尝试由后往前截取文本，即取tail-only和head+tail来进行训练，没有取得更好的效果。
  - head-only:keep the first 256*5 tokens
  - tail-only：keep the last 256*5 tokens
  - head+tail: empirically select the first 256*2 and the last 256*3 tokens

4.参考上面的论文，我们尝试进行further pretrain，即先用给定的数据集在预训练语言模型上的基础上再次训练语言模型，再finerune分类任务。有一定的效果。

5.尝试改变lr和weight_decay，没有取得很好的效果。

6.尝试进行数据清洗，效果不是很好。

7.尝试将预测得到的test set的label和train一起训练，即Pseudo-Label(伪标签)。伪标签对模型的提升非常大，其具体做法简单来说就是，把预测结果中多数模型都一致认为的类别当做其正确的标签，然后将该标签及其对应的内容增加到原始训练集中再进行训练。这个过程可以反复迭代，单模型效果越好，伪标签的准确性越高，后面再训练效果会更好。在实际使用中，我们将所有的伪标签数据都加到原始训练集中，也实验了仅加其的10%，但是很多时候前者效果更好。但是采取这种方法会出现一定程度的过拟合问题。

8.将原baseline的3分段样本改成了5分段，每段长度还是256，效果有提升。

9.对于经过预训练模型以后的5个文段的输出，直接通过Highway，然后再通过一个Attention（参考HAN的sentence-level），再进行分类，效果也有提升。

10.针对label不均衡的问题：
  - 我们的损失函数尝试了focal-loss，效果不太好，主要问题是0标签的分数太低，测了一下大概只有0.4几。后来还尝试了一下简单的加权交叉熵，效果也不太好。
  - 我们还尝试使用数据增强的方法来扩充“0”label和“2”label，使他们的数量和“1”label一样多。经过尝试效果并不如意。

11.经过对比试验，我们发现将训练总步数设置为7000步，lr设置为7e-6时效果比较好。

12.我们尝试过在训练2000至3000步的时候冻结bert部分，也有一定提升。

13.用无监督的聚类把训练集聚类成不同领域类别，比如新闻、科技等，这次比赛提供的数据也是从不同领域的网站爬虫下来的。然后针对不同的领域每个领域训练一个模型。预测的时候，也是把测试集分到各个领域里，然后调用各个领域的模型进行分类。这么做的原因是：比如针对不同领域，负面情绪和中性的边界都不太一样，要刻画这个分类边界不是很容易，但是分成很多领域以后，来刻画这种分类边界就很容易了。我们尝试了一下不同的聚类方法，包括K-means,高斯混合模型（GMM）,LDA主题模型等，发现这个聚类的效果并不是很好。

14.尝试用要训练的预训练语言模型的词表来做数据集的预处理、数据清洗。BERT的词表里也有特殊符号，说明BERT训练的语料里也是有这些特殊符号的，预训练模型能捕获特殊符号代表的含义，我们就没必要去掉特殊符号。有效果提升。我们尝试将同义词、emoji表情、HTML标记等去掉，都会造成模型微调效果的下降，原因可能是，首先这些同义词、emoji表情、HTML标记等本身就包含一定的语义信息，去掉的话肯定会影响效果，其次，在预训练语言模型预训练的时候，就包含了很多这样的数据，这些预训练模型中已经学习到了同义词、emoji表情、HTML标记等的语义信息。我们得出的结论是，在使用BERT类似的预训练语言模型微调做文本分类任务时，由于这些预训练模型能学习到非常深层次的语义信息，进行数据清洗是没有必要的。

15.模型融合方面：
  - 尝试用ligtgbm做stacking，没有收到很好的效果。
  - 我们尝试使用已经提交过的且分数较高的文件，计算不同文件之间的相关系数，选择相关系数小即模型差异较大的文件进行投票，效果也有很明显的提升
  - 简单的袋装投票只能提高极小的准确率，而且准确率不会随着模型的增多而提高。此外，由于设置合理的模型权重非常困难，我们尝试了一种新的多分类器融合方式：模型1与模型2采用不同增强的数据集训练，保证了两个模型的差异性，模型3采取了原数据降级处理，只选择难例样本训练。因此，当模型1与模型2对测试样本分类结果相同时，保证了其大概率的准确性，可以跳过模型3输出结果；当模型1与模型2分类结果不同时，进一步引入模型3做难例样本识别。  

16.我们使用的数据增强方法（Data Augmentation）：
  - 本次比赛禁止引入外部数据，所以互译的方法我们没有尝试。
  - 参考针对文本分类任务进行数据增强的这篇论文 [EDA: Easy Data Augmentation Techniques for Boosting Performance on Text Classification Tasks](https://arxiv.org/abs/1901.11196)里的方法，我们设计了针对汉语的数据增强。对原始句子进行同义词替换、随机插入、随机交换、随机删除四种操作，增加训练集的多样性，防止过拟合。经过试验，我们发现效果反而下降了。我们猜测数据增强对于基于预训练语言模型的文本分类任务用处不大，因为预训练语言模型已经包含了足够的数据多样性。


## 总结

　　总结这次比赛我们失误的方面，经过后来与第一名的方案进行对比，我们的欠缺点在于设计的模型不够多，仅有的高分模型太少了，导致差异不够大，做ensemble的时候可用的模型过少。可以尝试将RoBERTa各个层的隐藏状态向量利用起来会好一些。  

　　虽然这次比赛最后的名次并不是很好，复赛的过程中一度上过榜一，不过后来B榜又掉下来了。通过这次比赛我有着非常大的收获。首先，让自己通过这样一个理论与实践相结合的实操过程，对预训练语言模型的应用和理解更深刻了，在实践上对预训练模型的相关代码也掌握的更好，对pytorch的使用更加的熟练；其次，学习到了非常有用的比赛tricks，为以后的科研和比赛积累了宝贵的经验；最后，还认识了一些有趣的小伙伴。最后，再次感谢这次比赛中非常靠谱的队友们！

