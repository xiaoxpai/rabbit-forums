
mapper
```java
   List<MaterialPart> getMdjByNameList(@Param("ids") List<String> firsts,@Param("ids2") List<String> seconds, @Param("ids3") List<String> thirds);
```

```xml 
 <select id="getMdjPartsByFirstAndThirdNameList"
            resultType="com.example.core.baseinfo.bo.MdjPart" >
            SELECT *
            FROM mdj
            WHERE name IN
           <if test="ids != null and ids.size()>0">
               <foreach collection="ids" item="first" open="(" separator="," close=")">
                   #{first}
               </foreach>
           </if>

          <if test="ids2 != null and ids2.size()>0">
            <if test="ids!=null and ids.size()>0">or</if>
                name IN
              <foreach collection="ids2" item="second" open="(" separator="," close=")">
                  #{second}
              </foreach>
          </if>

         <if test="ids3 != null and ids3.size() >0">
               <if test="ids2 !=null and ids2.size()>0">or</if>
                name in
             <foreach collection="ids3" item="third" open="(" separator="," close=")">
                 #{third}
             </foreach>
         </if>
    </select>
```    

 


    