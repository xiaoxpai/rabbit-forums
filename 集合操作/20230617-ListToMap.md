
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



## 非递归实现树层级结构

>数据量大且频繁操作的话，递归实现方式会内存溢出问题


 ```java
private List<RespMaterialPartTreeDTO> buildPartTree(List<RespMaterialPartTreeDTO> materialPartTrees) {
        // 用于存放树的集合
        List<RespMaterialPartTreeDTO> partTree = new ArrayList<>();

        Map<Long, RespMaterialPartTreeDTO> map = new LinkedHashMap<>(materialPartTrees.size());

        materialPartTrees.forEach(e -> map.put(e.getId(), e));

        map.forEach((key, currentObj) -> {
            //判断对象不为空
            if (ObjectUtil.isNotNull(currentObj)) {
                //从map中获取对应的父节点
                RespMaterialPartTreeDTO parentObj = map.get(currentObj.getParentId());
                //如果存在父节点
                if (ObjectUtil.isNotNull(parentObj)) {
                    //获取子节点
                    List<RespMaterialPartTreeDTO> children = parentObj.getChildren();

                    //如果子节点为空，就说明已经没有子节点了，就创建一个新的集合返回
                    if (CollectionUtil.isEmpty(children)) {
                        children = new LinkedList<>();
                        parentObj.setChildren(children);
                    }
                    //将当前节点添加到父节点的子节点中
                    children.add(currentObj);
                } else {
                    //如果不存在父节点，就说明是根节点，就将当前节点添加到树中
                    partTree.add(currentObj);
                }
            }
        });

        return partTree;
    }
 ```