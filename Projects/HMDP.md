# 相关的知识




# 基于Session 的登录
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314154742195.png)
## 发送短信验证码
从上面的流程图中可以看出，发送短信验证码主要有这几个步骤：
1. 校验手机号是否符合格式。
2. 生成随机验证码。
3. 保存验证码到 Session。(`session. setAttribute ("code", code)`)
4. 发送验证码。暂时先不做真正的发送了，使用控制台输出来模拟一下。

由**前端**的请求可知，在点击“发送验证码”按钮后传输的 URL 格式是这样的：
`http://localhost:8080/api/user/code?phone=13656617663`
也就是说，这一块的实现应该在 `/user` 接口下。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260318170913124.png)

查看 `UserController`, 和 `UserService`, 补全请求响应和业务逻辑.
*UserController. Java*
```java
/**  
 * 发送手机验证码  
 * @param phone  
 * @param session  
 * @return  
 */  
@PostMapping("/code")  
public Result sendCode(@RequestParam("phone") String phone, HttpSession session) {  
    //  调用userService中的业务逻辑,发送短信验证码并保存验证码  
    return userService.sendCode(phone, session);  
}
```

*UserService. Java*
```java
/**  
 * 发送验证码的业务实现  
 * @param phone  
 * @param session  
 * @return 成功请求  
 */  
@Override  
public Result sendCode(String phone, HttpSession session) {  
  
    //1.正则校验手机号，不符合返回错误信息  
    if (RegexUtils.isPhoneInvalid(phone)) {  
        return Result.fail("手机号格式错误!");  
    }  
  
    //2.生成验证码  
    String code = RandomUtil.randomNumbers(6);  
  
    //3.保存验证码到Session  
    session.setAttribute("VerificationCode",code);  
  
    //4.发送验证码  
    log.debug("发送成功,验证码:{}", code);  
  
    return Result.ok();  
}
```

几个**注意点**:
- 在写业务逻辑的时候, 注意 Controller 层专注实现**请求响应** , 而 Service 层专注实现**业务逻辑**.
- 善用 `hutool` 这样的**工具类 jar 包** (示例代码中**生成验证码**部分的 `RandomUtil` 就是这个包中的工具类), 能省很多事.
- 在 log 部分动态打印日志使用的 `{}` 占位符需要加入 `@Slf4j` 注解. 所以这个到底是什么?
- 在 session 中保存信息使用 `session.setAttribute("key", value)` , 取出值使用 `session.getAttribute("key", value)`

## 验证码注册登录
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260318171931730.png)

这一步主要是实现登录和注册的功能 .
从这里开始, 就要涉及到**数据库**的操作了. 需要了解 `Mybatis` 和 `Mybatis-plus` 相关的知识 .

依旧先厘清步骤：
1. 前端获取手机号和验证码，**验证码是否一致？** 若验证码和 Session 中保存的不一致则登录失败。
2. 根据手机号查询用户。
3. **用户是否存在？** 若不存在，则根据传入的手机号创建新用户，并且保存用户到数据库。
4. 保存用户到 session 。

首先依旧查看前端发的啥**请求** . 点击"登录"按钮之后, 出现了 `/login` 请求, 是 POST 类型的. **请求体**里面的数据如下图, 就是电话号和验证码:
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260318172211034.png)

然后实现 `UserController`, 和 `UserService`, 里面的东西。
首先，*UserController*
```java
/**  
 * 登录功能  
 * @param loginForm 登录参数，包含手机号、验证码；或者手机号、密码  
 */  
@PostMapping("/login")  
public Result login(@RequestBody LoginFormDTO loginForm, HttpSession session){  
    //  实现登录功能  
    return userService.login(loginForm,session);  
}
```

*UserService*
```java
/**  
 * 登录业务逻辑  
 * @param loginForm  
 * @param session  
 * @return ok状态和user数据  
 */  
@Override  
public Result login(LoginFormDTO loginForm, HttpSession session) {  
  
    String phone = loginForm.getPhone();  
    String code = loginForm.getCode();  
    Object cacheCode = session.getAttribute("VerificationCode");  
  
    //1.校验手机号和验证码  
    // 不一致：登录失败
    if (cacheCode == null || !code.equals(cacheCode.toString())) {  
        return Result.fail("验证码错误或已过期!");  
    }  
  
    //2.根据手机号查询用户  
    User user = query().eq("phone", phone).one();  
  
    //3.用户是否存在?  
    //  不存在:直接创建新用户,保存到数据库,再保存到Session  
    if (user == null){  
        user = createUserByPhone(phone);  
    }  
  
    //4.保存用户到Session  
    session.setAttribute("User" , BeanUtil.copyProperties(user, UserDTO.class));  
    return Result.ok(user);  
}
```

创建新用户的实现逻辑稍微有点复杂，将其抽象成一个方法：
```java
private User createUserByPhone(String phone){  
    //创建用户  
    User user = new User();  
    user.setPhone(phone);  
    user.setNickName(SystemConstants.USER_NICK_NAME_PREFIX + RandomUtil.randomString(10));  
  
    //存入数据库  
    save(user);  
    return user;  
}
```

**注意点**：
- Mybatis-plus 进行数据库的交互。比如： `User user = query().eq("phone", phone).one();` 
	这里，在 `User` 类前有个注解 `@TableName("tb_user")`；
	`UserService` 继承了 `ServiceImlp` ：
	`public class UserServiceImpl extends ServiceImpl<UserMapper, User>`
有上述两个条件，才能够使用 `query()` 进行查询。

- `BeanUtil.copyProperties(user, UserDTO.class)` 这一句，就是把 user 中的**属性值复制**到一个新的 userDTO 对象并返回。这里是为了隐藏用户的敏感信息，所以才用 userDTO。


# 改进：基于 Redis 的登录
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260321135233728.png)
