# 常用注解

## @Bean
此注解是一个对于**方法**的注解。带有此注解的方法，其返回值会被放入 Spring 容器中以供调用。

*示例代码*
向 Spring 容器中添加了一个名为 `"fengye"` 的 Person 类.
```java
@Bean("fengye")  
public static Person person(){  
    Person person = new Person();  
    person.setId(1);  
    person.setName("fengye");  
    person.setSex("male");  
    person.setAge(18);  
    return person;  
}
```

该怎么使用呢？


