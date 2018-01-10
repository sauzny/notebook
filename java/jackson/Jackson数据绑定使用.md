# Jackson数据绑定使用

## 一、基础依赖包

包 | 说明
---|---
jackson-core-{version}.jar | 提供基于"流模式"解析的相关 API，它包括 JsonPaser 和 JsonGenerator。 Jackson 内部实现正是通过高性能的流模式 API 的 JsonGenerator 和 JsonParser 来生成和解析 json。
jackson-annotations-{version}.jar | 注解包，提供标准注解功能
jackson-databind-{version}.jar | 数据绑定包， 提供基于"对象绑定" 解析的相关 API （ ObjectMapper ） 和"树模型" 解析的相关 API （JsonNode）；基于"对象绑定" 解析的 API 和"树模型"解析的 API 依赖基于"流模式"解析的 API

## 二、基础用法举例

Jackson 最常用的 API 就是基于"对象绑定" 的 ObjectMapper。

### 2.1 ObjectMapper 读写方法：

- 通过 writeValue 系列方法将java对象序列化为json，并将json存 储成不同的格式，String（writeValueAsString），Byte Array（writeValueAsString），Writer， File，OutStream 和 DataOutput。
- 通过 readValue 系列方法从不同的数据源像 String ， Byte Array， Reader，File，URL， InputStream 将 json 反序列化为 java 对象。

### 2.2 ObjectMapper 属性配置

在调用 writeValue 或调用 readValue 方法之前，可以设置 ObjectMapper 的相关配置信息。这些配置信息应用 java 对象的所有属性上。示例如下：

### 2.3 简单示例

```java

ObjectMapper mapper = new ObjectMapper(); 

//在反序列化时忽略在 json 中存在但 Java 对象不存在的属性 
mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);

//在序列化时日期格式默认为 yyyy-MM-dd'T'HH:mm:ss.SSSZ 
mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);

//在序列化时忽略值为 null 的属性 
mapper.setSerializationInclusion(Include.NON_NULL); 

//忽略值为默认值的属性 
mapper.setDefaultPropertyInclusion(Include.NON_DEFAULT);

// 构建一个java对象
Person person = new Person(); 
person.setName("Tom"); 
person.setAge(40);

// 序列化
String jsonString = mapper.writerWithDefaultPrettyPrinter().writeValueAsString(person); 

// 反序列化
Person deserializedPerson = mapper.readValue(jsonString, Person.class);
```
### 2.4 Jackson的常用注解

**@JsonProperty**

用于属性，把属性的名称序列化时转换为另外一个名称。

``` 
@JsonProperty("birth_ d ate")
private Date birthDate;
```

**@JsonFormat**

用于属性或者方法，把属性的格式序列化时转换成指定的格式。

```
@JsonFormat(timezone = "GMT+8", pattern = "yyyy-MM-dd HH:mm") 
public Date getBirthDate()
```

**@JsonPropertyOrder**

用于类， 指定属性在序列化时 json 中的顺序

```
@JsonPropertyOrder({ "birth_Date", "name" }) 
public class Person
```
**@JsonCreator**

用于构造方法，和 @JsonProperty 配合使用，适用有参数的构造方法。 

```
@JsonCreator 
public Person(@JsonProperty("name")String name) {…}
```

**@JsonAnySetter**

用于属性或者方法，设置未反序列化的属性名和值作为键值存储到 map 中 

```
@JsonAnySetter
public void set(String key, Object value) { map.put(key, value); }
```

**@JsonAnyGetter**

用于方法 ，获取所有未序列化的属性 

```
@JsonAnyGetter 
public Map<String, Object> any() { return map; }
```

## 三、Jackson 的高阶应用

### 3.1 日期格式处理

- 普通日期的类型

在getter方法上使用注解 ` @JsonFormat(pattern = "yyyy-MM-dd", timezone="GMT+8")`

- jdk 8 的日期类型

增加jar包：

```
<dependency> 
    <groupId>com.fasterxml.jackson.datatype</groupId> 
    <artifactId>jackson-datatype-jsr310</artifactId> 
    <version>2.9.1</version> 
</dependency>
```

增加代码：

```
// mapper.findAndRegisterModules(); 注册所有的modules

mapper.registerModule(new JavaTimeModule());

//对于 Jackson 2.5 以下版本，需要添加代码
//objectMapper.registerModule(new JSR310Module ())
```

- joda 的日期类型

增加jar包：

```
<dependency> 
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-joda</artifactId> 
    <version>2.9.1</version> 
</dependency>
```

增加代码：

```
mapper.registerModule(new JodaModule());
```


### 3.2 List Map 泛型反序列化

Array 和 Collection 的处理与 List，Map 相似，这里不再详述。

List 泛型使用示例

```java
CollectionType javaType = mapper.getTypeFactory().constructCollectionType(List.class, Person.class); 
List<Person> personList = mapper.readValue(jsonInString, javaType); 
List<Person> personList = mapper.readValue(jsonInString, new TypeReference<List<Person>>(){});
```

Map 泛型使用示例

```java
//第二参数是 map 的 key 的类型，第三参数是 map 的 value 的类型 
 MapType javaType = mapper.getTypeFactory().constructMapType(HashMap.class,String.class, Person.class); 
 Map<String, Person> personMap = mapper.readValue(jsonInString, javaType); 
 Map<String, Person> personMap = mapper.readValue(jsonInString, new TypeReference<Map<String, Person>>() {});
```

### 3.3 属性可视化

属性的 getter 方法用于序列化， 而 setter 方法用于反序列化。

修改默认属性可视化的举例：

```java
/*
 PropertyAccessor 支持的类型有 ALL,CREATOR,FIELD,GETTER,IS_GETTER,NONE,SETTER 
 Visibility 支持的类型有 ANY,DEFAULT,NON_PRIVATE,NONE,PROTECTED_AND_PUBLIC,PUBLIC_ONLY
 */
 
mapper.setVisibility(PropertyAccessor.FIELD, Visibility.ANY); 

public class Person { 
    public int age; 
    protected String name; 
} 

```

### 3.4 属性过滤

在将 Java 对象序列化为 json 时 ，有些属性需要过滤掉，不显示在 json 中 ， Jackson 有多种实现方法。

类注解
```java
@JsonIgnoreProperties(value = { "age","birth_date" }) 
public class Person

```

属性注解

```java
@JsonIgnore 
public int getAge() 
```

SimpleBeanPropertyFilter 方式

```
//设置 Filter 类或接口 
@JsonFilter("myFilter")
public interface MyFilter {}

// 设置 addMixIn
mapper.addMixIn(Person.class,MyFilter.class);
    
// 调用 SimpleBeanPropertyFilter 的 serializeAllExcept 方法
SimpleBeanPropertyFilter newFilter = SimpleBeanPropertyFilter.serializeAllExcept("age"); 
 
//或重写 SimpleBeanPropertyFilter 的 serializeAsField 方法 
SimpleBeanPropertyFilter newFilter = new SimpleBeanPropertyFilter() { 
    @Override 
    public void serializeAsField(Object pojo, JsonGenerator jgen, 
    SerializerProvider provider, PropertyWriter writer) 
    throws Exception { 
        if (!writer.getName().equals("age")) { 
            writer.serializeAsField(pojo, jgen, provider); 
        } 
    } 
}; 

//设置 FilterProvider 
FilterProvider filterProvider = new SimpleFilterProvider().addFilter("myFilter", newFilter); 
mapper.setFilterProvider(filterProvider).writeValueAsString(person);

```

### 3.5 属性过滤