## Nginx命令大全
start nginx

//停止
nginx.exe -s stop

//安全停止
nginx.exe -s quit

//重新载入
nginx.exe -s reload

//查看版本
nginx -v

## Nginx主配置
#启动的worker进程数
worker_processes  1;

#配置影响 Nginx 服务器与用户的网络连接
events {
	#设置每个worker进程的最大连接数，它决定了Nginx的并发能力
    worker_connections  1024;
}

## http 段配置
http{
    sendfile           on;  #开启高效传输模式
    tcp_nopush         on;  #减少网络报文段的数量
    keepalive_timeout  65;  #保持连接的时间，也叫超时时间，单位秒
    gzip               on;  #启用gzip压缩功能           
}

## server 段配置
server{
	listen       80;       # 配置监听的端口
	server_name  localhost;    # 配置的域名
    location / {
        proxy_pass http://upstream段名字;
    }
}

## location 段配置
location / {
    default_type text/html;
	root   /usr/share/nginx/html;  # 网站根目录
	index  index.html index.htm;   # 默认首页文件
	deny   172.168.22.11;          # 禁止访问的ip地址，可以为all
	allow  172.168.33.44;          # 允许访问的ip地址，可以为all
}

## upstream 段配置
upstream 名字 {
    ip_hash;
    least_conn;
    server 127.0.0.1:3001; 
    server 127.0.0.1:3002;
    server 127.0.0.1:3003;
}
