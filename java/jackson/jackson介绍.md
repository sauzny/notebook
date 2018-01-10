# jackson介绍


数据绑定是最常用的方式。

## 三种API

Jackson提供了三种可选的JSON处理方法（一种方式及其两个变型）：

- 流式 API：（也称为"增量分析/生成"） 读取和写入 JSON 内容作为离散事件。流式API读取和写入JSON内容离散事件。 JsonParser读取数据，而JsonGenerator写入数据。它是三者中最有效的方法，是最低开销和最快的读/写操作。它类似于XML的Stax解析器。

    - JsonGenerator - 写入JSON字符串。
    - JsonParser - 解析JSON字符串。


- 树模型 ：提供一个 JSON 文档可变内存树的表示形式。树模型类似于 XML DOM。

    ObjectMapper 生成树 ；树组成 JsonNode 节点集。

- 数据绑定： JSON和POJO相互转换，基于属性访问器规约或注解。有两种变体：

    - 简单数据绑定： 是指从Java Map、List、String、Numbers、Boolean和空值进行转换
    - 完整数据绑定 ：是指从任何 Java bean 类型 （及上文所述的"简单"类型） 进行转换
