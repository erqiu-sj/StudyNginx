# 反向代理

```
# 访问localhost：8081端口时会代理到http://localhost:3000
server {
listen 8081;
server_name localhost;
        location / {
            root   html;
            proxy_pass  http://localhost:3000;
            index  index.html index.htm;
        }

}
```

# Nginx Localhost 指令说明

语法规则：`location [=|~|~*|^~] /uri/ { … }`

- `=` 开头表示精确匹配
- `^~` 开头表示 uri 以某个常规字符串开头，理解为匹配 url 路径即可。nginx 不对 url 做编码，因此请求为/static/20%/aa，可以被规则^~ /static/ /aa 匹配到（注意是空格）。以 xx 开头
- `~` 开头表示区分大小写的正则匹配
- `~*` 开头表示不区分大小写的正则匹配
- `!~`和`!~*` 分别为区分大小写不匹配及不区分大小写不匹配 的正则
- `/` 通用匹配，任何请求都会匹配到。

# 基本的负载均衡

** 常见的几种分配策略 **

- 轮询
  `每个请求按时间顺序逐一分配到不同的服务器，如果服务器宕机了。会自动剔除`
- weight
  `权重。默认值为1，权重越高被分配的客户端就越多`
- ip_hash
  `每个请求按照ip的hash结果分配，这样每个访客固定访问一个服务器，可以解决seesion问题`
- fair
  `按照后端的相应时间分配，后端响应时间短的优先分配`

```
Http块中的配置
upstream myserver{
  ip_hash;
server localhost:3000 weight=10;
server localhost:8080;
fair;
}
Server块中的配置
server {
listen 8081;
server_name localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            # root   html;
            proxy_pass http://myserver;
            # index  index.html index.htm;
        }

}
```

# 动静分离
