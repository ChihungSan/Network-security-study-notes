# 关于cprapid.com解析的问题分析

## 一、问题描述

​	某客户官网网站域名：aaa.com.cn，IP：111.x.x.84，客户反应可以通过111-x-x-x.cprapid.com直接进行访问，该问题同样存在客户其它IP地址下，见下表：

|            | **ping 111-x-x-x.cprapid.com** | **访问 111-x-x-x.cprapid.com** |
| ---------- | ------------------------------ | ------------------------------ |
| 111.x.x.52 | 请求超时                       | 502                            |
| 111.x.x.53 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.54 | 111.x.x.54                     | 502                            |
| 111.x.x.55 | 111.x.x.55                     | 502                            |
| 111.x.x.64 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.65 | 111.x.x.65                     | 502                            |
| 111.x.x.66 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.67 | 111.x.x.67                     | 502                            |
| 111.x.x.68 | 请求超时                       | 502                            |
| 111.x.x.69 | 111.x.x.69                     | 502                            |
| 111.x.x.70 | 111.x.x.70                     | 502                            |
| 111.x.x.71 | 111.x.x.71                     | 可以访问                       |
| 111.x.x.72 | 111.x.x.72                     | 502                            |
| 111.x.x.73 | 111.x.x.73                     | 502                            |
| 111.x.x.74 | 111.x.x.74                     | 502                            |
| 111.x.x.75 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.76 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.77 | 111.x.x.77                     | 可以访问                       |
| 111.x.x.78 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.79 | 111.x.x.79                     | 502                            |
| 111.x.x.80 | 111.x.x.80                     | 502                            |
| 111.x.x.81 | 111.x.x.81                     | 502                            |
| 111.x.x.82 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.83 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.84 | 111.x.x.84                     | 可以访问                       |
| 111.x.x.85 | 111.x.x.85                     | 502                            |
| 111.x.x.86 | 111.x.x.86                     | 502                            |
| 111.x.x.87 | 111.x.x.87                     | 可以访问                       |
| 111.x.x.88 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.89 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.90 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.91 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.92 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.93 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.94 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |
| 111.x.x.95 | 111.x.x.53                     | ERR_CONNECTION_CLOSED          |

​	该问题经过排查，并不是域名恶意解析，而是由于服务器使用cpanel面板引入的配置引入的副作用问题。

​	cprapid.com 是 cpanel系统生成的默认子域名，通常用于服务器配置、预览或作为别名（ServerAlias）出现在 Apache 配置文件中。

## 二、可能引发的安全问题

​	这个现象本身不算高危漏洞，但确实引入了中低级别的安全风险，主要体现在以下几个方面（基于 cPanel 社区、支持文档和历史案例的总结）：

1. 域名混淆 / 钓鱼辅助风险（Phishing facilitation）

​	攻击者可以构造类似 “111-x-x-x.cprapid.com” 的链接，看起来“有点官方”（因为 cprapid.com 是 cPanel 官方域名），诱导用户点击访问你的网站。

​	如果你的网站有登录表单、支付页面等，攻击者可能用这个“备用入口”进行凭证窃取或中间人攻击（尤其结合自签/过期证书时）。历史上确实有案例：某些 cprapid.com 子域被用于 phishing 页面（比如假冒银行、假 cPanel 登录），虽然不是针对你的服务器，但域名本身被滥用过。

2. 绕过域名限制 / 访问控制（Bypass of intended access controls）

​	如果你未来在 .htaccess、Nginx 配置、Cloudflare WAF、防火墙规则里只针对 aaa.com.cn 做了 IP 白名单、Referer 检查、速率限制等，攻击者可以用 cprapid.com 变体绕过这些防护，直接访问网站内容。

​	这在某些共享主机或有严格访问策略的场景下，算是一个绕过机制的风险点。

3. 增加攻击面 / 信息泄露（Increased attack surface）

​	任何知道你服务器 IP 的人（比如通过你的网站 header、历史 DNS 记录、扫描等），都能猜到或直接试用 “111-x-x-x.cprapid.com” 来访问你的站点。

​	如果你的网站有未授权访问的后台、调试页面、旧版本漏洞，这个入口就等于多开了一扇门。

​	另外，cPanel 偶尔会因为配置变更（装 ea-Nginx、改端口等）把 cprapid alias 重新加回来，导致你以为解决了其实没解决。

4.其他间接风险

​	占用 SSL 证书名额（Let's Encrypt 有 100 个域名/证书限制），如果 AutoSSL 把 cprapid 也包含进去，可能导致正式域名续期失败。某些情况下会产生大量日志噪音、扫描尝试（有人看到 cprapid 域名就自动化扫你的 IP）。

## 三、问题复现

服务器IP地址：222.x.x.x

域名：bbb.com

操作系统：Ubuntu-22.04-x64

cPanel：v134.0.9

### 3.1 系统初始化

```shell
apt update && apt upgrade -y         
apt install curl wget perl screen -y 
reboot                               
```

### 3.2 安装Cpanel

```shell
cd /home && curl -o latest -L https://securedownloads.cpanel.net/latest && sh latest
```

### 3.3 初始化WHM

https://222.x.x.x:2087

登录root账号

### 3.4 在 WHM 里创建一个网站账号

Account Functions → Create a New Account

![img](https://ChihungSan.github.io/picx-images-hosting/cprapid/image.2ksgv8rctn.webp) 

### 3.5 登录 cPanel 部署测试网站

https://222.x.x.x:2083

File Manager-public_html-上传测试页面 index.html

### 3.6 解析域名

A记录 @ → 222.x.x.x

A记录 www → 222.x.x.x

### 3.7 验证

访问www.bbb.com，可以正常访问测试网页

![img](https://ChihungSan.github.io/picx-images-hosting/cprapid/image.468gba7nr.webp) 

​	cPanel 面板的“自动产生临时域名”（temporary domains / automatically-issued hostnames，如 xx-xx-xx-xx.cprapid.com 或 domain.cpanel.site）不是一个可以手动“开启/关闭”的开关。这是 cPanel 系统内置的自动机制，从 v90 开始引入，主要目的是在新服务器安装时或 hostname 未正确解析时，自动生成一个可用的 FQDN + SSL 证书，避免 WHM/cPanel 界面出现“不安全”警告。

​	部署完成后，发现显示的主机名就是上述怀疑的恶意域名格式：ip地址短横线连接.cprapid.com，其为自动生成。

![img](https://ChihungSan.github.io/picx-images-hosting/cprapid/image.4n89jah343.webp) 

​	Ping 222-x-x-x.cprapid.com 发现，同样解析到了正常的IP 222.x.x.x  

![img](https://ChihungSan.github.io/picx-images-hosting/cprapid/image.2rvoqo7xdq.webp) 

​	直接访问222-x-x-x.cprapid.com，被重定向至系统默认的页面

![img](https://ChihungSan.github.io/picx-images-hosting/cprapid/image.4n89jakrag.webp) 

​	经上述分析，客户遇到的问题基本排除是域名恶意解析，而是由于使用了Cpanel不正确的配置引起的问题。

四、处置意见

​	在 cPanel v134.0.9 中，这个行为本质上是 hostname 为 222-x-x-x.cprapid.com 导致的 fallback 机制 + Autodomain / ServerAlias 被添加到你的账号 VirtualHost 中。cPanel 官方没有提供“一键永久禁用 cprapid.com”的开关（从 v130+ 开始，cpanel.site 和 cprapid 都是内置临时机制，无法关闭）。

​	可以尝试登录 cPanel, 查找“域名”或“域名管理”部分, 选取临时域名按 '管理' 键, 进入管理面板后找 'Remove Domain' (应该是红色按键) 便可将临时域名删除。

​	如仍然无法解决请咨询cPanel提供商。

 

 

 

 

 

 

 

 