# Mybatis常用易忘的mapper语法

## 参数为数组

foreach中的item自定义 collection属性固定

```java
List<TSystemChannelinfo> getA(Object[] strArr);
```

```xml
<select id="getSystemChannelinfoByIds" parameterType="object" resultMap="TSystemChannelinfoResult">
    select  f_channelno from t_system_channelinfo
    where f_channelid in
    <foreach item="fChannelid" collection="array" open="(" separator="," close=")">
        #{fChannelid}
    </foreach>
</select>
```

如果需要判断传入数组为空
```java
 List<TSystemChannelinfo> getA(@Param("strArr") Object[] strArr);
```

```xml
<select id="getListByTableType" parameterType="list"  resultMap="TSystemParamtypeResult">
       select * from table
        <where>
        <if test="strArr!=null and strArr.length>0">
           and type in
            <foreach item="f_type" collection="strArr" open="(" separator="," close=")">
                #{f_type}
            </foreach>
        </if>
        </where>
    </select>
```


## 插入

```java
int insertTSystemChannelinfo(TSystemChannelinfo tSystemChannelinfo);
```

```xml
<insert id="insertTSystemChannelinfo" parameterType="com.xx.TSystemChannelinfo" useGeneratedKeys="true" keyProperty="id>
    insert into tableA
    <trim prefix="(" suffix=")" suffixOverrides=",">
        <if test="fChannelno != null and fChannelno != ''">f_channelno,</if>
        <if test="fChannelname != null and fChannelname != ''">f_channelname,</if>
        <if test="fNo != null">f_no,</if>
     </trim>
    <trim prefix="values (" suffix=")" suffixOverrides=",">
        <if test="fChannelno != null and fChannelno != ''">#{fChannelno},</if>
        <if test="fChannelname != null and fChannelname != ''">#{fChannelname},</if>
        <if test="fNo != null">#{fNo},</if>
     </trim>
</insert>
```


## 批量插入

```java
int batchBillOperateLog(List<BillOperateLog> billOperateLog);
```

```xml
<insert id="batchBillOperateLog" >
        insert into dbs1850.bill_operate_log( `bill_id`,`bill_type`,`date`,`user`,status) values
        <foreach item="item" index="index" collection="list" separator=",">
            (#{item.billId},#{item.billType},#{item.date},#{item.user},#{item.status})
        </foreach>
    </insert>
```



## 参数为集合

foreach中的item自定义 collection属性是接口方法参数名

1. case1

```java
List<TSystemChannelinfo> getA(List<Object> strArr);
```

```xml
<select id="getSystemChannelinfoByIds" parameterType="object" resultMap="TSystemChannelinfoResult">
    select  f_channelno from t_system_channelinfo
    where f_channelid in
    <foreach item="xx" collection="strArr" open="(" separator="," close=")">
        #{xx}
    </foreach>
</select>
```

2. case2
   ```java
      List<Integer> selectBizEnergyUnitByPid(List<Integer>  ids);
   ```

   

```xml
<select id="selectBizEnergyUnitByPid" parameterType="list" resultType="integer">
    select id from biz_energy_unit where parent_id in
    <foreach item="id" collection="list" open="(" separator="," close=")">
        #{id}
    </foreach>
</select>
```





## 参数为map

和参数为实体类差不多

```java
List<BizMaxMin> selectList(Map<String,Object> condition);
```

```xml
<select id="selectBizMaxMinList" parameterType="map" resultMap="BizMaxMinResult">
  select `max` ,`min`,f_deviceid
    from ${tableName}
    where f_gathertime &gt;= #{startDate}
    and f_paramid = #{fParamid}
    and f_deviceid in
    <foreach item="item" collection="fDeviceid" open="(" separator="," close=")">
        ${item}
    </foreach>
</select>
```
