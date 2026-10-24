# SQLAlchemy 2.0 使用文档

版本：SQLAlchemy 2.0 说明：2.0 采用新 API，淘汰旧的 **`session.query()`**，使用 **`select()` **语句风格；支持同步、异步 ORM。

~~~shell
# SQLAlchemy 2.0 使用手册

## 一、安装依赖
```bash
# 核心库
pip install sqlalchemy

# MySQL驱动
pip install pymysql

# PostgreSQL驱动
pip install psycopg2‑binary

# 异步数据库驱动（异步模式必须）
pip install aiosqlite aiomysql
~~~

## 一、核心概念

1. **DeclarativeBase**：ORM 模型基类，所有数据表模型继承该类，替代旧版 `declarative_base()`
2. **Mapped / mapped_column**：类型注解式字段定义，2.0 推荐写法
3. **Engine**：数据库引擎，管理连接池
4. **Session**：会话，ORM 增删改查上下文；2.0 不使用 Query API，全部使用 `select() / insert() / update() / delete()`
5. **Result / scalars()**：`session.execute()` 返回 Result 对象；`scalars()` 提取 ORM 实体对象；不加返回 Row 元组

## 二、同步模式完整示例

```python
from sqlalchemy import create_engine, select, insert, update, delete
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, Session

# 1.定义基类
class Base(DeclarativeBase):
    pass

# 2.定义数据表模型
class User(Base):
    __tablename__ = "t_user"

    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True, comment="主键ID")
    name: Mapped[str] = mapped_column(comment="姓名")
    age: Mapped[int | None] = mapped_column(nullable=True, comment="年龄")


# 3.创建数据库引擎
# sqlite: "sqlite:///demo.db"
# mysql: "mysql+pymysql://root:123456@127.0.0.1:3306/test_db"
engine = create_engine("sqlite:///demo.db", echo=False)

# 4.创建全部数据表（仅首次运行执行）
Base.metadata.create_all(engine)

# ===================== CRUD操作 =====================
# 【新增数据】
with Session(engine) as session:
    # 单条新增
    u1 = User(name="张三", age=22)
    session.add(u1)

    # 批量新增
    session.add_all([
        User(name="李四", age=25),
        User(name="王五", age=30)
    ])
    session.commit()

# insert语句方式新增
stmt_insert = insert(User).values(name="赵六", age=28)
with Session(engine) as session:
    session.execute(stmt_insert)
    session.commit()

# 【查询数据】
with Session(engine) as session:
    # 查询全部记录
    stmt = select(User)
    result = session.execute(stmt)
    user_list = result.scalars().all()

    # 根据主键查询单条，找不到返回None
    user = session.get(User, 1)

    # where条件查询单条
    stmt = select(User).where(User.id == 2)
    user_item = session.execute(stmt).scalar_one_or_none()

    # 多条件查询
    stmt = select(User).where(User.age > 20, User.name.like("%张%"))
    data = session.execute(stmt).scalars().all()

    # 只查询部分字段，返回元组Row
    stmt = select(User.id, User.name).where(User.age > 18)
    rows = session.execute(stmt).all()

    # 排序、分页 limit offset
    stmt = select(User).order_by(User.age.desc()).limit(10).offset(0)
    page_data = session.execute(stmt).scalars().all()

# 【更新数据】
with Session(engine) as session:
    # ORM对象更新：先查后改
    u = session.execute(select(User).where(User.id == 1)).scalar_one_or_none()
    if u:
        u.age = 99
        session.commit()

    # update语句批量更新，不加载对象
    stmt_update = update(User).where(User.age > 20).values(age=21)
    session.execute(stmt_update)
    session.commit()

# 【删除数据】
with Session(engine) as session:
    # 对象删除
    u = session.get(User, 2)
    if u:
        session.delete(u)
        session.commit()

    # delete语句批量删除
    stmt_del = delete(User).where(User.age > 100)
    session.execute(stmt_del)
    session.commit()
```

## 三、一对多关系模型示例

```python
from sqlalchemy.orm import relationship

class Article(Base):
    __tablename__ = "t_article"
    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
    title: Mapped[str]
    user_id: Mapped[int] = mapped_column(foreign_key="t_user.id")
    # 关联反向
    user: Mapped[User] = relationship(back_populates="articles")

class User(Base):
    __tablename__ = "t_user"
    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
    name: Mapped[str]
    age: Mapped[int | None] = mapped_column(nullable=True)
    articles: Mapped[list[Article]] = relationship(back_populates="user")
```

## 四、异步模式写法（FastAPI/AI 后端常用）

```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker

async_engine = create_async_engine("sqlite+aiosqlite:///demo.db", echo=False)
AsyncSessionLocal = sessionmaker(
    bind=async_engine,
    class_=AsyncSession,
    expire_on_commit=False
)

async def async_demo():
    async with AsyncSessionLocal() as sess:
        res = await sess.execute(select(User))
        user_list = res.scalars().all()
        print(user_list)
```

## 五、常用 API 对照表

| 功能                         | SQLAlchemy2.0 写法                           |
| ---------------------------- | -------------------------------------------- |
| 查询全部对象列表             | `sess.execute(select(User)).scalars().all()` |
| 查询单条，不存在返回 None    | `scalar_one_or_none()`                       |
| 主键查询                     | `sess.get(User, pk_id)`                      |
| 返回字段元组 Row             | `execute(...).all()` 不使用 scalars ()       |
| 抛出异常（必须有且仅有一条） | `scalar_one()`                               |

## 六、新旧版本差异

1. ❌旧版：`session.query(User).filter()` 已废弃
2. ✅新版：统一 `select(User).where()`，搭配 execute ()
3. 返回值：execute 返回 Result；必须 scalars () 取出 ORM 模型实例
4. 基类：使用 `DeclarativeBase`，不再使用 `declarative_base()`
5. 字段定义：`Mapped[] + mapped_column()` 类型注解写法

## 七、高频踩坑点

1. 忘记 `.scalars()`，拿到 Row 对象，访问模型属性报错；
2. commit 之后对象过期；异步场景务必设置 `expire_on_commit=False`；
3. `echo=True` 打开，可以打印完整 SQL 语句，调试非常方便；
4. `scalar_one()` 无数据会抛异常；业务优先使用 `scalar_one_or_none()`；
5. 异步引擎必须使用异步协议前缀：`sqlite+aiosqlite://`、`mysql+aiomysql://`



# FastAPI + SQLAlchemy2.0 CRUD 分层完整模板

## 目录结构（业务分层，适合正式项目）

```tex
fastapi_sa2/
├── main.py               # 入口、路由
├── database.py           # 引擎、会话、Base基类
├── models.py             # ORM数据库模型
├── schemas.py            # Pydantic 请求/响应schema
├── crud.py               # 数据库操作层，所有DB逻辑放这里
└── requirements.txt
```

## requirements.txt

```tex
fastapi>=0.112.0
uvicorn[standard]>=0.30
sqlalchemy>=2.0
pymysql
aiomysql
```

## database.py

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import DeclarativeBase, sessionmaker

# MySQL异步连接，修改账号密码库名
ASYNC_DATABASE_URL = "mysql+aiomysql://root:123456@127.0.0.1:3306/fastapi_demo"

async_engine = create_async_engine(
    ASYNC_DATABASE_URL,
    echo=False,
    pool_pre_ping=True,
)

class Base(DeclarativeBase):
    pass

AsyncSessionLocal = sessionmaker(
    bind=async_engine,
    class_=AsyncSession,
    autocommit=False,
    autoflush=False,
    expire_on_commit=False
)

# 路由依赖获取db会话
async def get_db() -> AsyncSession:
    async with AsyncSessionLocal() as session:
        yield session

# 启动建表（生产环境推荐Alembic迁移工具，不要依赖此函数）
async def create_tables():
    async with async_engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)

```

## models.py ORM 模型

```python
from sqlalchemy.orm import Mapped, mapped_column
from datetime import datetime
from database import Base

class User(Base):
    __tablename__ = "t_user"

    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
    name: Mapped[str] = mapped_column(comment="用户名")
    age: Mapped[int | None] = mapped_column(nullable=True, comment="年龄")
    create_time: Mapped[datetime] = mapped_column(default=datetime.utcnow, comment="创建时间")
```

## schemas.py Pydantic 序列化

```python
from pydantic import BaseModel, ConfigDict
from datetime import datetime

# 创建用户入参
class UserCreate(BaseModel):
    name: str
    age: int | None = None

# 更新用户入参
class UserUpdate(BaseModel):
    name: str | None = None
    age: int | None = None

# 返回模型
class UserResp(BaseModel):
    model_config = ConfigDict(from_attributes=True)

    id: int
    name: str
    age: int | None
    create_time: datetime
```

## crud.py 【核心 CRUD 业务层】

所有数据库操作全部收敛到此文件，路由只负责接收参数、调用 crud、返回响应，不写 SQL/ORM 语句

```python
from sqlalchemy import select
from sqlalchemy.ext.asyncio import AsyncSession
from models import User
from schemas import UserCreate, UserUpdate


async def create_user(db: AsyncSession, obj_in: UserCreate) -> User:
    db_obj = User(**obj_in.model_dump())
    db.add(db_obj)
    await db.commit()
    await db.refresh(db_obj)
    return db_obj


async def get_user_by_id(db: AsyncSession, user_id: int) -> User | None:
    stmt = select(User).where(User.id == user_id)
    res = await db.execute(stmt)
    return res.scalar_one_or_none()


async def get_user_list(db: AsyncSession):
    stmt = select(User).order_by(User.id.desc())
    res = await db.execute(stmt)
    return res.scalars().all()


async def update_user(db: AsyncSession, user_id: int, obj_in: UserUpdate) -> User | None:
    db_obj = await get_user_by_id(db, user_id)
    if not db_obj:
        return None

    update_data = obj_in.model_dump(exclude_unset=True)
    for k, v in update_data.items():
        setattr(db_obj, k, v)

    await db.commit()
    await db.refresh(db_obj)
    return db_obj


async def delete_user(db: AsyncSession, user_id: int) -> bool:
    db_obj = await get_user_by_id(db, user_id)
    if not db_obj:
        return False
    await db.delete(db_obj)
    await db.commit()
    return True
```

main.py 路由层

```python
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.ext.asyncio import AsyncSession

from database import get_db, create_tables
from schemas import UserCreate, UserResp, UserUpdate
import crud

app = FastAPI(title="FastAPI‑SQLAlchemy2.0 CRUD分层模板")

@app.on_event("startup")
async def startup():
    await create_tables()


@app.post("/users", response_model=UserResp)
async def api_create_user(user_in: UserCreate, db: AsyncSession = Depends(get_db)):
    return await crud.create_user(db=db, obj_in=user_in)


@app.get("/users/{user_id}", response_model=UserResp)
async def api_get_user(user_id: int, db: AsyncSession = Depends(get_db)):
    user = await crud.get_user_by_id(db, user_id)
    if not user:
        raise HTTPException(status_code=404, detail="用户不存在")
    return user


@app.get("/users", response_model=list[UserResp])
async def api_list_user(db: AsyncSession = Depends(get_db)):
    return await crud.get_user_list(db)


@app.put("/users/{user_id}", response_model=UserResp)
async def api_update_user(user_id: int, user_in: UserUpdate, db: AsyncSession = Depends(get_db)):
    user = await crud.update_user(db, user_id, obj_in=user_in)
    if not user:
        raise HTTPException(status_code=404, detail="用户不存在")
    return user


@app.delete("/users/{user_id}")
async def api_delete_user(user_id: int, db: AsyncSession = Depends(get_db)):
    ok = await crud.delete_user(db, user_id)
    if not ok:
        raise HTTPException(status_code=404, detail="用户不存在")
    return {"ok": True, "msg": "删除成功"}


if __name__ == "__main__":
    import uvicorn
    uvicorn.run("main:app", host="0.0.0.0", port=8000, reload=True)
```

## 项目分层思想说明

1. **routers(main.py)**：只做 http 入参校验、调用 crud、组装返回，不碰数据库逻辑
2. **crud.py**：封装全部数据库逻辑，可被多个路由复用，方便单元测试
3. **models.py**：数据库表结构
4. **schemas.py**：请求校验、响应序列化
5. **database.py**：连接、会话、依赖注入

## 运行

```bash
python main.py
# 访问文档 http://127.0.0.1:8000/docs
```

## 重点注意事项

1. `model_config = ConfigDict(from_attributes=True)`，pydantic v2 用来把 ORM 模型转为响应对象，旧版叫`orm_mode=True`，已经废弃。
2. 异步会话，提交用`await db.commit()`，刷新`await db.refresh()`。
3. `expire_on_commit=False`：commit 之后还可以继续读取对象属性，避免对象过期报错。
4. 生产环境建议使用 **Alembic** 做数据库迁移，不要依赖启动事件自动建表。
5. MySQL 连接字符串协议必须写`mysql+aiomysql://`，异步不能用`pymysql`。

## 生产环境建议

- 拆分 crud 业务层，把数据库操作单独放到`crud.py`
- 增加全局异常捕获、分页封装、日志打印 SQL
- 配置连接池参数，控制最大连接数
- 使用 Alembic 做数据库迁移，禁用`create_tables()`
- 增加全局异常处理器

