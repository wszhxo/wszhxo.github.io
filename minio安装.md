# minio安装



## windows安装

1. https://docs.min.io/cn/minio-quickstart-guide.html下载minio.exe

2. 进入minio安装目录

```xml
.\minio.exe server D:\Files
```

![image-20210319103754572](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora300carimage-20210319103754572.png)

这是文件上传的密钥，以及minio管理平台的账号密码
MINIO_ACCESS_KEY：minioadmin
MINIO_SECRET_KEY：minioadmin

3. http://localhost:9000/输入上述账号密码 点击右下角+创建bucket

4. 注意开放9000端口

5. ```xml
   <dependency>
       <groupId>io.minio</groupId>
       <artifactId>minio</artifactId>
       <version>7.1.2</version>
   </dependency>
   ```

控制层

```java
package com.zhx.demo.controller;


import com.zhx.demo.utils.MinIOUtil;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;

@Controller
public class MinIOController {
    // 上传
    @PutMapping("/minio/upload")
    @ResponseBody
    public String upload(@RequestParam("file") MultipartFile file){
        try {
            MinIOUtil.upload(file);
            return file.getOriginalFilename()+" 上传成功！";
        } catch (Exception e) {
            e.printStackTrace();
            return file.getOriginalFilename()+" 上传失败！";
        }
    }
    // 获取下载路径
    @GetMapping("/minio/getDownloadUrl")
    @ResponseBody
    public String getDownloadUrl(@RequestParam("objectName") String objectName){
        try {
            String url = MinIOUtil.getDownloadUrl(objectName);
            return objectName+" 的下载路径为："+url;
        } catch (Exception e) {
            e.printStackTrace();
            return "获取失败！";
        }
    }
}
```

工具类

```java
package com.zhx.demo.utils;

import io.minio.GetPresignedObjectUrlArgs;
import io.minio.MinioClient;
import io.minio.PutObjectArgs;
import io.minio.http.Method;
import org.springframework.web.multipart.MultipartFile;

import java.io.InputStream;

public class MinIOUtil {
    final static String AC = "minioadmin";
    final static String SE = "minioadmin";
    final static String BUCKET = "pic"; // 存放文件的文件夹
    final static MinioClient CLIENT = MinioClient.builder().endpoint("http://localhost:9000/").credentials(AC, SE).build(); // 单例创建一个MinIO客户端对象

    // 上传
    public static void upload(MultipartFile file) throws Exception {
        String filename = file.getOriginalFilename(); // 获取完整文件名
        String contentType = file.getContentType(); // 获取文件类型
        InputStream ips = file.getInputStream(); // 获取文件流
        // 链式编程  上传文件
        CLIENT.putObject(PutObjectArgs.builder().bucket(BUCKET).object(filename).stream(ips, -1, 5 * 1024 * 1024 * 1024L).contentType(contentType).build());
    }

    // 获取下载路径
    public static String getDownloadUrl(String objectName) throws Exception {
        return CLIENT.getPresignedObjectUrl(GetPresignedObjectUrlArgs.builder().object(objectName).method(Method.GET).bucket(BUCKET).build());
    }
}
```

启动项目是用postman测试

![image-20210319113009049](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora300carimage-20210319113009049.png)

![image-20210319113036052](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora300carimage-20210319113036052.png)

## linux安装

查看cpu相关信息

```
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c           
```

查看服务器位数64还是32

```
getconf LONG_BIT 
```

下载，授权，启动

```
wget https://dl.min.io/server/minio/release/linux-amd64/minio   
chmod +x minio
./minio server /data
```



![image-20210319133405273](https://zhanghx.oss-cn-beijing.aliyuncs.com/typora300carimage-20210319133405273.png)

接下来的操作同上