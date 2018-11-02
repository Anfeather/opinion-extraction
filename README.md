# 评论上的情感分析：主题与情感词抽取 2017.10.26

查看完整文档： [https://zhuanlan.zhihu.com/p/30468509](https://zhuanlan.zhihu.com/p/30468509)

### 问题描述

- 针对评论网站上的用户评论进行细粒度的情感分析，区别于传统的粗粒度的情感分类（判断一句话的表达情感的正/负性），评论者在一句话中往往会提到多个角度，并在每个角度都抱有不同的观点内容与正/负极性
- 举例：金拱门快餐的食品质量一般，但是服务很周到
- 抽取结果：食品质量 → 一般；服务 → 周到，这里 “食品质量” 与 “服务” 是两个不同的角度（aspect，也叫opinion target），前一个角度对应的情感词（opinion word）是 “一般”，极性为负（negative），后一个角度对应的情感词为 “周到”，极性为正（positive）
- 问题抽象：其实可以看作一个类似于分词问题的 “序列标注” 问题，如下图所示，给出分词后的输入序列，输出两个同等长度的 BIO 序列，一个作为角度词抽取的输出序列结果，一个作为情感词抽取的输出序列结果，这里 BIO 标记为序列标注问题的惯用标记法，“B” 即为欲标记的目标内容的开始词，“I” 为欲标记内容的中间词（或结尾词），“O” 为不标记的内容
- （另外，将这个问题抽象成序列标注问题还有一个很大的缺点，就是角度词和情感词的抽取是单独的，不是成对匹配的，即就算抽取出两个角度词和两个情感词，也不能将每个情感词对应到每个角度词上，万一两个情感词说的都是同一个角度呢，比如 “美国记者我不知道，但是香港记者啊最快且最吼”，这个问题暂时不知道其他的解决办法）

### 目标

- 刚从 keras 转到 tensorflow（极其智障的做法，一定要先学 tf 再学 keras），实践一下
- 实践一下序列标注这类 seq2seq 类问题的操作方式
- 探索一下文本上的细粒度情感分析

### 数据集

> SemEval 2014 ( Restaurant ) ：这是一个标注数据集，看到很多论文都在用，实验使用了Restaurant 的那一部分数据，数据内容是用户在网上对餐厅的评价
> 从数据示例看，数据集只提供了角度词（aspectTerm）的抽取结果，没有情感词的抽取结果，训练加测试数据总共3841条，需要人工标注情感词结果（WTF...）不过还好我拿到了别人标注过的一个结果，提供者是南洋理工大学的 Wang Wenya （感激不尽），也是下面要说的这个参考文献的作者

### 参考文献

> Coupled Multi-Layer Attentions for Co-Extraction of Aspect and Opinion Terms（AAAI 2017）
> 因为这个论文是目前这个数据集上成绩最好的，准确度达到了 0.85+ ，本来想实现一下然后改进一下投个论文啥的，后来我想多了。。。论文中提出的模型不知道高到哪里去了，根本看不懂，也请教过世外高人神秘高手都没什么结果。然后就直接照着写了一遍，结果跑出来准确度只能到 0.7，因为不理解模型原理也没法去调试，所以就放弃了。。。

> 有兴趣的可以去研究下这个论文，目前作者已经提供了源代码下载 https://github.com/happywwy/Coupled-Multi-layer-Attentions
> 在这里放一下模型的架构图，简单来说就是 tensor+GRU+attention 三个内容的组合，反正不能较劲，一贯原则，看不懂就撤

![](https://pic1.zhimg.com/80/v2-a4d4dd6598cb76a131342e0e926ab566_hd.jpg)

### 还是先上结论

- 像主题词与情感词提取这种细粒度情感分析问题并不是一个简单的问题，目前是当成一个序列标注问题来处理，可是无法满足成对提取的要求，目前我还不知道什么更好的办法，分开提取的话，反正据论文里说能达到 86%+
- tensorflow 比 keras 强大，不能只学 keras 这种傻瓜式的高层接口，还是要学习下底层的具体的东西，能实现的东西要更灵活，对模型的理解也更深刻
- 但是 tensorflow 真的是一个特别麻烦且脑回路特别扭曲的一个框架，所以以后可能转战据说很优雅的 pytorch 了
