# URL编码

- 对于Url来说，之所以要进行编码，是因为Url中有些字符会引起歧义。
- Url 编码的原则：就是使用安全的字符去表示那些不安全的字符。
- RFC3986 协议规定 URL 只允许包含两类字符：“保留字符” 和 “未保留字符”。“保留字符” 和 “未保留字符” 都属于是 ASCII 字符。

- **未保留字符有**

1. 英文字母：a-zA-Z
2. 数字：0-9
3. 4个特殊字符：-_.~

- **保留字符有：! \* ' ( ) ; : @ & = + $ , / ? # [ ]** 



- 对 “保留字符” 进行 URL 编码：

如果一个 “保留字符” 在特定上下文中具有特殊含义，并且 URL 中必须使用该 “保留字符” 用于其它目的，那么必须对不表示特殊含义的 “保留字符” 进行 URL 编码。（比如斜线字符/用于 URL 不同部分的分界，但是斜线字符/又需要出现在URL一个路径成分的内部）

URL 编码一个 “保留字符”，首先需要把该 “保留字符” 的 ASCII 的值表示为两个 16 进制的数字，然后在其前面放置转义字符 %，置入 URL 中的相应位置。（比如，URL 编码斜线字符 /，斜线字符 / 的 ASCII 的值为 47，10 进制的 47 等于 16进制的 2F，因此斜线字符 / 经过 URL 编码后为 %2F）

- 对于非ASCII字符，需要使用ASCII字符集的超集进行编码得到相应的字节，然后对每个字节执行百分号编码。对于Unicode字符，RFC文档建议使用utf-8对其进行编码得到相应的字节，然后对每个字节执行百分号编码。如"中文"使用UTF-8字符集得到的字节为0xE4 0xB8 0xAD 0xE6 0x96 0x87，经过Url编码之后得到"%E4%B8%AD%E6%96%87"。



- 关于SQL注入中--+和— 的区分：

1、空格编码为+的情况：

提交表单时请求时Content-Type：**application/x-www-form-urlencoded**的情况下，URL请求查询字符串中出现空格时，需替换为+。

2、其他情况空格编码为%20



参考

- https://www.runoob.com/w3cnote/ascii.html
- https://blog.csdn.net/freeking101/article/details/68922983
- https://cloud.tencent.com/developer/article/2213382
- https://blog.csdn.net/freeking101/article/details/68922983
- https://www.cnblogs.com/xiaxveliang/p/14438336.html

