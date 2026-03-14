# 常用注解

## 注册
这一块所记的注解，都是把某个类或者实例**注册(Register)** 到 IOC 容器中的。
### `@Bean`
此注解是一个对于**方法**的注解。带有此注解的方法，其**返回值**会被注册到 Spring 容器中以供调用。
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

### `@Component` 及其MVC 分层变种
可以使用 `@Component`, `@Controller`, `@Service` , `@Repository` 来对类进行注解，直接将这个**类**的一个实例放入容器中。

事实上，`@Controller`, `@Service` , `@Repository` 底层的实现**都是**通过 `@Component`，他们存在的意义只是让代码可读性更高。
(Gemini 说在后面的面相切面编程 AOP 里面还有其他的区别。)

*例如*
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260313171820910.png)
在典型的 MVC 分层中，就可以使用这样的注解。

###  `@Import` 
自己写的类，可以使用 `@Component` **或者**`@Configuration` 和 `@Bean` 来将其注册到容器。那么，**第三方库**中的类该怎么注册到容器呢？
没错，就是用 **`@Import(Xxx.class)`** 来注册。

*例如*
```java
@Configuration  
//注册CoreConstants类  
@Import(CoreConstants.class)  
//扫描包  
@ComponentScan(basePackages = "com.fengye.spring.ioc")  
public class AppConfig {  
}
```

***
## 依赖注入
这一部分的注解，能够实现**依赖注入**，将 IoC 容器中的 Beans **注入**到某个类中。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260313173726786.png)
### `@Autowired`
**自动装配**。底层的原理就是 Spring 调用容器的 `getBean()` 方法。

自动装配**只注入一个**组件时：
- 如果在容器中只找到**一个**相同类型的 Bean，那么**直接注入**。
- 如果容器中有**多个**相同类型的 Bean，那么按照**变量名**匹配；无相同变量名的即报错。

*例如*
```java
public class UserController {  
    @Autowired  
    UserService userService;  
}
```

`@Resource` 、`@Value`

# Spring MVC
是一个整合了Servelet API 的web 开发框架。

## 响应处理
主要涉及到 2 个注解：`@ResponseBody`、`@RequestMapping`




*示例*
```java
// @Controller 表明这是一个控制器  
@Controller  
public class UserController {  
  
    //@ResponseBody 表明该方法返回的数据直接写入 HTTP 响应正文中  
    @ResponseBody  
    //@RequestMapping("/path") 映射指定路径下的请求 ，把请求路径和方法进行绑定
    @RequestMapping("/user")  
    public String handle() {  
        return "handling requests";  
    }  
}
```
因为此项目之前在 `application.properties` 中配置过 8085 端口, 访问 `localhost:8085/user` 就能看到return的字符串了。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314171129828.png)





