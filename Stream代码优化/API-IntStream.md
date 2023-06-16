

# 001___Stream.of() 用法

## 优化之前的代码

```java
 private void firstAddPart1(Long bigId, SysEmpDTO userInfo) {
        // 查询对应分类信息
        RespMaterialBigSortDTO respMaterialBigSortDTO = materialBigSortMapper.queryById(bigId);

        List<Long> ids = new ArrayList<>();
        ids.add(bigId);
        ids.add(respMaterialBigSortDTO.getParentId());

        //生成树层级结构
        List<RespMaterialBigSortDTO> list = materialBigSortMapper.queryBigSortsByIds(ids);

        int layer = 1;
        Long parentId = 0L;
        StringBuffer ancestrys = new StringBuffer();
        ancestrys.append("0,");
        for (RespMaterialBigSortDTO bigSortDTO : list) {
            MaterialRuleDetail ruleDetail = materialRuleDetailMapper.queryByBigIdAndLayerNum(bigId, layer);

            MaterialPart part = materialPartManager.pack(bigId, bigSortDTO.getCode(),
                    bigSortDTO.getName(), parentId, ancestrys.toString(), layer, ruleDetail, userInfo);

            materialPartMapper.insertPart(part);

            parentId = part.getId();
            ancestrys.append(parentId).append(",");
            layer++;
        }
    }
```

# 优化之后的代码

```java
   private void firstAddPart(Long bigId, SysEmpDTO userInfo) {
        // 查询对应分类信息
        RespMaterialBigSortDTO respMaterialBigSortDTO = materialBigSortMapper.queryById(bigId);

        //使用了Stream.of()方法将bigId和parentId转换为List  ★★★★★
        //Stream.of() 方法返回一个流，其元素是指定值，如果没有指定值，则返回一个空流。 
        // 传递的参数是一个可变参数，那么就可以传递一个数组，或者传递一个元素列表。 该方法的重载版本接受两个或更多参数。
        List<Long> ids = Stream.of(bigId, respMaterialBigSortDTO.getParentId())
                .collect(Collectors.toList());
                
        //生成树层级结构
        List<RespMaterialBigSortDTO> list = materialBigSortMapper.queryBigSortsByIds(ids);

        //顶级目录
        StringBuffer ancestrys = new StringBuffer("0,");

       //使用了IntStream.range()方法遍历List  ★★★★★
        IntStream.range(0, list.size())
                .forEach(i -> {
                    Long parentId = 0L;

                    RespMaterialBigSortDTO bigSortDTO = list.get(i);

                    MaterialRuleDetail ruleDetail = materialRuleDetailMapper.queryByBigIdAndLayerNum(bigId, i + 1);
                   
                    MaterialPart part = materialPartManager.pack(bigId, bigSortDTO.getCode(),
                            bigSortDTO.getName(), parentId, ancestrys.toString(), i + 1, ruleDetail, userInfo);

                    materialPartMapper.insertPart(part);

                    parentId = part.getId();
                    ancestrys.append(parentId).append(",");
                });
    }
```

# 002_Stream.map()

>使用 Stream API 可以将循环转化为函数式风格的代码，使代码更加简洁易懂。同时，使用 map() 方法可以将 req.getDbIds() 中的每个元素映射为一个新的 ApplyDBInfo 对象，最后使用 collect() 方法将映射结果收集到一个新的 List 中。

优化之前的代码片段
```java
     List<ApplyDBInfo> dbs = new ArrayList<>();
        for (Long id : req.getDbIds()) {
            ApplyDBInfo tollDB = new ApplyDBInfo(mainForm.getId(), id);
            dbs.add(tollDB);
        }
```


优化之后的代码
```java
        List<ApplyDBInfo> dbs = req.getDbIds().stream()
                .map(id -> new ApplyDBInfo(mainForm.getId(), id))
                .collect(Collectors.toList());
```



## 003_Stream.collect(Collectors.toMap(key,Obj));

```java  
        List<RespMaterialBigSortTreeDTO> bigTree = materialBigSortMapper.queryTree();

        // 将List转为Map
        //解释下面这段代码： Collectors.toMap()方法接收两个参数，第一个参数是要作为key的字段，第二个参数是整个对象。
        // 这里的key是id，value是整个对象，这样就可以通过id来获取整个对象了。
        // 这里的Function.identity()是一个静态方法，返回一个Function对象，这个对象的apply方法返回一个与传入参数相同的值。
        Map<Long, RespMaterialBigSortTreeDTO> map = bigTree.stream().collect(Collectors.toMap(
                RespMaterialBigSortTreeDTO::getId,
                Function.identity()));

```