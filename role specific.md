# 对话系统中role specific相关论文阅读

## Am I me or you

### 摘要（abstract）

该论文说，目前的对话领域中SOTA模型无法在整个对话过程中保持自己的身份，也就是它们不能很好的意识到自己扮演的角色。因此，该论文提出了如何形式化和量化该问题，并给出了一些缓解该问题的解决策略。最后将模型mistaken identity这个问题减少了65%，在LIGHT这个数据集上。并且这个问题仍然有待更好的解决方法提出。

### 判别是否保持identity的方法

+ 首先是明确输入：总的输入包括两部分context和dialogue history。其中context包括三个部分：系统的身份，和对话对象的身份、系统身份的一些描述（persona）、以及对话背景场景和一些相关信息。

![](https://i.imgur.com/jkUacue.png)

+ Measuring Role-Playing Accuracy: RPA

基于Poly-encoder构建一个RPA分类器，和blender中的retrieval方法类似，只不过这里RPA分类器的目标是从response candidate中选出符合角色身份的response，而不只是相关度高的response。

### 提一些缓解该问题的解决方法

#### Utterance Re-Ranking


就是和blender中的retrieval方法一样，只不过打分结果取决于是否符合角色身份以及回答是否相关。

#### Partial And Complete Efficient Re-ranking(PACER)

这个和上面的方法有些区别，它是想说直接给candidate response打分，那么可能造成生成的response不具备多样性，很多具有角色身份的回答无法生成。因此，作者在beam search的过程中，也将候选的beams放到RPA里打分，那么原来的beam中的token的分数会受RPA打分的影响而重新排序，这样就使得最后生成的response的token具有很强的role specific的特点。

#### Unlikelihood

Unlikelihood loss是用来减少生成句子中negative candidate token，这里的negative candidate token就是RPA分类器判定的导致生成的response具有wrong character的token。

#### Multi-objective Training

把RPA classifier和response generation两个model组合再一起，先训RPA，后训generation （注意这里的RPA就是简单的两层transformer layer，不是poly-encoder了）

#### Expanded Decoder Attention

这个方法是我觉得我们比较好借鉴的，就是通过expanded attention的方式来迫使模型始终去注意那些和自己角色身份有关的context，生成模型的decoder中会额外加一层attention，这里文中给了3中方法：

+ profile grounding：单独在输入context抽取一些与角色身份强相关的内容单独编码（根据RPA打分选择对应的subsets），expanded attention去从这一组单独的编码中做K、V的查询，使得模型生成的response包含character and role description.
+ decoder中每个transformer layer根据cross attention weights选择前k个token去re-attend。也就是期望模型自己去学习哪些token更需要去关注。
+ trainable mask：它是说context先过一个mlp，通过softmax选出k个token，然后单独过encoder，然后这些token作为decoder expanded attention的K、V查询集。
+ 最后一个方法，我们知道RPA classifier的目的是判断candidate response是否符合context中的角色信息，那么RPA分类器中的attention weights就说明了context中哪些token包括的角色身份信息更多，那么这些token就更直接去re-attend.

根据实验结果，发现方法1和4比较有效。。。😓

### 总结

根据实验结果看出来，re-rank是最有效的，其次是profile grounding expanded attention。但用在我们江姐数据集上需要手动构建candidate response，看上去很痛苦。


## Building a Role Specified Open-Domain Dialogue System Leveraging Large-Scale Language Models

通过人去筛选输出是否合适，不合适的话进入一下Pipline。主要是做retrieval，如果retrieval的response没有保持角色身份，那么再使用generation model生成response。同样需要构建retrieval candidate。

![](https://i.imgur.com/Ca1kmVW.png)


## Training Millions of Personalized Dialogue Agents

暴力做法，直接构建一个巨大的数据集，包括500万个角色和7亿个基于角色的对话。不可参考。

## Personalizing Dialogue Agents: I have a dog, do you have pets too?

提出了persona-chat数据集，本质上就是在对话之前加入一段persona描述context。

