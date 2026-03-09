---
author: 'Lanxinmob'
title: 'iptables note'
postSlug: 'iptables note'
featured: false
draft: false
tags:
  - '笔记'
  - 'iptables'
  - 'linux'
  - '计算机网络'
ogImage: ''
description: '深入学习防火墙和iptables配置背后的五链四表'
pubDatetime: 2026-02-10T09:00:00Z
toc: true
---

### 防火墙

#### 访问控制

- **端口级**：`-p tcp --dport 22 -j ACCEPT`
- **IP级**：`-s 192.168.1.100 -j ACCEPT`
- **接口级**：`-i eth0` 、`-i lo`

#### 记录访问状态

- **连接跟踪**：`-m state --state NEW,ESTABLISHED`
- **频率限制**：`-m recent --set` `--update --seconds 1 --hitcount 10 -j DROP`
- **速率限制**：` -p icmp --icmp-type 8 -m limit --limit 1/s`（限制每秒包的数量，防**ICMP**洪水）
- **特征匹配**：`-m string --string "cmd.exe" --algo bm -j DROP`（直接在数据包里搜字符串`cmd.exe`，这个功能现在用得比较少，因为现在绝大多数网站都用了 **HTTPS**）

#### 控制流量特征

- **抗 SYN 洪水**：`-p tcp --syn --dport 80 -m connlimit --connlimit-above 20 -j DROP` （限制同一个 IP 地址，同时只能有 20 个连接）

#### 减少信息泄露

- **DROP vs REJECT**

  - **REJECT**：就知道有这个端口，只是关了。
  - **DROP**：直接丢弃，**不回任何包**。只能看到 `filtered`。无法判断是网络不好，IP不存在，还是被防火墙拦了，大大增加了扫描难度。

- **隐藏指纹**

  禁止 **ICMP（Ping）** 回显：`iptables -A INPUT -p icmp --icmp-type 8 -j DROP`，让机器在网络上“隐形”。

  不同操作系统，默认的 TTL 值不同。修改 **TTL（time to live）** 值，让别人不知道是什么操作系统。

  ```bash
  # 伪装成 Windows
  iptables -t mangle -A OUTPUT -j TTL --ttl-set 128
  ```

### Packet flow

![File:Netfilter-packet-flow.svg](https://upload.wikimedia.org/wikipedia/commons/thumb/3/37/Netfilter-packet-flow.svg/960px-Netfilter-packet-flow.svg.png?20210421135414)

数据包进入Linux系统后的 **Packet Flow**：

1. **数据包（包头（MAC头、IP头、TCP头）和数据 ）到达网卡**。

2. **XDP 处理**：网卡驱动接管数据包，**XDP (eXpress Data Path)** 程序最先介入（可在此进行超高速丢包或重定向，不经过内核协议栈）。

3. **链路层处理**：如果通过 XDP，进入链路层。内核进行 **Bridge Check**（网桥检查）。

   *   如果是二层流量（交换机模式），直接在链路层转发。
   *   如果是三层流量（IP模式），剥离MAC头，进入网络层（IP层）处理流程。

4. **进入 PREROUTING 链**

   1.  **Raw 表**：决定该包是否豁免连接跟踪（NOTRACK）。
   1.  **Conntrack (连接跟踪)**：内核为数据包分配“身份状态”（NEW/ESTABLISHED）。如果是新连接，在此建立档案；如果是老连接，直接关联已有状态。

   > 连接跟踪提取**源IP、源端口、目标IP、目标端口、协议** 作为key记录所有流经的数据流的状态。

   3. **Mangle 表**：修改包头特征（如 TOS、TTL、打 Mark 标记）。

   > **TOS (Type of Service)** 
   >
   > - IP 包头里本来就有的字段。如果你在打游戏（需要低延迟），同时在下载电影（需要大吞吐量）。那么**Mangle **可以给游戏的包修改 TOS 为 Minimize-Delay（最小延迟），给下载的包修改 TOS 为 Maximize-Throughput（最大吞吐）。路由器看到游戏的包，会给它插队，优先发送。
   >
   > **打 Mark 标记**
   >
   > ```bash
   > iptables -t mangle -A PREROUTING -s 192.168.1.88 -j MARK --set-mark 100
   > iptables -t mangle -A PREROUTING -s 192.168.1.0/24 -j MARK --set-mark 200
   > ```
   >
   > 配合设定的路由表实现策略路由，例如看到 **MARK 100** -> 走电信网关，看到 **MARK 200** -> 走移动网关。

   4. **Nat 表 (DNAT)**：进行**目标地址转换**。如果需要把访问公网 IP 的包转发给内网服务器，必须在此刻修改目标 IP。**只有连接的第一个包（NEW）经过此表，后续包自动由 Conntrack 处理。**

5. **路由决策（Routing Decision）**：内核拿着路由表，根据数据包的**目标IP**，决定下一步往哪里走。如果目标IP是本机 -> 进入 INPUT 链。如果目标IP不是本机 -> 进入 FORWARD 链（转发给其他机器）。

**分流处理 (三条路径)**

**路径 A：入站流量 **

*   **场景**：访问本机的 SSH、Web 服务。
*   **INPUT 链**：
    1.  **Mangle 表**：最后修改包头的机会。
    2.  **Nat 表**：针对入站包的特殊转换。
    3.  **Filter 表**：**本机防火墙核心**，逐一执行各个规则。
*   **终点**：数据包通过内核 socket 接口，被拷贝到用户空间，交给应用程序（如 Nginx、SSHD）。

**路径 B：转发流量**

*   **场景**：路由器模式，数据包从 WAN 口进，要去 LAN 口。
*   **FORWARD 链**：
    1.  **Mangle 表**：修改包特征。
    2.  **Filter 表**：**网络防火墙核心**。控制内网与外网、或不同网段间的通信权限（如 `FORWARD DROP`）。
*   **流向**：处理完毕后，汇入出站流程。

**路径 C：本机出站流量**

*   **场景**：本机运行 `curl` 或 `ping` 外部。
*   **起点**：应用程序产生数据。
*   **路由决策**：查路由表，决定从哪个网卡（接口）出去，下一跳网关是谁。
*   **OUTPUT 链**：
    1.  **Raw 表**：决定是否追踪。
    2.  **Conntrack**：记录这条发出的新连接。
    3.  **Mangle 表**：修改包头。
    4.  **Nat 表 (DNAT)**：修改本机发出的包的目标地址。
    5.  **Filter 表**：控制本机能否访问外部。
*   **流向**：处理完毕后，汇入出站流程。

##### POSTROUTING 链

**路径 B (转发)** 和 **路径 C (本机出站)** 的流量最终汇聚于此，准备离开网卡。

1.  **Mangle 表**：最后修改包头的机会。
2.  **Nat 表 (SNAT)**：进行**源地址转换**。
    *   如果是路由器，必须在此将内网 IP（192.168.x.x）转换成公网 IP。
    *   与 **DNAT** 一样，仅处理首包，后续由 **Conntrack** 自动完成。

3. **出站 **：数据包交给网卡驱动程序。转化为物理信号，通过网线/光纤发送到网络中。

### 五链四表解构 

| 链名称          | 所在位置与时机                                               | 核心职责                                                     |
| :-------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **PREROUTING**  | **数据刚进入网卡**。此时内核还没查询路由表，不知道数据包是给谁的。 | **DNAT（目标地址转换）**。常用于端口转发，决定数据包的去向。 |
| **INPUT**       | **路由决策之后**。内核发现目标 IP 是本机，准备把数据包交给本机进程。 | **入站防火墙**。保护本机服务（如 SSH、Nginx）不被外部随意访问。 |
| **FORWARD**     | **路由决策之后**。内核发现目标 IP 不是本机，而是要路过本机去往别处。 | **转发控制**。控制内网与外网之间，或不同容器之间的通信权限。 |
| **OUTPUT**      | **本机产生数据**。本机进程（如 curl、ping）发出数据包，准备离开协议栈。 | **出站防火墙**。限制本机程序访问外部恶意 IP 或特定端口。     |
| **POSTROUTING** | **即将离开网卡**。无论数据包是本机发出的还是转发的，这是最后一道工序。 | **SNAT（源地址转换）**。常用于转换内网 IP 为公网 IP，确保回包能找到路。 |

| 表名称     | 优先级 | 核心功能                                           | 注意                                                         |
| :--------- | :----- | :------------------------------------------------- | :----------------------------------------------------------- |
| **raw**    | **1**  | **状态跟踪豁免**。决定数据包是否跳过连接跟踪机制。 | 一旦在这里标记 NOTRACK，后面的 nat 表和 state 模块都对该包失效。 |
| **mangle** | **2**  | **修改包头数据**。修改 TTL、TOS 或打上内核标记。   | 常用于**策略路由**（让特定流量走特定宽带）或隐藏系统指纹。   |
| **nat**    | **3**  | **地址转换**。修改源 IP、目标 IP 或端口映射。      | **只有连接的第一个包（NEW）会经过此表**。后续包由内核自动查表处理，不再经过这里。 |
| **filter** | **4**  | **过滤与放行**。决定数据包是 ACCEPT 还是 DROP。    | 如果不指定表名，iptables 默认就在操作 filter 表。            |

#### iptables配置

在明白数据包的流程后，iptables 的配置也就很好理解了。

```bash
# 1. 先将默认策略设为 ACCEPT
sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT

# 2. 清空当前所有规则 (重置状态)
sudo iptables -F
sudo iptables -X
sudo iptables -Z
sudo iptables -t mangle -F
sudo iptables -t nat -F

# 3. 启用 SYN Cookies (防 SYN Flood)
echo 1 > /proc/sys/net/ipv4/tcp_syncookies

# 4. 允许已建立的连接 (保证现在的 SSH 不掉线)
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# 5. 允许本地回环 (localhost 内部通信)
sudo iptables -A INPUT -i lo -j ACCEPT

# 6. [Web] 拦截包含 "cmd.exe" 的恶意扫描
# (这只对 HTTP 80 有效，HTTPS 443 是加密的，防火墙看不见内容)
sudo iptables -A INPUT -p tcp --dport 80 -m string --string "cmd.exe" --algo bm -j DROP

# 7. [Web] 限制单 IP 并发连接数 (防 CC/DoS)
# 限制 80 和 443 端口，单 IP 最大 20 个连接
sudo iptables -A INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 20 -j DROP
sudo iptables -A INPUT -p tcp --syn --dport 443 -m connlimit --connlimit-above 20 -j DROP


# 8. [Web] 限制新建连接速率
# 限制 80/443 端口：1秒内最多新建 10 个
sudo iptables -A INPUT -p tcp --dport 80 -m state --state NEW -m recent --set --name WEB80
sudo iptables -A INPUT -p tcp --dport 80 -m state --state NEW -m recent --update --seconds 1 --hitcount 10 --name WEB80 -j DROP

sudo iptables -A INPUT -p tcp --dport 443 -m state --state NEW -m recent --set --name WEB443
sudo iptables -A INPUT -p tcp --dport 443 -m state --state NEW -m recent --update --seconds 1 --hitcount 10 --name WEB443 -j DROP
# 9. [SSH] 防暴力破解
# 60秒内尝试连接超过 5 次，丢弃
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --set --name SSH
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 5 --name SSH -j DROP

# 10. 允许 SSH
# 只有未被第9步拦截的连接才会走到这里
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# 11. 允许 Web 服务
# 只有未被防御规则拦截的连接才会走到这里
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# 12. 不允许 Ping (防 Ping Flood)
sudo iptables -A INPUT -p icmp --icmp-type 8 -j DROP
# 或允许每秒 1 个 Ping，突发 10 个
sudo iptables -A INPUT -p icmp --icmp-type 8 -m limit --limit 1/s --limit-burst 10 -j ACCEPT

# 13. 修改 TTL 伪装系统
sudo iptables -t mangle -A OUTPUT -j TTL --ttl-set 128

# 14. 将 INPUT 链默认设为拒绝，关门。
sudo iptables -P INPUT DROP

sudo systemctl restart docker
sudo systemctl restart fail2ban
# 15. 保存规则
sudo iptables-save > /etc/iptables/rules.v4
```

| **-A** | **A**ppend   | **追加**规则                 | iptables -A INPUT      |
| ------ | ------------ | ---------------------------- | :--------------------- |
| **-P** | **P**olicy   | 设置**默认策略**（最后执行） | iptables -P INPUT DROP |
| **-p** | **p**rotocol | 指定**协议**                 | iptables -p tcp        |
| **-t** | **t**able    | 指定**表** (默认为 filter)   | iptables -t mangle     |
| **-I** | **I**nsert   | **插入**规则到链的**最前面** | iptables -I INPUT      |







---

#### 参考

[Linux 服务器有必要开启 iptables 防火墙么？](https://www.zhihu.com/question/23063802/answer/1966647714117817569?utm_psn=2003906044854810528)

[iptables-tutorial](https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html)
