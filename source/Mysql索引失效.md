# Mysql索引失效

## MYISAM引擎

在Mysql安装目录的Data下有frm.MYD和MYI三种文件类型,分别表示表结构,数据信息文件,索引信息文件

![image-20210516220101000](https://bucket-zhanghx.oss-cn-beijing.aliyuncs.com/typora3/image-20210516220101000.png)

索引和数据文件是分开的先从索引文件中找到叶子结点的data值,再更具data查新数据文件的对应一行数据