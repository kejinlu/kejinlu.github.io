---
layout: post
title: Disqus迁移至多说
categories:
- Programming
tags:
- Python
---

Disqus 国内貌似又没发访问了，博客的评论功能基本就废了，想想要不换个国产的得了，至少能用。
所以最终综合了一下选择了多说，注册，更改嵌入代码，也是很快，嵌入的时候有几个参数需要设置一下。
{% raw %}
```js
<div class="ds-thread" data-thread-key="{{ page.url | remove:'index.html' }}" 
data-title="{{ page.title }}" 
data-url="{{ site.url }}{{ page.url | remove:'index.html' }}">
</div>
```
{% endraw %}

这里需要自己设置的是 thread-key 表示文章的唯一标识，这里取了url的path部分；title 也就是文章的标题；以及当前文章的url。这些都可以通过jekyll的相关变量来实现。
设置好了，可以尝试访问下，然后就可以在多说的后台中看到数据了。

接下来就想办法把Disqus的历史评论数据搞过来，好在Disqus和多说都支持数据的导出导入。
Disqus的数据导出，该翻墙的还是得翻墙。导出来的是xml的格式，具体的数据格式说明可以看这里的xsd描述 [https://help.disqus.com/customer/portal/articles/472149-comments-export](https://help.disqus.com/customer/portal/articles/472149-comments-export) 。
Disqus的数据主要包括三个部分：category，thread，post。
一般情况下category就一个默认的，可以忽略，所以重点就是thread和post了，thread代表文章实体，post代表评论实体。

然后我们再看看多说导入数据的格式要求，[http://dev.duoshuo.com/docs/500d0629448f04782b00000a](http://dev.duoshuo.com/docs/500d0629448f04782b00000a) 这个就比较简单了，就thread和post。
接下来的事情就上脚本，做数据转换了。

直接python3来伺候了，python用来做文本处理还是比较便捷的，直接内置了诸如xml，json，optparse这些便捷的lib，手起刀落，一个转换的工具就好了，[https://github.com/kejinlu/disqus-migrate-to-duoshuo](https://github.com/kejinlu/disqus-migrate-to-duoshuo)。
