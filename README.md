# Clash-meta
自用的clash meta配置, 解决向日葵远程失败的问题

```
# true 是启用
# false 是禁用
# 分组
pr:
  &pr {
    type: select,
    proxies:
      [
        🚀 节点选择,
        🇭🇰 香港节点,
        🇨🇳 台湾节点,
        🇯🇵 日本节点,
        🇸🇬 新加坡节点,
        🇺🇲 美国节点,
        其它地区,
        #全部节点,
        🔃 自动选择,
        DIRECT,
      ],
  }
# 延迟检测 URL
p:
  &p {
    type: http,
    interval: 3600,
    health-check:
      {
        enable: true,
        url: https://www.gstatic.com/generate_204,
        interval: 300,
      },
  }

# 订阅名，记得修改成自己的
# 添删订阅在这里和下方订阅链接依葫芦画瓢就行
use: &use
  type: select
  use:
    #- 订阅一
    - 本地配置

# 订阅链接
# 对于订阅来说，path 为选填项，但建议启用
# 本地配置可以只填 path
proxy-providers:
 #订阅一:
  #  <<: *p
  #  url: 订阅地址

  本地配置:
    <<: *p
    path: ./profiles/rupv3RGDHhOG.yaml
    #url: 自己填


mode: rule
# ipv6 支持
ipv6: true
log-level: info
# 允许局域网连接
allow-lan: true
# socks5/http 端口
mixed-port: 7890
socks-port: 7891
# Meta 内核特性 https://wiki.metacubex.one/config/general
# 统一延迟
# 更换延迟计算方式,去除握手等额外延迟
unified-delay: true
# TCP 并发
# 同时对所有ip进行连接，返回延迟最低的地址
tcp-concurrent: true
# 外部控制端口
external-controller: :9090

geodata-mode: true

# Geo 数据库下载地址
# 使用 FastGit 代理 (https://fgit.cf)
# 源地址 https://github.com/MetaCubeX/meta-rules-dat
# 可以更换镜像站但不要更换其他数据库，可能导致无法启动
geox-url:
  geoip: "https://testingcf.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/geoip-lite.dat"
  geosite: "https://hub.gitmirror.com/https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/geosite.dat"
  mmdb: "https://hub.gitmirror.com/https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/country.mmdb"

# 进程匹配模式
# 路由器上请设置为 off
# always 开启，强制匹配所有进程
# strict 默认，由 Clash 判断是否开启
# off 不匹配进程，推荐在路由器上使用此模式
find-process-mode: strict

# 缓解移动设备耗电问题
# https://github.com/vernesong/OpenClash/issues/2614
keep-alive-interval: 30

# 全局客户端指纹
global-client-fingerprint: random # 随机指纹

# 缓存
profile:
  store-selected: true


# 域名嗅探
sniffer:
  enable: true
  sniff:
    TLS:
      ports: [443, 8443]
    HTTP:
      ports: [80, 8080-8880]
      override-destination: true
  # 解决向日葵远程连接失败的问题 https://clash-meta.gitbook.io/clash.meta-wiki-older/function/dns/sniffer
  skip-domain:
    - '*.rc.sunlogin.net'
  # - '*.baidu.com'

# tun 模式
tun:
  enable: true  # enable 'true'
  stack: system  # or 'gvisor'
  dns-hijack:
    - "any:53"
    - "tcp://any:53"
  auto-route: true
  auto-detect-interface: true
  # 严格路由，可防止地址泄漏，启用后你的设备将无法被其他设备访问
  strict-route: true


dns:
  enable: true
  ipv6: true
  listen: 0.0.0.0:7874
  enhanced-mode: fake-ip
  fake-ip-range: 28.0.0.1/8
  fake-ip-filter:
    - '*'
    - '+.lan'
    - '+.local'
  default-nameserver:
    - 223.5.5.5
    - 119.29.29.29
    - 114.114.114.114
    - '[2402:4e00::]'
    - '[2400:3200::1]'
  nameserver:
    - 'tls://8.8.4.4#dns'
    - 'tls://1.0.0.1#dns'
    - 'tls://[2001:4860:4860::8844]#dns'
    - 'tls://[2606:4700:4700::1001]#dns'
  proxy-server-nameserver:
    - https://doh.pub/dns-query
  nameserver-policy:
    "geosite:cn,private":
      - https://doh.pub/dns-query
      - https://dns.alidns.com/dns-query
  

# 多入站端口设置
# listeners:
#   - name: hk
#     type: mixed
#     port: 12991
#     proxy: 香港

#   - name: tw
#     type: mixed
#     port: 12992
#     proxy: 台湾

#   - name: sg
#     type: mixed
#     port: 12993
#     proxy: 新加坡

proxies:
  # - name: "WARP"
  #   type: wireguard
  #   server: engage.cloudflareclient.com
  #   port: 2408
  #   ip: "172.16.0.2/32"
  #   ipv6: "2606::1/128"        # 自行替换
  #   private-key: "private-key" # 自行替换
  #   public-key: "public-key"   # 自行替换
  #   udp: true
  #   reserved: "abba"           # 自行替换
  #   mtu: 1280
  #   dialer-proxy: "WARP前置"
  #   remote-dns-resolve: true
  #   dns:
  #     - https://dns.cloudflare.com/dns-query

proxy-groups:
   # 分隔,下面是地区分组
  - { name: 🇭🇰 香港节点, <<: *use, filter: "(?i)港|hk|hongkong|hong kong" }
  - { name: 🇨🇳 台湾节点, <<: *use, filter: "(?i)台|tw|taiwan" }
  - { name: 🇯🇵 日本节点, <<: *use, filter: "(?i)日|jp|japan" }
  - { name: 🇺🇲 美国节点, <<: *use, filter: "(?i)美|us|unitedstates|united states" }
  - { name: 🇸🇬 新加坡节点, <<: *use, filter: "(?i)(新|sg|singapore)" }
  - {
      name: 其它地区,
      <<: *use,
      filter: "(?i)^(?!.*(?:🇭🇰|🇯🇵|🇺🇸|🇸🇬|🇨🇳|港|hk|hongkong|台|tw|taiwan|日|jp|japan|新|sg|singapore|美|us|unitedstates)).*",
    }
  #- { name: 全部节点, <<: *use }
  - { name: 🔃 自动选择, <<: *use, tolerance: 2, type: url-test,filter: "(?!)🇭🇰|🇯🇵|🇺🇸|🇸🇬|🇨🇳|港|hk|hongkong|台|tw|taiwan|日|jp|japan|新|sg|singapore" }
  
  # 使用 WARP 的用户需要手动在下方的 proxies 字段内添加 WARP
  # 例如 [WARP, 全部节点, 🔃 自动选择, 🇭🇰 香港节点, 🇨🇳 台湾节点, 🇯🇵 日本节点, 🇸🇬 新加坡节点, 🇺🇲 美国节点, 其它地区, DIRECT],
  - {
      name: 🚀 节点选择,
      type: select,
      proxies:
        [🔃 自动选择, 🇭🇰 香港节点, 🇨🇳 台湾节点, 🇯🇵 日本节点, 🇸🇬 新加坡节点, 🇺🇲 美国节点, 其它地区, DIRECT],
    }
  # 这里的 dns 指海外解析 dns 走的节点，一般跟随🚀 节点选择即可
  - { name: dns, <<: *pr }
  # WARP 配置链式出站
  # - { name: WARP前置, <<: *pr, exclude-type: "wireguard" }

#  - { name: 广告拦截, type: select, proxies: [REJECT, DIRECT, 🚀 节点选择] }
  - { name: 🤖 ChatGPT, <<: *pr }
  # 🍎 苹果服务 推荐走全局直连
  - { name: 🍎 苹果服务, <<: *pr }
  - { name: Ⓜ️ 微软服务, <<: *pr }
  #- { name: Ⓜ️ 微软云盘, <<: *pr }
  - { name: 🎮 游戏平台, <<: *pr }
  - { name: 📢 谷歌FCM, <<: *pr }
  - { name: 📲 电报服务, <<: *pr }
  - { name: Twitter, <<: *pr }
#  - { name: Pixiv, <<: *pr }
#  - { name: ehentai, <<: *pr }
  # 下面两个看需求启用，打开之后会代理全站流量，可能导致部分版权视频反而无法播放或视频播放速度缓慢
  # 下面 rules 两条也要启用
# - {name: 哔哩哔哩, <<: *pr}
# - {name: 哔哩东南亚, <<: *pr}
#  - { name: 巴哈姆特, <<: *pr }
  - { name: 🎥 油管视频, <<: *pr }
  - { name: 🎥 网飞视频, <<: *pr }
  - { name: Spotify, <<: *pr }
  - { name: 🔍 GitHub, <<: *pr }
  - {
      name: 国内,
      type: select,
      proxies:
        [
          DIRECT,
          🚀 节点选择,
          🇭🇰 香港节点,
          🇨🇳 台湾节点,
          🇯🇵 日本节点,
          🇸🇬 新加坡节点,
          🇺🇲 美国节点,
          其它地区,
          #全部节点,
          🔃 自动选择,
        ],
    }
  # 其他就是所有规则没匹配到的
  # 可以理解为 ACL4SSR 配置里的 漏网之鱼
  # 换言之，其他走代理就是绕过中国大陆地址，不走就是 GFWList 模式
  - { name: 🐟 漏网之鱼, <<: *pr }
  #- { name: 广告拦截白名单, type: select, proxies: [DIRECT, REJECT, 🚀 节点选择] }
  #- { name: 广告拦截, type: select, proxies: [REJECT, DIRECT, 🚀 节点选择] }

rule-providers:
  reject:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/reject.txt"
    path: ./RuleSet/reject.yaml
    interval: 86400
  icloud:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/icloud.txt"
    path: ./RuleSet/icloud.yaml
    interval: 86400
  apple:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/apple.txt"
    path: ./RuleSet/apple.yaml
    interval: 86400
  microsoft:
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/zhanyeye/clash-rules-lite@release/microsoft-rules.txt"
    path: ./RuleSet/microsoft.yaml
    interval: 86400
  google:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/google.txt"
    path: ./RuleSet/google.yaml
    interval: 86400
  proxy:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/proxy.txt"
    path: ./RuleSet/proxy.yaml
    interval: 86400
  direct:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/direct.txt"
    path: ./RuleSet/direct.yaml
    interval: 86400
  private:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/private.txt"
    path: ./RuleSet/private.yaml
    interval: 86400
  gfw:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/gfw.txt"
    path: ./RuleSet/gfw.yaml
    interval: 86400
  greatfire:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/greatfire.txt"
    path: ./RuleSet/greatfire.yaml
    interval: 86400
  tld-not-cn:
    type: http
    behavior: domain
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/tld-not-cn.txt"
    path: ./RuleSet/tld-not-cn.yaml
    interval: 86400
  telegramcidr:
    type: http
    behavior: ipcidr
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/telegramcidr.txt"
    path: ./RuleSet/telegramcidr.yaml
    interval: 86400
  cncidr:
    type: http
    behavior: ipcidr
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/cncidr.txt"
    path: ./RuleSet/cncidr.yaml
    interval: 86400
  lancidr:
     type: http
     behavior: ipcidr
     url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/lancidr.txt"
     path: ./RuleSet/lancidr.yaml
     interval: 86400
     
  applications:
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/applications.txt"
    path: ./RuleSet/applications.yaml
    interval: 86400

rules:
  # 自定义规则
  - DOMAIN-SUFFIX,openai.com,🤖 ChatGPT
  - DOMAIN-SUFFIX,bing.com,DIRECT
  - DOMAIN-SUFFIX,a-nomad.com,DIRECT
  - DOMAIN-SUFFIX,api2d.net,DIRECT
  - DOMAIN-SUFFIX,appstorrent.ru,🚀 节点选择
  - DOMAIN-SUFFIX,qq.com,DIRECT
  - DOMAIN-SUFFIX,bing.net,Ⓜ️ 微软服务
  - RULE-SET,reject,REJECT
  - RULE-SET,applications,DIRECT
  - RULE-SET,private,DIRECT
  - RULE-SET,direct,DIRECT
  - RULE-SET,icloud,🍎 苹果服务
  - RULE-SET,apple,🍎 苹果服务
  - RULE-SET,google,📢 谷歌FCM
  - RULE-SET,telegramcidr,📲 电报服务
  - RULE-SET,microsoft,Ⓜ️ 微软服务
  - RULE-SET,proxy,🚀 节点选择
  - RULE-SET,tld-not-cn,🚀 节点选择
  - RULE-SET,gfw,🚀 节点选择
  - RULE-SET,lancidr,DIRECT
  - RULE-SET,cncidr,DIRECT
  - GEOIP,LAN,DIRECT,no-resolve
  - GEOIP,CN,DIRECT,no-resolve
  - MATCH,🐟 漏网之鱼


