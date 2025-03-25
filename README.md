
#### VirtualHere常用命令

```
usage: vhusbdx [-bhliqnu] [-c <configuration file>] [-r <log file>]
 
options:
   -b run as daemon in the background  作为守护程序运行在后台
   -c configuration file  加载配置启动
   -h display this usage  帮助
   -l display license text 许可证信息
   -i prefer ipv6 (dual/stack) 使用IPV6网络
   -r log to file instead of syslog 输出日志文件
```

#### 关于config.ini配置的参数

所述的`config.ini`文件具有以下参数（它们不区分大小写），所述参数被输入到一个所述的 `config.ininame=value` 格式，每行一个。

以下是可选的参数：

**`ServerName`** 出现在客户端中的服务器名称，例如`Virtual USB Hub`。如果将此值设置为特殊字符串`HOSTNAME`，它将采用服务器的主机名值。

**`License`** 服务器的许可证代码。注册服务器时，VirtualHere 通过电子邮件发送此值。

**`DeviceNicknames`** 这是格式，例如，如果设备插入相同的端口，地址总是相同的。您应该使用 VirtualHere 客户端通过右键单击设备并选择“重命名...”来设置昵称，而不是直接编辑此值，因为在连接客户端之前无法访问该地址。

**`UseAVAHI`**  1表示使用Bonjour在网络上广播此服务器的存在，0表示不使用广播消息在网络上宣传此服务器。默认为1开启。关闭此设置对于防火墙后面的客户端很有用，因为防火墙或子网会阻止 IP 广播数据包，他们宁愿直接在客户端中输入 IP 地址。

**`Hostname`** 这指定了与客户端通信时服务器的主机名。默认情况下，它是调用主机上的主机名实用程序的结果，但可以通过在此处指定一个值来覆盖。例如。USB服务器。请注意，如果网络上有多个 virtualhere 服务器，它们必须具有唯一的主机名

**`CompressionLimit`** 一旦 USB 传输大于此处指定的字节数，它将被压缩以节省网络传输时间。较小的值将使用更多的 CPU 来略微提高网络吞吐量，较大的值将使用较少的 CPU，但网络吞吐量会受到影响。从反复试验来看384似乎是正确的。

**`IgnoredDevices`** 这指定了插入服务器时要忽略的设备。此设置的格式`xxxx[/yyyy],xxxx/yyyy,...`，其中XXXX是设备的USB厂商ID和YYYY是设备的USB产品ID（可选）（没有前导零和值均为十六进制）。例如，在 Raspberry PI 上运行的 VirtualHere 会自动填充它，424/ec00因为这是内置于电路板中的基于 Raspberry Pi USB 的内部以太网适配器，不应共享。要找出特定设备的供应商/产品 ID，请将其插入并从 VirtualHere 客户端查看设备属性。您还可以为产品 ID 指定通配符，例如 424/*

**`IgnoredBuses`** （仅限 Linux）这指定要忽略的特定 USB 端口。它的格式hostcontroller-hub.port,hostcontroller-hub.port,...例如忽略连接到 Raspberry pi 上的端口 2 的设备，将此值设置为1-1.2

**`AllowedDevices`** 除此处列出的设备外，所有设备都将被忽略。它与IgnoredDevices设置相反。格式与IgnoredDevices设置相同，即`xxxx[/yyyy][,xxxx/yyyy,]...`（无前导零）。如果此值为空，则将允许所有设备（同时考虑上面的IgnoredBuses和IgnoreDevices设置）您还可以为产品 ID 指定通配符，例如 424/*

**`AutoAttachToKernel`** 将此设置1为允许设备在远程用户不使用设备时可供主机使用。默认适用1于 NAS、Android 和 Debian（64/32 位）以及0所有其他平台

**`ClaimPorts`** （仅限 Linux）默认情况下这是0（关闭）。当设置为1virtualhere 服务器时，将阻止服务器内核与设备交互。将此值设置为 1 可使服务器与对其选择的配置 1 敏感的 USB 设备更兼容。当这个设置打开时，内核与设备的唯一交互是查询设备描述符，但是目前外部集线器不能在这个设置打开的情况下使用。

**`PingInterval`** 默认值为4秒。这是从服务器到客户端的 ping 消息之间的秒数。客户端需要使用PingTimeout响应（见下文），然后才会考虑丢弃套接字并清理连接。有时，如果网络连接不稳定，套接字可能会断开，并且此设置和以下设置有助于针对不稳定连接调整服务器，但是您几乎应该始终将这些设置保留为默认值。

**`PingTimeout`** 默认值为9秒。如果服务器在此时间之后没有收到来自客户端的响应 ping，则认为连接已断开并已清除。

**`ControlTimeout`** 默认值为3秒。如果设备表现不佳（由于某种原因卡住），则此设置会检测到并在此时间内向客户端返回错误，而不是无限期地等待...

**`UseAvahiServiceFile`** （仅限 Linux）如果设置为1，VirtualHere 服务器将向 Linux 操作系统上的内置 avahi-daemon 注册自己。默认是0这意味着 VirtualHere 服务器将使用其内置的小型 avahi 类型服务器 (mdns)

**`ReverseClients`** （新，如果您升级到（至少）服务器 2.4.5 和客户端 3.1.1，则可以直接在 GUI 或客户端 API 中进行配置，无需手动编辑服务器配置文件），此设置将使服务器启动到客户端的连接，而不是客户端启动连接的通常方式。这对于防火墙或 NAT 后面的服务器很有用。指定每个客户端的主机 ip 和可选端口，以逗号分隔。例如ReverseClients=192.168.2.129,192.168.2.110。如果未指定端口，则默认为 7573，这是标准的 virtualhere 反向查找端口。在客户端中，确保在右键单击 USB 集线器 -> 指定集线器 -> 防火墙/NAT下选中启用反向连接。服务器将尝试依次连接到每个客户端。

**`ReverseClientsRetryPeriod`** 默认为 15 秒。这是服务器尝试反向客户端的连接尝试之间的秒数（它还没有连接到）。

###### 以下是高级参数，因此仅在购买的服务器上支持

**`TCPPort`** 这是 VirtualHere 服务器运行的 TCP 端口，如果未指定，则默认为`7575`. 您可以指定任何未使用的端口号，也可以使用 0 指定随机端口。（客户端仍然可以通过 bonjour 广播自动自动查找服务器）。

**`ClientIdMap`** 此设置允许您将客户端 ID重新映射到昵称。甲客户端ID是当在客户机的日志，它是由在客户机OS确定在自动传递和具有格式 <空间>（<用户名>）。例如，如果客户的用户名为“Michael Jones (michael)”，您可以将客户 ID 更改为“Mick”，那么这将出现在其他客户的使用信息中。这种设置格式是ClientIdMap=,[,,...]例如ClientIdMap=Michael Jones (michael),Mick,Steve Jones (steve),Steve. 客户端 ID 必须与客户端的用户名和全名完全匹配。这可以通过右键单击使用中的设备并查看属性中的 USER: 条目来找到。

**`HideClientInfo`** 默认值为0(false)。如果设置为1Device In-Use 信息不会显示给其他客户端

**`NetworkInterface`** ( Linux Only ) 这指定将服务器绑定到哪个网络接口。如果未指定（默认），则服务器将侦听所有网络接口上的传入客户端连接。否则，如果您有多个网卡并且只想侦听其中一个，请指定将服务器绑定到的 IP 地址。

**`ClientAuthorization`** 这指定了要运行以授权特定用户/设备组合的脚本。

**`ClientDeauthorization`** 与 **`ClientAuthorization`** 相同，除了在用户与设备断开连接时调用

**`SSLCert`** 用于 SSL 传输的 ssl PEM 证书文件（包含未加密的公钥和私钥）的完整路径。如果指定了此参数，则与服务器的所有通信都将通过 SSL 而不是普通 TCP 运行。端口将在下面的 sslPort 设置中指定。

**`SSLPort`** 如果指定了sslCert参数，则此端口将用于客户端通信。端口默认为`7574`

**`SSLUseClientCerts`** 将此参数设置1为打开客户端 SSL 证书。如果启用，VirtualHere 客户端必须在连接时向服务器提供 SSL 客户端证书

**`SSLCAFile`** 此文件包含使用服务器和客户端 SSL 证书的 PEM 编码证书颁发机构 (CA) 可信根证书。

**`VirtualHere`** 服务器还包含一个事件处理系统，可以将其配置为在特定时间调用脚本。这对于解决设备怪癖或执行特定操作（如日志记录）很有用，请参见 [此处](https://www.virtualhere.com/quirks)
