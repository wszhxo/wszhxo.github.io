Linux配置SSL证书实现HTTPS

注意此处的Nginx是docker的容器，因此ssl_certificate和ssl_certificate_key需要把证书放到容器中的nginx中的目录

如果是外置Nginx则无需注意

     server {
    		listen 80 ssl;
            server_name  skyworthpv.com;
    		 #从腾讯云获取到的第一个文件的全路径
            ssl_certificate /etc/ssl/skyworthpv.com_server.crt;
            #从腾讯云获取到的第二个文件的全路径
            ssl_certificate_key /etc/ssl/skyworthpv.com_server.key;
            ssl_session_timeout 5m;
            ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
            ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
            ssl_prefer_server_ciphers on; 
      #charset koi8-r;
    
        #access_log  logs/host.access.log  main;
    
         location / {
            root  /usr/local/beitai1850/dist;
    		try_files $uri $uri/ /index.html;
            index  index.html index.htm;
    
       
    	location /prod-api/{
    			proxy_set_header Host $http_host;
    			proxy_set_header X-Real-IP $remote_addr;
    			proxy_set_header REMOTE-HOST $remote_addr;
    			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    			proxy_pass http://192.168.1.60:9079/;
    	}
    	
    	
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
        
    }
