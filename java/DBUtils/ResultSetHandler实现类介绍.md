# ResultSetHandler实现类介绍

备注：DbUtils给我们提供了10个ResultSetHandler实现类，分别是：

- ① ArrayHandler：将查询结果的第一行数据，保存到Object数组中
- ② ArrayListHandler：将查询的结果，每一行先封装到Object数组中，然后将数据存入List集合
- ③ BeanHandler：将查询结果的第一行数据，封装到user对象
- ④ BeanListHandler：将查询结果的每一行封装到user对象，然后再存入List集合
- ⑤ ColumnListHandler：将查询结果的指定列的数据封装到List集合中
- ⑥ MapHandler：将查询结果的第一行数据封装到map结合（key=列名，value=列值）
- ⑦ MapListHandler：将查询结果的每一行封装到map集合（key=列名，value=列值），再将map集合存入List集合
- ⑧ BeanMapHandler：将查询结果的每一行数据，封装到User对象，再存入mao集合中（key=列名，value=列值）
- ⑨ KeyedHandler：将查询的结果的每一行数据，封装到map1（key=列名，value=列值 ），然后将map1集合（有多个）存入map2集合（只有一个）
- ⑩ ScalarHandler：封装类似count、avg、max、min、sum......函数的执行结果