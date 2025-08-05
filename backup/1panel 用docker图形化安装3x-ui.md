## 1panel 用docker图形化安装3x-ui

#安装1panel面板

<https://1panel.cn/docs/installation/online_installation/>

我使用的服务器为ubuntu，先通过ssh安装它

```
curl -sSL https://resource.fit2cloud.com/1panel/package/quick_start.sh -o quick_start.sh && bash quick_start.sh
```

* 选择你喜欢的语言：**2**

* 默认 **opt**

* 设置**端口**

* 设置**安全入口**

* 设置**账号**

* 设置**密码**

### 安装防火墙

<https://1panel.cn/docs/user_manual/hosts/firewall/>

#### 更新软件包

```
sudo apt update
```

#### 安装 UFW

```
sudo apt install ufw
```

#### 放开 1Panel 系统端口

```
sudo ufw allow 8090/tcp
```

上述命令中的 8090 端口需要替换为安装 1Panel 系统时自定义的端口

因还是需要通过命令，后续的所有操作由1panel内置终端操作

#### 启动 UFW

```
sudo ufw enable
```

#### 不放行22端口

为了安全着想

如果你的ssh非标准也不需要放开，请注意，如果你没有放开1panel，将无法在访问，则需要服务商提供通过vnc或控制台放开端口

<img width="870" height="815" alt="Image" src="https://github.com/user-attachments/assets/a6159e96-f0ad-49f1-a0aa-22062a909953" />

### 使用编排docker

```
version: "3"

services:
  3x-ui:
    image: ghcr.io/mhsanaei/3x-ui:latest
    container_name: 3x-ui
    hostname: my-3x-ui
    volumes:
      - ./db/:/etc/x-ui/
      - ./cert/:/root/cert/  #默认路径在/opt/1panel/docker/compose/下面
    environment:
      XRAY_VMESS_AEAD_FORCED: "false"
    tty: true
    network_mode: host
    restart: unless-stopped
```

<img width="904" height="527" alt="Image" src="https://github.com/user-attachments/assets/d370519c-d41e-4e75-a980-c8006fbb158c" />

#### 容器日志

已经成功运行，它原使用的是本地host

你可以通过浏览器访问 http\://<服务器IP地址>:2053 来访问 3x-ui 的 Web 界面，**如果你安装了防火墙，是访问不了的，请先放开端口**

```
erver ready
(0x2a354e0,0xc00060e1b0)
2024/12/27 09:12:33 Starting x-ui 2.4.10
(0x2a354e0,0xc0004c2630)
INFO - Web server running HTTP on [::]:2053
INFO - XRAY: infra/conf/serial: Reading config: &{Name:bin/config.json Format:json}
WARNING - XRAY: core: Xray 24.12.18 started
```

## 设置x-ui

官方教程

<https://github.com/MHSanaei/3x-ui>

> 并不建议使用80 443 端口，可以使用其他非标准端口

默认用户名:admin

默认密码：admin

请注意登录进去修改对应的账号密码，

<img width="880" height="712" alt="Image" src="https://github.com/user-attachments/assets/2e6a6f13-066c-4d2a-81c7-f5bcc6d45d78" />

### 安全设置

#### 修改密码用户名以及开启令牌

注意保存令牌

面板设置-安全设定

修改原用户名密码

搞定后保存后重启面板

<img width="908" height="679" alt="Image" src="https://github.com/user-attachments/assets/470da47a-3020-4d03-9815-788a9a26017a" />

#### 修改默认url地址

<img width="903" height="545" alt="Image" src="https://github.com/user-attachments/assets/427abf16-157f-4c69-9feb-e35b39584011" />

### 新建一个反向代理

前提是域名解析到当前服务器IP地址

通过1panel安装openresty（nginx），使用它新建一个反向代理

http\://127.0.0.1:2053 为本地地址

<img width="909" height="532" alt="Image" src="https://github.com/user-attachments/assets/ada25313-002c-486b-9bad-27fbf262b644" />

#### 申请证书

<img width="907" height="422" alt="Image" src="https://github.com/user-attachments/assets/ea1c6ccc-bc32-45d7-adbe-22644ffe499c" />

<img width="897" height="491" alt="Image" src="https://github.com/user-attachments/assets/75162cdc-cb03-4bee-bd1d-3f4949d571af" />

#### 启用https

<img width="888" height="663" alt="Image" src="https://github.com/user-attachments/assets/f4e85668-3b9f-4d40-be80-a9b06cf1ac04" />

#### 官方的nginx代码

```
location /sub {
#Nginx 子路径
#确保面板设置中的 “URI Path” 相同。/sub
#面板中的 需要以 结尾。url/
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Range $http_range;
    proxy_set_header If-Range $http_if_range; 
    proxy_redirect off;
    proxy_pass http://127.0.0.1:2053;
}
```

```
location ^~ /sub { 
#自用反代代码
    proxy_pass http://127.0.0.1:2053/sub;
    proxy_set_header Host $http_host;
    add_header Cache-Control no-cache;
    proxy_ssl_server_name on; 
}
```

<img width="884" height="581" alt="Image" src="https://github.com/user-attachments/assets/6198cd54-8f04-4dda-af79-51b6bb913618" />

##### 如果你出现了无限301 重定向次数的问题

请删除这个if判断的重定向代码

##### 如果你需要了奇怪的问题

比如打不开，证书没应用，我个人建议不要使用一键反向代理

请使用静态网站，然后在nginx中的配置文件修改规则即可

现在你可以通过 \[https\://域名:端口/子路径，就可以成功登录到x-ui了。]

### 添加入站列表

新建一个入站规则

无特殊需求默认即可

* 老的软件不支持vless （只需要注意你要用哪种协议，自己选择）

* 服务器防火墙 **开放**对应的**端口**

* 可以开启**tls**。证书路径为**/root/cert/**XXX公钥或秘钥证书文件名

  * 文件名查看地址为：**/opt/1panel/docker/compose/3x-ui/cert**，这个地址是刚才1panel一键申请证书的位置

  * 一般来说地址是：/root/cert/fullchain.pem /root/cert/privkey.pem

* **其他无特殊需求可以不需要设置**

![](https://lsky.5ee.net/lsky/2024/12/27/676ea3568c344.webp)

![](https://lsky.5ee.net/lsky/2024/12/27/676ea4eff0d97.webp)

