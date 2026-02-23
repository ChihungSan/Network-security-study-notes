# XML

XML 指可扩展标记语言（eXtensible Markup Language）。XML允许XML的使用者随心所欲地创建标签。也就是说，在“<”和“>”中的单词可以是任意的。这就是所谓的“可扩展”。

XML文档包含三个部分：

1. 一个XML文档声明
2. 一个关于文档类型的定义（对XML文档格式的描述）（三种：**内部DTD文档，**外部DTD文档，内外部DTD文档结合）
3. 用XML标识创建的内容



例子1内部DTD文档

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.6wr9io532x.webp)

例子2外部DTD文档

引用外部DTD文档声明格式：<!DOCTYPE 根元素 SYSTEM "DTD文件路径">

首先，新建一个DTD文档，文件名叫dtd_1.dtd，内容如下:

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.9o0bqqrayr.webp)

然后，新建一个XML文档。引用刚才新建的dtd_1.dtd文档后即可使用其中定义的元素。

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.5fl4gx152u.webp)

例子2内外部DTD文档结合

声明格式：<!DOCTYPE 根元素 SYSTEM "DTD文件路径" [定义内容]>

新建dtd_2.dtd文件，内容如下：

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.1hsr08q7bl.webp)

再新建XML文档，引用新建的dtd_2.dtd文档同时内部增加元素定义：

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.5fl4gx1b59.webp)

参考：

- https://blog.csdn.net/weixin_50260670/article/details/118553042
- https://www.cnblogs.com/findumars/p/5294120.html
- https://blog.csdn.net/lihongmao5911/article/details/133093417