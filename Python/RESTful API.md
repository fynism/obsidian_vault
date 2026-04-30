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
```python
from fastapi import FastAPI,Path

app = FastAPI()

@app.get("/")
async def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
async def read_item(item_id: int = Path(..., title="The ID of the item to get",description="书籍id,范围从0-100", ge=0, le=100)):
    return {"item_id": item_id, "content": f"This is No.{item_id} item"}

@app.get("/username/{username}")
async def read_user(username: str = Path(..., description="用户名，长度为3-50", min_length=3, max_length=50)):
    return {"username": username}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="127.0.0.1", port=8000)
```