

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