# stn

Strengthen the network.

- ###### 超时单位为毫秒
- ###### 下面配置文件的注释不能出现在实际的配置文件中

### 配置文件

```c++
{
  "setting": {
    "daemon": false, // 默认 false，仅支持 linux
    "pid_file": "", // 默认不设置
    "log_level": "debug", // 支持 debug, info, warn, error, 默认 error
    "log_file": "", // 默认输出到 stdout，stdout 为特殊值
    "log_file_max": "", // log_file 不为空时生效，默认 1024，单位 kb
    "uid": 0, // 默认不设置，仅支持 linux
    "gid": 0 // 默认不设置，仅支持 linux
  },

  // 域名解析
  "resolve": {
    "server": ["8.8.8.8:53", "[::1]:53", "system"], // 默认 system
    "timeout": 5000, // 默认 5000
    "retry": 1, // 重试次数，默认 1
    "cache_size": 256, // 缓存数目，默认 256
    "min_ttl": 60, // 默认 60 秒
    "max_ttl": 2147483647, // 默认 2147483647 秒
    "ipv6_first": false, // 优先使用 ipv6 的解析结果，默认 false
    "refresh": 3000 // 当 server 中含有 system 生效，刷新 system 的时间间隔，默认 3000
  },

  "in": [
    {
      "tag": "tproxy", // 不能含有空格
      "protocol": "tproxy", // 仅支持 linux
      "address": "[::]:1080", // 地址，支持 ipv4 ipv6，无默认值
      "tcp_timeout": 300000, // tcp 超时，默认 300000
      "udp_timeout": 60000 // udp 超时，默认 60000
    },
    {
      "tag": "socks5",
      "protocol": "socks5",
      "address": "[::]:1080", // 地址，支持 ipv4 ipv6，无默认值
      "tcp_timeout": 300000, // tcp 超时，默认 300000
      "udp_timeout": 60000 // udp 超时，默认 60000
    },
    {
      "tag": "stn_in",
      "protocol": "stn",
      "address": "[::]:1080", // 地址，支持 ipv4 ipv6，无默认值
      "password": "abcd", // 密码，无默认值
      "http_header": "", // HTTP 响应头部，默认 "HTTP/1.1 200 OK\r\n\r\n"
      "tcp_timeout": 300000, // tcp 超时，默认 300000
      "udp_timeout": 60000 // udp 超时，默认 60000
    }
  ],

  "out": [
    {
      "tag": "origin",
      "protocol": "origin",
      "redirect": "127.0.0.1:80", // 重定向，默认不设置
      "tcp_timeout": 300000, // tcp 超时，默认 300000
      "udp_timeout": 60000 // udp 超时，默认 60000
    },
    {
      "tag": "stn_out",
      "protocol": "stn",
      "address": "0.0.0.0:1080", // 地址，无默认值
      "password": "abcd", // 密码，无默认值
      "http_header": "", // HTTP 请求头部，无默认值
      "tcp_timeout": 300000, // tcp 超时，默认 300000
      "udp_timeout": 60000 // udp 超时，默认 60000
    },
    {
      "tag": "drop",
      "protocol": "drop"
    }
  ],

  "route": [
    {
      "tag": ["socks5"],
      "network": ["tcp", "udp"],
      "saddr": [
        "full socks5", // 完全匹配，例如 abc 匹配 abc，但不匹配 abcd
        "substring abc", // 存在即可，例如 abc 匹配 abc，abcd，aabc
        "domain a.com", // 主域名匹配，例如 a.com 匹配 a.com，a.a.com，但不匹配 aa.com
        "cidr 1.2.3.4/24",
        "regex (^|\\.)a\\.com$",
        "file /root/addr.txt"
      ],
      "sport": [80, 443],
      "daddr": [
        "full socks5",
        "substring abc",
        "domain a.com",
        "cidr 1.2.3.4/24",
        "regex ^a\\.com$",
        "file /root/addr.txt"
      ],
      "dport": [80, 443],

      "jump": "stn_out"
    }
  ]
}
```

### 路由模块说明

- 只有 origin 和 drop 这两个出口协议不能被路由模块再次匹配
- 当匹配不到时，将选择第一个出口作为 jump
- saddr 可以匹配上一个 tag
- saddr/daddr 中，当有 / 时判断为 cidr，无 / 时判断为正则表达式。
