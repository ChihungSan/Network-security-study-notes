# AJAX

1999年，微软公司发布 IE 浏览器5.0版，第一次引入新功能：允许 JavaScript 脚本向服务器发起 HTTP 请求。这个功能当时并没有引起注意，直到2004年 Gmail 发布和2005年 Google Map 发布，才引起广泛重视。2005年2月，AJAX 这个词第一次正式提出，它是 Asynchronous JavaScript and XML 的缩写，指的是通过 JavaScript 的异步通信，从服务器获取 XML 文档从中提取数据，再更新当前网页的对应部分，而不用刷新整个网页。后来，AJAX 这个词就成为 JavaScript 脚本发起 HTTP 通信的代名词，也就是说，只要用脚本发起通信，就可以叫做 AJAX 通信。W3C 也在2006年发布了它的国际标准。

AJAX 不是一种新技术，而是一个在 2005 年被 Jesse James Garrett 提出的新术语，用来描述一种使用现有技术集合的‘新’方法，包括:HTML 或 XHTML，CSS，JavaScript，DOM，XML，XSLT，XMLHttpRequest。尽管 X 在 AJAX 中代表 XML, 但由于 JSON 的许多优势，目前 JSON 的使用比 XML 更加普遍。JSON 和 XML 都被用于在 AJAX 模型中打包信息。

具体来说，AJAX 包括以下几个步骤。

1. 创建 XMLHttpRequest 实例
2. 发出 HTTP 请求
3. 接收服务器传回的数据
4. 更新网页数据

参考：

- https://wangdoc.com/javascript/bom/xmlhttprequest
- https://juejin.cn/post/6973994091272994847