---
title: SSM实现Layui分页
abbrlink: 1
categories: 
- 业务实现
---


## 论那些年layui踩过的坑
<!--more-->

## layui分页的特点,加载完文档就异步请求后台,返回json数据加载

[Layui官方文档](https://www.layui.com/doc/modules/table.html) 

## <font color="#FF4040">只要在 table.render中开启分页 page: true异步请求是自动加上两个参数,在后台接受即可</font>

![1558056732588](SSM实现Layui分页/1558056732588.png)

## Layui的json格式要求:

```javascript
{
  "status": 0,
  "message": "", 
  "total": 180, 
  "data": {
    "item": [{}, {}]
  }
}
```

## 所以SpringMVC中这样写,首先你得有PageBean实体类,在最后面我附上了我的PageBean实体类

```java
@RequestMapping(value = "/library/listBook", produces = "text/plain;charset=UTF-8")
	public @ResponseBody String listBook(@RequestParam(value = "page", defaultValue = "1") Integer page,
			@RequestParam(value = "limit", defaultValue = "6") Integer limit,PageBean pageBean,Model model) {
        //封装进PageBean中
		pageBean.setCurrentPage(page);
		pageBean.setPageSize(limit);
		// 转化为json
		List<Bookinfo> list = bookService.listAllBook(pageBean);
		PageBean pb=bookService.getPb();
		// list转成json
		JSONObject obj = new JSONObject();
		// Layui table 组件要求返回的格式
		obj.put("code", 0);
		obj.put("msg", "");
		obj.put("count",pb.getTotalCount());
		obj.put("data", list);
		return obj.toString();

	}
```

## 1.json发送到浏览器中文乱码问题,在SpringMVC控制层的方法上加入

```java
@RequestMapping(value = "省略", 
produces = "text/plain;charset=UTF-8")
```

## 2,关于带搜索功能的分页,因此我把搜索关键字段也放入的PageBean分页类中

我这里设置了,三个字段

```java
	private String bname;// 书名字
	private String author;// 作者
	private String cname;// 类别
```

只要开启了[Layui的重载](https://www.layui.com/demo/table/reload.html)
就可以异步关键词搜索

html中加入

```html
<!-- 搜索条件 -->
	<div class="demoTable layui-form">
	  	<div class="layui-inline">
	    	<input class="layui-input" name="bname" id="bname" autocomplete="off"  placeholder="请输入书名">
	    </div>
	    <div class="layui-inline">
	    	<input class="layui-input" name="author" id="author" autocomplete="off" placeholder="请输入作者">
	    </div>
	    <div class="layui-inline">
			<div class="layui-input-block">
			   	<select name="cname" id="cname">
				  <option value="">请选择书本类别</option>
				  <option value="010">北京</option>
				  <option value="021">上海</option>
				  <option value="0571">杭州</option>
				</select> 
			</div>
	    </div> 
 		<button class="layui-btn" data-type="reload">搜索</button>
  	</div>
```

js中加入,先获取输入框的值,<font color="#FF4040">**'testReload'这个对应,table.render的id: 'testReload'**</font>,没有的话必须加上,where就是异步提交的三个参数,我在后台用PageBean接受,因为它会自动映射进相应的字段

```javascript
var $ = layui.$, active = {
		   reload: function(){
		      var bname = $('#bname');
		      var author = $('#author');
		      var cname = $('#cname');
		      //执行重载
		      table.reload('testReload', {
		    	  //一定要加不然乱码
		    	method: 'post'
		        ,page: {
		          curr: 1 //重新从第 1 页开始
		        }
		        ,where: {
		        	//bname表示传到后台的参数,bname.val()表示具体数据
		        	  bname: bname.val(),
		        	  author: author.val(),
		        	  cname: cname.val(),
		        }
		      });
		    }
  };
  $('.demoTable .layui-btn').on('click', function(){
	    var type = $(this).data('type');
	    active[type] ? active[type].call(this) : '';
	  });
  
```

![1558058966637](SSM实现Layui分页/1558058966637.png)



## PageBean实体类,可以根据需求把数据List也封装进去

```java
public class PageBean {
	// 当前页数
	private Integer currentPage;
	// 总记录数
	private Integer totalCount;
	// 每页显示条数
	private Integer pageSize;
	// 总页数
	private Integer totalPage;
	// 起始索引
	private Integer index;
	// 搜索条件
	private String bname;// 书名字
	private String author;// 作者
	private String cname;// 类别
	
	public PageBean() {
		
	}
	public PageBean(Integer currentPage, Integer totalCount, Integer pageSize) {
		this.totalCount = totalCount;

		this.pageSize = pageSize;

		this.currentPage = currentPage;

		if (this.currentPage == null) {
			// 如页面没有指定显示那一页.显示第一页.
			this.currentPage = 1;
		}

		if (this.pageSize == null) {
			// 如果每页显示条数没有指定,默认每页显示3条
			this.pageSize = 3;
		}

		// 计算总页数
		this.totalPage = (this.totalCount + this.pageSize - 1) / this.pageSize;

		// 判断当前页数是否超出范围
		// 不能小于1
		if (this.currentPage < 1) {
			this.currentPage = 1;
		}
		// 不能大于总页数
		if (this.currentPage > this.totalPage) {
			this.currentPage = this.totalPage;
		}

	}

//	// 计算起始索引
//	public int getStart() {
//		return (this.currentPage - 1) * this.pageSize;
//	}
	public Integer getCurrentPage() {
		return currentPage;
	}

	public void setCurrentPage(Integer currentPage) {
		this.currentPage = currentPage;
	}

	public Integer getTotalCount() {
		return totalCount;
	}

	public void setTotalCount(Integer totalCount) {
		this.totalCount = totalCount;
	}

	public Integer getPageSize() {
		return pageSize;
	}

	public void setPageSize(Integer pageSize) {
		this.pageSize = pageSize;
	}

	public Integer getTotalPage() {
		return totalPage;
	}

	public void setTotalPage(Integer totalPage) {
		this.totalPage = totalPage;
	}

	public String getAuthor() {
		return author;
	}

	public void setAuthor(String author) {
		this.author = author;
	}

	public String getCname() {
		return cname;
	}

	public void setCname(String cname) {
		this.cname = cname;
	}

	public String getBname() {
		return bname;
	}

	public void setBname(String bname) {
		this.bname = bname;
	}
	
	public Integer getIndex() {
		
		return (this.currentPage - 1) * this.pageSize;
	}
	public void setIndex(Integer index) {
		this.index = index;
	}
	@Override
	public String toString() {
		return "PageBean [currentPage=" + currentPage + ", totalCount=" + totalCount + ", pageSize=" + pageSize
				+ ", totalPage=" + totalPage + ", index=" + index + ", bname=" + bname + ", author=" + author
				+ ", cname=" + cname + "]";
	}
	
	
}
```

