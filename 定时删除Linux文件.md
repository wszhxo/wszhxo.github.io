定时删除Linux文件

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-quartz</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>


<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
</dependency>
```

工具类

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.io.IOException;
import java.util.Arrays;
import java.util.Date;

@Component
public class FileSchedule {
    private static final Logger logger = LoggerFactory.getLogger(FileSchedule.class);
    @Scheduled(cron = "0 0 23 * * ?")
    private void deleteGeoConvertCache() throws IOException {
        String[] cmd = new String[]{"sh","-c","find /data/myfiles/  -name '*.*'  -atime +7 -ls  -exec rm {} \\;"};
        logger.info("定时删除任务：时间：{} 执行了：{}",new Date(), Arrays.toString(cmd));
        CommandUtil.run(cmd);
    }
}
```

```java
package com.example.delFileSchedule.utils;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.Scanner;
import java.util.concurrent.TimeUnit;

public class CommandUtil {
    public static String run(String command) throws IOException {
        Scanner input = null;
        String result = "";
        Process process = null;
        try {
            process = Runtime.getRuntime().exec(command);
            try {
                //等待命令执行完成
                process.waitFor(10, TimeUnit.SECONDS);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            InputStream is = process.getInputStream();
            input = new Scanner(is);
            while (input.hasNextLine()) {
                result += input.nextLine() + "\n";
            }
            result = command + "\n" + result; //加上命令本身，打印出来
        } finally {
            if (input != null) {
                input.close();
            }
            if (process != null) {
                process.destroy();
            }
        }
        return result;
    }

    public static String run(String[] command) throws IOException {
        Scanner input = null;
        String result = "";
        Process process = null;
        try {
            process = Runtime.getRuntime().exec(command);
            try {
                //等待命令执行完成
                process.waitFor(10, TimeUnit.SECONDS);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            InputStream is = process.getInputStream();
            input = new Scanner(is);
            while (input.hasNextLine()) {
                result += input.nextLine() + "\n";
            }
            result = Arrays.toString(command) + "\n" + result; //加上命令本身，打印出来
        } finally {
            if (input != null) {
                input.close();
            }
            if (process != null) {
                process.destroy();
            }
        }
        return result;
    }


}
```

控制层

```java
@RestController
public class CmdController {
    @RequestMapping("/delFile")
    public String delFile2() throws IOException {
        String[] cmd = new String[]{"sh","-c","find /data/myfiles/  -name '*.*'  -atime +7 -ls  -exec rm {} \\;"};
        return CommandUtil.run(cmd);
    }
    @RequestMapping("/delFile2")
    public String delFile3() throws IOException {
        String[] cmd = new String[]{"sh","-c","find /data/myfiles/  -name '*'  -atime +7 -ls  -exec rm {} \\;"};
        return CommandUtil.run(cmd);
    }
    @RequestMapping("/test")
    public String test2() {
        return " OK ";
    }
}
```