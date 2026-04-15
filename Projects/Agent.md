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

# LangChain 框架

## API 调用
### 调用 LLM
因为我使用的是 DeepseekAPI , 它兼容 openai 的规范, 所以可以直接导入 `langchain_openai` 包.
LangChain 中封装了许多大模型相关的方法, 比如下面代码中的 `Stream()` 方法 ,能够实现流式输出.


```Python
from langchain_openai import ChatOpenAI  
import os  
  
# 初始化 DeepSeek 模型  
# 注意：deepseek-chat 对应的是 DeepSeek-V3# 如果你想用 R1，模型名称请改为 deepseek-reasoner
model = ChatOpenAI(  
    model='deepseek-chat',  
    openai_api_key=os.getenv("DEEPSEEK_API_KEY"), 
    openai_api_base='https://api.deepseek.com',  
    max_tokens=1024  
)  
  
# 调用模型  
  
res = model.stream(input="你是哪一款模型")  
  
for chunk in res:  
    print(chunk.content, end="", flush=True)
```

### 调用聊天模型
其中有三种 message 类型, 和前文提到的 OpenAI 库对应.
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260414173258866.png)

*代码示例*
```Python
from langchain_core.messages import HumanMessage,AIMessage,SystemMessage  
from langchain_openai import ChatOpenAI  
import os  
  
# 初始化 DeepSeek 模型  
# 注意：deepseek-chat 对应的是 DeepSeek-V3# 如果你想用 R1，模型名称请改为 deepseek-reasonermodel = ChatOpenAI(  
    model='deepseek-chat',  
    openai_api_key=os.getenv("DEEPSEEK_API_KEY"),  
    openai_api_base='https://api.deepseek.com',  
    max_tokens=1024  
)  
  
messages = [  
    SystemMessage(content="你是一个边塞诗人"),  
    HumanMessage(content="写一首唐诗"),  
    AIMessage(content="锄禾日当午，汗滴禾下土，谁知盘中餐，粒粒皆辛苦"),  
    HumanMessage(content="按照上一个回复的格式，写一首唐诗。"),  
]  
  
res = model.stream(messages)  
  
for chunk in res:  
    print(chunk.content,end="",flush=True)
```

#### `Messages` 的简写形式
中间 `messages` 的部分可以**简写**：
```Python
messages = [  
    ("system","你是一个边塞诗人"),  
    ("human","写一首唐诗"),  
    ("ai","锄禾日当午，汗滴禾下土，谁知盘中餐，粒粒皆辛苦"),  
    ("human","按照上一个回复的格式，写一首唐诗。")  
]
```

那么，简写有什么**好处**呢？
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260414193122776.png)

### 文本嵌入模型 (Embedding Models)

```python
# 使用阿里云dashscope  
from langchain_community.embeddings import DashScopeEmbeddings  
  
model = DashScopeEmbeddings()  
  
print(model.embed_query("我喜欢你"))  
print(model.embed_documents(["我喜欢你",'我稀饭你','晚上吃啥']))
```

### LangChain API 小结
以 TongYi 模型为例，`LangChain` 提供了这些标准 API.
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260414200131416.png)

***
## Prompt 模板
使用 `promptTemplate` 类来模板化提示词。

### 通用提示词模板
也是一种 `ZeroShotPrompt`.
*代码示例*
```Python
from langchain_community.llms.tongyi import Tongyi  
from langchain_core.prompts import PromptTemplate  
  
prompt_template = PromptTemplate.from_template(  
    "我的邻居姓：{lastname},刚生了{gender},你帮我起个名字，简单回答"  
)  
  
#prompt_text = prompt_template.format_prompt(lastname='张',gender="女儿")  
  
model = Tongyi(model="qwen-max");  
  
# res = model.invoke(input=prompt_text)  
# print(res)  
  
# 创建一个链，将提示词模板和模型连接起来  
chain =prompt_template | model  
res = chain.invoke(input={"lastname":'张',"gender":"女儿"})  
print(res)
```

其中，第 `195` 行的这一句里面，`|` 是**管道符**，用来讲提示词模板和模型链接起来，很像 linux 中的管道符，将前一个语句的结果传入下一个语句作输出。

`196` 行这里**实际上的执行顺序**是: 先把 `input` 字典里面的内容输入 ` prompt_template.invoke()` 方法, 然后将这个语句的结果顺着管道符传给 ` model.invoke() ` 方法, 最终得到输出结果.

### FewShot 提示词模板
这是 `FewShot` 模式的提示词模板，主要格式如下图所示。它能够提供多个例子给模型，供其参考。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260415143813501.png)

*示例代码*
```Python
from langchain_core.prompts import PromptTemplate, FewShotPromptTemplate  
from langchain_community.llms.tongyi import Tongyi  
  
example_template = PromptTemplate(  
    input_variables=["word", "antonym"],  
    template="单词：{word},反义词：{antonym}"  
)  
  
examples_data = [  
    {  
        "word": "大",  
        "antonym": "小"  
    },  
    {  
        "word": "上",  
        "antonym": "下"  
    }  
]  
  
few_shot_template = FewShotPromptTemplate(  
    # 示例数据的模板  
    example_prompt=example_template,  

    # 示例的数据（用来注入动态数据的），list内套字典  
    examples=examples_data,  
  
    # 示例之前的提示词  
    prefix="告知我单词的反义词，我提供如下的示例",  
  
    # 示例之后的提示词  
    suffix="基于前面的示例告知我，{input_word}的反义词是？",  
  
    # 声明再前缀或后缀中需要注入的变量名  
    input_variables=['input_word']  
)  
  
prompt_text = few_shot_template.invoke(input={"input_word":"左"}).to_string()  
print(prompt_text)  
  

model = Tongyi(model="qwen-max")  
print(model.invoke(input=prompt_text))
```

### `ChatPromptTemplate` 模板
### 模版类两个方法的介绍
`PromptTemplate` , `FewShotPromptTemplate` , `ChatPromptTemplate` 都继承于一个模版提示词的基类 `BasePromptTemplate`. 在这个类中, 实现了 `format` 方法.
而 invoke 方法是顶级父类 `Runnable` 中的方法.

![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260415145412915.png)
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260415145837489.png)