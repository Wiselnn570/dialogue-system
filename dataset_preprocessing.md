# 数据处理规则

目前一共128条数据，每个人处理32条。

+ 数据检查，特别是dialogue history，和background，speaker这些是否能对上，生僻字简化之类的。
+ 所有dialogue history改成双人对话。如下图：
这个样本有3个speaker，要改写成2个样本，每个样本只能有2个speaker且必需包括江姐：
![](https://i.imgur.com/1qIwJSp.png)
改成写成两条数据：
![](https://i.imgur.com/lqAjo5f.png)

+ dialogue history必需是其他人一句话，江姐一句话，不能出现其他人说两句话，然后江姐在说，如下图：
![](https://i.imgur.com/pyoEOxK.png)

+ 每条数据的dialogue history 最后一句话一定是“江姐说：”，并且回答要有效，尽量不要只回答一个，是，好的，这种短词，可以适当自己编一下。
+ 有些对话内容要试着改编，比如：对话中有些utterance只有一句“好。”，可以适当丰富一下。或者有些地方对话人称指代不明，都要适当修改。
+ 数据中某些符号删掉：这里我会给一个vocab.txt文件，里面没有的符号就不要。比如：“  ”  .......
+ 在改数据的时候加入一些相关知识，每个人必需整理20条knowledge，可以包括各个方面，生日，原名，户籍，理想，喜欢的事，喜欢的人等等，尽量丰富。比如：一个生日就算一条knowledge，如下图：

![](https://i.imgur.com/HHtVABn.png)

## 总之，咱们需要注意的就是，保证dialogue history一定要可读，可理解！

