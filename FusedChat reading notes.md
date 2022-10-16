# FusedChat 论文阅读

## part 1 abstract .

+ 目的：构建一个Tod + oDD 的对话系统。
+ 提出了一个新的数据集FusedChat（基于multiWOZ）。
+ baseline分为两类：一是单独训练一个分类器决定response是tod还是odd形式，称为classification-based two-stage model；二是直接使用一个end2end模型，该模型预测response之前，会先预测response类型，称为 two-in-one fused model。


## Part 2 introduction .

+ 简述了tod和odd任务各自的境况，这个不赘述。
+ 简单说了一下本工作的内容，具体把摘要的三个点谈了一下，总结为本文的contribution。


## Part 3 FusedChat Construction .

### General requirements for the added ODDs

+ FusedChat数据集数据基于MultiWOZ，总共分为两类：TOD-grounded ODDs、ODD-grounded TODs。
+ 数据处理规则：
  + 每一位creator（补充数据的人） 自己完成user和system的对话，确保对话双方能够充分了解对方意思（前提）。
  + creator写odd时谈到的topic要扣住tod的内容
  + creator创建的对话要具有odd的特征，而不是tod，不需要有特定任务。
  + 另外creator也需要注意，不要让system编造出超出尝识的句子。

### Appending ODDs

TOD-grounded ODDs：creator根据给到的multiWOZ中的TOD样本，编写与其相关的ODD文本。至少写一轮的交流，其内容反映了概念或知识 发现现有的TOD部分。（odd turns 在 tod turns后面）

![](https://i.imgur.com/Xbua3Oj.png)


### Prepending ODDs

ODD-grounded TODs：creator根据给到的multiWOZ中的TOD样本，编写与其相关的ODD文本。然后将原本的tod turns 移到写好的odd turns后面，并且将改tod turns 重写（based on co-reference and ellipsis）。

Inter-mode Dependency：这里建议直接看论文，目的时加强重写的tod与odd之间的联系。

![](https://i.imgur.com/ibkkhRr.png)



## Part 4 Approaches for inter-mode dialogues .


### classification-based two-stage model

总共包括三个model：model_{tod}(e2e模型，GPT2，只在FusedChat中tod turns上finetune)、model_{odd}(dialoGPT，只在FusedChat中odd turns上fine-tune)、classifier（bert，在FusedcChat 所有数据上finetune，二分类）.


### two-in-one fused model

一个模型：two-in-one model（GPT2，直接在FusedChat所有数据上finetune，生成哪种类型的回复模型自己决定）。

## FusedChat as a new benchmark

结果没什么好说的，因为是新的数据集，所以没有参考。但是就 ODD-grounded TODs in FusedChat 这个结果来看，感觉它的inform、success、bleu都离目前比较好的模型（在multiWOZ数据集上）有一段距离（比如bart、T5这些肯定是比GPT2好的）


## Discussions and Future Work

一些展望：第一个是主要到FusedChat这个数据集只有一轮切换，是不是可以尝试多轮tod和odd切换；第二个是不同场景的转换对于模型和数据的要求是很严格且昂贵的，比如你需要重新组织数据集然后finetune，所以目前zero-shot和few-shot看起来是个不错的方向。我们如果做好这点，最后的模型也不要仅仅局限在江姐这个人物数据上。
