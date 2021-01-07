# stn

Strengthen the network.

- ###### 超时单位为毫秒
- ###### 下面配置文件的注释不能出现在实际的配置文件中

### 配置文件

```c++
{
  "setting": {
    "daemon": false, // 默认 false
    "pid_file": "", // 默认不设置
    "log_level": "debug", // 支持 debug, info, warn, error, 默认 error
    "log_file": "", // 默认输出到 stdout
    "log_file_max": "", // log_file 不为空时生效，默认 1024，单位 kb
    "uid": 0, // 默认不设置
    "gid": 0 // 默认不设置
  },

  // 入口组
  "ins": [
    {
      "protocol": "tproxy", // 指定入口协议，无默认值
      "address": "0.0.0.0", // 监听地址，支持 ipv4 ipv6，默认 "::"
      "port": 1110, // 监听端口，无默认值
      "tcp_timeout": 300000, // tcp 超时，默认 300000
      "udp_timeout": 300000 // udp 超时，默认 60000
    },
    {
      "protocol": "stn",
      "address": "::", // 监听地址，支持 ipv4 ipv6，默认 "::"
      "port": 1111, // 监听端口，无默认值
      "password": "abcd", // 密码，无默认值
      "http_header": "", // HTTP 响应头部，默认 "HTTP/1.1 200 OK\r\n\r\n"
      "tcp_timeout": 300000, // tcp 超时，默认 300000
      "udp_timeout": 300000 // udp 超时，默认 60000
    }
  ],

  // 出口，虽然是数组，但只支持一个，预计支持多出口
  "outs": [
    {
      "protocol": "origin",
      "tcp_timeout": 300000, // tcp 超时，默认 300000
      "udp_timeout": 300000 // udp 超时，默认 60000
    },
    {
      "protocol": "stn",
      "address": "1.2.3.4", // 监听地址，支持 ipv4 ipv6，无默认值
      "port": 1111, // 监听端口，无默认值
      "password": "abcd", // 密码，无默认值
      "http_header": "", // HTTP 请求头部，无默认值
      "tcp_timeout": 300000, // tcp 超时，默认 300000
      "udp_timeout": 300000 // udp 超时，默认 60000
    }
  ]
}
```
