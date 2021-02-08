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
    "log_file": "", // 默认输出到 stdout
    "log_file_max": "", // log_file 不为空时生效，默认 1024，单位 kb
    "uid": 0, // 默认不设置，仅支持 linux
    "gid": 0 // 默认不设置，仅支持 linux
  },

  "in": [
    {
      "tag": "tproxy",
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
    }
  ],

  "route": [
    {
      "tag": ["socks5"],
      "network": ["tcp", "udp"],
      "saddr": ["socks5", "1.2.3.4/24", "^a\\.com$", "file /root/addr.txt"],
      "sport": [80, 443],
      "daddr": ["socks5", "1.2.3.4/24", "^a\\.com$", "file /root/addr.txt"],
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
