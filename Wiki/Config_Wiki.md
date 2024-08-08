Markdown语法：https://markdown.com.cn/basic-syntax/
# 一、普适wiki
## TUN模式与HTTP PROXY模式
**模式的主要区别：**

1. **TUN Only** 模式:
    - TUN（Tunnel）模式主要是通过操作系统的网络层来重定向所有流量。它创建一个虚拟网络接口，将通过此接口的所有流量都发送到代理服务器。
    - 优点是可以透明地处理所有应用程序的流量，无需对每个应用进行单独配置。
    - 适用于需要对整个设备的网络流量进行代理的场景。
2. **HTTP Proxy & TUN** 模式:
    - 这种模式结合了HTTP代理和TUN的优点。HTTP代理主要用于处理HTTP和HTTPS流量，而TUN模式处理所有其他类型的流量。
    - 通过HTTP代理，应用程序可以显式地将它们的HTTP/HTTPS请求通过代理服务器发送。
    - TUN部分则确保其他类型的流量（如DNS查询、非HTTP协议等）也能通过代理服务器。
    - 适用于需要细粒度控制不同类型流量的场景。例如，你可以为浏览器设置HTTP代理，同时让其他应用的流量通过TUN接口。

安卓端代理软件设置中开启系统代理（为 VPN Service 添加 http 代理），即开启http proxy，就是让本身支持代理的应用自己主动走代理，而不需要在 tun 上流重组后再走代理，可以提高性能。不启用就全部走流重组。

默认情况下，你开启一个 VPN ，会启动一个 TUN ，并且系统有相应提示。应用访问网络，默认会走 TUN ，因为 TUN 会有更高的跃点数
但是，安卓 9.0 左右开始，VPN 应用可以附加一个 HTTP 代理，支持的应用会通过这个 HTTP 代理来建立连接，省去了 TUN 协议栈本身的开销，以及 SNI 嗅探的开销，并且理论上也更容易解决 DNS 污染的问题（ Redir 下可能会因为拿到被污染的 IP 而影响判断，Fake-IP 模式可以解决问题）  
VPN 应用附加的 HTTP 代理，效果和你在 WiFi 设置或者是移动网络设置里面附加一个 HTTP 代理是差不多的，区别是代理服务器在本机上运行。在 Windows 上，因为 appcontainer 的限制，本机上跑的代理（即在代理软件中使用系统代理模式），默认情况下 appcontainer 中的应用是无法连接的，表现为 Microsoft 商店打不开、系统自带搜索白屏、邮件查收不了、反馈中心用不了等关于 UWP 应用联网的问题  
而安卓是没这个限制的，本地上跑的代理可以随便连接，只要端口没有被占用就可以打开那个代理。

**总的来说：**  

TUN Only 模式更简单，适用于全局代理；而 HTTP Proxy & TUN 模式则**效率更高**提供了更灵活的配置，适用于需要区分不同类型流量的情况，**但system proxy会被检测**。

**模式推荐选择：**

① Windows端：只开启TUN模式。使全局流量都能先经代理软件处理，并规避uwp应用无法联网等问题。  
② ios端：TUN Only。降低被软件检测到代理的概率,并增强代理软件dns拦截的有效性。  
③ Android端：TUN（安卓端的真正意义上的TUN需要root权限，此处指安卓系统的VPN）＋http proxy，然后开启“允许应用绕过VPN”，然后在“应用访问控制”中排除银行、学习强国、数字人民币等应用。提高代理效率和性能。
# 二、Clash Meta
## 备用GEO位置信息文件订阅链接
geosite: "https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/geosite.dat"  
mmdb: "https://raw.githubusercontent.com/Loyalsoldier/geoip/release/Country.mmdb"
## allow-lan
允许局域网用于共享网络环境给同一局域网的其他设备。要注意的是两个设备都连接同一个wifi，而不是一个连接另一个开的热点。因此在非永久的家庭WiFi下，请勿开启允许局域网
## FakeIP
有些app会自己去请求DNS获取IP，这样导致有些域名类型的规则无法进行匹配，所以用FakeIP来解决这个问题，原理是截取这些DNS请求，返回一个假的IP响应，然后在获取到这个假的IP的请求时将相关域名映射到请求中；但是有时候系统的一些域名会缓存这些假IP，导致关闭代理软件后会用这个假的IP直接发起请求，这就会导致一些问题，针对这种情况可以配置fake-ip-filter来使这些域名返回真实的ip