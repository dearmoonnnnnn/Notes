[Clash使用说明 · Issue #8 · zhongfly/blog · GitHub](https://github.com/zhongfly/blog/issues/8)

代理模式

- Global，全局模式。所有流量全部通过所选择的服务器进行代理

- Rule，规则模式。根据配置文件中的规则进行代理。

- Direct。所有进入Clash的流量都走直连，不使用代理。

- Script。根据高级脚本进行分流






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

