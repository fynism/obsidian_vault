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
(Gemini 说在后面的面向切面编程 AOP 里面还有其他的区别。)

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
是一个整合了Servelet API 的web 开发框架。Web 开发实际上就是请求响应，而SpringMVC 封装了许多关于请求相应的功能。

## 请求响应
主要涉及到 2 个注解：`@ResponseBody`、`@RequestMapping`

*示例*
```java
// @Controller 表明这是一个控制器  
@Controller  
//或者直接使用@RestController 相当于 @ResponseBody + @Controller
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


**路径映射 `@RequestMapping("/path")`**
此注解把请求**路径**和**方法**进行了绑定，访问指定路径的时候执行被注解的方法。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314174934364.png)
- 其中，括号内的路径支持使用**通配符**匹配：
	- `*` ：匹配多个任意字符 (0 ~ n)。
	- `**` ：匹配多个任意层级目录。
	-  `?` ：匹配单个任意字符 (1 ~ n)。
	- 如果访问路径同时满足多个通配符，那么优先访问匹配**最精确**的。

- 可以进行**请求限定**。

### 接受 get 请求
如果需要接受 `get` 请求带有的参数，直接把参数写在 `RequestMapping` 所注解的方法的参数列表中。

*示例*
```java
/**  
 * * @param name  
 * @param age  
 * @return  
 *  
 * 用于响应带有参数的Get请求，直接把params写在方法的参数中。  
 */  
@RequestMapping("/handle01")  
public String handle01(String name, Integer age) {  
    return "you input : name:" + name + " age:" + age;  
}
```
使用ApiFox 测试结果ru
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260316154901902.png)

## 拦截器
SpringMVC 内置**拦截器**机制，允许在请求被目标方法处理的**前后**进行拦截，执行一些额外操作; 比如: **权限验证、日志记录、数据共享**等。

![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314194432822.png)

### 使用步骤
*实现案例*
1. 实现 `HandlerInterceptor` 接口，**重写**拦截器的三个处理方法：`preHandle`,`postHandle`,`afterCompletion`。
```java
//UserControllerInterceptor.java  
@Component  
public class UserControllerInterceptor implements HandlerInterceptor {  
  
    //在执行控制器方法之前执行  
    @Override  
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {  
        System.out.println("preHandler finished");  
        return true;  
    }  
  
    //在控制器方法执行之后，视图渲染之前执行  
    @Override  
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {  
        System.out.println("postHandler finished");  
    }  
  
    //在视图渲染之后执行  
    @Override  
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {  
        System.out.println("afterCompletionHandler finished");  
    }  
}
```

2. 实现 `WebMvcConfigurer` 接口，在SpringMVC 的**配置类**中写明拦截器的具体拦截路径。
```java
//SpringMvcConfig.java  
@Configuration  
@RequiredArgsConstructor  
public class SpringMvcConfig implements WebMvcConfigurer{  
  
    //配合@RequiredArgsConstructor，使用构造方法注入拦截器对象  
    private final UserControllerInterceptor userControllerInterceptor;  
  
    //重写addInterceptors方法,指定拦截路径  
    @Override  
    public void addInterceptors(InterceptorRegistry registry) {  
        registry.addInterceptor(userControllerInterceptor)  
                .addPathPatterns("/user");  
    }  
}
```

> [!notice] 在**每次**访问 `http://localhost:8085/user` 的时候都会触发拦截器的三个方法。

> [!tips] 在前后端分离项目中，`postHandle` 较少使用，因为此时响应通常即将被转化为 JSON 数据返回，不能够进行什么有价值的。
### 多拦截器执行顺序
如下图。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314195328680.png)