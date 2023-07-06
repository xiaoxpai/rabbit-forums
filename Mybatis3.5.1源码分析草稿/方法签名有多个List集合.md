
mapper
```java
 MaterialPart getMaterialPartsByFirstAndThirdNameList(@Param("firstList") List<String> firstList, @Param("thirdList") List<String> thirdList);
```

```xml 
 <select id="getMaterialPartsByFirstAndThirdNameList"
            resultType="com.foxconn.core.baseinfo.bo.MaterialPart" >
        select
            id, big_id, code, name, parent_id, ancestrys, layer_num, valid
        from material_part
        where
        <if test="firstList != null and firstList.size() > 0">
            name in
            <foreach collection="firstList" item="item" separator="," open="(" close=")">
                #{item}
            </foreach>
        </if>
        <if test="thirdList != null and thirdList.size() > 0">
            <if test="firstList != null and firstList.size() > 0"> or </if>
            name in
            <foreach collection="thirdList" item="item" separator="," open="(" close=")">
                #{item}
            </foreach>
        </if>
    </select>
```    

## ⚠ 经测试可以运行通过，但是业务数据查询不出来，待改进！

递归
1.基本情况
    当问题达到基本状况时，递归停止


2.递归情况
    划分一个或多个小问题，递归调用解决这些小问题，每次递归调用，问题规模都会减小，知道达到基本状况
    