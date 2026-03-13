# 常用注解

## `@Bean`
此注解是一个对于**方法**的注解。带有此注解的方法，其返回值会被放入 Spring 容器中以供调用。
常和 `@Configuration` 注解搭配使用。

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
获取容器中的 Bean 对象。
```java
//获取Bean对象  
//1. 通过bean的id获取  
Object fengye = run.getBean("fengye");  
System.out.println(fengye);  
  
//2.通过bean的类型获取  
Person fengye2 = run.getBean(Person.class);  
System.out.println(fengye2);

//3.若有多个相同类型的bean,那么可以使用getBeansOfType方法来获取Map  
Map<String, Person> fengyes = run.getBeansOfType(Person.class);  
System.out.println(fengyes);
```

**注意：**
- 如果在**使用类型获取** bean 的时候，容器中存在多个**相同类型**的 bean，那么那么 `getBean` 方法会抛出 `noUniqueBeanDefinition` 异常。
- Bean 不允许重名，尽管在编译时不会出现异常，但是运行时 Spring 框架会提示报错。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260313141652471.png)

`@ComponentScan `
`@Import`