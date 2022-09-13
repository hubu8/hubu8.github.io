# 快速过滤器转QFilter


<!--more-->

## 前端传回后端的数据结构

![img](/kd/632081d5e8477c0001c8d7ac)

- text:无实际用途，为第一个搜索值，没有包含全部，所以搜索条件大多不用，除非搜索条件固定且单一
- searchFields:搜索框中键入的搜索条件
- fieldNames:前后端预设的搜索key,即前后端约定的搜索条件
- source:搜索控件的元数据，与本博客无关

## searchFields

​	它本身是一个ArrayList，每一个元素是一个LinkedHashMap，LinkedHashMap中包含两个键值对，第一个键值对为搜索字段映射（字段之间为或关系），第二个键值对为搜索值映射（值之间为匹配或关系），多个LinkedHashMap之间为and关系。

​	fieldNames:前后端约定的搜索字段，

## 数据关系转Filter

```java
/**
     * @param fastFilter            快速过滤条件
     * @param fastFilterRelationMap 关键词标识与单据字段映射关系
     * @return
     */
    protected List<QFilter> buildFastFilters(FastFilter fastFilter, Map<String, String> fastFilterRelationMap) {
        List<QFilter> qFilters = new ArrayList<>();
        if (fastFilter == null) {
            return qFilters;
        }
        List<Map<String, List<Object>>> fastFilterMap = fastFilter.getFastFilter();
        for (Map<String, List<Object>> searchKeyMap : fastFilterMap) {
            //关键词对应标识
            List<Object> fieldNameList = searchKeyMap.get("FieldName");
            //关键词值
            List<Object> valueList = searchKeyMap.get("Value");

            fieldNameList.stream().map(fieldName -> {
                String filterKey = fastFilterRelationMap.get(fieldName);
                return valueList.stream().map(v -> new QFilter(filterKey, QFilter.like, "%" + v + "%"))
                        .reduce((a, b) -> a.or(b)).orElse(null);
            }).filter(filter -> Optional.ofNullable(filter).isPresent())
                    .reduce((a, b) -> a.or(b))
                    .ifPresent(qFilters::add);
        }
        return qFilters;
    }
```


