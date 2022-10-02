---
title: Layui下拉框异步获取后台数据
categories:
  - Layui
abbrlink: 3704156750
---



## Layui访问接口,后台返回json格式,通过ajax渲染

```html
 <select name="awardYear" id="awardYear" > </select>
```

```javascript
      $.ajax({
        url: layui.setter.host + "/award/dict/awardYear",
        dataType: 'json',
        type: 'get',
        success: function (data) {
          $.each(data, function (index, item) {
            for(var i=0; i<item.length; i++){
              if(item[i].dictValue!=undefined){
                alert(item[i].dictValue)
                $("select[name=awardYear]").append('<option value="'+item[i].dictKey+'">'+item[i].dictValue+'</option>');
              }
            }
          });
          layui.form.render("select");
        }
      })
```

有待完善....