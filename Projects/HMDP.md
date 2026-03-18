# 相关的知识




# 基于Session 的短信验证码
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314154742195.png)

# 登录验证功能

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
    //  发送短信验证码并保存验证码  
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

几个注意点:
- 在写业务逻辑的时候, 注意 Controller 层专注实现**请求响应** , 而 Service 层专注实现**业务逻辑**.
- 善用 `hutool` 这样的**工具类 jar 包** (示例代码中**生成验证码**部分的 `RandomUtil` 就是这个包中的工具类), 能省很多事.
- 在 log 部分动态打印日志使用的 `{}` 占位符需要加入 `@Slf4j` 注解.
- 在 session 中保存信息使用 `session.setAttribute("key", value)` , 取出值使用 `session.getAttribute("key", value)`


