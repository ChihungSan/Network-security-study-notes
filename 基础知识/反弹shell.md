# 反弹shell

在渗透测试实战中，我们经常会遇到Linux系统环境，而让Linux主机反弹个shell是再常见不过的事情了。反弹shell，就是攻击机监听在某个TCP/UDP端口为服务端，目标机主动发起请求到攻击机监听的端口，并将其命令行的输入输出转到攻击机。

反弹shell的方式有很多，那具体要用哪种方式还需要根据目标主机的环境来确定，比如目标主机上如果安装有netcat，那我们就可以利用netcat反弹shell，如果具有python环境，那我们可以利用python反弹shell。如果具有php环境，那我们可以利用php反弹shell。

参考：

- https://xz.aliyun.com/t/9488?time__1311=n4%2BxuDgD9AdWqhDBqDwmDUhixBGCs3DnYRoD&alichlgref=https%3A%2F%2Fwww.google.com%2F