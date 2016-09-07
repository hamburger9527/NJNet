# 怎么解决新帖控制器和精华控制器高度类似的情况?

## 让新帖控制器继承自精华控制器

![](../LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20160801_44.png)

## 但是他们的子控制器, LMJTopicVc加在的数据不一样
### 要判断TopicVc的父控制器的类型, 在去加载`a`参数

![](../LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20160801_48.png)

---

![](../LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20160801_47.png)


###点击新帖控制器的时候, 进入topic的评论页的时候会崩溃, 因为可能没有评论数据
####所以要做一个判断

![](../LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20160801_45.png)
