# MyBatis DAO层只写接口没有写实现类，如何实现动态代理机制

若编写接口的实现类，那么DAO层的实现类是进行sql操作数据库，而mapper.xml中编写的也是这种SQL操作。有理由相信，mapper.xml代替了接口实现类的作用。

是动态代理，动态代理接口的所有方法，每次接口被调用，就会进入动态代理对象的invoke方法，然后加载xml中的sql完成操作数据库，再返回结果。

第一步：通过反射机制给接口生成对象
第二步：动态代理反射对象，这样接口被调用，就会触发动态代理

看动态代理的最大的好处就是接口与其实现类的解耦。
原本接口和动态类之间是强关联状态,接口不能实例化,实现类必须实现接口的所有方法,有了动态代理之后,接口与实现类的关系并不是很大,甚至不需要实现类就可以完成调用,比如Mybatis这种形式,其并没有创建该接口的实现类,而是用一个方法拦截器转向到自己的通用处理逻辑。

MapperProxy
该类是Mapper接口的Proxy角色,继承了InvocationHandler,所以具有方法拦截功能,看代码注释。

```java
@Override 
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable { 
try { 
    if (Object.class.equals(method.getDeclaringClass())) { 判断是否为object,因为其不是接口 
        return method.invoke(this, args); 
} else if (isDefaultMethod(method)) { //判断是否为接口总的默认方法,jdk8允许接口中声明默认方法. 
    return invokeDefaultMethod(proxy, method, args); 
} 
} catch (Throwable t) { 
    throw ExceptionUtil.unwrapThrowable(t); 
} 
//对正常Mapper请求的处理 
final MapperMethod mapperMethod = cachedMapperMethod(method); 
return mapperMethod.execute(sqlSession, args); 
} 
```

对于正常的Mapper接口中的方法调用,mybatis都会转向到MapperMethod的execute方法中执行,拿到结果返回给调用方Client,整个代理过程结束.对于正常调用是有缓存的,并且该代理类是项目启动时就生成好的,对于性能影响并不是很大实用性还是很高的。

MapperMethod如何对Mapper方法拦截的？
现在我们定位到，最终的拦截代码位于MapperMethod类的execute方法中，当把这个方法的代码分析完成，本文的内容也就分析完成了。

从MapperMethod的构造方法开始看起：

org.apache.ibatis.binding.MapperMethod

```java
public class MapperMethod {  
    private final SqlCommand command;  
    private final MethodSignature method;  
    public MapperMethod(Class<?> mapperInterface, Method method, Configuration config) {    
        this.command = new SqlCommand(config, mapperInterface, method);    
        this.method = new MethodSignature(config, mapperInterface, method);  
        } 
    ...
}
```

在MapperMethod的构造方法中，给SqlCommand、MethodSignature两个类型的成员变量进行了赋值，这两个类都是MapperMethod的内部类。

这里不对SqlCommand源码继续展开分析，主要关注在构造SqlCommand对象的时候，传入了3个参数：

mapperInterface：Mapper映射器接口的class对象
method：当前调用的Mapper映射器接口的方法对象
config：表示mybatis配置解析后的对象(前面我们已经看到过)
通过这3个参数，SqlCommand可以为我们提供以下信息：

1 唯一定位当前被调用的Mapper接口的方法，对应的要执行的sql

这个很容易做到，有了mapperInterface，以及method。就可以通过以下方式，来拼接出namespace.id

String statementId = mapperInterface.getName() + "." + methodName;
SqlCommand提供了一个getName方法，返回这个namespace.id。这也是为什么，要求Mapper映射接口，要与xml映射文件namespace属性值相同，方法名与<insert>、<select>等xml元素的id属性值相同的原因。

2 确定要执行的sql的类型

如INSERT、UPDATE、DELETE、SELECT等。因为底层还是通过SqlSession来执行，因此必须知道要执行的sql的类型，选择调用SqlSession的不同方法，如insert、delete、update、selectOne、selectList等。

在第一步确定了要执行的sql的statementId之后，我们可以通过Configuration类来获得这个statementId对应的MappedStatement对象。mybatis在解析xml的过程中，会将<insert>、<select>等xml元素都封装成一个MappedStatement对象，其提供了一个getSqlCommandType()方法，表示这个sql的类型。这个逻辑可以用以下简化后的代码来表示：

String statementId = mapperInterface.getName() + "." + method.getName();MappedStatement ms = configuration.getMappedStatement(statementName);SqlCommandType type= ms.getSqlCommandType();
有了这两个信息之后，我们来看MapperMethod的execute方法是如何执行的？

MapperMethod#execute

```java
 public Object execute(SqlSession sqlSession, Object[] args) {    
    Object result;    
    //根据SqlCommand的不同类型，调用sqlSession不同的方法     
    //1、执行sqlSession.insert    
    if (SqlCommandType.INSERT == command.getType()) {      
         Object param = method.convertArgsToSqlCommandParam(args);      
         result = rowCountResult(sqlSession.insert(command.getName(), param));    
         //2、执行sqlSession.update    
    } else if (SqlCommandType.UPDATE == command.getType()) {      
        Object param = method.convertArgsToSqlCommandParam(args);      
        result = rowCountResult(sqlSession.update(command.getName(), param));    
        //3、执行sqlSession.delete    } 
    else if (SqlCommandType.DELETE == command.getType()) {      
        Object param = method.convertArgsToSqlCommandParam(args);      
        result = rowCountResult(sqlSession.delete(command.getName(), param));    
        //4、对于select，根据Mapper接口方法的返回值类型，选择调用SqlSession的不同方法    
    } else if (SqlCommandType.SELECT == command.getType()) {      
        if (method.returnsVoid() && method.hasResultHandler()) {       
            executeWithResultHandler(sqlSession, args);        
            result = null;      
            //4.1 如果方法的返回值是一个集合，调用selectList方法      
        } else if (method.returnsMany()) {        
            result = executeForMany(sqlSession, args);      
            //4.2 如果方法的返回值是一个Map，调用selectMap方法      
        } else if (method.returnsMap()) {        
            result = executeForMap(sqlSession, args);      
            //4.3 如果方法的返回值是Cursor，调用selectCursor方法      
        } else if (method.returnsCursor()) {        
            result = executeForCursor(sqlSession, args);      
            //4.4 否则调用sqlSession.selectOne方法       
        } else {        
            Object param = method.convertArgsToSqlCommandParam(args);        
            result = sqlSession.selectOne(command.getName(), param);     
        }    
    } else if (SqlCommandType.FLUSH == command.getType()) {        
        result = sqlSession.flushStatements();    
    } else {      
        throw new BindingException("Unknown execution method for: " + command.getName());    
    }    
    if (result == null && method.getReturnType().isPrimitive() && !method.returnsVoid()) {      
        throw new BindingException("Mapper method '" + command.getName()          
         + " attempted to return null from a method with a primitive return type (" + method.getReturnType() + ").");   
        }    
    return result;  
}
 ```

至此，我们已经基本上从源码层面已经深入的分析了Mybatis的Mapper映射接口的内部工作原理，简单总结就是一句话：通过JDK动态代理，根据映射器接口+当前要执行的方法，确定要执行的sql，对sql的类型进行处理，最后还是委派给SqlSession来完成。