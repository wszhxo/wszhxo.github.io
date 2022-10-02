关于mysql 的条件字段排序 和 应用内的排序效率问题



> 应用场景：前端页面传入乱序的n个id，输出的数据需要与传入的顺序一致  
>
> mysql 有一个order by field的功能可以处理 都说效率低，如果不用，采用应用内处理就要双重循环排列成与输入一致的顺序，这里就测试一下两者不同处理下运行时间

mysql 的字段排序功能

```sql
SELECT * FROM table1  where id in (.....)  order by field(id,....)
```



## 测试代码

mysql代码

```
有字段排序

<select id="test1" resultMap="TSystemDeviceinfoResult">
    SELECT
    *
    FROM
    t_system_deviceinfo  sd
    where sd.f_deviceid in
    <foreach collection="array" item="id" open="(" close=")" separator="," >
        #{id}
    </foreach>
    order by field
    <foreach item="id" collection="array" open="(sd.f_deviceid," separator="," close=")">
        #{id}
    </foreach>
</select>


无字段排序

<select id="test2" resultMap="TSystemDeviceinfoResult">
    SELECT
    *
    FROM
    t_system_deviceinfo  sd
    where sd.f_deviceid in
    <foreach collection="array" item="id" open="(" close=")" separator="," >
        #{id}
    </foreach>

</select>
```







java测试代码

```

public static long test1()
{
    long t1=System.currentTimeMillis();
    //模拟传入参数 ，n个随机的id
    int[] deviceIds=new int[]{6,4411,5411,3266。。。。。。};
    //字段排序
    List<TSystemDeviceinfo> list=  deviceinfoMapper.test1(deviceIds);
    long t2=System.currentTimeMillis();
    return t2-t1;
}

public static long test2()
{
    long t1=System.currentTimeMillis();
    List<TSystemDeviceinfo> listnew=   new ArrayList<>();
     //模拟传入参数 ，n个随机的id
    int[] deviceIds=new int[]{6,4411,5411,3266。。。。。。};
    //没有字段排序
    List<TSystemDeviceinfo> list=   deviceinfoMapper.test2(deviceIds);
    //处理成与传入参数一致顺序
    for (int i = 0; i < deviceIds.length; i++) {
        for (TSystemDeviceinfo tSystemDeviceinfo : list) {
            if (deviceIds[i]==tSystemDeviceinfo.getfDeviceid().intValue()){
                listnew.add(tSystemDeviceinfo);
            }
        }

    }
    long t2=System.currentTimeMillis();
    return t2-t1;
}
```



| -                | -mysql排序                        | 应用内排序                        | 结果               |
| ---------------- | --------------------------------- | --------------------------------- | ------------------ |
| 测试id传入200个  | 五次执行 47  39 37 38  38         | 五次执行49 39 39 37 40            | mysql快了几毫秒    |
| 测试id传入1000个 | 五次执行1559 1480 1541  1520 1481 | 五次执行3068 3007 3002  3008 3015 | mysql  快了  1.5秒 |

得出结论： 无论数据量多少 使用mysql排序速度更快

