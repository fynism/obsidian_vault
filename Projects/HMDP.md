# 相关的知识




# 基于Session 的短信验证码
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314154742195.png)

# 验证码相关

## 发送短信验证码
从上面的流程图中可以看出，发送短信验证码主要有这几个步骤：
1. 校验手机号是否符合格式。
2. 生成随机验证码。
3. 保存验证码到 Session。(`session. setAttribute ("code", code)`)
4. 发送验证码。暂时先不做真正的发送了，使用控制台输出来模拟一下。

由前端的请求可知，在点击“发送验证码”按钮后传输的 URL 格式为：



