
# 集合操作

## List 转 Map 

```java
        //写法1  可读性高
        // 将List转为Map
        Map<Long, RespMaterialBigSortTreeDTO> map = new LinkedHashMap<>(bigTree.size());
        for (RespMaterialBigSortTreeDTO resp : bigTree) {
            map.put(resp.getId(), resp);
        }


        //写法2  使用jdk8新特性
        Map<Long, RespMaterialBigSortTreeDTO> map = bigTree.stream()
                                                .collect(Collectors.toMap(RespMaterialBigSortTreeDTO::getId, 
                                                Function.identity(), 
                                                (oldValue, newValue) -> newValue, //param3: 当两个对象的键相同时，如何合并这两个对象的值，使用新值替换旧值
                                                LinkedHashMap::new)); //param4:可以指定Map的实现类 

        //写法3 使用jdk8新特性
        Map<Long, RespMaterialBigSortTreeDTO> map = bigTree.stream().collect(Collectors.toMap( 
        RespMaterialBigSortTreeDTO::getId,
        Function.identity())); //Collectors.toMap()方法接收两个参数，第一个参数是要作为key的字段，第二个参数是整个对象。
                                //这里的Function.identity()是一个静态方法，返回一个Function对象，这个对象的apply方法返回一个与传入参数相同的值。                                               


        //
```        