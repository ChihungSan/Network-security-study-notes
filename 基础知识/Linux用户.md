# Linux用户

cat /etc/passwd下可以查看所有用户

用户分为三种：（看第三列的数值）

1. 超级用户：root，UID=0
2. 普通用户：UID范围（1000~60000）
3. 系统用户（伪用户、特殊用户）：UID范围（201~999）：**不能真正登录系统和使用**。一般安装完系统或者某种程序服务就会自动添加这些伪用户。

如下图所示，除了root和moon,其他都是伪用户

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.6po1n8e62q.webp)