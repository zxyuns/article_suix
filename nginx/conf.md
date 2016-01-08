# nginx常用配置中文注释

```dash
# 定义Nginx运行的用户和用户组,系统中必须有此用户
user root suix;   

# 启动进程,通常设置成和cpu的数量相等
worker_processes  1;

# 全局错误日志及PID文件
error_log  /var/log/nginx/error.log; #错误日志定义等级，[ debug | info | notice | warn | error | crit ]
pid        /var/run/nginx.pid;

# 更改worker进程的最大打开文件数限制
worker_rlimit_nofile 65535;

# 工作模式及连接数上限
events {
    # epoll是多路复用IO(I/O Multiplexing)中的一种方式,但是仅用于linux2.6以上内核,可以大大提高nginx的性能
    use   epoll;

    # 单个后台worker process进程的最大并发链接数 （最大连接数=连接数*进程数）
    worker_connections  1024;
    
    # 设置是否允许,Nginx在已经得到一个新连接的通知时,接收尽可能更多的连接
    # multi_accept on; 
}

# 设定http服务器，利用它的反向代理功能提供负载均衡支持
http {
  # 设定mime类型,类型由mime.type文件定义
  include       /etc/nginx/mime.types;
  default_type  application/octet-stream;

  # 设定日志格式
  access_log    /var/log/nginx/access.log;

  # sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，对于普通应用，
  # 必须设为 on,如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，以平衡磁盘与网络I/O处理速度，降低系统的uptime.
  sendfile        on;

  # 这两个指令影响nginx的底层网络，它们决定操作系统如何处理网络层buffer和什么时候把buffer内容刷新给终端用户
  # tcp_nopush     on;
  tcp_nodelay        on;

  # 连接超时时间
  keepalive_timeout  65;
  
  # 开启gzip压缩
  gzip  on;
  # 为指定的客户端禁用gzip功能
  gzip_disable "MSIE [1-6]\.(?!.*SV1)";

  # nginx默认会用client_header_buffer_size这个buffer来读取header值，如果header过大，它会使用large_client_header_buffers来读取
  client_header_buffer_size    1k;
  large_client_header_buffers  4 4k;

  # 包含其它配置文件，如自定义的虚拟主机
  include /etc/nginx/conf.d/*.conf;
  include /etc/nginx/sites-enabled/*;

  # 设定负载均衡的服务器列表
  upstream mysvr {
      # weigth参数表示权值，权值越高被分配到的几率越大
      server 192.168.8.1x:3128 weight=5;# 本机上的Squid开启3128端口
      server 192.168.8.2x:80  weight=1;
      server 192.168.8.3x:80  weight=6;
  }
  upstream mysvr2 {
      # weigth参数表示权值，权值越高被分配到的几率越大
      server 192.168.8.x:80  weight=1;
      server 192.168.8.x:80  weight=6;
  }

  # 虚拟主机配置
  server {
    # 侦听80端口
    listen       80;

    # 定义使用www.suix.com访问
    server_name  www.suix.com;

    # 设定本虚拟主机的访问日志
    access_log  logs/www.xx.com.access.log  main;

    # 默认请求
    location / {
          # 定义服务器的默认网站根目录位置
          root   /root;

          # 定义首页索引文件的名称
          index index.php index.html index.htm;   
          
          # 按顺序检查文件是否存在，返回第一个找到的文件。结尾的斜线表示为文件夹 -$uri/。如果所有的文件都找不到，会进行一个内部重定向到最后一个参数。
          try_files $uri $uri/ /index.html = 404;

    }
    # 定义错误提示页面
    error_page   500 502 503 504 /50x.html;  
        location = /50x.html {
        root   /root;
    }

    # 静态文件，nginx自己处理
    location ~ ^/(images|javascript|js|css|flash|media|static)/ {
        root /var/www/virtual/htdocs;
        # 过期30天，静态文件不怎么更新，过期可以设大一点，如果频繁更新，则可以设置得小一点。
        expires 30d;
    }
    # PHP 脚本请求全部转发到 FastCGI处理. 使用FastCGI默认配置.
    location ~ \.php$ {
        root /root;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /home/www/www$fastcgi_script_name;
        include fastcgi_params;
    }
    # 设定查看Nginx状态的地址
    location /NginxStatus {
        stub_status            on;
        access_log              on;
        auth_basic              "NginxStatus";
        auth_basic_user_file  conf/htpasswd;
    }
    # 禁止访问 .htxxx 文件
    location ~ /\.ht {
        deny all;
    }
  }

  # 第一个虚拟服务器
  server {
    # 侦听192.168.8.x的80端口
    listen       8080;
    server_name  192.168.8.x;

    # 对aspx后缀的进行负载均衡请求
    location ~ .*\.aspx$ {
      # 定义服务器的默认网站根目录位置
      root   /root;      

      # 定义首页索引文件的名称
      index index.php index.html index.htm;   
      
      # 请求转向mysvr 定义的服务器列表
      proxy_pass  http://mysvr ;
      
      #以下是一些反向代理的配置.
      proxy_redirect off;
      
      # 后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

      # 允许客户端请求的最大单文件字节数
      client_max_body_size 10m;  

      # 缓冲区代理缓冲用户端请求的最大字节数
      client_body_buffer_size 128k;  

      # nginx跟后端服务器连接超时时间(代理连接超时)
      proxy_connect_timeout 90;  

      # 后端服务器数据回传时间(代理发送超时)
      proxy_send_timeout 90;        

      # 连接成功后，后端服务器响应时间(代理接收超时)
      proxy_read_timeout 90;         

      # 设置代理服务器（nginx）保存用户头信息的缓冲区大小
      proxy_buffer_size 4k;             

      # proxy_buffers缓冲区，网页平均在32k以下的话，这样设置
      proxy_buffers 4 32k;              

      # 高负荷下缓冲大小（proxy_buffers*2）
      proxy_busy_buffers_size 64k;    

      # 设定缓存文件夹大小，大于这个值，将从upstream服务器传
      proxy_temp_file_write_size 64k;  
    }
  }
}
```