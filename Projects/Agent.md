# OpenAI 库的使用

<img src="https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260414152917025.png"/>

使用 OpenAI 库，一般分为 3 步：
1. **创建 client 对象**
 在这个步骤中，能够创建一个初始化的 client 对象，需要使用 `api_key` 和 `base_url`
   ![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260414153030558.png)
2. **调用模型**
这一部分是调用模型，使用 `client.chat.completions.create` 方法进行模型的调用, 返回值为 `response` 类型.
有 2 个主要参数: `model` 和 `messages`
- `model`: 模型类型
- `messages`: 字典, 有三个键
	- 第一个是 system 角色, 模型的初始设定
	- 第二个是 assistant 角色, 模型对用户的第一个提示语句
	- 第三个是 user 角色, 即为用户输入的 prompt
<img src="https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260414151715244.png"/>

3. 使用返回值
