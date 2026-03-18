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

查看 `UserController`,  


