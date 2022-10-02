Mybatis实现前台显示脱敏数据

> 数据库查询出数据后，Mybatis就会拦截得到的数据判断是否含有脱敏注解，实现脱敏

```java
import java.util.function.Function;

public interface Desensitizer extends Function<String,String> {
}

```

```java
public enum ShieldingRules {

    /**
     * 中文名(李斯 -> 李*)
     */
    CHINESE_NAME(s->s.replaceAll("(?<=.{1}).", "*")),

    /**
     * 身份证号
     */
    ID_CARD(s->s.replaceAll("(?<=\\w{3})\\w(?=\\w{4})", "*")),

    /**
     * 手机号
     */
    MOBILE_PHONE(s->s.replaceAll("(?<=\\w{3})\\w(?=\\w{4})", "*")),


    /**
     * 电子邮件
     */
    EMAIL(s->s.replaceAll("(\\w+)\\w{3}@(\\w+)", "$1***@$2"));

    private  final Desensitizer desensitizer;

    ShieldingRules(Desensitizer desensitizer) {
        this.desensitizer = desensitizer;
    }
    public Desensitizer getDesensitizer() {
        return desensitizer;
    }

}
```

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Tuomin {

    ShieldingRules tuomin();
}
```

```java
import lombok.extern.slf4j.Slf4j;
import org.apache.ibatis.executor.resultset.ResultSetHandler;
import org.apache.ibatis.plugin.Interceptor;
import org.apache.ibatis.plugin.Intercepts;
import org.apache.ibatis.plugin.Invocation;
import org.apache.ibatis.plugin.Signature;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.reflection.SystemMetaObject;
import org.springframework.stereotype.Component;

import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

/**
 * 数据脱敏Mybatis拦截器
 *
 * @author zhx
 */
@Component
@Slf4j
@Intercepts({
        @Signature(type = ResultSetHandler.class, method = "handleResultSets", args = {java.sql.Statement.class})
})
public class TuominInterceptor implements Interceptor {
    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        List<Object> proceed = (List<Object>) invocation.proceed();
        proceed.forEach(this::tuomin);
        return proceed;
    }

    /**
     * 获取实体类的所有字段包括父类
     * @param clazz
     * @return
     */
    private static List<Field> getAllField(Class<?> clazz){
        List<Field> fields = new ArrayList<>();
        while (clazz!=null){
            fields.addAll(new ArrayList<>(Arrays.asList(clazz.getDeclaredFields())));
            clazz = clazz.getSuperclass();
        }
        return fields;
    }
    private void tuomin(Object o) {
        Class<?> aClass = o.getClass();
        //Todo 配置化
        if (aClass.getName().equals("com.che300.module.system.user.pojo.SysUserExtend")){
            List<Field> allField = getAllField(aClass);
            Field[] array = allField.toArray(new Field[0]);
            MetaObject metaObject = SystemMetaObject.forObject(o);
            Stream.of(array).filter(field -> field.isAnnotationPresent(Tuomin.class)).forEach(field -> doTuomin(metaObject, field));

        }
    }

    /**
     * 更加枚举规则脱敏
     * @param metaObject
     * @param field
     */
    private void doTuomin(MetaObject metaObject, Field field) {
        String name = field.getName();
        Object value = metaObject.getValue(name);
        if (String.class == metaObject.getGetterType(name) && value != null) {
            Tuomin annotation = field.getAnnotation(Tuomin.class);
            ShieldingRules tuomin = annotation.tuomin();
            String apply = tuomin.getDesensitizer().apply((String) value);
            metaObject.setValue(name, apply);

        }
    }
}
```

在对应需要脱敏的字段加入@Tuomin(tuomin = ShieldingRules.ID_CARD)