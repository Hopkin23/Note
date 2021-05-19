# Long类型太长，而Java序列化JSON丢失精度问题的方法

参考：https://www.cnblogs.com/zimug/p/13557662.html

Java序列化JSON时long型数值,会出现精度丢失的问题。 原因： java中得long能表示的范围比js中number大,也就意味着部分数值在js中存不下(变成不准确的值). 

解决办法一： 使用ToStringSerializer的注解，让系统序列化 时，保留相关精度

```Java
@JsonSerialize(using=ToStringSerializer.class)
private Long createdBy;
```

上述方法需要在每个对象都配上该注解，此方法过于繁锁。

解决办法(二）： 使用全局配置，将转换时实现自动ToStringSerializer序列化

```java
@Configuration
public class JacksonConfig {

  @Bean
  @Primary
  @ConditionalOnMissingBean(ObjectMapper.class)
  public ObjectMapper jacksonObjectMapper(Jackson2ObjectMapperBuilder builder)
  {
    ObjectMapper objectMapper = builder.createXmlMapper(false).build();

    // 全局配置序列化返回 JSON 处理
    SimpleModule simpleModule = new SimpleModule();
    //JSON Long ==> String
    simpleModule.addSerializer(Long.class, ToStringSerializer.instance);
    objectMapper.registerModule(simpleModule);
    return objectMapper;
  }
}
```