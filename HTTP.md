
# HTTP 请求与响应
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314183214954.png)
来看一个*实例*吧。以现在在做的黑马点评的验证码那一部分为例子。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314184257890.png)
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260314184552552.png)
- **请求首行**主要包括：请求方式 (`POST`) 、请求路径、协议版本。
- 下面的都是**请求头** request headers 了。主要都是**键: 值**的形式
- 第二张图片中的就是**请求体**，一般 POST 请求传输的大量 JSON 类型文件会通过这里传输。