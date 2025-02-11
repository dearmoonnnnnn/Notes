[Clash使用说明 · Issue #8 · zhongfly/blog · GitHub](https://github.com/zhongfly/blog/issues/8)

代理模式

- Global，全局模式。所有流量全部通过所选择的服务器进行代理

- Rule，规则模式。根据配置文件中的规则进行代理。

- Direct。所有进入Clash的流量都走直连，不使用代理。

- Script。根据高级脚本进行分流





# 零、问题 & 概念

## 概念

##### 1. TUN Mode

隧道模式。用于实现**整机代理**，让所有网络流量都经过代理。

## 问题

##### 1. TUN Mode 和 Global 的区别是什么

- **TUN 模式**能够处理更多的底层流量和协议，尤其是 UDP 流量，适用于那些不容易通过传统代理协议（如 HTTP/HTTPS）处理的流量，比如游戏流量、实时语音和视频流等。

  **Global 模式**更常见于基于 **HTTP/HTTPS** 或 **SOCKS** 的代理系统中，依赖操作系统的网络层代理配置。它不需要修改操作系统的底层网络层，只要配置代理工具即可。






# Windows 平台下让 PowerShell 使用 Clash 代理。



## 方法一： 设置系统环境变量

1. 找到环境变量

2. 在用户变量或系统变量中，点击新建

   - 添加以下变量：

   - ```
     HTTP_PROXY：http://127.0.0.1:端口号
     HTTPS_PROXY：http://127.0.0.1:端口号
     ALL_PROXY：http://127.0.0.1:端口号
     ```

     - `127.0.0.1` 是本地地址， 端口号与 Clash 中的相同，默认为 7890

3. 保存，重启 PowerShell 或重启电脑，确保环境变量生效

## 方法二：PowerShell 中临时设置代理

```
$proxy = "http://127.0.0.1:端口号"

# 设置 HTTP 和 HTTPS 代理
[System.Net.WebRequest]::DefaultWebProxy = New-Object System.Net.WebProxy($proxy)
[System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

# 设置环境变量
$env:HTTP_PROXY = $proxy
$env:HTTPS_PROXY = $proxy
$env:ALL_PROXY = $proxy
```


