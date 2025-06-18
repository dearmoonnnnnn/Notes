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

##### 2. 两种代理模式 HTTP(s) 和 SOCKS5 的区别？

- 前者是在**应用层**进行代理，适合审查、缓存等。

- 后者在**会话层**进行代理，适合密码验证等。
  - 更偏底层

##### 3. ”请勿将代理服务器用于本地（intranet）地址“ 是什么意思?

- 设置 -> 网络和 Internet -> 代理 -> 手动设置代理服务器 -> 编辑 -> 请勿将服务器用于本地地址

- **作用**：避免访问本地网络资源时走代理，如

  - 本地开发服务器（如 `localhost:3000`）
  - 内网服务（公司 OA 系统、内网打印机）

- **举例**：

  | 访问地址                | 如果开启此选项 | 如果关闭此选项           |
  | ----------------------- | -------------- | ------------------------ |
  | `127.0.0.1:8000`        | 不走代理       | 会走 Clash 的代理        |
  | `192.168.1.1`（路由器） | 直连           | 可能走代理，甚至访问失败 |
  | `localhost`             | 直连本地服务   | 可能被转发到代理，出错   |

- 使用场景：

  | 使用场景                                               | 是否建议开启         |
  | ------------------------------------------------------ | -------------------- |
  | 你需要访问本机或局域网服务（如 NAS、内网 API）         | ✅ 建议开启           |
  | 你想让本机服务（如 Gemini 本地 HTTP 请求）**也走代理** | ❌ 建议关闭           |
  | 你不确定有没有内网资源要访问                           | ✅ 默认开启更安全稳定 |

##### 4. 如果 clash 开启了 global 模式，且代理设置打开了“请勿将代理服务器用于本地地址”，本地地址会走代理吗?

本地地址仍然会绕过代理，直连。

如何让本地地址走代理？

- 方法1：在系统代理设置中关闭“不要对本地地址使用代理服务器”

- 方法2：启用 TUN 模式

  - 底层劫持所有 TCP 流量（不依赖系统代理设置）

  - > [!NOTE]
    >
    > TUN 模式必须开启 Service Mode，并正确配置 `tun:` 字段，否则可能断网。


# 一、Windows 平台下让 PowerShell 使用 Clash 代理。



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

# 二、代理的账号和密码

## 问题描述：

在Windows上打开了Clash的代理后，Android Studio 中的 Gemini 要求输入代理账号和密码/

### 可能原因1: 在Clash配置了 authentication | 经排查，排除该原因

打开 Clash 的配置文件（`.yaml`），查找是否有

```yaml
authentication:
  - user:pass
```



##### 解决方案：

- 填写在配置中的设置的 user 和 pass
- 或者去掉 authentication 配置，保存并重启 Clash



### 可能原因2：使用了 Clash 的 Http(s) 代理，但 Gemini 默认只支持 SOCKS5

##### 解决方案：在 Android Studio 中设置

```rust
File -> Settings -> Appearance & Behavior -> System Settings -> HTTP Proxy
```

设置代理方式如下：

- **Manual proxy configuration**
  - Host name: `127.0.0.1`
  - Port: Clash 的 HTTP 代理端口（默认是 `7890`）
  - 勾选 `No Proxy for: localhost, 127.0.0.1`
  - 如果 Clash 没有设置认证，就不要填写账号密码。

### 可选：使用 TUN 模式避免代理设置

- 具体见“四、TUN 模式”

# 三、代理协议

## 1. 两种代理协议

| 协议类型            | 简介                                                         |
| ------------------- | ------------------------------------------------------------ |
| **HTTP/HTTPS 代理** | 专为转发 HTTP 或 HTTPS 请求而设计的代理，工作在**应用层**。  |
| **SOCKS5 代理**     | 通用型的网络代理协议，工作在**会话层**（第五层），支持几乎所有类型的流量，包括 TCP 和 UDP。 |



##### 详细对比

| 特性             | HTTP/HTTPS 代理                | SOCKS5 代理                      |
| ---------------- | ------------------------------ | -------------------------------- |
| 工作层级         | 应用层（HTTP 协议）            | 会话层（TCP/UDP 协议）           |
| 支持协议         | 仅支持 HTTP/HTTPS              | 支持 **几乎所有协议**（TCP/UDP） |
| 是否解析请求内容 | 解析请求内容（可缓存、审查等） | 不解析，仅转发二进制数据         |
| 支持 UDP         | ❌ 不支持                       | ✅ 支持 UDP                       |
| 支持身份验证     | 支持（但不常用）               | 支持用户名+密码验证              |
| 适用场景         | 浏览器代理、爬虫、Web 流量     | 全局代理、游戏、P2P、隐私通信    |
| 实现复杂度       | 简单，常用于网页代理           | 稍复杂，功能更强大               |



## 2. 工作原理

HTTP(S) 代理

- 客户端（浏览器等）将请求转发给 **HTTP 代理服务器**，代理服务器再将请求转发给目标服务器
- ==支持**明文 HTTP**,也支持 CONNECT 方法实现 HTTP 代理（即“隧道”代理）== 
- 代理服务器能“看见”内容，因此可用于审查、缓存等



SOCKS5 代理

- 通用型的代理协议，能代理任意 TCP/UDP 流量
- 更底层，只负责转发数据，不负责解析内容
- 支持身份认证（用户名密码）
- 应用：游戏、P2P、VoIP、FTP



## 3. 查看当前使用的协议

打开 Clash 配置文件查看（`config.yaml`）

根据端口类型

```yaml
mixed-port: 7890      # 开启 HTTP + SOCKS 通用端口
socks-port: 7891      # 单独的 SOCKS5 代理端口
port: 7890            # （老版本）HTTP 代理端口
allow-lan: true
tun:
  enable: true
```

如果是 `mixed-port` ，不是 Clash 决定用哪种协议，由发起连接的客户端程序自己决定协议类型。

# 四、TUN 模式

##### 动机

一些软件不支持代理端口设置，使用 TUN 模式可以将所有系统的所有流量强制转发



##### 步骤

1. 关闭防护墙和杀毒软件
2. 已管理员模式启动 Clash
3. 启用 Service Mode
4. 打开 TUN 开关



## 问题1：启动TUN模式后电脑断网

 ✅ 已以管理员权限启动 Clash
 ✅ 已启用 Service Mode
 ✅ 成功打开 TUN 模式开关
 ❌ 启用 TUN 后电脑断网
 ❌ `config.yaml` 中 **未自动添加 `tun:` 字段**

## 🎯 问题可能原因与解决方法

------

### 🚩 原因 1：虚拟网卡驱动未正确安装

Clash 的 TUN 模式依赖一个 **虚拟网卡（clash-tun）**，如果驱动没有正确加载，就会导致网络异常或断网。

#### ✅ 检查方法：

1. 打开 Windows 设备管理器（快捷键：Win + X → 设备管理器）
2. 展开 “网络适配器”
3. 查找类似名称的网卡：

```
Clash for Windows TUN
或者
clash-tun
或者
utun / wintun
```

#### ❌ 如果没有看到：

说明 TUN 驱动安装失败或未被加载。

#### ✅ 解决方法：

1. 在 Clash 安装目录中查找 `resources/static/wintun.dll`
2. 安装 TUN 驱动：

```bat
# 打开管理员 CMD，执行
wintun-install.bat
```

或者：

- 手动下载安装 [Wintun 驱动](https://www.wintun.net/)
- 使用 Clash For Windows 版本 >= 0.20.x（内置驱动）

------

### 🚩 原因 2：DNS 劫持配置错误导致网络解析失败

当 TUN 模式开启后，Clash 会接管 DNS，如果 DNS 没设置好，系统就“看不到互联网”。

#### ✅ 添加 DNS 配置：

编辑你的 `config.yaml`，添加如下内容（如果没有 `dns:` 字段）：

```yaml
dns:
  enable: true
  listen: 0.0.0.0:53
  enhanced-mode: fake-ip
  nameservers:
    - 8.8.8.8
    - 1.1.1.1
  fallback:
    - tls://dns.google
    - tls://1.1.1.1
```

如果你已经有 `dns:` 字段，只需确保里面的服务器能正常访问（建议用 8.8.8.8）。

------

### 🚩 原因 3：TUN 虚拟网卡没有设置正确的路由

有时系统无法正确设置默认路由到虚拟网卡，导致所有网络请求“找不到出口”。

#### ✅ 添加 `tun:` 字段（手动）

由于 Clash GUI 没自动写入，你可以手动添加以下字段到 `config.yaml`：

```yaml
tun:
  enable: true
  stack: system
  dns-hijack:
    - any:53
  auto-route: true
  auto-detect-interface: true
```

⚠️ 注意缩进，`tun:` 字段必须在 `mixed-port:` 等顶层字段同一缩进级别下。

------

## ✅ 推荐操作流程汇总

1. **确认 Clash 是管理员启动 & 启用 Service Mode**
2. **确认设备管理器中存在 TUN 虚拟网卡**
3. **手动编辑 `config.yaml` 添加 `tun:` 字段**
4. **添加或修改 `dns:` 字段，确保能解析外网域名**
5. 重启 Clash，并在 GUI 中打开 TUN 模式

------

## 🎁 参考完整配置片段（合并版）

```yaml
mixed-port: 7890
allow-lan: false
external-controller: 127.0.0.1:51033
secret: 12899b79-1dcd-40e0-b33b-d64996c26862

tun:
  enable: true
  stack: system
  dns-hijack:
    - any:53
  auto-route: true
  auto-detect-interface: true

dns:
  enable: true
  listen: 0.0.0.0:53
  enhanced-mode: fake-ip
  nameservers:
    - 8.8.8.8
    - 1.1.1.1
  fallback:
    - tls://dns.google
    - tls://1.1.1.1
```

------

## 如果仍然断网怎么办？

🛠 请检查这些点：

| 检查项                  | 说明                                        |
| ----------------------- | ------------------------------------------- |
| 管理员启动 Clash        | 必须 ✅                                      |
| TUN 网卡是否存在        | 设备管理器中看到 clash-tun                  |
| 本地 DNS 是否被劫持     | 建议 flush DNS 后再试：`ipconfig /flushdns` |
| 安装了防火墙 / 杀毒软件 | 有些防火墙会拦截 tun 接口                   |



# 五、Allow LAN

`allow-lan` 是 Clash 配置文件中的一个布尔开关，作用是：

> ✅ **是否允许局域网设备访问 Clash 的代理端口（如 mixed-port / HTTP / SOCKS5）**

------

## 🔍 举例说明：

假设你在电脑 A 上运行 Clash，配置如下：

```yaml
mixed-port: 7890
allow-lan: false
```

此时：

- 你自己的电脑（本机）可以通过 `127.0.0.1:7890` 使用代理 ✅
- 但其他局域网中的设备（如手机、另一台电脑）**不能访问**你电脑上的代理 ❌

------

## ✅ 如果你想让其他设备共享 Clash 的代理

就需要这样设置：

```yaml
allow-lan: true
```

然后：

1. 找到你当前设备的局域网 IP，例如 `192.168.1.100`
2. 在其他设备（如手机）上设置代理为：

```
代理地址：192.168.1.100
端口：7890（或 Clash 配置的 mixed-port）
```

> 📌 **前提是防火墙允许访问这个端口**，可以手动添加放行规则，或在首次运行 Clash 时点击“允许访问专用网络”。

------

## 🚫 安全警告

开启 `allow-lan: true` 之后：

- 只要其他设备知道你 IP 和端口，就可以使用你的代理；
- 如果你不限制 IP 白名单或不开启认证，可能会被局域网里的其他人**“蹭代理”**甚至恶意使用！

------

## 🔒 如何更安全地使用 `allow-lan: true`

### 方法 1：使用 Clash GUI 的「仅本机访问」选项（建议）

- 在 Clash for Windows 中打开：
   `Settings > General > LAN Access`
  - 设为 **Allow LAN**（局域网允许）
  - 再设定绑定地址为你的私有 IP，如 `192.168.1.100`

### 方法 2：绑定 IP 限制访问范围

```yaml
bind-address: 192.168.1.100   # 限制代理端口只监听此 IP
```

------

## ✅ 总结：

| 配置项             | 含义                                 |
| ------------------ | ------------------------------------ |
| `allow-lan: false` | 默认关闭，仅本机可用                 |
| `allow-lan: true`  | 开启局域网访问，允许其他设备共享代理 |
| 配合使用           | 建议加上 IP 绑定 + 认证，确保安全    |

