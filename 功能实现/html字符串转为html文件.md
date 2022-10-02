```
@PostMapping("/downloadHtml")
public void downloadPdf(String urlStr) throws IOException {
    File dest = new File("D:\\pdf\\ee.html");
    FileOutputStream fos = new FileOutputStream(dest);//字节输出流
    ByteArrayInputStream tInputStringStream = new ByteArrayInputStream(urlStr.getBytes());
    BufferedInputStream bis = new BufferedInputStream(tInputStringStream);//为字节输入流加缓冲
    BufferedOutputStream bos = new BufferedOutputStream(fos);//为字节输出流加缓冲
    int length;
    byte[] bytes = new byte[1024*20];
    while((length = bis.read(bytes, 0, bytes.length)) != -1){
        fos.write(bytes, 0, length);
    }
    bos.close();
    fos.close();
    bis.close();
    tInputStringStream.close();
}
```





