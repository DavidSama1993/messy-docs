# Numpy & Pandas 常用函数 API 文档

---

## 第一部分：NumPy 常用 API

NumPy（Numerical Python）是 Python 科学计算的基础库，核心是 **ndarray**（N 维数组对象）。以下按功能模块分类整理常用 API。

---

# 一、numpy.ndarray 类

## 1.1 ndarray 简介

`ndarray` 是 NumPy 的核心数据结构，表示一个**同构**、**固定大小**的 N 维数组对象，所有元素必须是相同数据类型。与 Python list 相比，它具备以下优势：

- **存储连续**：内存块连续存储，访问效率高
- **矢量化运算**：无需显式循环即可对整个数组进行批量运算（基于 C/Fortran 实现）
- **广播机制**：不同形状的数组之间可进行算术运算
- **丰富的 API**：提供线性代数、傅里叶变换、随机数等内置函数

## 1.2 ndarray 的常用属性

| 属性名        | 类型             | 描述                                          |
| ------------- | ---------------- | --------------------------------------------- |
| **ndim**      | int              | 数组的维度数（轴的数量）                      |
| **shape**     | tuple of int    | 数组各维度大小的元组                          |
| **size**      | int              | 数组元素的总数                                |
| **dtype**     | numpy.dtype      | 数组元素的数据类型                            |
| **itemsize**  | int              | 单个元素所占字节数                            |
| **T**         | ndarray          | 数组的转置                                    |
| **data**      | buffer           | 指向数组数据存储区的 Python 缓冲区对象        |
| **strides**   | tuple of int    | 每个维度的步长（字节）                        |
| **flags**     | dict             | 数组的内存布局信息（C/F 连续、只写等）        |

## 1.3 ndarray 的常用方法

### 1.3.1 形状与变形

- **ndarray.reshape(shape, order='C'):** 返回一个新形状的数组，不修改原数组
- **ndarray.resize(new_shape):** 就地修改数组形状（必要时重复或丢弃元素）
- **ndarray.flatten(order='C'):** 返回一维拷贝
- **ndarray.ravel(order='C'):** 返回一维视图（默认），比 flatten 更省内存
- **ndarray.T:** 数组的转置
- **ndarray.transpose(axes):** 按指定轴顺序进行转置
- **ndarray.swapaxes(axis1, axis2):** 交换两个轴
- **ndarray.squeeze(axis=None):** 移除长度为 1 的维度

### 1.3.2 索引与迭代

- **ndarray.reshape(-1, n) / (-1,):** 用 -1 自动推断该轴大小
- **ndarray.item(*args):** 将数组元素复制到 Python 标量
- **ndarray.tolist():** 转换为 Python 列表
- **ndarray.tostring() / tobytes():** 转换为字节串
- **ndarray.itemset(*args):** 将 Python 标量写入数组元素
- **ndarray.nonzero():** 返回非零元素的索引
- **ndarray.argmin() / ndarray.argmax(axis=None, out=None):** 沿指定轴最小/最大元素的索引
- **ndarray.argsort(axis=-1, kind='quicksort', order=None):** 沿指定轴排序的索引

### 1.3.3 计算与统计

- **ndarray.max(axis=None) / min(axis=None):** 沿轴最大/最小值
- **ndarray.mean(axis=None) / sum(axis=None):** 沿轴均值/求和
- **ndarray.var(axis=None) / std(axis=None):** 沿轴方差/标准差
- **ndarray.prod(axis=None) / cumprod(axis=None):** 沿轴乘积/累积乘积
- **ndarray.cumsum(axis=None):** 沿轴累积和
- **ndarray.any(axis=None) / all(axis=None):** 沿轴是否存在/全部为真

### 1.3.4 内存与拷贝

- **ndarray.copy(order='C'):** 返回数组的副本
- **ndarray.view([dtype][, type]):** 以指定类型查看内存

---

# 二、NumPy 数组创建函数

NumPy 提供多种方式创建 ndarray，按使用场景可分为 4 类：**依据现有数据**、**依据数值范围**、**依据特定形状**、**依据随机分布**。

## 2.1 依据现有数据创建

- **numpy.array(object, dtype=None, copy=True, order='K', subok=False, ndmin=0):** 最基础的数组创建函数，依据任意序列对象（list / tuple / ndarray 等）创建数组
- **numpy.asarray(a, dtype=None, order=None):** 将输入转换为 ndarray；若已是 ndarray 且 dtype 一致则不复制
- **numpy.frombuffer(buffer, dtype=float, count=-1, offset=0):** 从字节流 buffer 解释为 1 维数组
- **numpy.fromiter(iterable, dtype, count=-1):** 从可迭代对象构建 1 维数组
- **numpy.fromfunction(function, shape, **kwargs):** 通过对每个坐标执行函数来构造数组

## 2.2 依据数值范围创建

- **numpy.arange([start,] stop[, step,] dtype=None):** 类似 Python `range`，返回等差序列（左闭右开）
- **numpy.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None):** 在区间 `[start, stop]` 内生成 `num` 个等距点，默认包含 stop
- **numpy.logspace(start, stop, num=50, endpoint=True, base=10.0, dtype=None):** 在对数刻度上等距，`base**start` 到 `base**stop`
- **numpy.geomspace(start, stop, num=50, endpoint=True, dtype=None):** 在对数尺度上等比分布（几何级数）

## 2.3 依据特定形状创建

- **numpy.zeros(shape, dtype=float, order='C'):** 创建全 0 数组
- **numpy.ones(shape, dtype=None, order='C'):** 创建全 1 数组
- **numpy.empty(shape, dtype=float, order='C'):** 创建未初始化的数组（内容为随机垃圾值）
- **numpy.full(shape, fill_value, dtype=None, order='C'):** 创建用 fill_value 填充的数组
- **numpy.zeros_like(a, dtype=None, order='K', subok=True):** 形状与 a 相同的全 0 数组
- **numpy.ones_like(a, dtype=None, order='K', subok=True):** 形状与 a 相同的全 1 数组
- **numpy.empty_like(a, dtype=None, order='K', subok=True):** 形状与 a 相同的未初始化数组
- **numpy.full_like(a, fill_value, dtype=None, order='K', subok=True):** 形状与 a 相同的全填充数组
- **numpy.eye(N, M=None, k=0, dtype=float, order='C'):** 创建对角线为 1、其余为 0 的二维数组（单位矩阵变体）
- **numpy.identity(n, dtype=None):** 创建 n×n 单位矩阵
- **numpy.diag(v, k=0):** 创建对角数组或提取对角线

## 2.4 依据随机分布创建

> 需 `import numpy.random` 或 `np.random`（新版用 `numpy.random.default_rng()` 创建 Generator 实例，推荐）。

- **numpy.random.rand(*d0, d1, ..., dn):** 均匀分布 [0, 1) 的随机数组（不给参数返回单个数）
- **numpy.random.randn(*d0, d1, ..., dn):** 标准正态分布 N(0, 1) 的随机数组
- **numpy.random.randint(low, high=None, size=None, dtype=int):** `[low, high)` 区间的随机整数
- **numpy.random.uniform(low=0.0, high=1.0, size=None):** `[low, high)` 均匀分布
- **numpy.random.normal(loc=0.0, scale=1.0, size=None):** 正态分布 `N(loc, scale²)`
- **numpy.random.choice(a, size=None, replace=True, p=None):** 从 1 维数组 a 中抽样
- **numpy.random.shuffle(x):** 就地打乱数组
- **numpy.random.permutation(x):** 返回打乱后的副本
- **numpy.random.seed(seed=None):** 设置随机种子以保证结果可复现

---

# 三、NumPy 数值运算函数

本节分类涵盖基本运算、指数对数、舍入取整、三角函数、元素检测与逻辑运算。

## 3.1 基本算术运算

> 接受 `ndarray` 或类数组对象作为输入，可配合 axis / dtype / out 等参数；以下以 `a`、`b` 描述输入数组。

- **numpy.add(a, b, /, out=None, *, where=True):** 元素级加法
- **numpy.subtract(a, b):** 元素级减法
- **numpy.multiply(a, b):** 元素级乘法
- **numpy.divide(a, b):** 元素级除法（返回浮点）
- **numpy.floor_divide(a, b):** 元素级整除（向下取整）
- **numpy.mod(a, b) / numpy.remainder(a, b):** 元素级取模
- **numpy.power(a, b):** 元素级幂运算（a 的 b 次方）
- **numpy.float_power(a, b):** 元素级幂运算，结果始终为浮点
- **numpy.negative(a):** 一元取负（`-x`）
- **numpy.positive(a):** 一元取正（`+x`）
- **numpy.abs(a) / numpy.absolute(a):** 元素级绝对值

> **运算符对照**：`+` `-` `*` `/` `//` `%` `**` 等运算符等价于上述函数，但**会自动广播**，因此运算符更常用。

## 3.2 指数与对数函数

- **numpy.sqrt(x, /, out=None):** 元素级平方根
- **numpy.cbrt(x):** 元素级立方根
- **numpy.square(x):** 元素级平方（等价于 x**2）
- **numpy.exp(x):** 元素级自然指数 `e^x`
- **numpy.exp2(x):** 元素级 `2^x`
- **numpy.expm1(x):** 元素级 `e^x - 1`（在 x 接近 0 时精度更高）
- **numpy.log(x):** 元素级自然对数 `ln(x)`
- **numpy.log2(x):** 元素级以 2 为底的对数
- **numpy.log10(x):** 元素级以 10 为底的对数
- **numpy.log1p(x):** 元素级 `ln(1 + x)`（在 x 接近 0 时精度更高）

## 3.3 舍入与取整函数

- **numpy.round(a, decimals=0, out=None):** 四舍五入到指定小数位数（与 Python `round` 行为不同）
- **numpy.around(a, decimals=0, out=None):** `round` 的别名
- **numpy.floor(x, /, out=None):** 向下取整（不超过输入的最大整数）
- **numpy.ceil(x, /, out=None):** 向上取整（不小于输入的最小整数）
- **numpy.trunc(x):** 向零方向取整（截断小数部分）
- **numpy.rint(x):** 四舍五入到最近的整数（结果仍为浮点）

## 3.4 元素检测函数

- **numpy.isnan(x, /, out=None):** 检测 NaN（不是数字），返回 bool 数组
- **numpy.isinf(x, /, out=None):** 检测正/负无穷
- **numpy.isfinite(x, /, out=None):** 检测有限数（既非 NaN 也非 inf）
- **numpy.isposinf(x):** 检测正无穷
- **numpy.isneginf(x):** 检测负无穷

## 3.5 元素比较与最值函数

- **numpy.greater(a, b) / greater_equal(a, b):** `>` / `>=` 元素比较
- **numpy.less(a, b) / less_equal(a, b):** `<` / `<=` 元素比较
- **numpy.equal(a, b) / not_equal(a, b):** `==` / `!=` 元素比较
- **numpy.maximum(x1, x2):** 元素级最大值
- **numpy.minimum(x1, x2):** 元素级最小值
- **numpy.fmax(x1, x2):** 元素级最大值（忽略 NaN）
- **numpy.fmin(x1, x2):** 元素级最小值（忽略 NaN）

## 3.6 逻辑运算函数

- **numpy.logical_and(x1, x2):** `x1 AND x2`（元素级逻辑与）
- **numpy.logical_or(x1, x2):** `x1 OR x2`（元素级逻辑或）
- **numpy.logical_not(x):** `NOT x`（元素级逻辑非）
- **numpy.logical_xor(x1, x2):** `x1 XOR x2`（元素级逻辑异或）
- **numpy.all(a, axis=None):** 是否所有元素都为真
- **numpy.any(a, axis=None):** 是否存在元素为真

## 3.7 数组操作与转换函数

- **numpy.where(condition, x=None, y=None):** 三种用法：(1) 返回满足条件的索引；(2) 三参数形式按条件从 x/y 选取；(3) 一参数时同 `nonzero`
- **numpy.nan_to_num(x, copy=True, nan=0.0, posinf=None, neginf=None):** 将 NaN、正负无穷替换为指定数值
- **numpy.clip(a, a_min, a_max, out=None, \**kwargs):** 将数组元素截断到 `[a_min, a_max]`
- **numpy.cumsum(a, axis=None) / numpy.cumprod(a, axis=None):** 沿轴累积和/累积积
- **numpy.diff(a, n=1, axis=-1, prepend=\<no value>, append=\<no value>):** 离散差分（沿轴的相邻元素差）
- **numpy.sign(x):** 元素级符号函数（-1 / 0 / 1）

## 3.8 三角函数

- **numpy.sin / cos / tan(x, /, out=None):** 正弦 / 余弦 / 正切
- **numpy.arcsin / arccos / arctan(x):** 反正弦 / 反余弦 / 反正切
- **numpy.sinh / cosh / tanh(x):** 双曲正弦 / 余弦 / 正切
- **numpy.arcsinh / arccosh / arctanh(x):** 反双曲函数
- **numpy.deg2rad(x, /, out=None):** 角度 → 弧度
- **numpy.rad2deg(x, /, out=None):** 弧度 → 角度
- **numpy.hypot(x1, x2):** 平方根的欧氏范数 `sqrt(x1² + x2²)`

## 3.9 数论函数

- **numpy.lcm(x1, x2, /):** 最小公倍数
- **numpy.gcd(x1, x2, /):** 最大公约数

## 3.10 矢量化函数

- **numpy.vectorize(pyfunc, otypes=None, doc=None, excluded=None, cache=False):** 将普通函数「矢量化」，对每个元素逐个调用（本质仍是循环，主要用于书写简洁而非性能提升）

---

# 四、NumPy 统计函数

> 大多数函数可作为 `ndarray` 的方法调用，也可作为 `numpy.函数名` 的顶层函数调用，axis 参数行为一致。

## 4.1 基本统计函数

- **ndarray.sum / numpy.sum(a, axis=None, dtype=None, out=None):** 沿轴求和
- **ndarray.mean / numpy.mean(a, axis=None):** 沿轴求均值
- **ndarray.std / numpy.std(a, axis=None):** 沿轴求标准差
- **ndarray.var / numpy.var(a, axis=None):** 沿轴求方差
- **ndarray.prod / numpy.prod(a, axis=None, dtype=None):** 沿轴求乘积
- **ndarray.median / numpy.median(a, axis=None):** 沿轴求中位数
- **ndarray.min / numpy.min(a, axis=None):** 沿轴最小值
- **ndarray.max / numpy.max(a, axis=None):** 沿轴最大值
- **ndarray.argmin / numpy.argmin(a, axis=None):** 沿轴最小值索引
- **ndarray.argmax / numpy.argmax(a, axis=None):** 沿轴最大值索引
- **numpy.percentile(a, q, axis=None):** 沿轴计算 q 分位数（0-100）
- **numpy.quantile(a, q, axis=None):** 沿轴计算 q 分位数（0-1）
- **numpy.cumsum / numpy.cumprod(a, axis=None):** 沿轴累积和 / 累积积
- **numpy.ptp(a, axis=None):** 沿轴最大值与最小值之差（peak to peak）
- **numpy.average(a, weights=None, axis=None):** 沿轴加权平均
- **numpy.histogram(a, bins=10, range=None, density=False, weights=None):** 计算直方图

## 4.2 布尔统计函数

- **numpy.any(a, axis=None):** 任意元素为真
- **numpy.all(a, axis=None):** 全部元素为真

---

# 五、NumPy 去重与排序函数

- **numpy.unique(ar, return_counts=False, return_inverse=False, return_index=False, axis=None):** 返回数组中的唯一元素（默认一维），可返回去重计数、首次出现索引、唯一值下标
- **numpy.sort(a, axis=-1, kind='quicksort', order=None):** 沿轴排序（返回新数组）
- **ndarray.sort(axis=-1, kind='quicksort', order=None):** 就地排序
- **numpy.argsort(a, axis=-1, kind='quicksort', order=None):** 返回能令数组有序的索引
- **numpy.lexsort(keys, axis=-1):** 按多个键排序（最后一个键是主键）
- **numpy.partition(a, kth, axis=-1, kind='introselect', order=None):** 类似快排的分区，kth 位置必为有序
- **numpy.argpartition(a, kth, axis=-1, kind='introselect', order=None):** 返回分区索引
- **numpy.searchsorted(a, v, side='left', sorter=None):** 在已排序数组中查找插入位置
- **numpy.count_nonzero(a, axis=None):** 统计非零元素个数

---

# 六、NumPy 矩阵与线性代数

## 6.1 矩阵创建与转换

- **numpy.matrix(data, dtype=None, copy=True):** 创建矩阵对象（ndarray 的子类）
- **numpy.asarray(a, dtype=None, order=None):** 转换为 ndarray（也可用于矩阵）
- **ndarray.view():** 以另一种 dtype 解读内存
- **ndarray.flatten() / ravel():** 摊平为 1 维
- **ndarray.reshape(shape):** 变形
- **ndarray.transpose(axes) / ndarray.T:** 转置

## 6.2 矩阵乘法

- **numpy.dot(a, b, out=None):** 两个数组的点积：1 维为内积、2 维为矩阵乘、高维为张量积
- **numpy.matmul(a, b, out=None):** 矩阵乘积（要求 2 维或兼容广播）
- **ndarray @ ndarray:** `@` 运算符等价于 `numpy.matmul`
- **numpy.tensordot(a, b, axes=2):** 张量点积（沿指定轴）
- **numpy.einsum(subscripts, *operands, out=None):** 爱因斯坦求和约定

## 6.3 矩阵分析

- **numpy.linalg.inv(a):** 计算矩阵逆
- **numpy.linalg.det(a):** 计算行列式
- **numpy.linalg.matrix_rank(a, tol=None):** 计算矩阵秩
- **numpy.linalg.solve(a, b):** 求解线性方程组 ax = b
- **numpy.linalg.lstsq(a, b, rcond='warn'):** 最小二乘解
- **numpy.linalg.qr(a, mode='reduced'):** QR 分解
- **numpy.linalg.svd(a, full_matrices=True, compute_uv=True, hermitian=False):** 奇异值分解
- **numpy.linalg.eig(a):** 特征值与右特征向量
- **numpy.linalg.eigvals(a):** 仅特征值
- **numpy.linalg.norm(x, ord=None, axis=None, keepdims=False):** 矩阵 / 向量范数
- **numpy.linalg.trace(a, offset=0, axis1=0, axis2=1, dtype=None, out=None):** 对角线元素之和

---

# 七、NumPy 输入输出与文件读写

## 7.1 二进制文件

- **numpy.save(file, arr, allow_pickle=True, fix_imports=True):** 将数组保存为 `.npy` 文件
- **numpy.savez(file, *args, **kwds):** 将多个数组保存为 `.npz` 文件
- **numpy.load(file, mmap_mode=None, allow_pickle=False, fix_imports=True, encoding='ASCII'):** 读取 `.npy` / `.npz` 文件
- **numpy.savez_compressed(file, *args, **kwds):** 保存为压缩 `.npz`

## 7.2 文本文件

- **numpy.loadtxt(fname, dtype=float, comments='#', delimiter=None, converters=None, skiprows=0, usecols=None, unpack=False, ndmin=0, encoding='bytes', max_rows=None):** 从文本文件加载数据
- **numpy.savetxt(fname, X, fmt='%.18e', delimiter=' ', newline='\n', header='', footer='', comments='# ', encoding=None):** 将数组保存为文本
- **numpy.genfromtxt(fname, comments='#', delimiter=None, skip_header=0, skip_footer=0, converters=None, missing_values=None, filling_values=None, usecols=None, names=None, excludelist=None, deletechars="".join(sorted(NameValidator.defaultdeletechars)), replace_space='_', autostrip=False, case_sensitive=True, defaultfmt='f%i', unpack=None, usemask=False, loose=True, bad_chars=None, replace_str=None, encoding=None):** 处理缺失值的灵活文本读取
- **numpy.fromstring(string, dtype=float, count=-1, sep=''):** 从字符串解析为数组
- **numpy.fromregex(file, regexp, dtype, encoding=None):** 从文件按正则提取数据

---

# 八、NumPy 广播机制

广播（Broadcasting）描述了 NumPy 在算术运算时如何处理不同形状的数组：

- **核心规则 1**：两个数组维度数不同时，在较小的数组左侧补 1。
- **核心规则 2**：输出数组的形状是各输入数组各维度大小的最大值。
- **核心规则 3**：若某维度上输入大小不一致且都不为 1，则报错。

```python
# 示例：标量与数组广播
a = np.array([[1, 2, 3], [4, 5, 6]])
b = 10
print(a + b)  # 等价于 a + np.full_like(a, 10)

# 示例：列向量与行向量广播
a = np.array([[1], [2], [3]])  # shape (3, 1)
b = np.array([10, 20, 30])       # shape (3,)
print(a + b)
# [[11 21 31]
#  [12 22 32]
#  [13 23 33]]
```

---

# 九、NumPy 线性代数模块（numpy.linalg 速查）

| 函数                    | 作用                       |
| ----------------------- | -------------------------- |
| **numpy.linalg.inv**    | 求矩阵逆                   |
| **numpy.linalg.det**    | 求行列式                   |
| **numpy.linalg.solve**  | 解线性方程组               |
| **numpy.linalg.lstsq**  | 最小二乘解                 |
| **numpy.linalg.qr**     | QR 分解                    |
| **numpy.linalg.svd**    | SVD 分解                   |
| **numpy.linalg.eig**    | 特征值与特征向量           |
| **numpy.linalg.norm**   | 范数                       |
| **numpy.linalg.trace**  | 对角线元素和               |
| **numpy.linalg.matrix_rank** | 矩阵秩                |
| **numpy.linalg.cholesky** | Cholesky 分解            |
| **numpy.linalg.lu**     | LU 分解                    |

---

# 第二部分：Pandas 常用 API

Pandas 是基于 NumPy 的数据分析库，核心是 **Series**（一维带标签数组）和 **DataFrame**（二维表格），并提供完善的数据清洗、聚合、IO、可视化等能力。

---

# 十、pandas.Series 类

## 10.1 Series 简介

`Series` 是 Pandas 一维带标签的数组对象，由**索引**（index，左侧标签）和**值**（values，右侧数据）组成。索引默认从 0 开始递增整数，可指定任意类型（字符串、日期等）。

```python
s = pandas.Series([1, 2, 3, 4])
s = pandas.Series([1, 2, 3], index=['a', 'b', 'c'])
s = pandas.Series({'a': 1, 'b': 2, 'c': 3})   # 字典也可直接创建
```

## 10.2 Series 的常用属性

| 属性名        | 类型           | 描述                              |
| ------------- | -------------- | --------------------------------- |
| **index**     | Index          | 索引（标签数组）                  |
| **values**    | ndarray        | 底层 NumPy 数组                   |
| **dtype**     | numpy.dtype    | 数据类型                          |
| **name**      | str            | Series 名称（DataFrame 列名来源） |
| **size**      | int            | 元素个数                          |
| **shape**     | tuple          | 形状                              |
| **ndim**      | int            | 维度数（恒为 1）                  |
| **strides**   | tuple          | 步长                              |
| **hasnans**   | bool           | 是否包含 NaN                      |
| **is_monotonic_increasing** | bool | 是否单调递增             |
| **is_monotonic_decreasing** | bool | 是否单调递减             |

## 10.3 Series 元素访问

- **s.head(n=5):** 返回前 n 行
- **s.tail(n=5):** 返回后 n 行
- **s.loc[label]:** 按标签取值
- **s.iloc[position]:** 按位置取值
- **s.at[label]:** 单元素快速访问（仅标签）
- **s.iat[position]:** 单元素快速访问（仅位置）

## 10.4 Series 去重与计数

- **s.unique():** 返回数组中的唯一值（返回 ndarray）
- **s.nunique(dropna=True):** 去重计数（值个数）
- **s.value_counts(normalize=False, sort=True, ascending=False, bins=None, dropna=True):** 各值出现次数（降序），`normalize=True` 返回频率

## 10.5 Series 排序与排名

- **s.sort_index(axis=0, ascending=True, inplace=False, kind='quicksort', na_position='last'):** 按索引排序
- **s.sort_values(axis=0, ascending=True, inplace=False, kind='quicksort', na_position='last'):** 按值排序
- **s.rank(axis=0, method='average', ascending=True, na_option='keep', pct=False):** 计算排名（`method` 可选 average/min/max/first/dense）

## 10.6 Series 统计函数

- **s.count():** 非 NaN 元素个数
- **s.sum(axis=None, skipna=True, min_count=0):** 求和
- **s.mean(axis=None, skipna=True):** 均值
- **s.std(axis=None, skipna=True, ddof=1) / s.var():** 标准差 / 方差
- **s.median(axis=None, skipna=True):** 中位数
- **s.quantile(q=0.5, interpolation='linear'):** 分位数
- **s.min() / s.max():** 最小 / 最大
- **s.idxmin() / s.idxmax():** 最小 / 最大值的标签
- **s.describe(percentiles=None, include=None, exclude=None):** 常见统计信息概览
- **s.corr(other, method='pearson', min_periods=None):** 与另一个 Series 的相关系数
- **s.cov(other, min_periods=None):** 与另一个 Series 的协方差

## 10.7 Series 元素替换与映射

- **s.replace(to_replace=None, value=None, inplace=False, limit=None, regex=False, method='pad'):** 替换指定值（`to_replace` 可为标量 / list / dict / regex）
- **s.map(arg, na_action=None):** 用函数或映射关系（dict / Series）逐元素映射
- **s.apply(func, convert_dtype=True, args=(), **kwds):** 自定义函数逐元素 / 整体运算

## 10.8 Series 缺失值处理

- **s.isnull() / s.isna():** 检测缺失值（NaN）
- **s.notnull() / s.notna():** 检测非缺失值
- **s.dropna(axis=0, inplace=False, how=None):** 删除缺失值
- **s.fillna(value=None, method=None, axis=None, inplace=False, limit=None):** 填充缺失值
  - `method=None`/`'ffill'`/`'bfill'`/`'pad'`/`'backfill'`
- **s.interpolate(method='linear', axis=0, limit=None, inplace=False, limit_direction='forward', limit_area=None, downcast=None, **kwargs):** 插值（默认线性）

---

# 十一、pandas.DataFrame 类

## 11.1 DataFrame 简介

`DataFrame` 是 Pandas 二维表格型数据结构，由**行索引**（index）、**列标签**（columns）和**数据**（values）三部分组成，每列可以是不同数据类型。

```python
df = pandas.DataFrame(data, index=None, columns=None, dtype=None, copy=False)
```

可通过 dict、list of dict、list of list、NumPy 数组、Series 等创建。

## 11.2 DataFrame 的常用属性

| 属性名        | 类型           | 描述                          |
| ------------- | -------------- | ----------------------------- |
| **index**     | Index          | 行索引                        |
| **columns**   | Index          | 列索引                        |
| **values**    | ndarray        | 底层 NumPy 数组（同 dtype 时） |
| **dtypes**    | Series         | 每列的数据类型                |
| **shape**     | tuple          | 行数 × 列数                   |
| **size**      | int            | 元素总数（行×列）              |
| **ndim**      | int            | 维度数（恒为 2）              |
| **T**         | DataFrame      | 转置                          |

## 11.3 DataFrame 概览函数

- **df.head(n=5):** 前 n 行
- **df.tail(n=5):** 后 n 行
- **df.info(verbose=None, buf=None, max_cols=None, memory_usage=None, null_counts=None):** 概览：列、dtype、非空数、内存占用
- **df.describe(percentiles=None, include=None, exclude=None):** 数值列统计信息；`include='all'` 适用所有列
- **df.ndim / df.shape / df.size / df.dtypes / df.values / df.index / df.columns:** 见属性表

## 11.4 DataFrame 去重与排序

- **df.drop_duplicates(subset=None, keep='first', inplace=False, ignore_index=False):** 删除重复行
  - `subset`：按指定列判断重复
  - `keep`：`'first'`/`'last'`/`False`（删除所有重复）
- **df.sort_values(by, axis=0, ascending=True, inplace=False, kind='quicksort', na_position='last', ignore_index=False):** 按值排序
- **df.sort_index(axis=0, level=None, ascending=True, inplace=False, kind='quicksort', na_position='last', sort_remaining=True, ignore_index=False):** 按索引排序
- **df.rank(axis=0, method='average', ascending=True, na_option='keep', pct=False):** 排名

## 11.5 DataFrame 聚合统计

- **df.sum(axis=0, skipna=True, min_count=0):** 求和
- **df.mean(axis=0, skipna=True):** 均值
- **df.median(axis=0, skipna=True):** 中位数
- **df.std(axis=0, skipna=True, ddof=1) / df.var():** 标准差 / 方差
- **df.min(axis=0, skipna=True) / df.max(axis=0, skipna=True):** 最小 / 最大
- **df.cumsum(axis=None) / df.cumprod(axis=None):** 累积和 / 累积积
- **df.cummax(axis=None) / df.cummin(axis=None):** 累积最大值 / 累积最小值
- **df.diff(periods=1, axis=0):** 离散差分
- **df.idxmin(axis=0, skipna=True) / df.idxmax(axis=0, skipna=True):** 最小 / 最大值所在行 / 列索引
- **df.corr(method='pearson', min_periods=1):** 相关系数矩阵
- **df.cov(min_periods=None, ddof=1):** 协方差矩阵

## 11.6 DataFrame 元素替换与函数应用

- **df.replace(to_replace=None, value=None, inplace=False, limit=None, regex=False, method='pad'):** 替换元素（支持 dict / list / regex）
- **df.apply(func, axis=0, raw=False, result_type=None, args=(), **kwds):** 沿轴应用函数（axis=0 按列、axis=1 按行）
- **df.applymap(func, na_action=None, \**kwargs):** 逐元素应用函数（仅 DataFrame）

## 11.7 DataFrame 缺失值处理

- **df.isnull() / df.isna():** 缺失值检测
- **df.notnull() / df.notna():** 非缺失值检测
- **df.dropna(axis=0, how='any', thresh=None, subset=None, inplace=False):** 删除缺失行 / 列
  - `axis`=0 删除行，=1 删除列
  - `how='any'` 任一缺失即删除；`'all'` 全部缺失才删除
  - `subset`：判断缺失的列集合
  - `thresh`：保留至少 N 个非缺失值的行 / 列
- **df.fillna(value=None, method=None, axis=None, inplace=False, limit=None, downcast=None):** 填充
  - `value` 可为标量 / dict / Series / DataFrame
  - `method=None`/`'ffill'`/`'bfill'`/`'pad'`/`'backfill'`
  - `limit`：最多填充连续 NaN 的个数
- **df.ffill(axis=0) / df.bfill(axis=0):** 向前 / 向后填充的便捷方法（Pandas 2.x 新增）
- **df.interpolate(method='linear', axis=0, limit=None, inplace=False, limit_direction='forward', limit_area=None, downcast=None, **kwargs):** 插值
  - `method` 选项：`linear`/`time`/`index`/`values`/`nearest`/`zero`/`slinear`/`quadratic`/`cubic`/`barycentric`/`krogh`/`polynomial`/`spline`/`piecewise_polynomial`/`from_derivatives`/`pchip`/`akima`

---

# 十二、DataFrame 结构修改

## 12.1 插入列

- **df.insert(loc, column, value, allow_duplicates=False):** 在指定位置 `loc` 插入新列
  - `loc`：插入位置（0 ~ ncols）
  - `column`：列名
  - `value`：值（标量 / 数组 / Series）

## 12.2 重命名

- **df.rename(mapper=None, index=None, columns=None, axis=None, copy=True, inplace=False, level=None, errors='ignore'):** 重命名索引或列（支持函数或 dict）
- **df.set_index(keys, drop=True, append=False, inplace=False, verify_integrity=False):** 将一列或多列设为行索引
- **df.reset_index(level=None, drop=False, inplace=False, col_level=0, col_fill='', allow_duplicates=False, names=None):** 重置索引，`drop=True` 丢弃旧索引

## 12.3 删除

- **df.drop(labels=None, axis=0, index=None, columns=None, level=None, inplace=False, errors='raise'):** 删除指定行 / 列
  - `labels`：按标签删除
  - `axis`=0 删除行，=1 删除列
  - `errors='raise'`/`'ignore'`：标签不存在时是否报错

## 12.4 选择（定位）

- **df.loc[row_label, col_label]:** 基于标签的索引器，支持单标签、列表、切片、布尔数组
- **df.iloc[row_pos, col_pos]:** 基于整数位置的索引器
- **df.at[row_label, col_label]:** 单元素快速访问（仅标签）
- **df.iat[row_pos, col_pos]:** 单元素快速访问（仅位置）

## 12.5 合并与拼接

- **pandas.concat(objs, axis=0, join='outer', ignore_index=False, keys=None, levels=None, names=None, sort=False, verify_integrity=False, copy=True):** 沿轴拼接多个 DataFrame / Series
  - `axis=0` 纵向堆叠（默认），`axis=1` 横向拼接
  - `join='outer'`（默认，并集）/ `'inner'`（交集）
  - `ignore_index=True` 重置索引
- **pandas.merge(left, right, how='inner', on=None, left_on=None, right_on=None, left_index=False, right_index=False, sort=False, suffixes=('_x', '_y'), copy=True, indicator=False, validate=None):** 类似 SQL JOIN 的合并
  - `how`：`'inner'`/`'outer'`/`'left'`/`'right'`/`'cross'`
  - `on`：连接键；`left_on`/`right_on` 左右不同名时
- **df.join(other, on=None, how='left', lsuffix='', rsuffix='', sort=False, validate=None):** 按索引拼接的便捷方法

---

# 十三、Pandas 数据导入导出

Pandas 支持多种数据源的读入 / 写出，常用 API 成对出现。

## 13.1 导入（read_*）函数

> 通用参数 `header`/`names`/`index_col`/`dtype`/`nrows`/`encoding`/`parse_dates`/`na_values` 等适用于所有 reader。

- **pandas.read_csv(filepath_or_buffer, sep=',', delimiter=None, header='infer', names=None, index_col=None, usecols=None, dtype=None, nrows=None, skiprows=None, skip_blank_lines=True, na_values=None, keep_default_na=True, parse_dates=False, encoding=None, **kwds):** 读取 CSV / TSV
- **pandas.read_table(filepath_or_buffer, sep='\t', **kwargs):** 读表格文件，默认 tab 分隔（已不推荐，优先用 read_csv + sep）
- **pandas.read_excel(io, sheet_name=0, header=0, names=None, index_col=None, usecols=None, dtype=None, parse_dates=False, engine=None, converters=None, true_values=None, false_values=None, squeeze=False, **kwds):** 读取 Excel（需 openpyxl / xlrd）
  - `sheet_name` 可为字符串、整数、列表（返回 dict）
- **pandas.read_json(path_or_buf, orient=None, typ='frame', dtype=True, convert_axes=True, convert_dates=True, keep_default_dates=True, numpy=False, precise_float=False, date_unit=None, encoding=None, lines=False, chunksize=None, compression='infer', **kw):** 读取 JSON
- **pandas.read_sql(sql, con, index_col=None, coerce_float=True, params=None, parse_dates=None, columns=None, chunksize=None, **kwds):** 通过 SQLAlchemy 等读取数据库表 / SQL 查询
- **pandas.read_sql_query(sql, con, **kwargs):** 仅读 SQL 查询
- **pandas.read_sql_table(table_name, con, schema=None, index_col=None, coerce_float=True, parse_dates=None, columns=None, chunksize=None, **kwargs):** 仅读整张数据库表
- **pandas.read_html(io, match='.+', flavor=None, header=None, index_col=None, skiprows=None, attrs=None, parse_dates=False, thousands=',', decimal='.', converters=None, na_values=None, keep_default_na=True, displayed_only=True):** 读取 HTML 中的表格（需 lxml / html5lib / beautifulsoup4）
- **pandas.read_xml(path_or_buffer, xpath='./*', namespaces=None, elems_only=False, attrs_only=False, parser='lxml', **kwds):** 读取 XML
- **pandas.read_clipboard(**kwargs):** 读取剪贴板内容（类似 read_csv）
- **pandas.read_pickle(filepath_or_buffer, compression='infer', storage_options=None):** 读取 pickle 二进制（Python 对象序列化）
- **pandas.read_parquet(path, engine='auto', columns=None, storage_options=None, use_nullable_dtypes=False, **kwargs):** 读取 Parquet 列存格式（需 pyarrow / fastparquet）
- **pandas.read_feather(path, columns=None, use_threads=True, storage_options=None):** 读取 Feather（二进制列存）

## 13.2 导出（to_*）方法

- **df.to_csv(path_or_buf=None, sep=',', na_rep='', float_format=None, columns=None, header=True, index=True, index_label=None, mode='w', encoding=None, compression='infer', quoting=None, quotechar='"', line_terminator=None, chunksize=None, date_format=None, doublequote=True, escapechar=None, decimal='.'):** 写出 CSV
- **df.to_excel(excel_writer, sheet_name='Sheet1', na_rep='', float_format=None, columns=None, header=True, index=True, index_label=None, startrow=0, startcol=0, engine=None, merge_cells=True, encoding=None, inf_rep='inf', freeze_panes=None, storage_options=None):** 写出 Excel（需 openpyxl / xlsxwriter）
- **df.to_json(path_or_buf=None, orient=None, date_format=None, double_precision=10, force_ascii=True, date_unit='ms', default_handler=None, lines=False, compression='infer', index=True, storage_options=None):** 写出 JSON
- **df.to_sql(name, con, schema=None, if_exists='fail', index=True, index_label=None, chunksize=None, dtype=None, method=None):** 写出到数据库（需 SQLAlchemy）
- **df.to_pickle(path, compression='infer', protocol=5, storage_options=None):** 序列化为 pickle
- **df.to_parquet(path=None, engine='auto', compression='snappy', index=None, partition_cols=None, storage_options=None, **kwargs):** 写出 Parquet
- **df.to_feather(path, **kwargs):** 写出 Feather
- **df.to_dict(orient='dict', into=<class 'dict'>):** 转字典
- **df.to_records(index=True, column_dtypes=None, index_dtypes=None):** 转结构化 ndarray（numpy.rec.fromrecords）
- **df.to_numpy(dtype=None, copy=False, na_value=_NoDefault.no_default):** 转 NumPy ndarray
- **df.to_markdown(buf=None, mode='wt', index=True, storage_options=None, **kwargs):** 转 Markdown 表格
- **df.to_latex(buf=None, column_format=None, longtable=None, escape=False, encoding='utf-8', multicolumn=None, multicolumn_format=None, multirow=None, caption=None, label=None, position=None, **kwargs):** 转 LaTeX
- **df.to_html(buf=None, columns=None, col_space=None, header=True, index=True, na_rep='NaN', formatters=None, float_format=None, sparsify=None, index_names=True, justify=None, max_rows=None, max_cols=None, show_dimensions=False, decimal='.', bold_rows=True, classes=None, escape=True, notebook=False, border=None, table_id=None, render_links=False, encoding=None):** 转 HTML
- **df.to_clipboard(**kwargs):** 复制到剪贴板
- **df.to_xarray(**kwargs):** 转 xarray

---

# 十四、Pandas 日期时间处理

## 14.1 时间类型

| 类型           | 作用                                  |
| -------------- | ------------------------------------- |
| **Timestamp**  | 单个时间点（datetime64[ns] 的封装）   |
| **Timedelta**  | 时间间隔（timedelta64[ns] 的封装）    |
| **Period**     | 时间周期（含频率，如月度、季度）      |
| **datetime64** | NumPy 底层时间戳类型                  |
| **timedelta64**| NumPy 底层时间差类型                  |

### 14.1.1 Timestamp 属性

- **Timestamp.year / month / day:** 年 / 月 / 日
- **Timestamp.hour / minute / second / microsecond:** 时 / 分 / 秒 / 微秒
- **Timestamp.dayofweek / dayofyear / weekofyear:** 周中日 / 年中日 / 年中周
- **Timestamp.quarter:** 季度
- **Timestamp.weekday_name:** 周几名称
- **Timestamp.date():** 转为 `datetime.date`
- **Timestamp.time():** 转为 `datetime.time`
- **Timestamp.tz_localize(tz):** 本地化时区
- **Timestamp.tz_convert(tz):** 转换时区

### 14.1.2 Timedelta 属性

- **Timedelta.days / seconds / microseconds / nanoseconds:** 各单位数值
- **Timedelta.total_seconds():** 总秒数
- **Timedelta.components:** 拆解为 days / hours / minutes / seconds / milliseconds / microseconds / nanoseconds

### 14.1.3 构造函数

- **pandas.Timestamp(value, freq=None, tz=None, unit=None, year=None, month=None, day=None, hour=None, minute=None, second=None, microsecond=None, nanosecond=None, tzinfo=None, **kwargs):** 创建时间戳
  - `value` 可为 str、datetime、date 等
- **pandas.Timedelta(value, unit=None, **kwargs):** 创建时间差
  - `value` 可为 str、int、timedelta 等
  - `unit`：`'Y'`/`'M'`/`'W'`/`'D'`/`'days'`/`'hours'`/`'minutes'`/`'seconds'`/`'milliseconds'`/`'microseconds'`/`'nanoseconds'`
- **pandas.Period(value=None, freq=None, ordinal=None, year=None, month=None, quarter=None, day=None, hour=None, minute=None, second=None, freq_offset=None):** 创建周期
  - `freq`：`'Y'`/`'Q'`/`'M'`/`'W'`/`'D'`/`'h'` 等

## 14.2 日期索引的生成

- **pandas.date_range(start=None, end=None, periods=None, freq=None, tz=None, normalize=False, name=None, inclusive='both', **kwargs):** 生成固定频率的日期时间索引 DatetimeIndex
  - `start`/`end`：起止时间
  - `periods`：生成数量
  - `freq`：频率，默认 `'D'`（每日），`'B'`（工作日），`'h'`（小时），`'ME'`（月末），`'YE'`（年末），`'W'`（周）等
  - `tz`：时区
  - `normalize=True` 归一化为 00:00:00
  - `inclusive='both'`/`'left'`/`'right'`/`'neither'`：是否包含端点
- **pandas.timedelta_range(start=None, end=None, periods=None, freq=None, name=None, closed=None, **kwargs):** 生成 TimedeltaIndex
- **pandas.period_range(start=None, end=None, periods=None, freq=None, name=None):** 生成 PeriodIndex
- **pandas.interval_range(start=None, end=None, periods=None, freq=None, name=None, closed='right'):** 生成 IntervalIndex
- **pandas.to_datetime(arg, errors='raise', dayfirst=False, yearfirst=False, utc=None, format=None, exact=True, unit=None, infer_datetime_format=False, origin='unix', cache=True):** 将字符串 / 时间戳 / 整数等解析为 datetime 类型
- **pandas.to_timedelta(arg, unit=None, errors='raise'):** 解析为 Timedelta

## 14.3 dt 访问器（Series 专用）

> 仅对 datetime / timedelta / period 类型的 Series 生效，支持批量提取时间属性。

### 14.3.1 datetime 类型属性的方法

- **Series.dt.year / month / day / hour / minute / second:** 年 / 月 / 日 / 时 / 分 / 秒
- **Series.dt.dayofweek / dayofyear / weekofyear / weekday:** 周中日 / 年中日 / 周序号
- **Series.dt.quarter / week / days_in_month / daysinmonth:** 季度 / 周序号 / 月天数
- **Series.dt.date / Series.dt.time:** 转 date / time
- **Series.dt.is_month_start / is_month_end:** 是否月初 / 月末
- **Series.dt.is_quarter_start / is_quarter_end:** 是否季度初 / 季度末
- **Series.dt.is_year_start / is_year_end:** 是否年初 / 年末
- **Series.dt.is_leap_year:** 是否闰年

### 14.3.2 操作方法

- **Series.dt.to_period(freq=None):** datetime → Period
- **Series.dt.to_pydatetime():** datetime → Python datetime 数组
- **Series.dt.tz_localize(tz, ambiguous='raise', nonexistent='raise'):** 本地化时区
- **Series.dt.tz_convert(tz):** 转换时区
- **Series.dt.normalize():** 时间归一化为 00:00:00
- **Series.dt.strftime(date_format):** 格式化输出（接受 strftime 格式串）
- **Series.dt.round(freq, ambiguous='raise', nonexistent='raise'):** 按频率舍入
- **Series.dt.floor(freq, ambiguous='raise', nonexistent='raise'):** 按频率向下取整
- **Series.dt.ceil(freq, ambiguous='raise', nonexistent='raise'):** 按频率向上取整
- **Series.dt.total_seconds():** timedelta 转总秒数
- **Series.dt.components:** timedelta 拆分（days/hours/minutes/...）

## 14.4 时间相关 DataFrame 方法

- **df.between_time(start_time, end_time, include_start=True, include_end=True, axis=None):** 选出指定时段内的行（索引为时间）
- **df.at_time(time, asof=False, axis=None):** 选出指定时间点的行（每日统一）
- **df.first(offset) / df.last(offset):** 选取前 / 后一段时间的数据（基于时间索引）
- **df.tshift(periods=1, freq=None, axis=0):** 时间索引整体平移（不移数据）
- **df.shift(periods=1, freq=None, axis=0, fill_value=None):** 数据平移（可指定 fill_value 填充缺失）

## 14.5 重采样

> 索引必须是 DatetimeIndex / TimedeltaIndex / PeriodIndex。

- **df.resample(rule, axis=0, closed=None, label=None, convention='start', kind=None, loffset=None, base=None, origin='start_day', offset=None):** 重采样对象，`rule` 为频率字符串
- **Resampler.sum() / mean() / median() / max() / min() / std() / var() / count() / size() / first() / last() / nth() / ohlc():** 降采样常用聚合函数
- **Resampler.agg(func, *args, **kwargs):** 多聚合
- **Resampler.asfreq(fill_value=None):** 不聚合，直接按新频率取值（缺失用 NaN 或 fill_value）
- **Resampler.ffill() / Resampler.bfill():** 升采样向前 / 向后填充
- **Resampler.interpolate(method='linear', **kwargs):** 升采样插值
- **Resampler.transform(func, *args, **kwargs):** 行级变换（保持原 shape）

---

# 十五、Pandas 数据聚合与分组

## 15.1 groupby 分组

- **df.groupby(by=None, axis=0, level=None, as_index=True, sort=True, group_keys=True, squeeze=False, observed=False, dropna=True):** 按指定列、字典、Series、函数或多重键分组，返回 `DataFrameGroupBy` 对象
  - `by`：列名 / list of 列名 / dict / Series / function
  - `axis`：0 按行分组，1 按列分组
  - `as_index=False`：分组键不作为索引
  - `dropna=True`：排除 NaN 键
- **DataFrameGroupBy 对象常用属性 / 方法：**
  - **gb.groups:** 返回 dict，`{group_key: 索引集合}`
  - **gb.indices:** 返回 dict，`{group_key: 索引数组}`
  - **gb.ngroups:** 分组数
  - **gb.size():** 每组大小（Series）
  - **gb.count():** 每组非空元素数
  - **gb.get_group(name):** 获取指定组的内容

## 15.2 常用内置聚合函数（直接调用即可）

> 数值统计类（按组统计后每组返回标量）：`sum`、`mean`、`median`、`min`、`max`、`std`、`var`、`count`、`size`、`quantile`、`describe`、`first`、`last`、`nth(n)`、`nunique`、`prod`、`sem`、`skew`、`kurt`、`cumsum`、`cumprod`、`cummax`、`cummin`

## 15.3 agg / aggregate 聚合

- **DataFrameGroupBy.agg(arg, *args, **kwargs):** 灵活的聚合，支持多种形式
  - **形式 1**：字符串聚合 `gb.agg("sum")`
  - **形式 2**：列表聚合 `gb.agg(["mean", "max", "min", "count"])`
  - **形式 3**：字典聚合（按列不同函数）`gb.agg({"salary": "mean", "hiredate": "min", "commission_pct": "max"})`
  - **形式 4**：命名聚合 `gb.agg(平均薪资=("salary", "mean"), 人数=("eid", "count"))`
  - **形式 5**：自定义函数 `gb.agg({"salary": calculate_age})`（接收每组的 Series）

## 15.4 transform 转换

- **DataFrameGroupBy.transform(func, *args, **kwargs):** 对每个分组应用函数，结果**广播回原数据的每一行**（与 agg 不同，不会降维）
  - 使用场景：组内标准化、组内填充、组内排名等
  - 支持 str / 函数 / list of functions / dict

## 15.5 filter 过滤

- **DataFrameGroupBy.filter(func, dropna=True, *args, **kwargs):** 按组筛选
  - **func(子 DataFrame) → bool：** 返回 True 保留该组所有行，False 删除整个组
  - 入选函数入参必须是**分组子表**，可在内部使用任意聚合

## 15.6 apply 自定义函数

- **DataFrameGroupBy.apply(func, *args, **kwargs):** 最灵活的方法，对每个分组调用 func，可返回标量 / Series / DataFrame
  - 注意：在较新 Pandas 版本中可能产生变形警告，建议优先用 agg / transform / filter

## 15.7 离散化分箱（cut/qcut）

- **pandas.cut(x, bins, right=True, labels=None, retbins=False, precision=3, include_lowest=False, duplicates='raise', ordered=True):** 按边界分箱（详见下表）
- **pandas.qcut(x, q, labels=None, retbins=False, precision=3, duplicates='raise'):** 按分位数分箱

### pandas.cut 参数详解

| 参数名            | 默认值 | 类型                              | 核心作用                                                                       |
| ----------------- | ------ | --------------------------------- | ------------------------------------------------------------------------------ |
| **x**             | 必填   | 一维数组 / Series                 | 要分箱的数据源，必须是数值型                                                   |
| **bins**          | 必填   | int / list / IntervalIndex        | 分箱区间规则：整数→自动均分；列表→自定义分割点；IntervalIndex→预定义区间      |
| **right**         | True   | bool                              | 区间闭合方向：True→`right`(a,b]；False→`left`[a,b)                            |
| **labels**        | None   | None / list / False               | 自定义标签：None→返回区间字符串；list→自定义标签；False→整数编码              |
| **retbins**       | False  | bool                              | 是否返回分箱的边界数组                                                         |
| **precision**     | 3      | int                               | 区间边界小数精度                                                               |
| **include_lowest**| False  | bool                              | 是否包含最小值                                                                 |
| **duplicates**    | 'raise'| str                               | 重复分割点处理：`'raise'`报错，`'drop'`去重                                    |
| **ordered**       | True   | bool                              | 返回的分类标签是否为有序类别                                                   |

---

# 十六、Pandas 其他常用 API 速查

## 16.1 索引操作类

- **df.set_index(keys, drop=True, append=False, inplace=False, verify_integrity=False):** 列 → 索引
- **df.reset_index(level=None, drop=False, inplace=False, ...):** 索引 → 列
- **df.reindex(labels=None, axis=None, index=None, columns=None, method=None, fill_value=np.nan, limit=None, ...):** 按新索引重排，缺失填 fill_value / method
- **df.reindex_like(other, method=None, copy=True, limit=None, tolerance=None, fill_value=nan):** 与另一个对象的索引对齐
- **df.rename(mapper=None, index=None, columns=None, axis=None, ...):** 重命名
- **df.rename_axis(mapper=None, index=None, columns=None, axis=None, copy=True, inplace=False):** 重命名索引 / 列 的名称

## 16.2 数据对齐与算术

- **df.add(other, axis='columns', level=None, fill_value=None):** 加法（支持按行 / 列）
- **df.sub / df.mul / df.div(other, ...):** 减 / 乘 / 除
- **df.radd / df.rsub / df.rmul / df.rdiv:** 反向运算

## 16.3 数据重塑

- **df.melt(id_vars=None, value_vars=None, var_name=None, value_name='value', col_level=None, ignore_index=True):** 宽表 → 长表
- **df.pivot(index=None, columns=None, values=None):** 长表 → 宽表（无聚合）
- **df.pivot_table(values=None, index=None, columns=None, aggfunc='mean', fill_value=None, margins=False, dropna=True, margins_name='All', observed=False, sort=True):** 类似 Excel 数据透视表（带聚合）
- **df.stack(level=-1, dropna=True, sort=True, future_stack=False):** 列索引旋转为行索引
- **df.unstack(level=-1, fill_value=None, sort=True):** 行索引旋转为列索引
- **df.explode(column, ignore_index=False):** 列表型列展开为多行

## 16.4 函数应用与映射

- **df.pipe(func, *args, **kwargs):** 函数式管道
- **df.apply(func, axis=0, ...):** 沿轴应用
- **df.applymap(func, na_action=None, **kwargs):** 元素级
- **Series.map(arg, na_action=None):** 元素级（Series）

## 16.5 缺失值与异常值处理

- **df.isnull() / df.isna():** 检测 NaN
- **df.notnull() / df.notna():** 反向
- **df.dropna():** 丢弃缺失
- **df.fillna(value, method=None, ...):** 填充
- **df.ffill() / df.bfill():** 便捷填充
- **df.interpolate(method='linear', ...):** 插值
- **df.replace(to_replace, value):** 替换任意值

## 16.6 数据类型转换

- **df.astype(dtype, copy=False, errors='raise'):** 转换列类型
- **df.convert_dtypes(infer_objects=True, convert_string=True, convert_integer=True, convert_boolean=True, convert_floating=True, dtype_backend='numpy_nullable'):** 自动推断最佳类型
- **df.infer_objects(copy=True):** 推断 object 列的最佳类型
- **df.to_numpy():** 转 ndarray
- **Series.to_numpy():** 转 ndarray
- **df.to_dict():** 转 dict

## 16.7 字符串处理（df.str / Series.str）

- **Series.str.contains(pat, case=True, flags=0, na=None, regex=True):** 是否包含子串
- **Series.str.startswith(pat, na=None) / endswith(pat, na=None):** 前缀 / 后缀匹配
- **Series.str.replace(pat, repl, n=-1, case=None, flags=0, regex=None):** 替换
- **Series.str.split(pat=None, n=-1, expand=False, regex=None):** 拆分
- **Series.str.cat(others=None, sep=None, na_rep=None, join='left'):** 拼接
- **Series.str.strip(to_strip=None) / lstrip / rstrip:** 去空格
- **Series.str.upper() / lower() / title() / capitalize() / swapcase():** 大小写
- **Series.str.len():** 字符串长度
- **Series.str.find(sub, start=0, end=None):** 查找子串位置
- **Series.str.extract(pat, flags=0, expand=True):** 正则提取
- **Series.str.get(i):** 取第 i 个字符
- **Series.str.pad(width, side='left', fillchar=' '):** 填充至定长
- **Series.str.slice(start=None, stop=None, step=None):** 切片

## 16.8 窗口函数

- **df.rolling(window, min_periods=None, center=False, win_type=None, on=None, axis=0, closed=None):** 滑动窗口
  - **Rolling.sum() / mean() / std() / var() / min() / max() / median() / corr() / cov():** 窗口统计
- **df.expanding(min_periods=1, axis=0, method='single'):** 扩展窗口
- **df.ewm(com=None, span=None, halflife=None, alpha=None, min_periods=0, adjust=True, ignore_na=False, axis=0):** 指数加权移动

## 16.9 分类数据（Categorical）

- **pandas.Categorical(values, categories=None, ordered=None, dtype=None, fastpath=False):** 创建分类类型
- **pandas.CategoricalDtype(categories=None, ordered=False):** 分类类型 dtype
- **df.astype('category') / Series.astype('category'):** 转分类类型
- **Series.cat.categories:** 类别的索引
- **Series.cat.ordered:** 是否有序
- **Series.cat.codes:** 编码为整数
- **Series.cat.rename_categories(new_categories):** 重命名类别
- **Series.cat.reorder_categories(new_categories, ordered=None):** 重排类别
- **Series.cat.add_categories(new_categories):** 增类别
- **Series.cat.remove_categories(removals):** 删类别
- **Series.cat.set_categories(new_categories, ordered=None, rename=False):** 重置类别

---

