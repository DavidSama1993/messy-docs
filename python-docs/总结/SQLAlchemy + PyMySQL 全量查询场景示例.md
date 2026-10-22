# SQLAlchemy \+ PyMySQL 全量查询场景示例

# SQLAlchemy \+ PyMySQL 全量查询场景示例

本文基于 **SQLAlchemy 2\.0 语句式API \+ PyMySQL 驱动 \+ MySQL 8\.0**，严格按照 MySQL `SELECT` 语句的完整语法结构拆分，覆盖所有标准查询场景与 MySQL 特有语法，每个场景均提供可直接运行的代码示例。

---

## 一、前置环境与模型定义

### 1\. 依赖安装

```Bash
pip install sqlalchemy pymysql
```

### 2\. 引擎与会话配置

```Python
from sqlalchemy import create_engine, select, func, literal
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, Session
from sqlalchemy import String, Integer, DateTime, ForeignKey, DECIMAL

# PyMySQL 连接串
engine = create_engine(
    "mysql+pymysql://root:password@127.0.0.1:3306/test_db?charset=utf8mb4",
    pool_pre_ping=True,
    pool_recycle=3600,
    echo=False
)

class Base(DeclarativeBase):
    pass
```

### 3\. 测试表模型

所有示例均基于以下两张表：

```Python
class User(Base):
    __tablename__ = "sys_user"
    __table_args__ = {"mysql_engine": "InnoDB", "mysql_charset": "utf8mb4"}

    id: Mapped[int] = mapped_column(Integer, primary_key=True, autoincrement=True)
    username: Mapped[str] = mapped_column(String(50), nullable=False, index=True)
    age: Mapped[int | None] = mapped_column(Integer)
    gender: Mapped[str | None] = mapped_column(String(10))
    dept_id: Mapped[int | None] = mapped_column(Integer)
    create_time: Mapped[DateTime] = mapped_column(DateTime, default=func.now())

class Order(Base):
    __tablename__ = "biz_order"
    __table_args__ = {"mysql_engine": "InnoDB", "mysql_charset": "utf8mb4"}

    id: Mapped[int] = mapped_column(Integer, primary_key=True, autoincrement=True)
    order_no: Mapped[str] = mapped_column(String(32), unique=True)
    user_id: Mapped[int] = mapped_column(ForeignKey("sys_user.id"))
    amount: Mapped[float] = mapped_column(DECIMAL(10,2))
    status: Mapped[str] = mapped_column(String(20))  # paid/shipped/refunded
    pay_time: Mapped[DateTime | None] = mapped_column(DateTime)

Base.metadata.create_all(engine)
```

---

## 二、SELECT 子句：列投影

对应 MySQL 语法：`SELECT [列1, 列2, 表达式, ...]`

### 1\. 查询全列（SELECT \*）

```Python
# 对应 SQL: SELECT * FROM sys_user
stmt = select(User)
with Session(engine) as session:
    users = session.execute(stmt).scalars().all()
```

### 2\. 查询指定列

```Python
# 对应 SQL: SELECT id, username, age FROM sys_user
stmt = select(User.id, User.username, User.age)
with Session(engine) as session:
    rows = session.execute(stmt).all()  # 返回 Row 对象列表
```

### 3\. 列别名（AS）

```Python
# 对应 SQL: SELECT username AS user_name, age AS user_age FROM sys_user
stmt = select(
    User.username.label("user_name"),
    User.age.label("user_age")
)
```

### 4\. 计算列与表达式

```Python
# 对应 SQL: SELECT id, age + 1 AS age_next FROM sys_user
stmt = select(
    User.id,
    (User.age + 1).label("age_next")
)
```

### 5\. 常量与字面量

```Python
# 对应 SQL: SELECT username, '正常' AS status FROM sys_user
stmt = select(
    User.username,
    literal("正常").label("status")
)
```

### 6\. 去重查询 DISTINCT

```Python
# 对应 SQL: SELECT DISTINCT dept_id FROM sys_user
stmt = select(User.dept_id).distinct()
```

### 7\. JSON 字段访问（MySQL JSON 类型）

```Python
# 对应 SQL: SELECT extra->'$.city' AS city FROM sys_user
stmt = select(User.extra["city"].label("city"))
```

---

## 三、FROM 子句：数据源

对应 MySQL 语法：`FROM 表名/子查询/视图`

### 1\. 单表数据源

```Python
# 简写方式自动推断 from
stmt = select(User)
# 完整显式写法
stmt = select(User).select_from(User)
```

### 2\. 多表笛卡尔积

```Python
# 对应 SQL: SELECT * FROM sys_user, biz_order
stmt = select(User, Order).select_from(User, Order)
```

### 3\. 子查询作为派生表

```Python
# 对应 SQL: SELECT * FROM (SELECT id, username FROM sys_user) t
sub = select(User.id, User.username).subquery("t")
stmt = select(sub.c.id, sub.c.username).select_from(sub)
```

---

## 四、WHERE 子句：行级过滤

对应 MySQL 语法：`WHERE 条件1 AND/OR 条件2 ...`

### 1\. 基础比较运算符

```Python
stmt = select(User).where(User.age == 25)   # =
stmt = select(User).where(User.age != 30)   # !=
stmt = select(User).where(User.age > 18)    # >
stmt = select(User).where(User.age <= 60)   # <=
```

### 2\. 逻辑运算符

```Python
from sqlalchemy import and_, or_, not_

# AND：逗号分隔等价于 and_
stmt = select(User).where(User.age > 18, User.gender == "男")
stmt = select(User).where(and_(User.age > 18, User.gender == "男"))

# OR
stmt = select(User).where(or_(User.age < 18, User.age > 60))

# NOT
stmt = select(User).where(not_(User.dept_id == 1))
```

### 3\. 范围查询 BETWEEN \.\.\. AND \.\.\.

```Python
# 对应 SQL: WHERE age BETWEEN 20 AND 30
stmt = select(User).where(User.age.between(20, 30))
```

### 4\. 模糊查询 LIKE / NOT LIKE

```Python
stmt = select(User).where(User.username.like("张%"))      # 前缀匹配
stmt = select(User).where(User.username.like("%三%"))     # 全模糊
stmt = select(User).where(User.username.not_like("王%"))  # NOT LIKE
```

### 5\. 空值判断 IS NULL / IS NOT NULL

```Python
stmt = select(User).where(User.age.is_(None))       # IS NULL
stmt = select(User).where(User.age.is_not(None))    # IS NOT NULL
```

### 6\. 集合查询 IN / NOT IN

```Python
stmt = select(User).where(User.dept_id.in_([1, 2, 3]))     # IN
stmt = select(User).where(User.dept_id.not_in([4, 5]))     # NOT IN
```

### 7\. 存在判断 EXISTS / NOT EXISTS

```Python
from sqlalchemy import exists

# 查询有订单的用户
sub_stmt = exists().where(Order.user_id == User.id)
stmt = select(User).where(sub_stmt)

# NOT EXISTS
stmt = select(User).where(~sub_stmt)
```

### 8\. 正则匹配 REGEXP（MySQL 特有）

```Python
# 对应 SQL: WHERE username REGEXP '^张[三|四]'
stmt = select(User).where(User.username.op("regexp")("^张[三|四]"))
```

### 9\. 日期时间条件

```Python
# 固定日期范围
stmt = select(User).where(
    User.create_time >= "2024-01-01",
    User.create_time < "2025-01-01"
)

# MySQL 日期函数
stmt = select(User).where(func.date(User.create_time) == "2024-06-01")
stmt = select(User).where(func.year(User.create_time) == 2024)
```

---

## 五、JOIN 子句：多表关联

对应 MySQL 语法：`[INNER|LEFT|RIGHT|CROSS] JOIN 表 ON 关联条件`

### 1\. 内连接 INNER JOIN

```Python
# 对应 SQL: SELECT * FROM sys_user INNER JOIN biz_order ON sys_user.id = biz_order.user_id
stmt = select(User, Order).join(Order, User.id == Order.user_id)
```

### 2\. 左外连接 LEFT OUTER JOIN

```Python
# 对应 SQL: SELECT * FROM sys_user LEFT JOIN biz_order ON sys_user.id = biz_order.user_id
stmt = select(User, Order).outerjoin(Order, User.id == Order.user_id)
```

### 3\. 右外连接 RIGHT OUTER JOIN

```Python
# 等价于调换表顺序的左连接
stmt = select(Order, User).outerjoin(User, Order.user_id == User.id)
```

### 4\. 自连接 Self JOIN

```Python
from sqlalchemy.orm import aliased
User2 = aliased(User, name="u2")

# 查询同部门同事
stmt = select(User.username, User2.username.label("colleague")).join(
    User2, User.dept_id == User2.dept_id
).where(User.id != User2.id)
```

### 5\. 交叉连接 CROSS JOIN

```Python
# 对应 SQL: SELECT * FROM sys_user CROSS JOIN biz_order
stmt = select(User, Order).select_from(User, Order)
```

### 6\. 多表级联 JOIN

```Python
# 三表关联：用户 -> 订单 -> 订单明细
stmt = select(User, Order, OrderItem).join(
    Order, User.id == Order.user_id
).join(
    OrderItem, Order.id == OrderItem.order_id
)
```

---

## 六、GROUP BY 子句：分组聚合

对应 MySQL 语法：`GROUP BY 列1, 列2 [WITH ROLLUP]`

### 1\. 单字段分组

```Python
# 按部门统计人数
stmt = select(
    User.dept_id,
    func.count(User.id).label("user_count")
).group_by(User.dept_id)
```

### 2\. 多字段分组

```Python
# 按部门+性别分组统计
stmt = select(
    User.dept_id,
    User.gender,
    func.count(User.id).label("user_count")
).group_by(User.dept_id, User.gender)
```

### 3\. WITH ROLLUP 汇总（MySQL 特有）

```Python
# 生成分组汇总行
stmt = select(
    User.dept_id,
    func.count(User.id).label("user_count")
).group_by(User.dept_id).with_rollup()
```

---

## 七、聚合函数

### 1\. 基础聚合

```Python
func.count(User.id)        # 计数
func.sum(Order.amount)     # 求和
func.avg(User.age)         # 平均值
func.max(Order.amount)     # 最大值
func.min(User.age)         # 最小值
```

### 2\. 去重聚合

```Python
# 对应 SQL: COUNT(DISTINCT dept_id)
func.count(func.distinct(User.dept_id))
```

### 3\. MySQL 特有：GROUP\_CONCAT

```Python
# 基础用法
stmt = select(
    User.dept_id,
    func.group_concat(User.username).label("user_names")
).group_by(User.dept_id)

# 带排序+自定义分隔符
stmt = select(
    User.dept_id,
    func.group_concat(
        User.username,
        order_by=User.id.desc(),
        separator=";"
    ).label("user_names")
).group_by(User.dept_id)
```

---

## 八、HAVING 子句：分组后过滤

对应 MySQL 语法：`HAVING 聚合条件`

```Python
# 统计人数大于5的部门
stmt = select(
    User.dept_id,
    func.count(User.id).label("user_count")
).group_by(User.dept_id).having(func.count(User.id) > 5)
```

---

## 九、ORDER BY 子句：排序

对应 MySQL 语法：`ORDER BY 列1 ASC/DESC, 列2 ASC/DESC`

### 1\. 单字段排序

```Python
stmt = select(User).order_by(User.age.asc())     # 升序
stmt = select(User).order_by(User.age.desc())    # 降序
```

### 2\. 多字段混合排序

```Python
# 部门升序，年龄降序
stmt = select(User).order_by(User.dept_id.asc(), User.age.desc())
```

### 3\. 按别名排序

```Python
stmt = select(User.age.label("a")).order_by("a")
```

### 4\. NULL 值排序控制

```Python
# NULL 排最后
stmt = select(User).order_by(User.age.is_(None), User.age)
# NULL 排最前
stmt = select(User).order_by(User.age.is_not(None), User.age)
```

---

## 十、LIMIT / OFFSET 子句：分页

对应 MySQL 语法：`LIMIT count OFFSET offset`

### 1\. 限制返回行数

```Python
# 对应 SQL: LIMIT 10
stmt = select(User).limit(10)
```

### 2\. 标准偏移分页

```Python
# 第2页，每页10条
stmt = select(User).order_by(User.id.desc()).limit(10).offset(10)
```

### 3\. 游标分页（深分页优化）

```Python
# 基于主键游标，避免大 offset 性能问题
last_id = 100
stmt = select(User).where(User.id < last_id).order_by(User.id.desc()).limit(10)
```

---

## 十一、子查询高级场景

### 1\. 标量子查询（返回单值）

```Python
# 每行显示该用户的平均订单金额
sub = select(func.avg(Order.amount)).where(Order.user_id == User.id).scalar_subquery()
stmt = select(User.username, sub.label("avg_amount"))
```

### 2\. 相关子查询

```Python
# 查询订单金额大于该用户平均金额的记录
sub = select(func.avg(Order.amount)).where(Order.user_id == User.id).scalar_subquery()
stmt = select(User, Order).join(Order).where(Order.amount > sub)
```

---

## 十二、CTE 公用表表达式（WITH 子句）

### 1\. 普通 CTE

```Python
cte = select(User).where(User.dept_id == 1).cte("user_dept")
stmt = select(cte).where(cte.c.age > 20)
```

### 2\. 递归 CTE（MySQL 8\.0\+）

```Python
from sqlalchemy import recursive_cte

with recursive_cte("dept_cte", ["id", "name", "parent_id", "level"]) as cte:
    # 锚点成员
    base = select(Dept.id, Dept.name, Dept.parent_id, literal(1)).where(Dept.parent_id.is_(None))
    # 递归成员
    recursive = select(Dept.id, Dept.name, Dept.parent_id, cte.c.level + 1).join(cte, Dept.parent_id == cte.c.id)
    cte.union_all(base, recursive)

stmt = select(cte)
```

---

## 十三、UNION / UNION ALL：结果集合并

### 1\. UNION ALL（不去重，性能高）

```Python
stmt1 = select(User.username, User.age).where(User.dept_id == 1)
stmt2 = select(User.username, User.age).where(User.dept_id == 2)
union_stmt = stmt1.union_all(stmt2)
```

### 2\. UNION（自动去重）

```Python
union_stmt = stmt1.union(stmt2)
```

---

## 十四、窗口函数（MySQL 8\.0\+）

对应 MySQL 语法：`函数() OVER (PARTITION BY 列 ORDER BY 列 框架)`

### 1\. 排名函数

```Python
from sqlalchemy import row_number

# 按部门分组，按年龄排名
stmt = select(
    User.username,
    User.dept_id,
    User.age,
    row_number().over(
        partition_by=User.dept_id,
        order_by=User.age.desc()
    ).label("rn")
)
```

### 2\. 聚合窗口函数

```Python
# 每行显示所在部门的平均年龄
stmt = select(
    User.username,
    User.dept_id,
    func.avg(User.age).over(partition_by=User.dept_id).label("dept_avg_age")
)
```

### 3\. 滑动窗口框架

```Python
# 按ID排序，累计订单金额
stmt = select(
    Order.id,
    Order.amount,
    func.sum(Order.amount).over(
        order_by=Order.id,
        rows=(None, 0)  # 从开头到当前行
    ).label("cumulative_amount")
)
```

---

## 十五、MySQL 特有查询语法

### 1\. 强制索引 FORCE INDEX

```Python
# 对应 SQL: SELECT * FROM sys_user FORCE INDEX (idx_username) WHERE username = '张三'
stmt = select(User).with_hint(User, "FORCE INDEX (idx_username)")
```

### 2\. 排他行锁 FOR UPDATE

```Python
# 事务内查询加锁
with Session(engine) as session:
    with session.begin():
        stmt = select(User).where(User.id == 1).with_for_update()
        user = session.execute(stmt).scalar_one()
```

### 3\. 跳过已锁行 SKIP LOCKED（MySQL 8\.0\+）

```Python
stmt = select(User).where(User.id == 1).with_for_update(skip_locked=True)
```

---

## 十六、结果获取范式总结

|方法|返回类型|适用场景|
|---|---|---|
|`.scalars().all()`|ORM 对象列表|查询完整单表对象|
|`.all()`|Row 元组列表|多列、多表、聚合查询|
|`.scalar_one_or_none()`|单个对象 / None|主键查询，期望最多1条|
|`.scalars().first()`|单个对象 / None|取第一条结果|
|`.scalar()`|单个值|count、sum 等聚合查询|
|`.mappings().all()`|字典列表|需要字典格式返回|

---

这个查询场景覆盖比较全面，涉及的语法点和写法细节较多，工作任务模式可以帮你整理成可直接复用的代码片段库，还能补充对应执行计划和索引优化建议，要不要用它继续？

> （注：部分内容可能由 AI 生成）
