# 备用GEO位置信息文件订阅链接
geosite: "https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/geosite.dat"
mmdb: "https://raw.githubusercontent.com/Loyalsoldier/geoip/release/Country.mmdb"
# allow-lan
允许局域网用于共享网络环境给同一局域网的其他设备。要注意的是两个设备都连接同一个wifi，而不是一个连接另一个开的热点。因此在非永久的家庭WiFi下，请勿开启允许局域网
# FakeIP
有些app会自己去请求DNS获取IP，这样导致有些域名类型的规则无法进行匹配，所以用FakeIP来解决这个问题，原理是截取这些DNS请求，返回一个假的IP响应，然后在获取到这个假的IP的请求时将相关域名映射到请求中；但是有时候系统的一些域名会缓存这些假IP，导致关闭代理软件后会用这个假的IP直接发起请求，这就会导致一些问题，针对这种情况可以配置fake-ip-filter来使这些域名返回真实的ip