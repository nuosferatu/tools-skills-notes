---
title: 配置 VS Code 远程访问服务器目录
date: 2020/07/02
tags:
- VSCode
categories:
- 工具
related_posts: true
toc: true
---

VS Code 配置 Remote-SSH，远程访问服务器的目录，像操作本地目录和文件一样，操作很简单。

<!-- more -->

# 安装 OpenSSH

## 安装 OpenSSH for Windows

以管理员权限运行 PowerShell，输入下面的命令检查是否已经安装了 OpenSSH：

```powershell
Get-WindowsCapability -Online | ? Name -like 'OpenSSH*'
# 返回内容：
Name  : OpenSSH.Client~~~~0.0.1.0
State : NotPresent
Name  : OpenSSH.Server~~~~0.0.1.0
State : NotPresent
```

如果没安装，通过以下命令安装：

```powershell
# 安装 OpenSSH 客户端
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
# 安装 OpenSSH 服务端
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
# 上面两条安装命令都会返回以下的内容：
Path          :
Online        : True
RestartNeeded : False
```

至此安装完毕，如果不需要了，通过以下命令卸载 OpenSSH：

```powershell
Remove-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Remove-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

## 初始化 OpenSSH

启动 sshd 服务：

```powershell
Set-Service -Name sshd -StartupType 'Automatic'
```

检查防火墙规则：

```powershell
Get-NetFirewallRule -Name *ssh*
# 返回内容：
Name                  : OpenSSH-Server-In-TCP
DisplayName           : OpenSSH SSH Server (sshd)
Description           : Inbound rule for OpenSSH SSH Server (sshd)
DisplayGroup          : OpenSSH Server
Group                 : OpenSSH Server
Enabled               : True
Profile               : Any
Platform              : {}
Direction             : Inbound
Action                : Allow
EdgeTraversalPolicy   : Block
LooseSourceMapping    : False
LocalOnlyMapping      : False
Owner                 :
PrimaryStatus         : OK
Status                : 已从存储区成功分析规则。 (65536)
EnforcementStatus     : NotApplicable
PolicyStoreSource     : PersistentStore
PolicyStoreSourceType : Local
```

要有名字为 OpenSSH-Server-In-TCP 的规则，且 `Enabled` 是 `True`，如果没有，要创建规则：

```powershell
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
```

初始化完毕，测试一下能否成功与远程服务器取得连接：

```powershell
ssh root@192.168.0.137 # 替换用户名和 IP
# 询问信息请确认
Are you sure you want to continue connecting (yes/no)? yes
# 如果有密码，请输入用户名的密码，回车即可完成连接
```

成功连接，如图所示：

![image-20200703100638426](https://aliyun-oss-pic-bucket.oss-cn-beijing.aliyuncs.com/img/image-20200703100638426.png)

说明 OpenSSH 已经安装和配置完毕，没什么问题。

# 配置 VS Code

安装 Remote Development Extension Pack，点击这个位置：

![image-20200703100938130](https://aliyun-oss-pic-bucket.oss-cn-beijing.aliyuncs.com/img/image-20200703100938130.png)

选择 **Remote-SSH: Connect to Host…**，输入 `用户名@IP` 如 `root@192.168.0.137`，提示选服务端类型，比如我的是 `Linux`，然后输密码，回车，就完成连接了。

按照下图可以打开远程服务器的目录，然后就可以像使用本地目录一样使用远程服务器的目录了：

![image-20200703101740723](https://aliyun-oss-pic-bucket.oss-cn-beijing.aliyuncs.com/img/image-20200703101740723.png)

完结撒花~