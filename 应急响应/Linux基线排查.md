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

   ​	/etc/shadow文件的第二列表示账户的密码设置，正常用户是一串加密后的字符，如果是*，代表考虑到安全因素，该用户在涉及之处没有设置密码，例如root用户。如果为root用户设置了密码，并且支持远程连接，那么可能就存在呗口令爆破的风险，此时的措施是安全转移，就是重新设置一个新用户，这个用户和root用户的权限一致，并限制root用户的远程登录限制。此时同样存在爆破的风险，但是从攻击者的角度来说，看到root用户就可以判断是特权管理员账号，但是是一个另外新建的账户，那么我怎么知道它是不是管理员账户呢？这也是一种迷惑吧。

   ​	新建用户设置密码

   ```shell
   useradd ubuntu
   passwd ubuntu
   ```

   ​	为新建的ubuntu账户赋予和root账户一样的权限

   ```shell
   # 赋权
   chmod u+w /etc/sudoers && vim /etc/sudoers
   
   # 在sudoers文件中新加一行
   ubuntu ALL=(ALL:ALL) ALL
   
   # 撤销权限
   chmod u-w /etc/sudoers
   ```
   
   ​	验证下新建ubuntu账户是否可以
   
   ![](https://ChihungSan.github.io/picx-images-hosting/linux_jixian/image.mmctry06.webp)
   
   ​	关闭root远程登录权限
   
   ```
   vim /etc/ssh/sshd_config
   ```
   
   修改为`PermitRootLogin no`，并`systemctl restart ssh`重启ssh
   
   预期效果：root用户无法直接登陆ssh，以sudo新用户身份登录系统后，执行sudo命令可获取root操作权
   
   
   
   > [!IMPORTANT] 
   >
   > ***sudo 和 su***
   >
   > 1.sudo
   >
   > sudo 是一个工具
   >
   > 核心作用是**临时赋予用户额外的特权**（通常是 root 权限），但只针对单条命令或指定的操作
   >
   > 它基于**特权控制系统**（通过 /etc/sudoers 或 /etc/sudoers.d/ 配置文件实现精细授权）
   >
   > 2.su
   >
   > su命令是当前用户用来切换到另一个用户的命令，参数为用户名。执行时会要求输入密码，这个密码是你要切换到的用户的密码。
   >
   > 所以sudo su的作用是：
   >
   > sudo su的含义就是要用root权限运行su命令，既然是用root权限运行su命令，那么就不需要输入切换到的用户的密码了。
   
   
   
   > [!IMPORTANT]
   >
   > 那么如果我就要强制使用强制使用root账户呢？
   >
   > 前面说过，root账户在系统安装的时候没有设置密码，那么要启用root账户，第一件事就是为root账户设置一个密码：
   >
   > ```shell
   > sudo passwd root
   > ```
   >
   > 设置完成后再次查看/etc/shadow文件发现，root用户密码列变为了加密字符串。
   >
   > 但是此时如果重启系统使用root账号登录的话，仍然不可以，只能使用普通账号先登录后，再使用`su root`切换到root账户。
   >
   > 如果要启用root账户可以远程通过ssh登录，需要如下步骤：
   >
   > ```shell
   > sudo vim /etc/ssh/sshd_config
   > ```
   >
   > 修改为`PermitRootLogin yes`，这里插一句，使用 / 搜索到之后，回车定位再 i 进行编辑
   >
   > 保存文件
   >
   > 之后重新启动ssh服务
   >
   > ```shell
   > sudo systemctl reload sshd #我尝试了这条命令不成功
   > 或
   > sudo systemctl restart ssh #这个可以
   > ```
   >
   > 上述配置完成后，就可以远程ssh登录root账户，但这是不安全的。
   >
   > 如果要删除root密码的话，使用`passwd -dl root`，删除后/etc/passwd文件中第二列变成 ！，账户锁定了。
   
   

​	




