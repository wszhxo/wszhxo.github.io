## java操作Solr

###  常见搜索

``` *:*```   表示搜索全部

```字段:值```   表示搜索某个字段

```字段:(? TO ??)```   表示搜索所有某个字段长度是1到2位的结果

```字段:(1.? TO 1.??)```   同上，例如 1.1   1.11  1.2  1.22  1.a  1.aa  这样字段的结果

```字段:[S30 TO S99]```   表示搜索所有某个字段在这个范围内的结果， s31 s32 到 s99之间的任意

```字段:[S30 TO S99] +字段:值 ```   合并两个查询结果

### java中的使用

从solr中查询出结果并映射到实体类中

```java
@Resource
SolrTemplate solrTemplate;
@Resource
HttpSolrClient httpSolrClient;

SolrQuery query = new SolrQuery();
query.setQuery("*:*");
query.setStart(0);//偏移量 从零开始
query.setRows(20);//每页显示20条
//query.set("rows", Integer.MAX_VALUE); //顯示Integer最大值的數據
httpSolrClient.setBaseURL("http://localhost:8983/solr/索引id");
QueryResponse rsp = httpSolrClient.query(query);
SolrDocumentList results = rsp.getResults();//里边有全部搜索结果，但不是实体类格式
long num =results.getNumFound();//所有搜索条数
List<实体类> list2 = solrTemplate.convertQueryResponseToBeans(rsp, 实体类.class);
		
```



根据条件搜索结果并分页

```java
public ScoredPage<实体类> findSysId(String SysId) {
		SimpleQuery query = new SimpleQuery();
		Criteria criteria = Criteria.where("字段").is(SysId);
		query.addCriteria(criteria);
		query.setOffset((long) 0);
		query.setRows(1);
		query.addSort(Sort.by(Sort.Direction.DESC, "score"));
		ScoredPage<实体类> scoredPage = solrTemplate.queryForPage(solr的索引id, query, 实体类.class);
		return scoredPage;
}
```

实体类

主键加入@Id，其他字段使用@Field或者@Indexed

```java
@Setter
@Getter
public class JournalContext implements Serializable {
	/**
	 * 
	 */
	private static final long serialVersionUID = 1L;
	@Field("xml_id")
	@Id
	private String xml_id;
	@Field("Sysid")
	String[] sysid;
	@Field("CatSouInd2")
	String[] catSouInd2;
	@Field("JournalName")
	@Indexed(name = "JournalName")
	String[] journalName;
}
```

