### 点击直接下载方式

直接传入文件名，还需要本地存储的路径就可以下载

```html
<a href="#" th:href="@{/download(pdfpath=${book.pdfpath})}">下载</a>
```

```java
  //下载pdf
    @ResponseBody
    @RequestMapping(value= "download")
    public String download(HttpServletRequest request, HttpServletResponse response,@RequestParam String pdfpath){
        String url = uploadfolder+pdfpath;//本地存储路径
        File fileurl = new File(url);
        String showValue = pdfpath; //文件名+文件类型
        try{
            //将文件读入文件流
            InputStream inStream = new FileInputStream(fileurl);
            //获得浏览器代理信息
            final String userAgent = request.getHeader("USER-AGENT");
            //判断浏览器代理并分别设置响应给浏览器的编码格式
            String finalFileName = null;
            if(StringUtils.contains(userAgent, "MSIE")||StringUtils.contains(userAgent,"Trident")){//IE浏览器
                finalFileName = URLEncoder.encode(showValue,"UTF8");
                System.out.println("IE浏览器");
            }else if(StringUtils.contains(userAgent, "Mozilla")){//google,火狐浏览器
                finalFileName = new String(showValue.getBytes(), "ISO8859-1");
            }else{
                finalFileName = URLEncoder.encode(showValue,"UTF8");//其他浏览器
            }
            //设置HTTP响应头
            response.reset();//重置 响应头
            response.setContentType("application/x-download");//告知浏览器下载文件，而不是直接打开，浏览器默认为打开
            response.addHeader("Content-Disposition" ,"attachment;filename=\"" +finalFileName+ "\"");//下载文件的名称
            // 循环取出流中的数据
            byte[] b = new byte[1024];
            int len;
            while ((len = inStream.read(b)) > 0){
                response.getOutputStream().write(b, 0, len);
            }
            inStream.close();
            response.getOutputStream().close();
        }catch(Exception e) {
            e.printStackTrace();
        }
        return "";
    }
```



第二种方式，后端传到前端一个url形式的pdf  比如localhost:80/upload/xx.pdf

前端通过js拼接成```<a href="localhost:80/upload/xx.pdf" id="bin" download="xx.pdf">```点击该按钮即可下载，

需要预览的话同样是这个url， js中执行```window.open(localhost:80/upload/xx.pdf, "_blank");```

或者把download属性删掉