# Linux基线排查

## 一、Linux账号管理与认证授权

### 1.用户账号口令设置

1. 检查空口令账户

   执行：awk -F: '($2==""){print$1}' /etc/shadow

   没有输出代表没有空口令账户

   ![](https://ChihungSan.github.io/picx-images-hosting/linux_jixian/image.4n89b1spr7.webp)

2. 检查密码安全策略

   执行：vim /etc/login.defs

   检查以下参数：

   ​	PASS_MAX_DAYS 密码最长过期天数 参考值90

   ​	PASS_MIN_DAYS 密码最小过期天数 参考值80

   ​	PASS_MIN_LEN 密码最小长度 参考值8

   ​	PASS_WARN_AGE密 码过期警告天数参考值 7

   键入“ / ”来检索字符并修改，加不加等于号好像都是可以的。也可以安装mousepad进行图形化修改

3. root用户远程登录限制（ssh）

   







shadow文件第二列为！是什么意思



ssh服务开启之后重启还会不会关闭



sudo 服务和root什么关系



为什么root没有密码？