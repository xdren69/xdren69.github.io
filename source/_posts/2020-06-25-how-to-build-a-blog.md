---
title: Hexo+Icarus博客搭建记录
date: 2020-06-25 18:41:34
toc: true
categories:
- 博客搭建
tags:
- Hexo,Icarus
excerpt: 这是一篇关于这个blog搭建的流程记录，由于时间问题，没有对所有模块完成debug，在此先记录下来，随后逐一解决
---

### 个人感受

我是一名前端小白，完全是跟着网上的教程走的，首先记录一下个人在看教程时的感受：

1. 对整体架构不是很清楚，只能跟着教程一步一步摸索着走，遇到问题再google
2. 找教程时要先看发布的时间，像Hexo和Icarus更新的挺快的，较早的教程是不适合现在的
3. 不用跟着一个教程走到黑，可以看适合自己的部分，多找几个教程对比验证，就可以找到比较合适的实现方式了

> 网上关于这部分的教程说的已经很好很详细了，我就没必要造重复造轮子了，针对**我自己**（大神请忽略。。）一开始摸不着头脑的情况，我想先列出一个整体框架，让大家清楚自己每走一步都在那个阶段，并清楚下一阶段该做什么，我觉得这是比较有帮助的

### 个人博客与博客网站的区别

这段话是我用了半年多再回过头来写的，个人还是很喜欢个人博客的，因为个人博客可以在本地浏览和编辑，相当于把本地的笔记本的功能与博客的功能结合起来。不满意的笔记可以直接放在Hexo的草稿部分，这一部分不会出现在博客中，只会出现在本地。十分方便

### 整体架构

这个blog搭建在github上的，使用了适用于个人blog的框架[Hexo](https://hexo.io/zh-cn/)，Hexo的主题(theme)可由用户自定义(可以类比android的手机主题)，很多大神[开源了很多主题](https://hexo.io/themes/)，其中我选择的是[Icarus](https://github.com/ppoffice/hexo-theme-icarus)，戳这里看[demo](https://blog.zhangruipeng.me/hexo-theme-icarus/)

> 以上便是整体框架，接下来按照此可以划分出三步流程：

### 整体流程

1. 在github上申请Repositories，并设置其为浏览器可访问的网页形式，具体教程可参照[godweiyang在知乎的回答](https://zhuanlan.zhihu.com/p/35668237)，PS：godweiyang自己改版了matery主题也很不错，大家也可以去看看他的[主页](https://godweiyang.com/)，如果喜欢就可以一路跟着他在知乎的教程啦~

2. 安装并配置Hexo，具体教程同样可参照[godweiyang在知乎的回答](https://zhuanlan.zhihu.com/p/35668237)
   * 安装好后，默认使用landscape主题，不过你现在已经可以尝试在此主题下写博客啦~PS：Hexo本身提供了标签、文章、归档等所有博客该有的功能，下一步的主题选择不影响当前编辑的内容的
   * 此时需要先停下来，**不着急进行下一步**，先阅读[Hexo的官方文档](https://hexo.io/zh-cn/docs/)，PS：很短，官方文档是最直接了解这个框架功能的方式~，先了解一下怎么发文章，图片放在哪个目录下
   
3. 在Hexo的基础上配置Icarus主题，完整过一遍[Icarus的文档](https://blog.zhangruipeng.me/hexo-theme-icarus/)，建议阅读顺序如下：
   1. [Icarus用户指南 - 主题配置](https://blog.zhangruipeng.me/hexo-theme-icarus/Configuration/icarus%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97-%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/)对整体网站的配置
   2. [Icarus用户指南 - 挂件](https://blog.zhangruipeng.me/hexo-theme-icarus/Widgets/icarus%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97-%E6%8C%82%E4%BB%B6/)对于侧边栏的配置
   3. 随后运行整个网站，你会发现还有一些地方在报错，接下来就配置这些[插件部分](https://blog.zhangruipeng.me/hexo-theme-icarus/categories/Plugins/)
   
4. 做一些个性化改进：
   1. 图床配置，我是使用的腾讯云的COS服务，一开始会赠送6个月的服务，教程见[这里](https://cloud.tencent.com/edu/learning/course-1825-20823)
   2. 配置live2d看板娘（就是右手边的那只黑猫~），教程可以看[这里](https://zoutq.com/2019/12/18/hexo-icarus博客更改记录/)
   
5. Latex支持：由于Hexo本身对于数学公式的支持不好，只能识别简单的latex公式，一旦公式复杂了，里面有一些和markdown冲突的符号，就会被Hexo错误解析，之前的做法是将包裹在$$间的latex公式嵌入在\<div\>\<\div\>之间，使得Hexo本身不去转译这段文本。这会造成：在本地Markdown编辑器无法看到公式的内容，如下图所示，十分不方便。具体修改方法是更换Markdown的引擎，可参照[这里](https://zhuanlan.zhihu.com/p/35988761)

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202102/28/095511-853203.png)

> 由于时间有限，不能改完所有bug使网站达到perfect，先记录下来，后期逐渐修改

### bug(待修正)记录

1.  busuanzi网站分析与live2d的看板娘冲突的问题
    有人已提过，相关[blog](https://boyinthesun.cn/post/error-live2d-busuanzi/)；由于加入live2d有两种实现方式，还有一种需要修改源码（官网推荐），教程[yingchi's blog](https://blog.peiyingchi.com/2020/02/25/hexo-icarus-settings/#LOGO%E9%85%8D%E7%BD%AE)
2.  navBar中的svg格式的logo不显示的问题（和footer用的是同一个logo，现在只有footer显示了）有人已提过[issue](https://github.com/ppoffice/hexo-theme-icarus/issues/599)
3.  给每一个页面加图片(optional)

### 最后

欢迎大家评论与交流~