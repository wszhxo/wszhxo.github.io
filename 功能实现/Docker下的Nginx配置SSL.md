# Docker下配置nginx的ssl证书



> 先用域名访问一下系统，证明域名已经和ip绑定

1. 把原来的docker的nginx镜像删了，因为需要配置443无法修改原有的。可以使用docker的可视化平台portainer.io删除，更加方便.

2. 重新拉取镜像```docker pull nginx```

3. 调整镜像的配置文件 

   1. ```docker run --name nginxconfig -d docker.io/nginx```
   2. ```docker cp nginxconfig:/etc/nginx/ /root/```
   3. ```docker stop nginxconfig```
   4. ```docker rm nginxconfig```

4. 创建容器```docker run --name nginx -p 80:80 -p 443:443 -v /root/nginx/:/etc/nginx/ -d docker.io/nginx```

5. 把证书文件复制到**容器下**的任意文件夹

6. 配置nginx.conf文件 以下两段关键配置

   ```
   	server {
   		listen       443 ssl;
        	ssl on;
           server_name  xxx.com;
           ssl_certificate /xxx/x.pem;
           ssl_certificate_key /xxx/x.key;
           ssl_session_timeout 5m;
           ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
           ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
           ssl_prefer_server_ciphers on;
           
           
           
           以下为原有的内容
           
           ...........
           
           
           }
   ```

   当请求http时自动跳转到https 

   ```
   server {
           server_name  xxxx.com;
           listen 80;      
            rewrite ^(.*) https://$server_name$1 permanent;   
       } 
       
      
   ```

   当想要直接通过ip访问http时，需要把```ssl on;```去掉 ，把上边的重定向脚本去掉，并修改server_name,

   最终配置会有两个server，http和https的server配置是单独的
   
   ```
   server {
           server_name  localhost;
           listen 80;   
       } 
       
      
   ```


### 涉及到的命令

1. 为了避免nginx的配置文件修改错误导致无法启动容器，因为配置文件在容器关闭时也能复制
   1. 复制容器的配置文件到外部```  docker cp 50f62b06acc0:/etc/nginx/nginx.conf     /xxxx/xxx/```
   2. 修改配置后重新放回容器中```docker cp    /xxxx/xxx/nginx.conf   50f62b06acc0:/etc/nginx/nginx.conf```
2. 重启nginx容器 ```docker restart nginx```

### 注意点

防火墙开启433端口后 要重启doceker ```systemctl restart docker```





