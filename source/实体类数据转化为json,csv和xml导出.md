将查询数据转换为csv，json和xml格式

### 实体类数据转化为json

转成json实体类没有要求,就是正常写

```java
List<实体类> infoList= getList();
return new Gson().toJson(infoList);//返回json数据
```

### 实体类数据转化为csv

导入依赖

```xml
<dependency>
    <groupId>com.opencsv</groupId>
    <artifactId>opencsv</artifactId>
    <version>4.6</version>
</dependency>

<dependency>
       <groupId>org.apache.commons</groupId>
       <artifactId>commons-lang3</artifactId>
       <version>3.8.1</version>
</dependency>
```

#### 第一种方法

```java
/**
     * CSV 使用@CsvBindByName 取得标题Header
     *
     * @param clazz
     * @return
     */
    private String buildHeader(Class<?> clazz) {
        return Arrays.stream(clazz.getDeclaredFields())
                .filter(f -> f.getAnnotation(CsvBindByPosition.class) != null
                        && f.getAnnotation(CsvBindByName.class) != null)
                .sorted(Comparator.comparing(f -> f.getAnnotation(CsvBindByPosition.class).position()))
                .map(f -> f.getAnnotation(CsvBindByName.class).column())
                .collect(Collectors.joining(",")) + "\n";
    }

//主要代码
  Writer writer = new StringWriter();
		StatefulBeanToCsv beanToCsv = new StatefulBeanToCsvBuilder(writer)
				.withEscapechar('\\').build();
		writer.append(buildHeader(AccreditAPIDTO.class));
		beanToCsv.write(alist);//alist就是集合
		writer.close();
        return writer.toString();//这个字符串保存为csv文件自动转成表格
```

#### 第二种方法

这种方法就是首先声明好下载到的路径，直接转换成csv在本地

```java
          Writer writer = new FileWriter(new File("G:\\NclServiceFile\\xxx.csv"));
          ColumnPositionMappingStrategy<AccreditAPIDTO> mapper =
              new ColumnPositionMappingStrategy<>();
          mapper.setType(AccreditAPIDTO.class);
          CSVWriter csvWriter = new CSVWriter(writer, CSVWriter.DEFAULT_SEPARATOR, CSVWriter.NO_QUOTE_CHARACTER, '\\', "\n");
          writer.append(buildHeader(AccreditAPIDTO.class));
         StatefulBeanToCsv beanToCsv = new StatefulBeanToCsvBuilder(writer)
             .withMappingStrategy(mapper)
             .withQuotechar(CSVWriter.NO_QUOTE_CHARACTER)
             .withSeparator(CSVWriter.DEFAULT_SEPARATOR)
              .withEscapechar('\\')
              .build();
          beanToCsv.write(alist);//alist就是数据集合
         csvWriter.close();
          writer.close();
```

实体类

CsvBindByName这个就是表格的标题

@CsvBindByPosition(position = 0) 这个字段顺序

```java
@Getter
@Setter
public class AccreditAPIDTO {
	@CsvBindByName(column = "MainTitle")
    @CsvBindByPosition(position = 0)
	ArrayList<String> mainTitle;//类型可以任意，看具体业务，总之都会转换成功
	
	@CsvBindByName(column = "ParallelTitle")
    @CsvBindByPosition(position = 1)
	ArrayList<String> parallelTitle;
	
	@CsvBindByName(column = "AuthorName")
    @CsvBindByPosition(position = 2)
	ArrayList<String> authorName;
	
}
```

### 实体类数据转化为xml

```java
 return JaxbUtil.convertToXml(accreditAPIXml, "utf-8");//accreditAPIXml
```

转换xml工具类

```java
package com.mitac.common.util;

import org.springframework.stereotype.Component;

import javax.xml.bind.JAXBContext;
import javax.xml.bind.Marshaller;
import javax.xml.bind.Unmarshaller;
import java.io.StringReader;
import java.io.StringWriter;

@Component
public class JaxbUtil {
    /**
     * JavaBean轉換成xml
     * 預設編碼UTF-8
     * @param obj
     * @param writer
     * @return
     */
    public static String convertToXml(Object obj) {
        return convertToXml(obj, "UTF-8");
    }
    /**
     * JavaBean轉換成xml
     * @param obj
     * @param encoding
     * @return
     */
    public static String convertToXml(Object obj, String encoding) {
        String result = null;
        try {
            JAXBContext context = JAXBContext.newInstance(obj.getClass());
            Marshaller marshaller = context.createMarshaller();
            marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            marshaller.setProperty(Marshaller.JAXB_ENCODING, encoding);
            StringWriter writer = new StringWriter();
            marshaller.marshal(obj, writer);
            result = writer.toString();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return result;
    }
    /**
     * xml轉換成JavaBean
     * @param xml
     * @param c
     * @return
     */
    @SuppressWarnings("unchecked")
    public static <T> T converyToJavaBean(String xml, Class<T> c) {
        T t = null;
        try {
            JAXBContext context = JAXBContext.newInstance(c);
            Unmarshaller unmarshaller = context.createUnmarshaller();
            t = (T) unmarshaller.unmarshal(new StringReader(xml));
        } catch (Exception e) {
            e.printStackTrace();
        }
        return t;
    }
}

```

把返回结果导出为对应文件，想要什么就转成什么格式

```java
FileUtils.writeStringToFile(new File("F:\\xxx.csv"), 返回的String);
```

依赖

```xml
 <dependency>
     <groupId>commons-io</groupId>
     <artifactId>commons-io</artifactId>
     <version>2.6</version>
</dependency>
```



