# PHPSSION

Session 中文是“会话”的意思，与 Cookie 类似，都是用来储存使用者相关资料的，比如用户名、访问权限、登陆时间等。与 Cookie 最大不同之处在于 Cookie 是将资料存放于客户端电脑之中，而 Session 则是将数据存放于服务器系统之下。

当开启一个 Session 时，PHP 将会创建一个随机的 Session ID（例如“t5is1r7ct740dn390kuv3mpcse”），每个用户的 Session ID 都是唯一的，而且 Session ID 与服务器上存储该用户 Session 数据的文本文件名称相同。

Session ID 会分别保存在客户端和服务器端两个位置。

- 客户端，使用临时的 Cookie 保存在浏览器指定目录中，Cookie 名称默认为“**PHPSESSID**”；
- 服务器端，以文本文件形式保存在指定的 Session 目录中。

默认情况下，这个 Session ID 将作为一个 Cookie 发送给 Web 浏览器，接下来 PHP 页面将使用这个 Cookie 来访问 Session 的信息。

参考：

- https://www.php.cn/faq/453179.html