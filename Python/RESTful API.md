# 请求基本概念

## 请求的基本实现
```python
from fastapi import FastAPI
app = FastAPI()
 
@app.get("/")
async def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id, "content": f"This is no {item_id} item"}
    
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="127.0.0.1", port=8000)
```

## Path 方法对参数进行约束

使用 `Path()` 方法 , 能够对参数进行一些范围上的约束。比如 `gt,ge,lt,le` 来约束**数字**的范围；`min_length, max_length` 来约束**字符串长度**的范围。

```python
@app.get("/")
async def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
async def read_item(item_id: int = Path(..., title="The ID of the item to get",description="书籍id,范围从0-100", ge=0, le=100)):
    return {"item_id": item_id, "content": f"This is No.{item_id} item"}

@app.get("/username/{username}")
async def read_user(username: str = Path(..., description="用户名，长度为3-50", min_length=3, max_length=50)):
    return {"username": username}

#...
```

## 查询参数
如果方法的形参没有出现在 URL 路径中，那么这些形参自动被定义为查询参数，一般使用 GET 方法的 `?` 符号来实现查询。

```python
@app.get("/items")
async def query_item(
    item_id: int = Query(10,description="书籍id,范围从0-100", ge=0, le=100),
    content:str = Query("这是默认内容", description="书籍内容")
    ):
    return {"item_id": item_id, "content": content}

```

在上面的例子中，当我访问 `http://127.0.0.1:8000/items?item_id=10&content=hello` 的时候，就能够得到相应的 `response`:
```json
{
  "item_id": 10,
  "content": "hello"
}
```
其中，`Query()` 方法的第一个参数为默认值，其他的参数都与 `Path()` 方法共通。

## 请求体参数
当想要传递大量 JSON 类型的数据的时候，经常使用 POST, PUT 等方法，将 JSON 放入请求体来传递。
这个很像 SpringBoot 里面的“使用对象来作为请求参数”。
这里是先使用 `pydantic` 来定义一个固定数据类型的“对象” ，然后在参数中传递它。
同时，像上述的说到的各种参数一样，也可以通过外部方法 `pydantic.Field()` 方法来对其进行约束。

*示例代码*
```python
from pydantic import BaseModel，Field
# ...
class User(BaseModel):
    username: str = Field(..., description="用户名，长度为3-50", min_length=3, max_length=50)
   password: str = Field(..., description="密码，长度为6-20", min_length=6, max_length=20)

@app.post("/users")
async def create_user(user: User):
    return {"username": user.username, "password": user.password}

#run...
```

# 中间件
这塔吗不是 SpringBoot 的

# 依赖注入相关

# ORM 相关

