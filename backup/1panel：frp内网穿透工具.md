
# frp是什么？

FRP 是一个**开源的反向代理工具**，主要用于**内网穿透**，解决内网设备（如家庭电脑、服务器）无法直接被公网访问的问题。

## **核心功能：**

* **反向代理**：将公网请求转发到内网指定设备或服务（如网站、远程桌面、NAS 等）。

* **内网穿透**：让位于局域网（内网）的设备，通过 FRP 搭建的通道，被公网用户访问，无需公网 IP。

* **支持多种协议**：TCP、UDP、HTTP、HTTPS 等，适配不同应用场景。

* **安全加密**：传输过程支持加密，保障数据安全。

## **适用场景：**

* 远程访问家里的 NAS、监控设备。

* 开发测试时，让外部用户访问本地开发环境。

* 搭建个人服务器（如博客、私有云）并对外提供服务。

## **特点：**

* 轻量级，配置简单，支持多平台（Windows/macOS/Linux）。

* 开源免费，社区活跃，可自定义扩展。

简单来说，FRP 是连接公网与内网的 “桥梁”，帮助用户轻松实现内网服务的对外访问，只要有一台服务器，使用frp，即可访问家庭网络内的nas。

# 1panel如何安装frp？

## 服务器端（公网）

安装1panel-应用-frp服务端-安装（可默认）

需注意：使用的是docker，网络为host，如果开启了防火墙，需要放开**对应的端口号**\

<img width="883" height="550" alt="Image" src="https://github.com/user-attachments/assets/4a4f1b4f-f728-4532-8b5b-b17b954060c4" />

## 客户端（家庭内nas）

我家庭 nas 也安装了1panel

安装1panel-应用-frp服务端-安装（服务端ip：需要更改为对应的公网ip）

<img width="886" height="548" alt="Image" src="https://github.com/user-attachments/assets/499f2627-db41-468f-8bb7-ba82f4ab846e" />

### 打开内网客户端配置界面

内网ip:7400

用户名：

密码：

<img width="495" height="242" alt="Image" src="https://github.com/user-attachments/assets/bd3443ae-2ed5-4e16-87d9-9817856983f5" />

### 登录

<img width="448" height="360" alt="Image" src="https://github.com/user-attachments/assets/5094e5ed-1121-4a54-80e6-9f2f91f61e68" />

### 配置

演示：将本地思源服务通过tcp协议映射到公网的6806上

```
[[proxies]]
name = "siyuan"
type = "tcp"  
localIP = "10.255.255.243"
localPort = 6806
remotePort = 6806
```

### 客户端日志

日志： \[siyuan] start proxy success

```
2025-04-26 03:12:45.060 [I] [sub/root.go:149] start frpc service for config file [/etc/frp/frpc.toml]
2025-04-26 03:12:45.060 [I] [client/service.go:314] try to connect to server...
2025-04-26 03:12:45.060 [I] [client/service.go:193] admin server listen on 0.0.0.0:7400
2025-04-26 03:12:45.891 [I] [client/service.go:306] [95e2c24883c1afbe] login to server success, get run id [95e2c24883c1afbe]
2025-04-26 03:12:45.891 [I] [proxy/proxy_manager.go:177] [95e2c24883c1afbe] proxy added: [siyuan]
2025-04-26 03:12:46.080 [I] [client/control.go:172] [95e2c24883c1afbe] [siyuan] start proxy success
```

### 服务端日志

已经可以成功通过公网ip:6806 进行访问

```
2025-04-26 03:16:10.476 [I] [frps/root.go:105] frps uses config file: /etc/frp/frps.toml
2025-04-26 03:16:10.580 [I] [server/service.go:237] frps tcp listen on 0.0.0.0:7000
2025-04-26 03:16:10.580 [I] [frps/root.go:114] frps started successfully
2025-04-26 03:16:10.580 [I] [server/service.go:351] dashboard listen on 0.0.0.0:7500
2025-04-26 03:16:12.172 [I] [server/service.go:582] [95e2c24883c1afbe] client login info: ip [49.75.xx.188:38454] version [0.62.0] hostname [] os [linux] arch [amd64]
2025-04-26 03:16:38.321 [I] [server/dashboard_api.go:227] Http request: [/api/proxy/tcp]
2025-04-26 03:16:38.321 [I] [server/dashboard_api.go:221] Http response [/api/proxy/tcp]: code [200]
2025-04-26 03:16:40.913 [I] [proxy/tcp.go:82] [95e2c24883c1afbe] [siyuan] tcp proxy listen port [6806]
2025-04-26 03:16:40.913 [I] [server/control.go:399] [95e2c24883c1afbe] new proxy [siyuan] type [tcp] success
2025-04-26 03:16:41.063 [W] [server/control.go:394] [95e2c24883c1afbe] new proxy [siyuan] type [tcp] error: proxy [siyuan] already exists
2025-04-26 03:17:02.987 [I] [proxy/proxy.go:204] [95e2c24883c1afbe] [siyuan] get a user connection [103.36.xx.136:45141]
2025-04-26 03:17:06.321 [I] [proxy/proxy.go:204] [95e2c24883c1afbe] [siyuan] get a user connection [103.36.xx.136:35295]
```

# 如何进行域名访问？

> 根据上述操作，frp**内网**6806到**服务器**的6806端口

* 使用**1panel**，**openresty**（它默认是doker，并占用了80 443端口）

  * 通过**内网**传递的http服务为80 443等会导致冲突

* 配置麻烦，特别是证书，需要每一个域名进行配置证书

* 建议使用tcp协议

* 开启1panel的自带的waf（linux自带的waf）限制端口的访问

  * 服务端不开放6806端口，域名解析，反向代理到80

    * 但由于是客户端穿透给服务端6806 默认服务端6806是启用状态 在不开放waf的情况下，只能在服务端本身才能访问

    * 使用1panel自带的openresty（nginx）使用反向代理 ，即可在1panel里面管理并配置证书即可

<img width="879" height="481" alt="Image" src="https://github.com/user-attachments/assets/fa59c6e8-09ef-41cf-b603-a6d9b02c15cc" />
