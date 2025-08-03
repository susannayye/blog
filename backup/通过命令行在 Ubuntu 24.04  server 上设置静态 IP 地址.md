
## 通过命令行在 Ubuntu 24.04  server 上设置静态 IP 地址

我们可以使用 Netplan 工具和配置文件，为 Ubuntu Server 设置静态 IP 地址。Netplan 的配置文件通常位于`/etc/netplan`目录，取决于你的系统版本和实际环境，文件名可能是`01-netcfg.yaml`或`50-cloud-init.yaml`。

1使用以下命令列出所有网络接口，确定 Ubuntu 上可用的网络接口名称：

```
ip link show
```

![使用 ip 命令列出 ubuntu 网络接口](https://img.sysgeek.cn/img/2024/05/set-static-ip-ubuntu-p6.png)

使用 ip 命令列出 ubuntu 网络接口

2找到 Netplan 的配置文件：

```
cd /etc/netplan
ls -l
```

![切换到 netplan 配置文件目录](https://img.sysgeek.cn/img/2024/05/set-static-ip-ubuntu-p7.png)

切换到 netplan 配置文件目录

3打开并编辑 Netplan 配置文件，例如`50-cloud-init.yaml`：

```
sudo vi 50-cloud-init.yaml
```

配置文件示例：

```
network:
    renderer: networkd
    ethernets:
        ens33: # 替换为你的网络接口名称
            dhcp4: false # 关闭 DHCP
            dhcp6: false # 关闭 DHCP
            addresses: [192.168.100.122/24] # 静态 IP 地址和子网掩码
            routes:
              - to: default
                via: 192.168.100.1 # 网关地址
            nameservers:
                addresses: [192.168.100.1] # DNS 服务器地址
                search: []
    version: 2
```

* 根据网络环境替换上述示例中的 IP 地址、网关和 DNS 服务器地址。
* 「renderer」配置为`networkd`，使用 systemd-networkd 作为网络配置的后端。桌面环境使用 `NetworkManager`，Ubuntu Server 和无头环境使用`networkd`。

![编辑 netplan 配置文件](https://img.sysgeek.cn/img/2024/05/set-static-ip-ubuntu-p8.png)

编辑 netplan 配置文件

4保存文件后，执行以下命令应用更改：

```
sudo netplan apply
```

5检查 IP 地址和网络连接：

```
ip addr show
ip route show
ping www.sysgeek.cn
```

![应用 netplan 配置并测试网络](https://img.sysgeek.cn/img/2024/05/set-static-ip-ubuntu-p9.png)

应用 netplan 配置并测试网络

***