### master 

#### nginx.conf
``` 
# 配置负载均衡服务器，upstream 后面的字符可以自定义
upstream balance {
    # 配置被转发的服务器，其中的 ip 推荐使用内网 ip，可以提高访问速度
    server server1_ip weight=1;
    server server2_ip weight=2;
    server server3_ip weight=2;
}
```

#### conf.d/master.conf
``` 
server {
    # 监听的端口
    listen 80;     
    # 域名，如果没有域名可以使用ip进行访问
    server_name default / 自己的域名;
        
    location / {
        add_header X-Content-Type-Options nosniff;
        proxy_set_header X-scheme $scheme;
        # 作用是我们可以获取到客户端的真实ip
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-Nginx-Proxy true;
        proxy_hide_header X-Powered-By;
        proxy_hide_header Vary;
      
        # 重点是这里，将代理转发给上方 upstream 中配置的两台服务器去处理，这里的 http:// 后的值必须和 upstream 后面的值一致
        proxy_pass http://balance;
    }
}
```

### slave1

#### conf.d/slave1.conf
```
server {
    listen       80;
    server_name  default;

    location / {
        root   /www;
        index  index.html index.htm index.php;
    }
}
```

### slave2

#### conf.d/slave2.conf
```
server {
    listen       80;
    server_name  default;

    location / {
        root   /www;
        index  index.html index.htm index.php;
    }
}
```