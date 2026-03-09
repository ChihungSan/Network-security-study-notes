# Gophish 钓鱼邮件部署

## 一、环境配置

### 1.1 购买域名

购买域名买一个和官方邮箱类似的域名，以此来达到迷惑的目的。

例如官方邮箱是：aaa.com，所以我就买了一个aaa.help（主要是因为便宜）

### 1.2 使用邮件推送服务

所谓邮件推送就是服务商帮助我们去发送邮件的服务。这里我使用的是阿里云的邮件推送服务（阿里云每日有2000封邮件推送的免费份额，可以白嫖）。

阿里云邮件推送服务官网：https://dm.console.aliyun.com/

在阿里云邮件推送服务官网点击左侧“邮件设置” - “发信域名”进行发信域名的配置。

点击右上角的“新建域名”，将刚才购买的域名填入。之后就会下下面列表生成一个记录，图中显示的是我已经通过验证的域名，如果是刚新建的域名的话，不会显示“验证通过”，而是显示“可使用-未备案”。

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.7w7df2pmsx.webp)

点击“配置”选项，可以看到有几行记录，我们只需要在我们购买的域名DNS配置中，将这几个记录添加就可以。

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.wj3x6ktk9.webp)

如下图所示，每一条都添加进去，对于上图“配置”中“主机记录”为空的记录，在添加域名DNS解析的时候，主机记录可以填“@”![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.9gx4ek4o3o.webp)

在添加DNS解析之后，点击“验证”，然后等待几分钟让DNS解析收敛，之后查看就会发现状态全部是“验证通过”。

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.b9gaw7ti7.webp)

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.7lkjlxxnqi.webp)

然后在"发信地址"中新建钓鱼邮件的发件人，发信域名就是刚才添加的买的域名，账号可以随便取，我这里取了“administrator”，然后选择“批量邮件”

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.9gx4ekg7he.webp)

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.6ikub29xvx.webp)

最后需要设置一下SMTP密码，在之后Gophish的设置中需要用到这个密码

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.5j4qxwsg1x.webp)



### 1.3 服务器部署

部署服务器：云服务器

服务器IP：111.x.x.x

操作系统：Ubuntu-22.04-x64

1. 系统初始化：

   ```shell
   apt update && apt upgrade -y
   apt install curl wget perl screen -y
   ```

2. 下载gophis

   ```shell
   wget https://github.com/gophish/gophish/releases/download/v0.10.1/gophish-v0.10.1-linux-64bit.zip
   mkdir gophish
   apt install unzip
   unzip gophish-v0.10.1-linux-64bit.zip -d ./gophish
   ```

3. 修改配置文件

   ```shell
   cd gophish
   vim config.json
   ```

​	修改config.json文件中admin_server的listen_url为0.0.0.0:2285，用于远程登录服务器gophish进行管理，2285为自定义端口号。

​	自定义端口号后记得在云服务器的安全策略中放通对应端口。

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.1sflclothl.webp)

4. 启动gophish

   ```shell
   # 脚本赋权
   chmod +x gophish
   # 服务启动
   ./gophish
   ```

   ![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.9kgqc8kai9.webp)

### 1.4 gophish设置

​	使用https://111.x.x.x:2285访问gophish，输入默认账户密码admin / gophish进行登录，登录之后可以在右上角的用户界面修改默认密码gophish@228

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.39lq6m0lt8.webp)

​	先点击“Sending Profile”进行设置，name就先填写“test”，“from”和“username”都填写前面的“发信地址”，如果和我一样使用阿里云的邮件推送的话，Host就填“smtpdm.aliyun.com:465”。然后在最下面点击“send test email”，测试一下是否能正常收到邮件，我就用了自己的qq邮箱。

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.9rjy7qmdoz.webp)

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.86u789u224.webp)



![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.73uhxe3swi.webp)

​	测试邮件收取正常后，就可以点击“save profile”进行保存

​	然后就需要配置我们要对哪些人进行钓鱼，点击"users&groups"新建一个组，因为我这里是要测试，所以就只填写了我自己的qq邮箱进行测试，实际钓鱼过程中肯定是非常多的人，因此我们可以使用下载模板然后批量导入的方式进行。

![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.5fl507on80.webp)







