# Nginx开启Gzip压缩

> 项目js过大，导致传输过程很慢，从而使页面打开缓慢。Gzip压缩可以压缩过大的文件，加快传输速率。

## 1. Vue项目配置

### 1.1 Vue安装插件

```
npm i compression-webpack-plugin@5.0.1
```

### 1.2 vue.congfig.js加入配置

```
const CompressionWebpackPlugin = require('compression-webpack-plugin')
const productionGzipExtensions = ['js', 'css']
```

```
configureWebpack: {
  name: name,
  resolve: {
    alias: {
      '@': resolve('src')
    }
  },
  plugins: [
    new CompressionWebpackPlugin({
      filename: '[path].gz[query]',  // 提示 compression-webpack-plugin@3.0.0的话asset改为filename
      algorithm: 'gzip',
      test: new RegExp('\\.(' + productionGzipExtensions.join('|') + ')$'),
      threshold: 10240,
      minRatio: 0.8
    })
  ]
},
```

## 2. Nginx配置

宿主机写一个nginx.conf 放入docker中的/etc/nginx/nginx.conf

nginx.conf 的http中加入以下配置

```conf
 gzip on;
	  gzip_static on;
  	gzip_disable "msie6";
	  gzip_min_length 500k; 
	  gzip_comp_level 3; 
    gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png; 
    gzip_vary on;
```

查看docker的nginx容器id,复制配置文件到docker容器中

```
 docker cp /usr/local/beitai1850/nginx.conf  50f62b06acc0:/etc/nginx/nginx.conf
```

重启nginx

```
docker restart 50f62b06acc0
```

