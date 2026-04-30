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
async def read_item(item_id: int = 10 ,content:str = "这是默认内容"):
    return {"item_id": item_id, "content": content}
```

在上面的例子中，当我访问 `http://127.0.0.1:8000/items?item_id=10&content=hello` 的时候，就能够得到一下j
