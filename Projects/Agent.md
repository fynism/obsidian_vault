# 小知识
在 py 中, 字典/列表/字符串都能够转换为标准的 JSON 格式, 需要使用 json 库.
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260414162153288.png)

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

3. **使用返回值**
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260414151952971.png)
`Response` 类型的结构如上图. 是一个**类 JSON** 的结构. 那么, 此时就可以使用 `print(response.choices[0].message.content)` 来获取到模型生成的内容.

## 使用流式输出
```python
# 1. 获取client对象  
client = OpenAI()
...
# 2. 调用模型  
response = client.chat.completions.create(  
    model="deepseek-chat",  
    messages=[  
        {"role": "system", "content": "你是一个Python编程传家，并且话很多"},  
        {"role": "assistant", "content": "好的，我是变成专家，并且话很多，你要问什么？"},  
        {"role": "user", "content": "输出1-10的数字，使用pythn代码"},  
    ],  
    stream=True  
)  
  
# 3.处理结果  
for chunk in response:  
    print(chunk.choices[0].delta.content,  
          end="",  # 每一行之间以空格分隔  
          flush=True   #立刻刷新缓冲区  
          )
```

关键代码是调用模型部分中的 `stream = True`, 能够实现**流式输出**, 也就是现在大部分的 AI Chat 中"一个字一个字" 说话的感觉. 
如果没有流式输出的话, 只能够等模型输出完全部的信息, 再一下子全部打印出来.

## 调用模型历史消息
```python

# 1. 获取client对象  
client = OpenAI(  
    base_url="https://api.deepseek.com/v1",  
)  
  
# 2. 调用模型  
response = client.chat.completions.create(  
    model="deepseek-chat",  
    messages=[  
        {"role": "system", "content": "你是AI助理，回答很简洁"},  
        {"role": "user", "content": "小明有2条宠物狗"},  
        {"role": "assistant", "content": "好的"},  
        {"role": "user", "content": "小红有3只宠物猫"},  
        {"role": "assistant", "content": "好的"},  
        {"role": "user", "content": "总共有几个宠物？"},  
    ],  
    stream=True  
)  
  
# 3.处理结果  
for chunk in response:  
    print(chunk.choices[0].delta.content,  
          end="",  # 每一行之间以空格分隔  
          flush=True   #立刻刷新缓冲区  
          )
```

在调用模型传递参数的时候, 可以在 messages 里面传入多条历史对话信息 ,模型依据传入的上下文能够进行更准确的判断.