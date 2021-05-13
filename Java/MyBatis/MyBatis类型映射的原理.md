自定义TypeHandler

SQL与Mapper接口的绑定关系是如何建立的？
这个过程在mybatis初始化阶段，解析xml配置文件的时候就确定了。具体逻辑是，当解析一个xml配置文件时，会尝试根据<mapper namespace="....">的namespace属性值，判断classpath下有没有这样一个接口的全路径与namespace属性值完全相同，如果有，则建立二者之间的映射关系。

关解析代码位于XMLMapperBuilder的 parse方法中：

XMLMapperBuilder#parse
```java
public void parse() {  if (!configuration.isResourceLoaded(resource)) {    configurationElement(parser.evalNode("/mapper"));    configuration.addLoadedResource(resource);   //根据namespace属性值，尝试绑定对应的Mapper接口    bindMapperForNamespace();  }  ...}
```
从bindMapperForNamespace方法名，既可以看出来，其作用正是将Mapper映射器接口绑定到某个xml文件的namespace属性值。具体逻辑如下：

XMLMapperBuilder#bindMapperForNamespace
```java
private void bindMapperForNamespace() {  
    //1 获得mapper元素的namespace属性值   
    String namespace = builderAssistant.getCurrentNamespace();  
    if (namespace != null) {    
        Class<?> boundType = null;    
        try {      
            //2、通过反射，尝试以namespace属性值为全路径，加载对应Mapper接口的Class对象      
            boundType = Resources.classForName(namespace);    } catch (ClassNotFoundException e) {      
                //3、如果没有对应的Mapper接口，将会抛出ClassNotFoundException      
                // 意味着没有对应的Mapper接口，不需要绑定    }    
                if (boundType != null) {      
                    if (!configuration.hasMapper(boundType)) {        
                        configuration.addLoadedResource("namespace:" + namespace);        
                        //4、如果存在这个Mapper，将其添加到Configuration类中        configuration.addMapper(boundType);      
                        }    
                }  
            }
    }
}
```
从上述源码的第4步中，调用了Configuration的addMapper方法，来维护需要生成动态代理类的Mapper接口。此外，Configuration还提供了一个getMapper方法，这个方法返回的就是Mapper接口的JDK动态代理类。 相关源码如下所示：

org.apache.ibatis.session.Configuration
```java
public class Configuration {
    ...
    protected MapperRegistry mapperRegistry = new MapperRegistry(this);
    ...
    public <T> void addMapper(Class<T> type) {  mapperRegistry.addMapper(type);}
    public <T> T getMapper(Class<T> type, SqlSession sqlSession) {  return mapperRegistry.getMapper(type, sqlSession);}
    ...
}
```

可以看到，Configuration类实际上将addMapper和getMapper委派给了MapperRegistry来执行：

addMapper方法会针对这个Mapper接口生成一个MapperProxyFactory工厂类。
getMapper方法，会通MapperProxyFactory工厂类，返回一个Mapper接口的动态代理类。
相关源码如下：

MapperRegistry#addMapper
```java
private final Map<Class<?>, MapperProxyFactory<?>> knownMappers = new HashMap();public <T> void addMapper(Class<T> type) {  
    //1 判断传入的type是否是一个接口，如果不是，则忽略。意味着Mapper必须是接口类型。  
    if (type.isInterface()) {    
        //2、判断Mapper之前是否已经注册过，如果注册过就抛出异常    
        if (hasMapper(type)) {      
            throw new BindingException("Type " + type + " is already known to the MapperRegistry.");    
        }    
        boolean loadCompleted = false;    
        try {      
            //3、针对Mapper接口的Class对象，生成一个MapperProxyFactoy工厂类，用于之后为这个Mapper接口生成动态代理类      
            //同时，将Class和MapperProxyFactoy的映射关系放入一个HashMap中，之后根据Class，就可以找到对应的工厂类      
            knownMappers.put(type, new MapperProxyFactory<T>(type));      
            //4、解析Mapper映射器接口方法上的注解，如@Select、@Insert等，并进行注册，这里不赘述      
            MapperAnnotationBuilder parser = new MapperAnnotationBuilder(config, type);      
            parser.parse();      
            loadCompleted = true;    
        } finally {      
            if (!loadCompleted) {        
                knownMappers.remove(type);      
            }    
        }  
    }
}
```

MapperRegistry还提供了一个getMapper方法，用于根据指定Mapper接口，返回其动态代理类。如下：

MapperRegistry#getMapper
```java
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {    
    //1、首先根据type参数，找到对应的MapperProxyFactory工厂类    
    final MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory<T>) knownMappers.get(type);    
    if (mapperProxyFactory == null) {      
        throw new BindingException("Type " + type + " is not known to the MapperRegistry.");    
    }    
    try {      
        //2、通过MapperProxyFactory工厂类，创建这个Mapper接口动态代理      
        return mapperProxyFactory.newInstance(sqlSession);    
    } catch (Exception e) {      
        throw new BindingException("Error getting mapper instance. Cause: " + e, e);    
    }  
}
```

注意第2步，在通过MapperProxyFactory创建代理类的时候，把SqlSession当做了参数。这是因为，动态代理类的内部实际上还是需要通过SqlSession来进行增删改查。