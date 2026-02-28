# Windows基线排查

 ## 一、什么是安全基线

​	安全基线是为了实现基本防护需求而制定的一系列基准，通过对系统生命周期不同阶段的安全检查，建立良好的安全配置项和安全措施，通过分析安全状态的变化趋势控制安全风险。

​	在建立事件响应机制之前，必须存在基础的能力。这些基础能力用于保障数字资产/业务的可用性、保密性、完整性

1. 安全基线的发展

- 1994年，我国首次颁布《中华人民共和国计算机信息系统安全保护条例》

- 1999年推出《计算机信息系统安全保护等级划分准则》

- 2007年，《信息安全等级保护管理办法》

- 2010年，公安部发布《关于推动信息安全等级保护测评体系建设和开展等级测评工作的通知》，决定在全国部署开展信息安全等保测评工作

2. 安全基线实施的必要性

- 将已有的安全投资发挥功效

- 实现企业间的通信安全

- 实现本企业网络、主机 、数据、应用安全

- 以最小成本提高风险对抗能力

## 二、windows服务器安全基线

### (1) 账户与认证

#### 1.基本要求：对登录操作系统的用户进行身份标识和鉴别

测试内容：检查系统登录是否需要密码

操作步骤：开机检测是否需要密码

预期结果：不能存在空密码帐户

#### 2.基本要求：管理缺省账户

测试内容：对于具有管理员权限的帐号，应使用非缺省Administrator帐户名称，即重命名管理员帐户。对于缺省的管理员权限账号Administrator应该禁用。同时需要禁用guest账号。

测试步骤：

`net loacalgroup`查看系统存在哪些组

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.232etyzlyr.webp)

使用`net localgroup administratros` , 查看administrators组中存在哪些具有管理员权限的账号

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.6t7nsdt76s.webp)

如果当前administrators组中只有administrator账号，是不能直接禁用administrator账号的，否则禁用后没有管理员权限的账号，无法管理系统了。

可以使用`net user username password /add && net localgroup administrators username /add`创建新的管理员权限账号并将其添加至administrators 组中

`net user administrator /active:no`禁用Administrator管理员账号

`net user guest /active:no`禁用guest账号

注意，使用如上命令仅仅将账户禁用，而不是删除该账户，也不是将administrator移出administrators组。

可以在powershell中使用`Get-LocalUser | Select-Object Name, Enabled | Format-Table -AutoSize`查看所有账户以及其状态

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.2vfabpr3t7.webp)

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.7i0xckgjvh.webp)

预期结果：禁用Administrator与Guest账户

#### 3. 基本要求：系统用户应具有不易被冒用的特点，口令应有复杂度要求并定期更换

测试内容：口令复杂度与口令定期更换

测试步骤：

gpedit.msc开启本地组策略编辑器，按下图配置

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.8z72e628di.webp)



预期结果：最短密码长度 8个字符，启用本机组策略中密码必须符合复杂性要求的策略；“密码最长

存留期”设置不大于“90天”

#### 4. 基本要求：应启用登录失败处理功能，限制非法登录次数等

测试内容：检查当用户连续认证失败次数的限制

测试步骤：

gpedit.msc开启本地组策略编辑器，按下图配置

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.6t7nsefym9.webp)

预期结果：“账户锁定阀值”设置为小于或等于 6次，“账户锁定时间”设置为大于5分钟

#### 5.基本要求：应限制非管理员账户关闭计算机

测试内容：检查是否仅有管理员账户授权本地关机、远程关机

测试步骤：

gpedit.msc开启本地组策略编辑器，按下图配置

在“从远程系统强制关机”设置，只保留Administrators组，将其它的组全部删除。

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.6t7nsek546.webp)

预期结果：除Administrators组外用户均无关机权限

### (2) 日志审核

#### 1.基本要求：配置日志文件大小

测试内容：设置日志文件大小至少为8192KB

操作步骤：

`eventvwr.msc`打开事件查看器，按下图配置

依次将重要的 "应用程序日志"、"系统日志"、 "安全日志" 的大小修改为“8192KB”

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.b9fz3px07.webp)



预期结果："应用日志"、"系统日志"、"安全日志"属性中的日志大小设置不小于"8192KB"



#### 2. 日志审核要求汇总

| 基本要求               | 测试内容           | 预期结果         | 备注（inf文件）      |
| ---------------------- | ------------------ | ---------------- | -------------------- |
| 用户登录日志审核       | 审核登录事件       | 成功和失败都审核 | AuditLogonEvents     |
| 账号管理日志审核       | 审核账户管理       | 成功和失败都审核 | AuditAccountManage   |
| 策略更改日志审核       | 审核策略更改       | 成功和失败都审核 | AuditPolicyChange    |
| 对象访问日志审核       | 审核对象访问       | 成功和失败都审核 | AuditObjectAccess    |
| 目录服务器访问日志审核 | 审核目录服务器访问 | 成功和失败都审核 | AuditDSAccess        |
| 进程追踪日志审核       | 审核进程追踪       | 成功和失败都审核 | AuditProcessTracking |
| 系统事件日志审核       | 审核系统事件       | 成功和失败都审核 | AuditSystemEvents    |
| 特权使用日志审核       | 审核特权使用       | 成功和失败都审核 | AuditPrivilegeUse    |





### (3） 入侵防范与访问控制

#### 1. 基本要求：对共享文件夹进行权限控制

测试内容：共享文件夹限制

操作步骤：

`compmgmt.msc`启动计算机管理，按下图配置：

对于不需要共享的文件，直接删除即可。

注意：在此处删除可以停止共享，但是在系统重启之后仍然会启用共享，使用`net share d$ /delete`这样的命令进行操作一样重启后会启用共享，所以该设置用于不做重启的服务器多。

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.64ee8fhiia.webp)

预期结果：共享分区、文件夹或文件应设置访问权限，用户里不应包含Everyone(任何人)。

#### 2. 修改远程桌面服务默认端口

测试内容：远程桌面服务端口管理

测试步骤：

`regedit`启动注册表编辑器，按如下路径找到对应位置：

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TerminalServer\WinStations\RDP-Tcp`

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TerminalServer\Wds\rdpwd\Tds\tcp` 

两个注册表中都找到“PortNumber”子项，默认值 00000D3D，是 3389 的十六 进制，修改成除 3389 外的其他任何值，并保存新值，重新启动系统。

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.4clfdjfs0x.webp)

预期结果：RDP服务不再是3389端口

#### 3.基本要求：应禁止远程访问操作系统注册表路径和子路径，防止系统被入侵破坏

测试内容：注册表远程管理

操作步骤：

`gpedit.msc`打开组策略编辑器

 将网络访问：可远程访问的注册表路径和子路径，配置为空

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.5q7yhkvy5t.webp)

预期结果：禁止远程连接注册表

#### 4. 基本要求：安装终端防护软件

测试内容：恶意代码防范

测试步骤：使用正版授权的计算机病毒防护软件，可安全有效地查杀各类计算机病毒，防范恶意代码执行，自动更新病毒特征库

预期结果：采购并安装正版授权计算机病毒防护软件



### (4）系统服务

#### 1. 基本要求：关闭不必要的系统服务

测试内容：系统服务管理

测试步骤：

`compmgmt.msc`开启计算机管理，按下图找到各项服务邮件停止

![](https://ChihungSan.github.io/picx-images-hosting/windows_jixian/image.39lq2nwdvv.webp)

1. Error Reporting Service 错误报告服务
2. Computer browser 浏览局域网计算机列表
3. Print Spooler 打印队列服务
4. Remote Registry 远程注册表操作
5. Routing and Remote Access 路由与远程访问
6. Shell Hardware Detection 为自动播放硬件事件提供通知Telnet 远程管理
7. TCP/IP NetBIOS Helper 允许客户端共享文件，打印机和登录到网络

预期结果：关闭不必要的服务，提高系统安全性

### (5) 系统更新

1. 基本要求：系统自动更新安全补丁

测试内容：系统服务管理

测试步骤：检查系统更新

预期结果：应安装关键和重要系统补丁，开启系统自动更新功能

## 三、基线排查脚本



security.inf脚本如下：

```ini
[Unicode]
Unicode=yes ;声明文件采用Unicode编码

[Event Audit] ;对应日志审核部分的配置，3代表成功和失败均审核
AuditLogonEvents = 3
AuditPolicyChange = 3
AuditObjectAccess = 3
AuditDSAccess = 3
AuditPrivilegeUse = 3
AuditSystemEvents = 3
AuditAccountManage = 3
AuditProcessTracking = 2

[Registry Values]
MACHINE\System\CurrentControlSet\Control\Lsa\RestrictAnonymous=4,1
MACHINE\System\CurrentControlSet\Control\Lsa\RestrictAnonymousSAM=4,1
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\DontDisplayLastUserNam
e=4,1
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\EnableForcedLogOff=4,1
MACHINE\System\CurrentControlSet\Control\Session Manager\Memory Management\ClearPageFile
AtShutdown=4,1

[System Access]
EnableGuestAccount = 0 ;禁用guest账户
MinimumPasswordLength = 8 ;密码最小长度8位
PasswordComplexity = 1 ;启用密码复杂性要求
PasswordHistorySize = 60 ;密码历史记录大小设置为 60 条,记录60个历史密码
LockoutBadCount = 5 ;5次错误密码锁定账户
LockoutDuration = 30 ;账户锁定时长30分钟

[Privilege Rights]
SeRemoteShutdownPrivilege = *S-1-5-32-544 ;管理员组 允许远程关机 / 重启计算机
SeTakeOwnershipPrivilege = *S-1-5-32-544 ;管理员组  获得文件或其他对象的所有权
SeNetworkLogonRight = *S-1-5-32-544,*S-1-5-32-545 ;管理员组和普通用户组 通过网络访问此计算机

[Version]
signature="$CHICAGO$" ;固定写法，告诉系统这是一个标准 Windows 安全配置文件
Revision=1 ;无意义
```



build_security_Strategy.bat脚本代码如下：

bat脚本如果在执行的过程中中文乱码，则另存为的时候选择ANSI编码即可。

将两个文件放到同一个路径下，管理员权限运行build_security_Strategy.bat脚本即可

```bat
@echo off
echo.
echo                 _       .-.                            
echo                :_;      : :                            
echo ,-.,-.,-. .--. .-. .--. : `-. .-..-. .--.  ,-.,-. .--. 
echo : ,. ,. :' '_.': :'  ..': .. :: :; :' .; ; : ,. :' .; :
echo :_;:_;:_;`.__.':_;`.__.':_;:_;`.__.'`.__,_;:_;:_;`._. ;
echo                                                   .-. :    

echo 一键执行，配置windows安全策略
echo 正在配置中......

secedit /configure /db gp.sdb /cfg security.inf

::管理缺失账户
for /f "skip=4 tokens=1-3" %%i in ('net user') do (
	if "%%i"=="Administrator"  echo 请修改默认管理员账号:%%i
	if "%%i"=="Guest"  echo 请禁用用户:%%i
	if "%%j"=="Administrator" echo 请修改默认管理员账号:%%j
	if "%%j"=="Guest"  echo Ç请禁用用户:%%j
	if "%%k"=="Administrator" echo 请修改默认管理员账号:%%k
	if "%%k"=="Guest"  echo 请禁用用户:%%k
)

::启用SNMP攻击保护
set   EnableDeadGWDetect=False
for /f "skip=2 tokens=1-3" %%i in ('REG QUERY HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters') do if "%%i"=="EnableDeadGWDetect" if "%%k"=="0x0" set EnableDeadGWDetect=True

if %EnableDeadGWDetect%==False (
	REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /f /v EnableDeadGWDetect /t REG_DWORD /d 0 
	echo  启用SNMP攻击保护成功
	rem echo 请添加EnableDeadGWDetect=0x0
)


::启用ICMP攻击保护
set   EnableICMPRedirect=False
for /f "skip=2 tokens=1-3" %%i in ('REG QUERY HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters') do (
	if "%%i"=="EnableICMPRedirect" if "%%k"=="0x0" set EnableICMPRedirect=True
)
if %EnableICMPRedirect%==False (
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /f /v EnableICMPRedirect /t REG_DWORD /d 0
echo 启用ICMP攻击保护成功
rem echo 请添加EnableICMPRedirect=0x0
) 

::启用SYN攻击保护
set   SynAttackProtect=False
for /f "skip=2 tokens=1-3" %%i in ('REG QUERY HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters') do (
	if "%%i"=="SynAttackProtect" if "%%k"=="0x2" set SynAttackProtect=True
)
if %SynAttackProtect%==False (
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /f /v SynAttackProtect /t REG_DWORD /d 2
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /f /v TcpMaxPortsExhausted /t REG_DWORD /d 5
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /f /v TcpMaxHalfOpen /t REG_DWORD /d 500
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /f /v TcpMaxHalfOpenRetried /t REG_DWORD /d 400
)

::禁用IP源路由
set   DisableIPSourceRouting=False
for /f "skip=2 tokens=1-3" %%i in ('REG QUERY HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters') do (
	if "%%i"=="DisableIPSourceRouting" if "%%k"=="0x1" set DisableIPSourceRouting=True
)
if %DisableIPSourceRouting%==False (
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /f /v DisableIPSourceRouting /t REG_DWORD /d 1
echo 禁用IP源路由成功
rem echo 请添加DisableIPSourceRouting=0x1
)

::启用碎片攻击保护
set  EnablePMTUDiscovery=False
for /f "skip=2 tokens=1-3" %%i in ('REG QUERY HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters') do (
	if "%%i"=="EnablePMTUDiscovery" if "%%k"=="0x0" set EnablePMTUDiscovery=True
)
if %EnablePMTUDiscovery%==False (
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /f /v EnablePMTUDiscovery /t REG_DWORD /d 0
echo 启用碎片攻击保护成功
rem echo 请添加EnablePMTUDiscovery=0x0
)

::远程桌面服务端口管理
set  tcp_PortNumber=False
set  rdp-tcp_PortNumber=False
for /f "skip=2 tokens=1-3" %%i in ('REG QUERY HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal" "Server\Wds\rdpwd\Tds\tcp') do (
	if "%%i"=="PortNumber" if "%%k"=="0xd3d" set tcp_PortNumber=True
)

for /f "skip=2 tokens=1-3" %%i in ('REG QUERY HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal" "Server\WinStations\RDP-Tcp') do (
	if "%%i"=="PortNumber" if "%%k"=="0xd3d" set rdp-tcp_PortNumber=True
)
if %tcp_PortNumber%==True if %rdp-tcp_PortNumber%==True  (
echo 请修改远程桌面端口不为默认端口3389
)

::终端服务登录管理
set  DontDisplayLastUserName=False
for /f "skip=2 tokens=1-3" %%i in ('REG QUERY HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows" "NT\CurrentVersion\Winlogon') do (
	if "%%i"=="DontDisplayLastUserName" if "%%k"=="0x1" set DontDisplayLastUserName=True
)
if %DontDisplayLastUserName% == False (
REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows" "NT\CurrentVersion\Winlogon /f /v DontDisplayLastUserName /t REG_DWORD /d 1
rem echo 请禁止显示上次登录名 DontDisplayLastUserName=0x1
)

::禁止windows自动登录
set AutoAdminLogon=False
for /f  "skip=2 tokens=1,3" %%i in ('REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows" "NT\CurrentVersion\Winlogon\ /v AutoAdminLogon') do (
	if "%%j"=="0" set AutoAdminLogon=True
)
if %AutoAdminLogon%==False (
REG ADD HKEY_LOCAL_MACHINE\Software\Microsoft\Windows" "NT\CurrentVersion\Winlogon\ /f /v AutoAdminLogon /t REG_SZ /d 0
echo 禁止windows自动登录成功
rem echo 请添加EnableDeadGWDetect=0
)

::操作系统补丁更新
::net start wuauserv

echo 配置完成
pause
```

