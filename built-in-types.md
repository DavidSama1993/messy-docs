# 内置类型

以下部分描述了解释器中内置的标准类型。

主要的内置类型有数字、序列、映射、类、实例和异常。

有些集合类是可变的。原地添加、减去或重新排列其成员的方法（并且不返回特定项）永远不会返回集合实例本身，而是返回 `None`。

有些操作受多种对象类型支持；实际上，几乎所有对象都可以比较相等性、检测真值，以及转换为字符串（使用 `repr()` 函数或略有差异的 `str()` 函数）。后一函数在对象被 `print()` 函数输出时会被隐式地使用。

## 真值检测

任何对象都可以进行真值检测，以便在 `if` 或 `while` 条件中使用，或者作为下述布尔运算的操作数。

默认情况下，一个对象被视为真值，除非其类在调用该对象时定义了返回 `False` 的 `__bool__()` 方法，或者定义了返回零的 `__len__()` 方法。[1] 如果调用其中任一方法时抛出异常，则该异常会被传播，并且该对象没有真值（例如 `NotImplemented`）。以下列出的是大多数被视为假值的内置对象：

- 被定义为假值的常量：`None` 和 `False`
- 任何数值类型的零：`0`、`0.0`、`0j`、`Decimal(0)`、`Fraction(0, 1)`
- 空的序列和集合：`''`、`()`、`[]`、`{}`、`set()`、`range(0)`

产生布尔结果的操作和内置函数总是返回 `0` 或 `False` 作为假值，返回 `1` 或 `True` 作为真值，除非另有说明。（重要的例外：布尔运算 `or` 和 `and` 总是返回其中一个操作数。）

## 布尔运算 --- and、or、not

以下是按优先级升序排列的布尔运算：

| 运算 | 结果 | 备注 |
| --- | --- | --- |
| `x or y` | 若 x 为真值，则为 x，否则为 y | (1) |
| `x and y` | 若 x 为假值，则为 x，否则为 y | (2) |
| `not x` | 若 x 为假值，则为 `True`，否则为 `False` | (3) |

备注：

1. 这是短路运算符，因此它只在第一个参数为假值时才会对第二个参数求值。
2. 这是短路运算符，因此它只在第一个参数为真值时才会对第二个参数求值。
3. `not` 的优先级低于非布尔运算符，因此 `not a == b` 会被解释为 `not (a == b)`，而 `a == not b` 是语法错误。

## 比较运算

Python 中有八种比较运算。它们都具有相同的优先级（高于布尔运算的优先级）。比较运算可以任意串联；例如，`x < y <= z` 等价于 `x < y and y <= z`，除了 y 只会被求值一次（但在两种情况下，当 `x < y` 为假时，z 都完全不会被求值）。

此表格汇总了比较运算：

| 运算 | 含义 |
| --- | --- |
| `<` | 严格小于 |
| `<=` | 小于或等于 |
| `>` | 严格大于 |
| `>=` | 大于或等于 |
| `==` | 等于 |
| `!=` | 不等于 |
| `is` | 对象标识 |
| `is not` | 否定的对象标识 |

除非另有说明，不同类型的对象比较时永远不会相等。`==` 运算符总是有定义的，但对于某些对象类型（例如类对象）它等价于 `is`。`<`、`<=`、`>` 和 `>=` 运算符仅在其有意义的地方被定义；例如，当其中一个参数是复数时，它们会抛出 `TypeError` 异常。

类的非同一实例通常会比较为不相等，除非该类定义了 `__eq__()` 方法。

一个类的实例不能相对于同一类的其他实例或其他类型的对象进行排序，除非该类定义了足够多的 `__lt__()`、`__le__()`、`__gt__()` 和 `__ge__()` 方法（一般来说，如果你想要比较运算符的常规含义，`__lt__()` 和 `__eq__()` 就足够了）。

`is` 和 `is not` 运算符的行为无法自定义；此外，它们可以应用于任意两个对象，并且永远不会抛出异常。

另外两种具有相同语法优先级的运算 `in` 和 `not in`，由可迭代类型或实现了 `__contains__()` 方法的类型支持。

## 数字类型 --- int、float、complex

存在三种不同的数字类型：整数、浮点数和复数。此外，布尔值是整数的子类型。整数具有无限的精度。浮点数通常使用 C 中的 `double` 来实现；有关你的程序所运行机器上浮点数的精度和内部表示的信息，可在 `sys.float_info` 中查看。复数具有实部和虚部，每部分都是一个浮点数。要从复数 z 中提取这些部分，可使用 `z.real` 和 `z.imag`。（标准库包含其他数字类型：用于有理数的 `fractions.Fraction`，以及用于用户可自定义精度的浮点数的 `decimal.Decimal`。）

数字由数字字面值或内置函数与运算符的运算结果创建。不带修饰的整数字面值（包括十六进制、八进制和二进制数）会生成整数。包含小数点或指数符号的数字字面值会生成浮点数。在数字字面值末尾附加 `'j'` 或 `'J'` 会生成虚数（实部为零的复数），你可以将其与整数或浮点数相加来得到具有实部和虚部的复数。

`int()`、`float()` 和 `complex()` 构造器可用来生成特定类型的数字。

Python 完全支持混合算术运算：当一个二元算术运算符的操作数具有不同的内置数字类型时，具有"较窄"类型的操作数会被拓宽为另一操作数的类型：

- 如果两个参数都是复数，则不执行转换；
- 如果任一参数是复数或浮点数，则另一个参数会被转换为浮点数；
- 否则，两者必须都是整数，不需要转换。

具有复数与实数操作数的算术运算由通常的数学公式定义，例如：

```python
x + complex(u, v) = complex(x + u, v)
x * complex(u, v) = complex(x * u, x * v)
```

不同类型数字之间的比较行为如同在比较这些数字的精确值。[2]

所有数字类型（`complex` 除外）都支持下列运算（运算的优先级请参阅 运算符优先级）：

| 运算 | 结果 | 备注 | 完整文档 |
| --- | --- | --- | --- |
| `x + y` | x 与 y 之和 | | |
| `x - y` | x 与 y 之差 | | |
| `x * y` | x 与 y 之积 | | |
| `x / y` | x 与 y 之商 | | |
| `x // y` | x 与 y 的向下取整商 | (1)(2) | |
| `x % y` | `x / y` 的余数 | (2) | |
| `-x` | x 取负 | | |
| `+x` | x 不变 | | |
| `abs(x)` | x 的绝对值或大小 | | `abs()` |
| `int(x)` | 将 x 转换为整数 | (3)(6) | `int()` |
| `float(x)` | 将 x 转换为浮点数 | (4)(6) | `float()` |
| `complex(re, im)` | 一个实部为 re、虚部为 im 的复数。im 默认为零。 | (6) | `complex()` |
| `c.conjugate()` | 复数 c 的共轭 | | |
| `divmod(x, y)` | `(x // y, x % y)` 对 | (2) | `divmod()` |
| `pow(x, y)` | x 的 y 次幂 | (5) | `pow()` |
| `x ** y` | x 的 y 次幂 | (5) | |

备注：

1. 也被称为整数除法。对于 `int` 类型的操作数，结果类型为 `int`。对于 `float` 类型的操作数，结果类型为 `float`。一般来说，结果是一个整数，但结果的类型不一定是 `int`。结果总是向负无穷方向舍入：`1//2` 为 `0`，`(-1)//2` 为 `-1`，`1//(-2)` 为 `-1`，而 `(-1)//(-2)` 为 `0`。
2. 不适用于复数。如有需要，请改用 `abs()` 转换为浮点数。
3. 从 `float` 转换为 `int` 会截断，丢弃小数部分。有关其他转换方式，请参阅函数 `math.floor()` 和 `math.ceil()`。
4. `float` 也接受字符串 `"nan"` 和 `"inf"`，并带有可选前缀 `"+"` 或 `"-"`，分别表示非数值（NaN）以及正或负无穷大。
5. Python 将 `pow(0, 0)` 和 `0 ** 0` 定义为 `1`，这是编程语言的普遍做法。
6. 接受的数字字面值包括数字 0 到 9 或任何 Unicode 等效字符（具有 Nd 属性的码位）。

   参见 Unicode 标准以获取具有 Nd 属性的码位的完整列表。

所有 `numbers.Real` 类型（`int` 和 `float`）还包括下列运算：

| 运算 | 结果 |
| --- | --- |
| `math.trunc(x)` | 将 x 截断为 `Integral` |
| `round(x[, n])` | 将 x 舍入到 n 位小数，采用半数舍入到偶数的规则。如果省略 n，则默认为 0。 |
| `math.floor(x)` | 小于等于 x 的最大 `Integral` |
| `math.ceil(x)` | 大于等于 x 的最小 `Integral` |

有关其他数字运算，请参阅 `math` 和 `cmath` 模块。

### 整数类型的按位运算

按位运算只对整数有意义。按位运算的结果是按照使用无限个符号位的二进制补码形式进行运算而计算出来的。

二元按位运算的优先级全部低于数字运算，但高于比较运算；一元运算 `~` 与其他一元数字运算（`+` 和 `-`）具有相同的优先级。

此表格按优先级升序列出了按位运算：

| 运算 | 结果 | 备注 |
| --- | --- | --- |
| `x \| y` | x 与 y 按位或 | (4) |
| `x ^ y` | x 与 y 按位异或 | (4) |
| `x & y` | x 与 y 按位与 | (4) |
| `x << n` | x 左移 n 位 | (1)(2) |
| `x >> n` | x 右移 n 位 | (1)(3) |
| `~x` | x 的各位取反 | |

备注：

1. 负的移位数是非法的，会引发 `ValueError`。
2. 左移 n 位等价于乘以 `pow(2, n)`。
3. 右移 n 位等价于除以 `pow(2, n)` 的向下取整除法。
4. 在有限的二进制补码表示中（工作位宽为 `1 + max(x.bit_length(), y.bit_length())` 或更大），至少多带一个符号扩展位来进行这些计算，就足以得到与具有无限个符号位时相同的结果。

### 整数类型的附加方法

`int` 类型实现了 `numbers.Integral` 抽象基类。此外，它还提供其他几个方法：

#### int.bit_length()

返回以二进制表示一个整数所需的位数，不包括符号位和前导零：

```python
n = -37
bin(n)
'-0b100101'
n.bit_length()
6
```

更准确地说，如果 x 非零，则 `x.bit_length()` 是使得 `2**(k-1) <= abs(x) < 2**k` 成立的唯一正整数 k。同样地，当 `abs(x)` 小到足以拥有一个正确舍入的对数时，`k = 1 + int(log(abs(x), 2))`。如果 x 为零，则 `x.bit_length()` 返回 `0`。

等价于：

```python
def bit_length(self):
    s = bin(self)       # binary representation:  bin(-37) --> '-0b100101'
    s = s.lstrip('-0b') # remove leading zeros and minus sign
    return len(s)       # len('100101') --> 6
```

3.1 版本新增。

#### int.bit_count()

返回整数的绝对值二进制表示中 1 的个数。这也被称为总体计数（population count）。示例：

```python
n = 19
bin(n)
'0b10011'
n.bit_count()
3
(-n).bit_count()
3
```

等价于：

```python
def bit_count(self):
    return bin(self).count("1")
```

3.10 版本新增。

#### int.to_bytes(length=1, byteorder='big', *, signed=False)

返回表示一个整数的字节数组。

```python
(1024).to_bytes(2, byteorder='big')
b'\x04\x00'
(1024).to_bytes(10, byteorder='big')
b'\x00\x00\x00\x00\x00\x00\x00\x00\x04\x00'
(-1024).to_bytes(10, byteorder='big', signed=True)
b'\xff\xff\xff\xff\xff\xff\xff\xff\xfc\x00'
x = 1000
x.to_bytes((x.bit_length() + 7) // 8, byteorder='little')
b'\xe8\x03'
```

整数使用 `length` 个字节表示，`length` 默认为 `1`。如果该整数无法用给定的字节数表示，则抛出 `OverflowError`。

`byteorder` 参数确定用于表示整数的字节顺序，默认为 `"big"`。如果 `byteorder` 为 `"big"`，则最高有效字节位于字节数组的开头。如果 `byteorder` 为 `"little"`，则最高有效字节位于字节数组的末尾。

`signed` 参数确定是否使用二进制补码来表示整数。如果 `signed` 为 `False` 且给出了负整数，则抛出 `OverflowError`。`signed` 的默认值为 `False`。

使用默认值可以方便地将整数转换为单个字节对象：

```python
(65).to_bytes()
b'A'
```

但是，在使用默认参数时，不要尝试转换大于 255 的值，否则你会得到一个 `OverflowError`。

等价于：

```python
def to_bytes(n, length=1, byteorder='big', signed=False):
    if byteorder == 'little':
        order = range(length)
    elif byteorder == 'big':
        order = reversed(range(length))
    else:
        raise ValueError("byteorder must be either 'little' or 'big'")

    return bytes((n >> i*8) & 0xff for i in order)
```

3.2 版本新增。

> **在 3.11 版本发生变更**：为 `length` 和 `byteorder` 添加了默认参数值。

#### classmethod int.from_bytes(bytes, byteorder='big', *, signed=False)

返回由给定字节数组所表示的整数。

```python
int.from_bytes(b'\x00\x10', byteorder='big')
16
int.from_bytes(b'\x00\x10', byteorder='little')
4096
int.from_bytes(b'\xfc\x00', byteorder='big', signed=True)
-1024
int.from_bytes(b'\xfc\x00', byteorder='big', signed=False)
64512
int.from_bytes([255, 0, 0], byteorder='big')
16711680
```

参数 `bytes` 必须是一个 bytes-like 对象或一个产生字节的可迭代对象。

`byteorder` 参数确定用于表示整数的字节顺序，默认为 `"big"`。如果 `byteorder` 为 `"big"`，则最高有效字节位于字节数组的开头。如果 `byteorder` 为 `"little"`，则最高有效字节位于字节数组的末尾。要请求主机系统的原生字节顺序，请使用 `sys.byteorder` 作为字节顺序的值。

`signed` 参数指示是否使用二进制补码来表示整数。

等价于：

```python
def from_bytes(bytes, byteorder='big', signed=False):
    if byteorder == 'little':
        little_ordered = list(bytes)
    elif byteorder == 'big':
        little_ordered = list(reversed(bytes))
    else:
        raise ValueError("byteorder must be either 'little' or 'big'")

    n = sum(b << i*8 for i, b in enumerate(little_ordered))
    if signed and little_ordered and (little_ordered[-1] & 0x80):
        n -= 1 << 8*len(little_ordered)

    return n
```

3.2 版本新增。

> **在 3.11 版本发生变更**：为 `byteorder` 添加了默认参数值。

#### int.as_integer_ratio()

返回一对整数，其比率等于原整数，并且分母为正。整数（整数本身）的整数比率总是整数本身作为分子、`1` 作为分母。

3.8 版本新增。

#### int.is_integer()

返回 `True`。为了与 `float.is_integer()` 保持鸭子类型兼容而存在。

3.12 版本新增。

### 浮点数的附加方法

`float` 类型实现了 `numbers.Real` 抽象基类。`float` 还具有以下附加方法。

#### classmethod float.from_number(x)

类方法，返回由数字 x 构造的浮点数。

如果参数是整数或浮点数，则返回具有相同值（在 Python 的浮点精度范围内）的浮点数。如果参数超出 Python `float` 的范围，将抛出 `OverflowError`。

对于一般的 Python 对象 x，`float.from_number(x)` 会委托给 `x.__float__()`。如果未定义 `__float__()`，则回退到 `__index__()`。

3.14 版本新增。

#### float.as_integer_ratio()

返回一对整数，其比率恰好等于原浮点数。该比率为最简形式且分母为正。对无穷大会抛出 `OverflowError`，对 NaN 会抛出 `ValueError`。

#### float.is_integer()

如果 `float` 实例是有限的且具有整数值，则返回 `True`，否则返回 `False`：

```python
(-2.0).is_integer()
True
(3.2).is_integer()
False
```

有两个方法支持与十六进制字符串之间的相互转换。由于 Python 的浮点数在内部存储为二进制数，浮点数与十进制字符串之间的转换通常都会引入小的舍入误差。相比之下，十六进制字符串可以精确地表示和指定浮点数。这在调试和数值计算工作中可能会很有用。

#### float.hex()

返回浮点数的十六进制字符串表示形式。对于有限浮点数，这种表示形式总是以 `0x` 开头并以 `p` 及指数结尾。

#### classmethod float.fromhex(s)

类方法，返回由十六进制字符串 s 表示的浮点数。字符串 s 的前后可以有空白字符。

请注意 `float.hex()` 是实例方法，而 `float.fromhex()` 是类方法。

十六进制字符串采用以下形式：

```
[sign] ['0x'] integer ['.' fraction] ['p' exponent]
```

其中可选的 `sign` 可以是 `+` 或 `-`，`integer` 和 `fraction` 是十六进制数字组成的字符串，`exponent` 是带有可选前导符号的十进制整数。大小写不敏感，并且 `integer` 或 `fraction` 中至少必须有一个十六进制数字。此语法类似于 C99 标准第 6.4.4.2 节中规定的语法，也类似于 Java 1.5 及更高版本所使用的语法。特别是，`float.hex()` 的输出可以作为 C 或 Java 代码中的十六进制浮点字面值使用，而 C 的 `%a` 格式字符或 Java 的 `Double.toHexString` 生成的十六进制字符串也可以被 `float.fromhex()` 接受。

请注意，指数是以十进制而非十六进制书写的，它给出的是系数要乘以的 2 的幂。例如，十六进制字符串 `0x3.a7p10` 表示浮点数 `(3 + 10./16 + 7./16**2) * 2.0**10`，即 `3740.0`：

```python
float.fromhex('0x3.a7p10')
3740.0
```

对 `3740.0` 应用反向转换会得到一个表示同一数字的不同的十六进制字符串：

```python
float.hex(3740.0)
'0x1.d380000000000p+11'
```

### 复数的附加方法

`complex` 类型实现了 `numbers.Complex` 抽象基类。`complex` 还具有以下附加方法。

#### classmethod complex.from_number(x)

类方法，将一个数字转换为复数。

对于一般的 Python 对象 x，`complex.from_number(x)` 会委托给 `x.__complex__()`。如果未定义 `__complex__()`，则回退到 `__float__()`。如果未定义 `__float__()`，则回退到 `__index__()`。

3.14 版本新增。

### 数字类型的哈希运算

对于数字 x 和 y（可能类型不同），要求只要 `x == y` 就有 `hash(x) == hash(y)`（有关详细信息，请参阅 `__hash__()` 方法的文档）。为了便于在多种数字类型（包括 `int`、`float`、`decimal.Decimal` 和 `fractions.Fraction`）上实现并保证效率，Python 对数字类型的哈希运算是基于一个为任意有理数定义的单一数学函数，因此它适用于 `int` 和 `fractions.Fraction` 的所有实例，以及 `float` 和 `decimal.Decimal` 的所有有限实例。本质上，此函数是通过对一个固定素数 P 取模来定义的。P 的值以 `sys.hash_info` 的 `modulus` 属性的形式提供给 Python。

> **CPython 实现细节**：目前，在具有 32 位 C long 的机器上使用的素数是 `P = 2**31 - 1`，而在具有 64 位 C long 的机器上则是 `P = 2**61 - 1`。

以下是详细规则：

- 如果 `x = m / n` 是一个非负有理数且 n 不能被 P 整除，则将 `hash(x)` 定义为 `m * invmod(n, P) % P`，其中 `invmod(n, P)` 给出 n 对 P 取模的逆元。
- 如果 `x = m / n` 是一个非负有理数且 n 能被 P 整除（但 m 不能），则 n 对 P 取模没有逆元，上述规则不适用；在这种情况下，将 `hash(x)` 定义为常量值 `sys.hash_info.inf`。
- 如果 `x = m / n` 是一个负有理数，则将 `hash(x)` 定义为 `-hash(-x)`。如果所得哈希值为 `-1`，则将其替换为 `-2`。
- 特定值 `sys.hash_info.inf` 和 `-sys.hash_info.inf` 分别被用作正无穷大或负无穷大的哈希值。
- 对于复数 z，实部和虚部的哈希值通过计算 `hash(z.real) + sys.hash_info.imag * hash(z.imag)` 来合并，并对 `2**sys.hash_info.width` 取模约简，使其落在 `range(-2**(sys.hash_info.width - 1), 2**(sys.hash_info.width - 1))` 内。同样，如果结果为 `-1`，则将其替换为 `-2`。

为了阐明上述规则，这里给出一些等价于内置哈希的 Python 示例代码，用于计算有理数、`float` 或 `complex` 的哈希值：

```python
import sys, math

def hash_fraction(m, n):
    """Compute the hash of a rational number m / n.

    Assumes m and n are integers, with n positive.
    Equivalent to hash(fractions.Fraction(m, n)).

    """
    P = sys.hash_info.modulus
    # Remove common factors of P.  (Unnecessary if m and n already coprime.)
    while m % P == n % P == 0:
        m, n = m // P, n // P

    if n % P == 0:
        hash_value = sys.hash_info.inf
    else:
        # Fermat's Little Theorem: pow(n, P-1, P) is 1, so
        # pow(n, P-2, P) gives the inverse of n modulo P.
        hash_value = (abs(m) % P) * pow(n, P - 2, P) % P
    if m < 0:
        hash_value = -hash_value
    if hash_value == -1:
        hash_value = -2
    return hash_value

def hash_float(x):
    """Compute the hash of a float x."""

    if math.isnan(x):
        return object.__hash__(x)
    elif math.isinf(x):
        return sys.hash_info.inf if x > 0 else -sys.hash_info.inf
    else:
        return hash_fraction(*x.as_integer_ratio())

def hash_complex(z):
    """Compute the hash of a complex number z."""

    hash_value = hash_float(z.real) + sys.hash_info.imag * hash_float(z.imag)
    # do a signed reduction modulo 2**sys.hash_info.width
    M = 2**(sys.hash_info.width - 1)
    hash_value = (hash_value & (M - 1)) - (hash_value & M)
    if hash_value == -1:
        hash_value = -2
    return hash_value
```

## 布尔类型 - bool

布尔值表示真值。`bool` 类型恰好有两个常量实例：`True` 和 `False`。

内置函数 `bool()` 可将任意值转换为布尔值，前提是该值可以被解释为真值（参见上文的 真值检测 一节）。

对于逻辑运算，请使用布尔运算符 `and`、`or` 和 `not`。当把位运算符 `&`、`|`、`^` 应用于两个布尔值时，它们返回一个等价于逻辑运算 "and"、"or"、"xor" 的 `bool`。但是，应优先使用逻辑运算符 `and`、`or` 和 `!=`，而不是 `&`、`|` 和 `^`。

> **自 3.12 版本弃用**：使用按位取反运算符 `~` 已弃用，并将在 Python 3.16 中引发错误。

`bool` 是 `int` 的子类（参见 数字类型 --- int、float、complex）。在许多数字上下文中，`False` 和 `True` 的行为分别类似于整数 0 和 1。但是，不建议依赖于此；请改用 `int()` 进行显式转换。

## 迭代相关类型

Python 支持在容器上进行迭代的概念。这是通过两个不同的方法实现的；它们用来允许用户自定义的类支持迭代。下文将更详细描述的序列总是支持迭代方法。

### 可迭代对象

容器对象要提供可迭代支持，需要定义一个方法：

#### container.__iter__()

返回一个迭代器对象。该对象需要支持下文描述的迭代器协议。如果容器支持不同类型的迭代，则可以提供额外的方法来专门请求这些迭代类型的迭代器。（支持多种迭代形式的一个对象示例是同时支持广度优先和深度优先遍历的树结构。）此方法对应于 Python/C API 中 Python 对象的类型结构体的 `tp_iter` 槽位。

### 迭代器

迭代器对象本身需要支持以下两个方法，它们共同构成迭代器协议：

#### iterator.__iter__()

返回迭代器对象本身。这是为了让容器和迭代器都能配合 `for` 和 `in` 语句使用所必需的。此方法对应于 Python/C API 中 Python 对象的类型结构体的 `tp_iter` 槽位。

#### iterator.__next__()

返回迭代器中的下一项。如果没有更多的项，则抛出 `StopIteration` 异常。此方法对应于 Python/C API 中 Python 对象的类型结构体的 `tp_iternext` 槽位。

Python 定义了多种迭代器对象，以支持对通用和特定序列类型、字典以及其他更专门的形式进行迭代。除了迭代器协议的实现之外，这些特定类型并不重要。

一旦迭代器的 `__next__()` 方法抛出 `StopIteration`，它必须在后续的调用中继续这样做。不遵守此特性的实现被视为已损坏。

### 生成器类型

Python 的生成器——或者更准确地说，生成器函数和生成器迭代器——提供了一种实现迭代器协议的便捷方式。

包含一个或多个 `yield` 表达式的函数是生成器函数。例如：

```python
def count_to_three():
    yield 0
    yield 1
    yield 2
    yield 3
```

生成器函数的行为与普通用户自定义函数相同（例如，它们具有相同的属性），只是调用生成器函数会返回一个生成器迭代器：

```python
count_to_three()
<generator object count_to_three at 0x7f33a2305000>
```

迭代生成器迭代器会执行底层生成器函数的代码，依次产生每个 yield 出的值：

```python
for number in count_to_three():
    print(number)
0
1
2
3

list(count_to_three())
[0, 1, 2, 3]
```

生成器函数的一个常见用途是实现自定义可迭代对象的 `__iter__()` 方法。例如：

```python
class CardDeck:
    def __iter__(self):
        yield 'three of clubs'
        yield 'ace of hearts'

list(CardDeck())
['three of clubs', 'ace of hearts']
```

### 生成器迭代器

生成器迭代器实现了迭代器协议。对它们进行迭代会驱动底层生成器函数的执行。

#### generator.__next__()

开始执行生成器函数，或在函数当前挂起的 `yield` 表达式处恢复执行。当生成器函数通过 `__next__()` 方法恢复时，当前的 `yield` 表达式总是求值为 `None`。执行随后继续到下一个 `yield` 表达式，生成器再次挂起，`yield` 关键字之后的表达式的值被返回给 `__next__()` 的调用方。如果生成器在未 yield 另一个值的情况下退出，`__next__()` 会抛出 `StopIteration` 异常，表示迭代已完成。

此方法通常会被隐式调用，例如通过 `for` 循环或内置的 `next()` 函数。

与普通迭代器相比，生成器迭代器还多几个方法，可用来控制底层生成器函数的执行：

#### generator.send(value)

向生成器函数"发送"一个值：`value` 参数成为当前 `yield` 表达式的结果。

除此之外，此方法的行为与 `__next__()` 类似：它恢复底层函数的执行，并返回下一个 yield 出的值或抛出 `StopIteration`。

当用 `send()` 来启动生成器时，必须以 `None` 作为参数调用它，因为此时没有可以接收该值的当前 `yield` 表达式。

#### generator.throw(value)

#### generator.throw(type[, value[, traceback]])

在生成器当前挂起的位置抛出异常。

除此之外，此方法的行为与 `__next__()` 类似：它恢复底层函数的执行，并返回下一个 yield 出的值或抛出 `StopIteration`。如果生成器函数没有捕获传入的异常，或抛出了不同的异常，那么该异常会传播给调用方。

当用 `throw()` 来启动生成器时，生成器会立即退出（即后续对 `__next__()` 的调用将抛出 `StopIteration`），并且被抛出的异常会传播给 `throw()` 的调用方。

在典型用法中，调用此方法时会传入单个参数，即一个异常实例，与 `raise` 关键字的使用方式类似。

但是，为了向后兼容，也支持第二种签名，这沿用了旧版本 Python 的惯例。`type` 参数应当是一个异常类，`value` 应当是一个异常实例。如果未提供 `value`，则调用 `type` 的构造器来获得实例。如果提供了 `traceback`，则将其设置在异常上，否则 `value` 中已存储的任何 `__traceback__` 属性都可能被清除。

> **在 3.12 版本发生变更**：第二种签名 `(type[, value[, traceback]])` 已弃用，并可能在未来的 Python 版本中被移除。

#### generator.close()

在生成器函数当前挂起的位置抛出 `GeneratorExit` 异常（等价于调用 `throw(GeneratorExit)`）。

如果生成器函数已经退出（由于异常或正常返回），或者抛出了 `GeneratorExit`（因为没有捕获该异常），则 `close()` 返回 `None`。如果生成器 yield 了一个值，则抛出 `RuntimeError`。如果生成器抛出了任何其他异常，该异常会传播给调用方。如果生成器在被关闭时返回了一个值，则该值由 `close()` 返回。

当生成器迭代器在退出之前被垃圾回收时，会自动调用 `close()`。

> **在 3.13 版本发生变更**：如果生成器在被关闭时返回了一个值，则该值由 `close()` 返回。此前，它返回 `None`。

当某个生成器方法（`__next__()`、`send()`、`throw()`、`close()`）正在执行时，调用其中任何一个方法都会抛出 `ValueError` 异常。

### 异步生成器迭代器

本小节描述异步生成器迭代器的方法，这些方法用于控制异步生成器函数的执行。

#### async agen.__anext__()

返回一个可等待对象，它在运行时开始执行异步生成器函数，或在函数当前挂起的 `yield` 表达式处恢复执行。当异步生成器函数通过 `__anext__()` 方法恢复时，当前的 `yield` 表达式在返回的可等待对象中总是求值为 `None`，该可等待对象在运行时会继续到下一个 `yield` 表达式。`yield` 关键字之后的表达式的值是完成协程所抛出的 `StopIteration` 异常的值。如果异步生成器在未 yield 另一个值的情况下退出，则可等待对象改为抛出 `StopAsyncIteration` 异常，表示异步迭代已完成。

此方法通常会被 `async for` 循环或内置的 `anext()` 函数隐式调用。

与普通异步迭代器相比，异步生成器迭代器还多几个方法，可用来控制底层生成器函数的执行：

#### async agen.asend(value)

返回一个可等待对象，它在运行时向底层异步生成器函数"发送"一个值：`value` 参数成为当前 `yield` 表达式的结果。

除此之外，此方法的行为与 `__anext__()` 类似：当返回的可等待对象运行时，它恢复底层函数的执行，并返回下一个 yield 出的值（作为所抛出 `StopIteration` 的值）或抛出 `StopAsyncIteration`。

当用 `asend()` 来启动异步生成器时，必须以 `None` 作为参数调用它，因为此时没有可以接收该值的 `yield` 表达式。

#### async agen.athrow(value)

#### async agen.athrow(type[, value[, traceback]])

返回一个可等待对象，它在运行时会在底层异步生成器函数当前挂起的位置抛出异常。

除此之外，此方法的行为与 `__anext__()` 类似：当返回的可等待对象运行时，它恢复底层函数的执行（并抛出一个异常），然后返回下一个 yield 出的值（作为所抛出 `StopIteration` 的值）或抛出 `StopAsyncIteration`。如果底层函数没有捕获传入的异常，或抛出了不同的异常，那么当可等待对象运行时，该异常会传播给可等待对象的调用方。

当用 `athrow()` 来启动生成器时，生成器会在可等待对象运行时退出（即后续对 `__anext__()` 的调用在运行时会抛出 `StopAsyncIteration`），并且被抛出的异常会传播给可等待对象的调用方。

在典型用法中，调用此方法时会传入单个参数，即一个异常实例，与 `raise` 关键字的使用方式类似。

但是，为了向后兼容，也支持第二种签名。异常实例的创建方式与 `generator.throw()` 中相同，即由三个参数创建。

> **在 3.12 版本发生变更**：第二种签名 `(type[, value[, traceback]])` 已弃用，并可能在未来的 Python 版本中被移除。

#### async agen.aclose()

返回一个可等待对象，它在运行时会在底层异步生成器函数当前挂起的位置向其抛出一个 `GeneratorExit`（等价于调用 `athrow(GeneratorExit)`）。

如果异步生成器函数随后正常退出、已被关闭，或者抛出了 `GeneratorExit`（因为没有捕获该异常），则返回的可等待对象将抛出 `StopIteration` 异常。此后对该异步生成器调用所返回的任何可等待对象都将抛出 `StopAsyncIteration` 异常。

如果异步生成器 yield 了一个值，则可等待对象会抛出 `RuntimeError`。如果异步生成器抛出了任何其他异常，该异常会传播给可等待对象的调用方。

如果异步生成器已因异常或正常退出而结束，则后续对 `aclose()` 的调用将返回一个什么都不做的可等待对象。

## 序列类型 --- list、tuple、range

有三种基本序列类型：`list`、`tuple` 和 `range` 对象。为处理二进制数据和文本字符串而定制的其他序列类型会在专门的小节中描述。

### 通用序列操作

大多数序列类型（可变和不可变的）都支持下表中的操作。`collections.abc.Sequence` ABC 被提供用来更轻松地在自定义序列类型上正确地实现这些操作。

此表按优先级升序列出了序列操作。在表格中，s 和 t 是相同类型的序列，n、i、j 和 k 是整数，x 是满足 s 所施加的任何类型和值限制的任意对象。

`in` 和 `not in` 操作具有与比较操作相同的优先级。`+`（拼接）和 `*`（重复）操作具有与对应数字运算相同的优先级。[3]

参见 内置类型操作的时间复杂度 以了解各种序列操作的开销。

| 操作 | 结果 | 备注 |
| --- | --- | --- |
| `x in s` | 若 s 中有某项等于 x 则为 `True`，否则为 `False` | (1) |
| `x not in s` | 若 s 中有某项等于 x 则为 `False`，否则为 `True` | (1) |
| `s + t` | s 与 t 的拼接 | (6)(7) |
| `s * n` 或 `n * s` | 等价于将 s 与其自身相加 n 次 | (2)(7) |
| `s[i]` | s 的第 i 项，起始为 0 | (3)(8) |
| `s[i:j]` | s 从 i 到 j 的切片 | (3)(4) |
| `s[i:j:k]` | s 从 i 到 j 步长为 k 的切片 | (3)(5) |
| `len(s)` | s 的长度 | |
| `min(s)` | s 的最小项 | |
| `max(s)` | s 的最大项 | |

相同类型的序列也支持比较运算。特别是，元组和列表按对应元素的比较结果以字典序进行比较。这意味着要比较相等，每个元素必须都比较相等，并且两个序列必须是相同类型且具有相同的长度。（有关完整细节，请参阅语言参考中的 比较运算。）

对可变序列的正向和反向迭代器使用索引来访问值。即使底层序列发生改变，该索引也会继续向前（或向后）推进。迭代器仅在遇到 `IndexError` 或 `StopIteration` 时（或当索引降到零以下时）才会终止。

备注：

1. 虽然 `in` 和 `not in` 操作在一般情况下只用于简单的成员检测，但某些专门的序列（如 `str`、`bytes` 和 `bytearray`）也使用它们进行子序列检测：

   ```python
   "gg" in "eggs"
   True
   ```

2. 小于 0 的 n 值会被当作 0 处理（这将产生一个与 s 相同类型的空序列）。请注意，序列 s 中的项不会被复制；它们会被多次引用。这一点经常困扰新的 Python 程序员；考虑如下代码：

   ```python
   lists = [[]] * 3
   lists
   [[], [], []]
   lists[0].append(3)
   lists
   [[3], [3], [3]]
   ```

   发生的事情是，`[[]]` 是一个包含一个空列表的单元素列表，所以 `[[]] * 3` 的三个元素都是对这个单一空列表的引用。修改 `lists` 的任何一个元素都会修改这个单一列表。你可以通过这种方式创建包含不同列表的列表：

   ```python
   lists = [[] for i in range(3)]
   lists[0].append(3)
   lists[1].append(5)
   lists[2].append(7)
   lists
   [[3], [5], [7]]
   ```

   更多解释可参见 FAQ 条目 如何创建多维列表？。

3. 如果 i 或 j 为负值，则索引相对于序列 s 的末尾：会替换为 `len(s) + i` 或 `len(s) + j`。但请注意，`-0` 仍然是 `0`。

4. s 从 i 到 j 的切片被定义为索引 k 满足 `i <= k < j` 的项的序列。

   - 如果 i 被省略或为 `None`，则使用 `0`。
   - 如果 j 被省略或为 `None`，则使用 `len(s)`。
   - 如果 i 或 j 小于 `-len(s)`，则使用 `0`。
   - 如果 i 或 j 大于 `len(s)`，则使用 `len(s)`。
   - 如果 i 大于或等于 j，则切片为空。

5. s 从 i 到 j 步长为 k 的切片被定义为索引 `x = i + n*k` 满足 `0 <= n < (j-i)/k` 的项的序列。换句话说，索引为 `i`、`i+k`、`i+2*k`、`i+3*k` 等等，在到达 j 时停止（但绝不包含 j）。当 k 为正数时，如果 i 和 j 更大则将其缩减为 `len(s)`。当 k 为负数时，如果 i 和 j 更大则将其缩减为 `len(s) - 1`。如果 i 或 j 被省略或为 `None`，它们会成为"末端"值（哪一端取决于 k 的符号）。注意，k 不能为零。如果 k 为 `None`，则按 `1` 处理。

6. 拼接不可变序列总是会产生一个新的对象。这意味着通过重复拼接来构建序列，其运行时开销在总序列长度上将是二次方级别的。要获得线性级别的运行时开销，你必须改用以下替代方案之一：

   - 如果要拼接 `str` 对象，你可以构建一个列表并在最后使用 `str.join()`，或者写入一个 `io.StringIO` 实例并在完成时获取其值
   - 如果要拼接 `bytes` 对象，你可以类似地使用 `bytes.join()` 或 `io.BytesIO`，或者可以使用 `bytearray` 对象进行原地拼接。`bytearray` 对象是可变的，并且具有高效的多分配机制
   - 如果要拼接 `tuple` 对象，请改为扩展一个 `list`
   - 对于其他类型，请查阅相关类的文档

7. 某些序列类型（例如 `range`）仅支持遵循特定模式的项序列，因此不支持序列拼接或重复。

8. 如果 i 超出序列范围，则抛出 `IndexError`。

### 序列方法

序列类型还支持以下方法：

#### sequence.count(value, /)

返回 `value` 在序列中出现的总次数。

#### sequence.index(value[, start[, stop]])

返回 `value` 在序列中第一次出现的索引。

如果 `value` 不在序列中，则抛出 `ValueError`。

`start` 或 `stop` 参数允许高效地搜索序列的子区间，从 `start` 开始到 `stop` 结束。这大致等价于 `start + sequence[start:stop].index(value)`，只是不复制任何数据。

> **小心**：并非所有序列类型都支持传入 `start` 和 `stop` 参数。

### 不可变序列类型

不可变序列类型普遍实现而可变序列类型未实现的唯一操作是对 `hash()` 内置函数的支持。

这种支持使得不可变序列（例如 `tuple` 实例）可用作 `dict` 的键，以及存储在 `set` 和 `frozenset` 实例中。

尝试对包含不可哈希值的不可变序列进行哈希运算会导致 `TypeError`。

### 可变序列类型

下表中的操作定义在可变序列类型上。`collections.abc.MutableSequence` ABC 被提供用来更轻松地在自定义序列类型上正确地实现这些操作。

表格中的 s 是可变序列类型的实例，t 是任意可迭代对象，x 是满足 s 所施加的任何类型和值限制的任意对象（例如，`bytearray` 只接受满足 `0 <= x <= 255` 值限制的整数）。

| 操作 | 结果 | 备注 |
| --- | --- | --- |
| `s[i] = x` | 将 s 的第 i 项替换为 x | |
| `del s[i]` | 移除 s 的第 i 项 | |
| `s[i:j] = t` | 将 s 从 i 到 j 的切片替换为可迭代对象 t 的内容 | |
| `del s[i:j]` | 从列表中移除 `s[i:j]` 的元素（与 `s[i:j] = []` 相同） | |
| `s[i:j:k] = t` | 将 `s[i:j:k]` 的元素替换为 t 的元素 | (1) |
| `del s[i:j:k]` | 从列表中移除 `s[i:j:k]` 的元素 | |
| `s += t` | 用 t 的内容扩展 s（在很大程度上与 `s[len(s):len(s)] = t` 相同） | |
| `s *= n` | 用 s 的内容重复 n 次来更新 s | (2) |

备注：

1. 如果 k 不等于 `1`，则 t 的长度必须与其所替换切片的长度相同。
2. 值 n 是一个整数，或一个实现了 `__index__()` 的对象。n 的零值和负值会清空序列。序列中的项不会被复制；它们会被多次引用，正如 通用序列操作 中对 `s * n` 的解释。

### 可变序列方法

可变序列类型还支持以下方法：

#### sequence.append(value, /)

将 `value` 追加到序列的末尾。这等价于 `seq[len(seq):len(seq)] = [value]`。

#### sequence.clear()

3.3 版本新增。

移除序列中的所有项。这等价于 `del sequence[:]`。

#### sequence.copy()

3.3 版本新增。

创建序列的浅拷贝。这等价于 `sequence[:]`。

> **提示**：`copy()` 方法不属于 `MutableSequence` ABC，但大多数具体的可变序列类型都提供了它。

#### sequence.extend(iterable, /)

用 `iterable` 的内容扩展序列。在很大程度上，这与 `seq[len(seq):len(seq)] = iterable` 相同。

#### sequence.insert(index, value, /)

在给定的索引处将 `value` 插入序列。这等价于 `sequence[index:index] = [value]`。

#### sequence.pop(index=-1, /)

取出 `index` 处的项并将其从序列中移除。默认情况下，序列中的最后一项会被移除并返回。

#### sequence.remove(value, /)

从序列中移除第一个满足 `sequence[i] == value` 的项。

如果 `value` 不在序列中，则抛出 `ValueError`。

#### sequence.reverse()

原地反转序列的各项。此方法在反转大型序列时节省空间。为提醒用户该操作是通过副作用实现的，它返回 `None`。

### 列表

列表是可变序列，通常用于存放同类项目的集合（其中相似程度因应用而异）。

#### class list(iterable=(), /)

列表可以用多种方式构建：

- 使用一对方括号来表示空列表：`[]`
- 使用方括号，以逗号分隔项目：`[a]`、`[a, b, c]`
- 使用列表推导式：`[x for x in iterable]`
- 使用类型构造器：`list()` 或 `list(iterable)`

构造器将构造一个列表，其中的项与 `iterable` 中的项具有相同的值与顺序。`iterable` 可以是序列、支持迭代的容器，或迭代器对象。如果 `iterable` 已经是一个列表，将创建并返回其副本，类似于 `iterable[:]`。例如，`list('abc')` 返回 `['a', 'b', 'c']`，而 `list((1, 2, 3))` 返回 `[1, 2, 3]`。如果没有给出参数，构造器将创建一个新的空列表 `[]`。

许多其他操作也会产生列表，包括内置的 `sorted()`。

列表对其项的类型是泛型的。

列表实现了所有通用序列操作和可变序列操作。列表还提供了以下附加方法：

#### sort(*, key=None, reverse=False)

此方法会对列表进行原地排序，只使用 `<` 来进行各项间比较。异常不会被屏蔽——如果有任何比较操作失败，整个排序操作将失败（而列表可能会处于被部分修改的状态）。

`sort()` 接受两个仅能通过关键字传入的参数（仅限关键字参数）：

- `key` 指定带有一个参数的函数，用于从每个列表元素中提取比较键（例如 `key=str.lower`）。对应于列表中每一项的键会被计算一次，然后在整个排序过程中使用。默认值 `None` 表示直接对列表项排序而不计算单独的键值。

  可以使用 `functools.cmp_to_key()` 工具函数将 2.x 风格的 `cmp` 函数转换为 `key` 函数。

- `reverse` 为一个布尔值。如果设为 `True`，则每个列表元素将按反向顺序进行排序，如同每次比较都被反转一样。

当排序大型序列时，此方法会原地修改序列以保证空间经济性。为提醒用户该操作是通过副作用实现的，它不会返回排序后的序列（请使用 `sorted()` 显式地请求一个新的已排序列表实例）。

`sort()` 方法保证是稳定的。如果一个排序保证不会改变比较结果相等的元素的相对顺序，则称其为稳定的——这在多轮排序中非常有用（例如，先按部门排序，再按薪酬等级排序）。

有关排序示例和简要的排序教程，请参阅 排序的技术。

> **CPython 实现细节**：在一个列表被排序期间，尝试改变甚至检查列表的效果是未定义的。Python 的 C 实现会在排序期间使列表看起来为空，如果检测到列表在排序过程中被改变则会引发 `ValueError`。

> **参见**：有关列表对象的线程安全保证的详细信息，请参阅 列表对象的线程安全。

### 元组

元组是不可变序列，通常用于存放异构数据的集合（例如由内置 `enumerate()` 产生的二元组）。元组也被用于需要同构数据的不可变序列的情况（例如允许存储到 `set` 或 `dict` 的实例中）。

#### class tuple(iterable=(), /)

元组可以用多种方式构建：

- 使用一对圆括号来表示空元组：`()`
- 使用单个元素的尾随逗号来表示单元素元组：`a,` 或 `(a,)`
- 使用以逗号分隔的项目：`a, b, c` 或 `(a, b, c)`
- 使用内置的 `tuple()`：`tuple()` 或 `tuple(iterable)`

构造器将构造一个元组，其中的项与 `iterable` 中的项具有相同的值与顺序。`iterable` 可以是序列、支持迭代的容器，或迭代器对象。如果 `iterable` 已经是一个元组，将不加改变地将其返回。例如，`tuple('abc')` 返回 `('a', 'b', 'c')`，而 `tuple([1, 2, 3])` 返回 `(1, 2, 3)`。如果没有给出参数，构造器将创建一个新的空元组 `()`。

请注意，实际上决定生成元组的其实是逗号而不是圆括号。圆括号是可选的，但在空元组的情况，或当需要避免语法歧义时除外。例如，`f(a, b, c)` 是带有三个参数的函数调用，而 `f((a, b, c))` 则是带有以三元组作为唯一参数的函数调用。

元组实现了所有通用序列操作。

元组对其内容的类型是泛型的。有关更多信息，请参阅 typing 文档中有关元组注解的内容。

对于通过名称访问比通过索引访问更清晰的异构数据集合，`collections.namedtuple()` 可能是比简单元组对象更为合适的选择。

### range 对象

`range` 类型表示不可变的数字序列，通常用于在 `for` 循环中循环特定次数。

#### class range(stop, /)

#### class range(start, stop, step=1, /)

`range` 构造器的参数必须为整数（可以是内置的 `int` 或任何实现了 `__index__()` 特殊方法的对象）。如果省略 `step` 参数，其默认值为 `1`。如果省略 `start` 参数，其默认值为 `0`。如果 `step` 为零，则会引发 `ValueError`。

如果 `step` 为正值，确定 range r 内容的公式为 `r[i] = start + step*i`，其中 `i >= 0` 且 `r[i] < stop`。

如果 `step` 为负值，range 的内容仍然由公式 `r[i] = start + step*i` 确定，但限制条件改为 `i >= 0` 且 `r[i] > stop`。

如果 `r[0]` 不符合值的限制条件，则该 range 对象为空。range 对象确实支持负索引，但会将其解读为从正索引所确定的序列的末尾开始索引。

允许 range 包含大于 `sys.maxsize` 的绝对值，但某些特性（例如 `len()`）可能会引发 `OverflowError`。

range 示例：

```python
list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
list(range(0, 30, 5))
[0, 5, 10, 15, 20, 25]
list(range(0, 10, 3))
[0, 3, 6, 9]
list(range(0, -10, -1))
[0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
list(range(0))
[]
list(range(1, 0))
[]
```

range 对象实现了除拼接和重复之外的所有通用序列操作（这是由于 range 对象只能表示遵循严格模式的序列，而重复和拼接通常都会破坏这种模式）。

##### start

`start` 参数的值（如果该参数未提供则为 `0`）

##### stop

`stop` 参数的值

##### step

`step` 参数的值（如果该参数未提供则为 `1`）

`range` 类型相比常规 `list` 或 `tuple` 的优势在于：无论 range 所表示的范围有多大，range 对象总是占用相同（少量）的内存（因为它只存储 `start`、`stop` 和 `step` 的值，并会根据需要计算具体单项或子范围）。

range 对象实现了 `collections.abc.Sequence` ABC，提供如包含检测、元素索引查找、切片等特性，并支持负索引（参见 序列类型 --- list、tuple、range）：

```python
r = range(0, 20, 2)
r
range(0, 20, 2)
11 in r
False
10 in r
True
r.index(10)
5
r[5]
10
r[:5]
range(0, 10, 2)
r[-1]
18
```

使用 `==` 和 `!=` 检测 range 对象是否相等是将其作为序列来比较。也就是说，如果两个 range 对象表示相同的值序列，则认为它们是相等的。（请注意比较结果相等的两个 range 对象可能会具有不同的 `start`、`stop` 和 `step` 属性，例如 `range(0) == range(2, 1, 3)` 或 `range(0, 3, 2) == range(0, 4, 2)`。）

> **在 3.2 版本发生变更**：实现 `Sequence` ABC。支持切片和负索引。使用 `int` 对象进行成员检测为常数时间，而不是逐一迭代所有项。
>
> **在 3.3 版本发生变更**：定义 `'=='` 和 `'!='` 以根据 range 对象所定义的值的序列来比较它们（而不是根据对象标识进行比较）。
>
> 新增 `start`、`stop` 和 `step` 属性。

> **参见**：linspace 配方 展示了如何实现一个适用于浮点数应用的惰性版本 range。

## 文本和二进制序列类型方法汇总

下表按类别汇总了文本和二进制序列类型的方法。

| 类别 | str 方法 | bytes 和 bytearray 方法 |
| --- | --- | --- |
| 格式化 | `str.format()`、`str.format_map()`、f-字符串、printf 风格的字符串格式化 | printf 风格的字节串格式化 |
| 搜索与替换 | `str.find()`、`str.rfind()`、`str.index()`、`str.rindex()`、`str.startswith()`、`str.endswith()`、`str.count()`、`str.replace()` | `bytes.find()`、`bytes.rfind()`、`bytes.index()`、`bytes.rindex()`、`bytes.startswith()`、`bytes.endswith()`、`bytes.count()`、`bytes.replace()` |
| 拆分与拼接 | `str.split()`、`str.rsplit()`、`str.splitlines()`、`str.partition()`、`str.rpartition()`、`str.join()` | `bytes.split()`、`bytes.rsplit()`、`bytes.splitlines()`、`bytes.partition()`、`bytes.rpartition()`、`bytes.join()` |
| 字符串分类 | `str.isalpha()`、`str.isdecimal()`、`str.isdigit()`、`str.isnumeric()`、`str.isalnum()`、`str.isidentifier()`、`str.islower()`、`str.isupper()`、`str.istitle()`、`str.isspace()`、`str.isprintable()` | `bytes.isalpha()`、`bytes.isdigit()`、`bytes.isalnum()`、`bytes.islower()`、`bytes.isupper()`、`bytes.istitle()`、`bytes.isspace()` |
| 大小写转换 | `str.lower()`、`str.upper()`、`str.casefold()`、`str.capitalize()`、`str.title()`、`str.swapcase()` | `bytes.lower()`、`bytes.upper()`、`bytes.capitalize()`、`bytes.title()`、`bytes.swapcase()` |
| 填充与剥离 | `str.ljust()`、`str.rjust()`、`str.center()`、`str.expandtabs()`、`str.strip()`、`str.lstrip()`、`str.rstrip()`、`str.removeprefix()`、`str.removesuffix()` | `bytes.ljust()`、`bytes.rjust()`、`bytes.center()`、`bytes.expandtabs()`、`bytes.strip()`、`bytes.lstrip()`、`bytes.rstrip()`、`bytes.removeprefix()`、`bytes.removesuffix()` |
| 转换与编码 | `str.translate()`、`str.maketrans()`、`str.encode()` | `bytes.translate()`、`bytes.maketrans()`、`bytes.decode()` |

## 文本序列类型 --- str

Python 中的文本数据由 `str` 对象（即字符串）处理。字符串是由 Unicode 码位构成的不可变序列。字符串字面值有多种不同的写法：

- 单引号：`'allows embedded "double" quotes'`
- 双引号：`"allows embedded 'single' quotes"`
- 三引号：`'''Three single quotes'''`、`"""Three double quotes"""`

三引号字符串可以跨越多行——所有关联的空白字符都将包含在字符串字面值中。

作为单个表达式的一部分且彼此之间只有空白字符的多个字符串字面值会被隐式地转换为单个字符串字面值。也就是说，`("spam " "eggs") == "spam eggs"`。

参见 字符串与字节串字面值 了解有关各种字符串字面值形式的更多信息，包括支持的转义序列，以及禁用大多数转义序列处理的 `r`（"原始"）前缀。

字符串也可以通过 `str` 构造器从其他对象创建。

由于不存在单独的"字符"类型，对字符串进行索引将产生长度为 1 的字符串。也就是说，对于非空字符串 s，`s[0] == s[0:1]`。

也不存在可变的字符串类型，但是 `str.join()` 或 `io.StringIO` 可以被用来基于多个片段高效率地构建字符串。

> **在 3.3 版本发生变更**：为了与 Python 2 系列向后兼容，再次允许在字符串字面值中使用 `u` 前缀。它对字符串字面值的含义没有影响，并且不能与 `r` 前缀同时使用。

#### class str(*, encoding='utf-8', errors='strict')

#### class str(object)

#### class str(object, encoding, errors='strict')

#### class str(object, *, errors)

返回 `object` 的字符串版本。如果未提供 `object`，则返回空字符串。在其他情况下，`str()` 的行为取决于是否给出了 `encoding` 或 `errors`，如下所示。

- 如果 `encoding` 和 `errors` 均未给出，`str(object)` 返回 `type(object).__str__(object)`，即 `object` 的"非正式"或格式良好的可打印字符串表示形式。对于字符串对象，这是该字符串本身。如果 `object` 没有 `__str__()` 方法，则 `str()` 将回退为返回 `repr(object)`。
- 如果至少给出了 `encoding` 或 `errors` 其中之一，则 `object` 应该是一个 bytes-like 对象（例如 `bytes` 或 `bytearray`）。在此情况下，如果 `object` 是一个 `bytes`（或 `bytearray`）对象，则 `str(bytes, encoding, errors)` 等价于 `bytes.decode(encoding, errors)`。否则的话，会在调用 `bytes.decode()` 之前获取缓冲区对象下层的 bytes 对象。有关缓冲区对象的信息，请参阅 二进制序列类型 --- bytes、bytearray、memoryview 和 缓冲协议。
- 将一个 `bytes` 对象传入 `str()` 而不给出 `encoding` 或 `errors` 参数将落入第一种情况，返回非正式的字符串表示（另请参阅 Python 的 `-b` 命令行选项）。例如：

  ```python
  str(b'Zoot!')
  "b'Zoot!'"
  ```

有关 `str` 类及其方法的更多信息，请参阅 文本序列类型 --- str 和下文 字符串方法 一节。要输出格式化字符串，请参阅 f-字符串 和 格式字符串语法 小节。此外，请参阅 文本处理服务 一节。

### 字符串方法

字符串实现了所有通用序列操作，还实现下文所述的附加方法。

字符串还支持两种风格的字符串格式化：一种提供了很大程度的灵活性和可定制性（参阅 `str.format()`、格式字符串语法 和 自定义字符串格式化），另一种基于 C 的 `printf` 风格的格式化，可处理的类型范围较窄，并且正确使用起来难度稍高，但对其可处理的情况来说通常速度更快（printf 风格的字符串格式化）。

标准库的 文本处理服务 一节涵盖了许多其他模块，提供各种文本相关的实用工具（包括 `re` 模块中的正则表达式支持）。

#### str.capitalize()

返回原字符串的副本，其首个字符大写，其余为小写。

> **在 3.8 版本发生变更**：第一个字符现在被放入 titlecase 而非 uppercase。这意味着诸如连体字母等字符只会将其第一个字母大写，而不是整个字符。

#### str.casefold()

返回原字符串消除大小写的副本。消除大小写的字符串可用于忽略大小写的匹配。

消除大小写类似于转为小写，但更彻底一些，因为它旨在消除字符串中的所有大小写区别。例如，德语小写字母 `'ß'` 相当于 `"ss"`。由于它已经是小写了，`lower()` 对 `'ß'` 不会做任何事；而 `casefold()` 会将其转换为 `"ss"`。例如：

```python
'straße'.lower()
'straße'
'straße'.casefold()
'strasse'
```

消除大小写的算法在 Unicode 标准第 3.13.3 节 'Default Case Folding' 中有描述。

3.3 版本新增。

#### str.center(width, fillchar=' ', /)

返回长度为 `width` 的字符串，原字符串在其中居中。使用指定的 `fillchar` 填充两边的空位（默认使用 ASCII 空格符）。如果 `width` 小于等于 `len(s)` 则返回原字符串。例如：

```python
'Python'.center(10)
'  Python  '
'Python'.center(10, '-')
'--Python--'
'Python'.center(4)
'Python'
```

#### str.count(sub[, start[, end]])

返回子字符串 `sub` 在 `[start, end]` 范围内非重叠出现的次数。可选参数 `start` 与 `end` 会被解读为切片表示法。

如果 `sub` 为空，则返回字符之间的空字符串个数，即字符串的长度加一。例如：

```python
'spam, spam, spam'.count('spam')
3
'spam, spam, spam'.count('spam', 5)
2
'spam, spam, spam'.count('spam', 5, 10)
1
'spam, spam, spam'.count('eggs')
0
'spam, spam, spam'.count('')
17
```

#### str.encode(encoding='utf-8', errors='strict')

返回编码为 `bytes` 的字符串。

`encoding` 默认值为 `'utf-8'`；请参阅 标准编码 了解可用的值。

`errors` 控制如何处理编码错误。如果为 `'strict'`（默认值），则抛出 `UnicodeError` 异常。其他可能的值有 `'ignore'`、`'replace'`、`'xmlcharrefreplace'`、`'backslashreplace'` 以及任何其他通过 `codecs.register_error()` 注册的名称。有关详细信息，请参阅 错误处理方案。

出于性能原因，`errors` 的值只有在确实发生编码错误、启用了 Python 开发模式 或使用了调试构建时才会进行有效性检查。例如：

```python
encoded_str_to_bytes = 'Python'.encode()
type(encoded_str_to_bytes)
<class 'bytes'>
encoded_str_to_bytes
b'Python'
```

> **在 3.1 版本发生变更**：加入了对关键字参数的支持。
>
> **在 3.9 版本发生变更**：现在会在 Python 开发模式 和调试模式下检查 `errors` 参数的值。

#### str.endswith(suffix[, start[, end]])

如果字符串以指定的 `suffix` 结束则返回 `True`，否则返回 `False`。`suffix` 也可以是由多个后缀构成的元组。如果有可选项 `start`，将从所指定位置开始检查。如果有可选项 `end`，将在所指定位置停止比较。使用 `start` 和 `end` 等价于 `str[start:end].endswith(suffix)`。例如：

```python
'Python'.endswith('on')
True
'a tuple of suffixes'.endswith(('at', 'in'))
False
'a tuple of suffixes'.endswith(('at', 'es'))
True
'Python is amazing'.endswith('is', 0, 9)
True
```

另请参阅 `startswith()` 和 `removesuffix()`。

#### str.expandtabs(tabsize=8)

返回字符串的副本，其中所有的制表符会由一个或多个空格替换，具体取决于当前列位置和给定的制表符宽度。每 `tabsize` 个字符设一个制表位（默认值 8 时制表位在列 0、8、16 等依次递增）。要将字符串展开，当前列将被设为零并逐一检查字符串中的每个字符。如果字符为制表符（`\t`），则会在结果中插入一个或多个空格符，直到当前列等于下一个制表位。（制表符本身不会被复制。）如果字符为换行符（`\n`）或回车符（`\r`），它会被复制并将当前列重设为零。任何其他字符将不加修改地复制并将当前列加一，不论该字符在被打印时会如何显示。例如：

```python
'01\t012\t0123\t01234'.expandtabs()
'01      012     0123    01234'
'01\t012\t0123\t01234'.expandtabs(4)
'01  012 0123    01234'
print('01\t012\n0123\t01234'.expandtabs(4))
01  012
0123    01234
```

#### str.find(sub[, start[, end]])

返回子字符串 `sub` 在 `s[start:end]` 切片内被找到的最小索引。可选参数 `start` 与 `end` 会被解读为切片表示法。如果 `sub` 未被找到则返回 `-1`。例如：

```python
'spam, spam, spam'.find('sp')
0
'spam, spam, spam'.find('sp', 5)
6
```

另请参阅 `rfind()` 和 `index()`。

> **备注**：`find()` 方法只应在需要知道 `sub` 的位置时使用。要检查 `sub` 是否为子字符串，请使用 `in` 运算符：

```python
'Py' in 'Python'
True
```

#### str.format(*args, **kwargs)

执行字符串格式化操作。调用此方法的字符串可以包含字符串字面值或者以花括号 `{}` 括起来的替换字段。每个替换字段可以包含位置参数的数字索引，或者关键字参数的名称。返回字符串的副本，其中每个替换字段都会被替换为对应参数的字符串值。例如：

```python
"The sum of 1 + 2 is {0}".format(1+2)
'The sum of 1 + 2 is 3'
"The sum of {a} + {b} is {answer}".format(answer=1+2, a=1, b=2)
'The sum of 1 + 2 is 3'
"{1} expects the {0} Inquisition!".format("Spanish", "Nobody")
'Nobody expects the Spanish Inquisition!'
```

请参阅 格式字符串语法 获取有关可以在格式字符串中指定的各种格式选项的说明。

> **备注**：当使用 `n` 类型（例如 `'{:n}'.format(1234)`）格式化数字（`int`、`float`、`complex`、`decimal.Decimal` 及其子类）时，该函数会临时性地将 `LC_CTYPE` 区域设置为 `LC_NUMERIC` 区域，以解码 `localeconv()` 的 `decimal_point` 和 `thousands_sep` 字段（如果它们是非 ASCII 或长度超过 1 字节），并且 `LC_NUMERIC` 区域与 `LC_CTYPE` 区域不一致。这个临时更改会影响其他线程。

> **在 3.7 版本发生变更**：当使用 `n` 类型格式化数字时，该函数在某些情况下会临时性地将 `LC_CTYPE` 区域设置为 `LC_NUMERIC` 区域。

#### str.format_map(mapping, /)

类似于 `str.format(**mapping)`，不同之处在于 `mapping` 会被直接使用而不是复制到一个 `dict`。适宜使用此方法的一个例子是当 `mapping` 为 dict 的子类的情况：

```python
class Default(dict):
    def __missing__(self, key):
        return key

'{name} was born in {country}'.format_map(Default(name='Guido'))
'Guido was born in country'
```

3.2 版本新增。

#### str.index(sub[, start[, end]])

类似于 `find()`，但在找不到子字符串时会引发 `ValueError`。例如：

```python
'spam, spam, spam'.index('spam')
0
'spam, spam, spam'.index('eggs')
Traceback (most recent call last):
  File "<python-input-0>", line 1, in <module>
    'spam, spam, spam'.index('eggs')
    ~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^
ValueError: substring not found
```

另请参阅 `rindex()`。

#### str.isalnum()

如果字符串中的所有字符都是字母或数字且至少有一个字符，则返回 `True`，否则返回 `False`。如果 `c.isalpha()`、`c.isdecimal()`、`c.isdigit()` 或 `c.isnumeric()` 之中有一个返回 `True`，则字符 `c` 是字母或数字。例如：

```python
'abc123'.isalnum()
True
'abc123!@#'.isalnum()
False
''.isalnum()
False
' '.isalnum()
False
```

#### str.isalpha()

如果字符串中的所有字符都是字母且至少有一个字符，则返回 `True`，否则返回 `False`。字母字符是指 Unicode 字符数据库中定义为 "Letter" 的字符，即通用类别属性为 `"Lm"`、`"Lt"`、`"Lu"`、`"Ll"` 或 `"Lo"` 之一的字符。注意，这与 Unicode 标准第 4.10 节 'Letters, Alphabetic, and Ideographic' 中定义的 "Alphabetic" 属性不同。例如：

```python
'Letters and spaces'.isalpha()
False
'LettersOnly'.isalpha()
True
'µ'.isalpha()  # non-ASCII characters can be considered alphabetical too
True
```

参见 Unicode 属性。

#### str.isascii()

如果字符串为空或字符串中的所有字符都是 ASCII，则返回 `True`，否则返回 `False`。ASCII 字符的码位范围是 U+0000-U+007F。例如：

```python
'ASCII characters'.isascii()
True
'µ'.isascii()
False
```

3.7 版本新增。

#### str.isdecimal()

如果字符串中的所有字符都是十进制字符且至少有一个字符，则返回 `True`，否则返回 `False`。十进制字符是指可以用来组成以 10 为基数的数字的字符，例如 U+0660，即阿拉伯-印度数字零。正式地说，十进制字符是 Unicode 通用类别 `"Nd"` 中的字符。例如：

```python
'0123456789'.isdecimal()
True
'٠١٢٣٤٥٦٧٨٩'.isdecimal()  # Arabic-Indic digits zero to nine
True
'alphabetic'.isdecimal()
False
```

#### str.isdigit()

如果字符串中的所有字符都是数字且至少有一个字符，则返回 `True`，否则返回 `False`。数字包括十进制字符和需要特殊处理的数字，如兼容性上标数字。这涵盖了不能用于组成以 10 为基数的数字的字符，如 Kharosthi 数字。正式地说，数字是指具有属性值 `Numeric_Type=Digit` 或 `Numeric_Type=Decimal` 的字符。

例如：

```python
'0123456789'.isdigit()
True
'٠١٢٣٤٥٦٧٨٩'.isdigit()  # Arabic-Indic digits zero to nine
True
'⅕'.isdigit()  # Vulgar fraction one fifth
False
'²'.isdecimal(), '²'.isdigit(),  '²'.isnumeric()
(False, True, True)
```

另请参阅 `isdecimal()` 和 `isnumeric()`。

#### str.isidentifier()

根据语言定义 名称（标识符和关键字） 一节，如果字符串是有效的标识符则返回 `True`。

`keyword.iskeyword()` 可以用来检测字符串 s 是否为保留标识符，如 `def` 和 `class`。

示例：

```python
from keyword import iskeyword

'hello'.isidentifier(), iskeyword('hello')
(True, False)
'def'.isidentifier(), iskeyword('def')
(True, True)
```

#### str.islower()

如果字符串中至少有一个区分大小写的字符 [4] 且此类字符均为小写则返回 `True`，否则返回 `False`。

#### str.isnumeric()

如果字符串中的所有字符都是数值字符且至少有一个字符，则返回 `True`，否则返回 `False`。数值字符包括数字字符，以及所有具有 Unicode 数值属性的字符，例如 U+2155，即普通分数五分之一。正式地说，数值字符是具有属性值 `Numeric_Type=Digit`、`Numeric_Type=Decimal` 或 `Numeric_Type=Numeric` 的字符。例如：

```python
'0123456789'.isnumeric()
True
'٠١٢٣٤٥٦٧٨٩'.isnumeric()  # Arabic-Indic digits zero to nine
True
'⅕'.isnumeric()  # Vulgar fraction one fifth
True
'²'.isdecimal(), '²'.isdigit(),  '²'.isnumeric()
(False, True, True)
```

另请参阅 `isdecimal()` 和 `isdigit()`。

#### str.isprintable()

如果字符串中的所有字符都是可打印的则返回 `True`，如果它包含至少一个不可打印字符则返回 `False`。

这里 "printable" 的意思是该字符适合供 `repr()` 在其输出中使用；"non-printable" 表示内置类型的 `repr()` 会以十六进制转义形式输出该字符。它与字符串被写入 `sys.stdout` 或 `sys.stderr` 时的处理无关。

可打印字符是那些在 Unicode 字符数据库（参见 `unicodedata`）中，其通用类别属于 Letter、Mark、Number、Punctuation 或 Symbol（L、M、N、P 或 S）组之一的字符；再加上 ASCII 空格 0x20。不可打印字符是 Separator 或 Other（Z 或 C）组中的字符，但 ASCII 空格除外。

例如：

```python
''.isprintable(), ' '.isprintable()
(True, True)
'\t'.isprintable(), '\n'.isprintable()
(False, False)
```

另请参阅 `isspace()`。

#### str.isspace()

如果字符串中只有空白字符且至少有一个字符则返回 `True`，否则返回 `False`。

例如：

```python
''.isspace()
False
' '.isspace()
True
'\t\n'.isspace() # TAB and BREAK LINE
True
'　'.isspace() # IDEOGRAPHIC SPACE
True
```

如果字符在 Unicode 字符数据库（参见 `unicodedata`）中，其通用类别为 `Zs`（"Separator, space"），或其双向类为 `WS`、`B` 或 `S` 之一，则其为空白字符。

另请参阅 `isprintable()`。

#### str.istitle()

如果字符串中至少有一个字符且为标题化字符串则返回 `True`，例如大写字符之后只能带非大小写字符而小写字符必须紧跟在大小写字符之后。否则返回 `False`。

例如：

```python
'Spam, Spam, Spam'.istitle()
True
'spam, spam, spam'.istitle()
False
'SPAM, SPAM, SPAM'.istitle()
False
```

另请参阅 `title()`。

#### str.isupper()

如果字符串中至少有一个区分大小写的字符 [4] 且此类字符均为大写则返回 `True`，否则返回 `False`。

```python
'BANANA'.isupper()
True
'banana'.isupper()
False
'baNana'.isupper()
False
' '.isupper()
False
```

#### str.join(iterable, /)

返回一个由 `iterable` 中的字符串拼接而成的字符串。如果 `iterable` 中存在任何非字符串值（包括 `bytes` 对象），则会引发 `TypeError`。调用此方法的字符串将作为元素之间的分隔符。例如：

```python
', '.join(['spam', 'spam', 'spam'])
'spam, spam, spam'
'-'.join('Python')
'P-y-t-h-o-n'
```

另请参阅 `split()`。

#### str.ljust(width, fillchar=' ', /)

返回长度为 `width` 的字符串，原字符串在其中靠左对齐。使用指定的 `fillchar` 填充空位（默认使用 ASCII 空格符）。如果 `width` 小于等于 `len(s)` 则返回原字符串。

例如：

```python
'Python'.ljust(10)
'Python    '
'Python'.ljust(10, '.')
'Python....'
'Monty Python'.ljust(10, '.')
'Monty Python'
```

另请参阅 `rjust()`。

#### str.lower()

返回原字符串的副本，其所有区分大小写的字符 [4] 均转换为小写。例如：

```python
'Lower Method Example'.lower()
'lower method example'
```

所用转换小写算法的描述见 Unicode 标准第 3.13.2 节 'Default Case Conversion'。

#### str.lstrip(chars=None, /)

返回原字符串的副本，移除其中的前导字符。`chars` 参数为指定要移除字符集合的字符串。如果省略或为 `None`，则 `chars` 参数默认移除空白符。实际上 `chars` 参数并非指定单个前缀；而是会移除其所有组合的值：

```python
'   spacious   '.lstrip()
'spacious   '
'www.example.com'.lstrip('cmowz.')
'example.com'
```

参见 `str.removeprefix()`，该方法将删除单个前缀字符串，而不是整个集合中的字符。例如：

```python
'Arthur: three!'.lstrip('Arthur: ')
'ee!'
'Arthur: three!'.removeprefix('Arthur: ')
'three!'
```

#### static str.maketrans(dict, /)

#### static str.maketrans(from, to, remove='', /)

此静态方法返回一个可供 `str.translate()` 使用的转换对照表。

如果只有一个参数，则它必须是一个将 Unicode 码位（整数）或字符（长度为 1 的字符串）映射到 Unicode 码位、（任意长度的）字符串或 `None` 的字典。字符键将会被转换为码位。

如果有两个参数，则它们必须是长度相等的字符串，并且在结果字典中，`from` 中的每个字符将被映射到 `to` 中相同位置的字符。如果有第三个参数，它必须是一个字符串，其中的字符将在结果中被映射到 `None`。

> **在 3.15 版本发生变更**：`dict` 现在可以是 `frozendict`。

#### str.partition(sep, /)

在 `sep` 首次出现的位置拆分字符串，返回一个 3 元组，其中包含分隔符之前的部分、分隔符本身，以及分隔符之后的部分。如果找不到分隔符，则返回的 3 元组中包含字符串本身以及两个空字符串。

例如：

```python
'Monty Python'.partition(' ')
('Monty', ' ', 'Python')
"Monty Python's Flying Circus".partition(' ')
('Monty', ' ', "Python's Flying Circus")
'Monty Python'.partition('-')
('Monty Python', '', '')
```

另请参阅 `rpartition()`。

#### str.removeprefix(prefix, /)

如果字符串以 `prefix` 字符串开头，返回 `string[len(prefix):]`。否则，返回原始字符串的副本：

```python
'TestHook'.removeprefix('Test')
'Hook'
'BaseTestCase'.removeprefix('Test')
'BaseTestCase'
```

3.9 版本新增。

另请参阅 `removesuffix()` 和 `startswith()`。

#### str.removesuffix(suffix, /)

如果字符串以 `suffix` 字符串结尾，并且 `suffix` 非空，返回 `string[:-len(suffix)]`。否则，返回原始字符串的副本：

```python
'MiscTests'.removesuffix('Tests')
'Misc'
'TmpDirMixin'.removesuffix('Tests')
'TmpDirMixin'
```

3.9 版本新增。

另请参阅 `removeprefix()` 和 `endswith()`。

#### str.replace(old, new, /, count=-1)

返回字符串的副本，其中出现的所有子字符串 `old` 都将被替换为 `new`。如果给出了 `count`，则只替换前 `count` 次出现。如果未指定 `count` 或为 `-1`，则全部替换。例如：

```python
'spam, spam, spam'.replace('spam', 'eggs')
'eggs, eggs, eggs'
'spam, spam, spam'.replace('spam', 'eggs', 1)
'eggs, spam, spam'
```

> **在 3.13 版本发生变更**：`count` 现在支持作为关键字参数。

#### str.rfind(sub[, start[, end]])

返回子字符串 `sub` 在字符串内被找到的最大索引，这样 `sub` 将包含在 `s[start:end]` 之内。可选参数 `start` 与 `end` 会被解读为切片表示法。如果未找到则返回 `-1`。例如：

```python
'spam, spam, spam'.rfind('sp')
12
'spam, spam, spam'.rfind('sp', 0, 10)
6
```

另请参阅 `find()` 和 `rindex()`。

#### str.rindex(sub[, start[, end]])

类似于 `rfind()`，但在子字符串 `sub` 未找到时会引发 `ValueError`。例如：

```python
'spam, spam, spam'.rindex('spam')
12
'spam, spam, spam'.rindex('eggs')
Traceback (most recent call last):
  File "<stdin-0>", line 1, in <module>
    'spam, spam, spam'.rindex('eggs')
    ~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^
ValueError: substring not found
```

另请参阅 `index()` 和 `find()`。

#### str.rjust(width, fillchar=' ', /)

返回长度为 `width` 的字符串，原字符串在其中靠右对齐。使用指定的 `fillchar` 填充空位（默认使用 ASCII 空格符）。如果 `width` 小于等于 `len(s)` 则返回原字符串。

例如：

```python
'Python'.rjust(10)
'    Python'
'Python'.rjust(10, '.')
'....Python'
'Monty Python'.rjust(10, '.')
'Monty Python'
```

另请参阅 `ljust()` 和 `zfill()`。

#### str.rpartition(sep, /)

在 `sep` 最后一次出现的位置拆分字符串，返回一个 3 元组，其中包含分隔符之前的部分、分隔符本身，以及分隔符之后的部分。如果找不到分隔符，则返回的 3 元组中包含两个空字符串以及字符串本身。

例如：

```python
'Monty Python'.rpartition(' ')
('Monty', ' ', 'Python')
"Monty Python's Flying Circus".rpartition(' ')
("Monty Python's Flying", ' ', 'Circus')
'Monty Python'.rpartition('-')
('', '', 'Monty Python')
```

另请参阅 `partition()`。

#### str.rsplit(sep=None, maxsplit=-1)

返回一个由字符串内单词组成的列表，使用 `sep` 作为分隔字符串。如果给出了 `maxsplit`，则最多进行 `maxsplit` 次拆分，从最右边开始。如果 `sep` 未指定或为 `None`，任何空白字符串都会被作为分隔符。除了从右侧开始拆分，`rsplit()` 的行为类似于下文详细描述的 `split()`。

#### str.rstrip(chars=None, /)

返回原字符串的副本，移除其中的末尾字符。`chars` 参数为指定要移除字符集合的字符串。如果省略或为 `None`，则 `chars` 参数默认移除空白符。实际上 `chars` 参数并非指定单个后缀；而是会移除其所有组合的值。例如：

```python
'   spacious   '.rstrip()
'   spacious'
'mississippi'.rstrip('ipz')
'mississ'
```

要删除单个后缀字符串而不是整个集合中的字符，请参阅 `removesuffix()` 方法。例如：

```python
'Monty Python'.rstrip(' Python')
'M'
'Monty Python'.removesuffix(' Python')
'Monty'
```

另请参阅 `strip()`。

#### str.split(sep=None, maxsplit=-1)

返回一个由字符串内单词组成的列表，使用 `sep` 作为分隔字符串。如果给出了 `maxsplit`，则最多进行 `maxsplit` 次拆分（因此，列表最多会有 `maxsplit+1` 个元素）。如果 `maxsplit` 未指定或为 `-1`，则不限制拆分次数（进行所有可能的拆分）。

如果给出了 `sep`，则连续的分隔符不会被组合在一起而是被视为分隔空字符串（例如 `'1,,2'.split(',')` 将返回 `['1', '', '2']`）。`sep` 参数可能由多个字符组成（要使用多个分隔符进行拆分，请使用 `re.split()`）。使用指定的分隔符拆分空字符串将返回 `['']`。

例如：

```python
'1,2,3'.split(',')
['1', '2', '3']
'1,2,3'.split(',', maxsplit=1)
['1', '2,3']
'1,2,,3,'.split(',')
['1', '2', '', '3', '']
'1<>2<>3<4'.split('<>')
['1', '2', '3<4']
```

如果 `sep` 未指定或为 `None`，则会应用另一种拆分算法：连续的空格会被视为单个分隔符，如果字符串包含前缀或后缀空格，则结果将不包含开头或末尾的空字符串。因而，使用 `None` 拆分空字符串或仅包含空格的字符串将返回 `[]`。

例如：

```python
'1 2 3'.split()
['1', '2', '3']
'1 2 3'.split(maxsplit=1)
['1', '2 3']
'   1   2   3   '.split()
['1', '2', '3']
```

如果 `sep` 未指定或为 `None` 且 `maxsplit` 为 0，则只考虑前导的连续空格。

例如：

```python
"".split(None, 0)
[]
"   ".split(None, 0)
[]
"   foo   ".split(maxsplit=0)
['foo   ']
```

另请参阅 `join()` 和 `rsplit()`。

#### str.splitlines(keepends=False)

返回由原字符串中各行组成的列表，在行边界的位置拆分。结果列表中不包含行边界，除非给出了 `keepends` 且为真值。

此方法会以下列行边界进行拆分。特别地，这些边界是 通用换行符 的一个超集。

| 表示 | 描述 |
| --- | --- |
| `\n` | 换行 |
| `\r` | 回车 |
| `\r\n` | 回车 + 换行 |
| `\v` 或 `\x0b` | 行制表符 |
| `\f` 或 `\x0c` | 换页符 |
| `\x1c` | 文件分隔符 |
| `\x1d` | 组分隔符 |
| `\x1e` | 记录分隔符 |
| `\x85` | 下一行（C1 控制码） |
| ` ` | 行分隔符 |
| ` ` | 段分隔符 |

> **在 3.2 版本发生变更**：`\v` 和 `\f` 被添加到行边界列表中。

例如：

```python
'ab c\n\nde fg\rkl\r\n'.splitlines()
['ab c', '', 'de fg', 'kl']
'ab c\n\nde fg\rkl\r\n'.splitlines(keepends=True)
['ab c\n', '\n', 'de fg\r', 'kl\r\n']
```

不同于 `split()`，当给出了分隔字符串 `sep` 时，对于空字符串此方法返回空列表，而末尾的换行不会产生额外的行：

```python
"".splitlines()
[]
"One line\n".splitlines()
['One line']
```

作为比较，`split('\n')` 的结果为：

```python
''.split('\n')
['']
'Two lines\n'.split('\n')
['Two lines', '']
```

#### str.startswith(prefix[, start[, end]])

如果字符串以指定的 `prefix` 开始则返回 `True`，否则返回 `False`。`prefix` 也可以是由多个前缀构成的元组。如果有可选项 `start`，将从所指定位置开始检查。如果有可选项 `end`，将在所指定位置停止比较。

例如：

```python
'Python'.startswith('Py')
True
'a tuple of prefixes'.startswith(('at', 'a'))
True
'Python is amazing'.startswith('is', 7)
True
```

另请参阅 `endswith()` 和 `removeprefix()`。

#### str.strip(chars=None, /)

返回原字符串的副本，移除其中的前导和末尾字符。`chars` 参数为指定要移除字符集合的字符串。如果省略或为 `None`，则 `chars` 参数默认移除空白符。实际上 `chars` 参数并非指定单个前缀或后缀；而是会移除其所有组合的值。

空白字符由 `str.isspace()` 定义。

例如：

```python
'   spacious   '.strip()
'spacious'
'www.example.com'.strip('cmowz.')
'example'
```

最外侧的前导和末尾 `chars` 参数值将从字符串中移除。开头端的字符将被移除，直至到达一个不包含于 `chars` 所指定字符集合之中的字符为止。末尾端也将执行类似的操作。

例如：

```python
comment_string = '#....... Section 3.2.1 Issue #32 .......'
comment_string.strip('.#! ')
'Section 3.2.1 Issue #32'
```

另请参阅 `rstrip()`。

#### str.swapcase()

返回原字符串的副本，其中大写字符转换为小写，反之亦然。例如：

```python
'Hello World'.swapcase()
'hELLO wORLD'
```

请注意 `s.swapcase().swapcase() == s` 并不一定为真。例如：

```python
'straße'.swapcase().swapcase()
'strasse'
```

另请参阅 `str.lower()` 和 `str.upper()`。

#### str.title()

返回原字符串的标题版本，其中每个单词第一个字母为大写，其余字母为小写。

例如：

```python
'Hello world'.title()
'Hello World'
```

该算法使用一种简单的与语言无关的定义，将连续的字母组合视为单词。该定义在多数情况下都很有效，但它也意味着代表缩写形式与所有格的撇号也会成为单词边界，这可能导致不希望的结果：

```python
"they're bill's friends from the UK".title()
"They'Re Bill'S Friends From The Uk"
```

`string.capwords()` 函数没有此问题，因为它只用空格来拆分单词。

或者，可以用正则表达式来构造针对撇号的变通方案：

```python
import re
def titlecase(s):
    return re.sub(r"[A-Za-z]+('[A-Za-z]+)?",
                  lambda mo: mo.group(0).capitalize(),
                  s)

titlecase("they're bill's friends.")
"They're Bill's Friends."
```

另请参阅 `istitle()`。

#### str.translate(table, /)

返回原字符串的副本，其中每个字符按给定的转换表进行映射。`table` 必须是通过 `__getitem__()` 实现索引操作的对象，通常为 mapping 或 sequence。当以 Unicode 码位（整数）为索引时，`table` 对象可以做以下任何一种操作：返回 Unicode 码位或字符串，将字符映射为一个或多个其他字符；返回 `None`，将字符从返回字符串中删除；或抛出 `LookupError` 异常，将字符映射为自身。

你可以使用 `str.maketrans()` 基于不同格式的字符到字符映射来创建一个转换映射表。

以下示例使用映射将 `'a'` 替换为 `'X'`，`'b'` 替换为 `'Y'`，并删除 `'c'`：

```python
'abc123'.translate({ord('a'): 'X', ord('b'): 'Y', ord('c'): None})
'XY123'
```

另请参阅 `codecs` 模块以了解更灵活的自定义字符映射方式。

#### str.upper()

返回原字符串的副本，其中所有区分大小写的字符 [4] 均转换为大写。请注意如果 `s` 包含不区分大小写的字符，或者如果结果字符的 Unicode 类别不是 `"Lu"`（Letter, uppercase），而是例如 `"Lt"`（Letter, titlecase），则 `s.upper().isupper()` 有可能为 `False`。

所用转换大写算法的描述见 Unicode 标准第 3.13.2 节 'Default Case Conversion'。

#### str.zfill(width, /)

返回原字符串的副本，在左边填充 ASCII `'0'` 数码使其长度变为 `width`。正负值前缀（`'+'`/`'-'`）的处理方式是在正负符号之后填充而非在之前。如果 `width` 小于等于 `len(s)` 则返回原字符串。

例如：

```python
"42".zfill(5)
'00042'
"-42".zfill(5)
'-0042'
```

另请参阅 `rjust()`。

### 格式化字符串字面值（f-字符串）

3.6 版本新增。

> **在 3.7 版本发生变更**：`await` 和 `async for` 可以用于 f-字符串内的表达式。
>
> **在 3.8 版本发生变更**：新增调试说明符 (`=`)。
>
> **在 3.12 版本发生变更**：f-字符串内表达式的许多限制已被移除。值得注意地，现在允许嵌套字符串、注释和反斜杠。

f-字符串（正式名称为格式化字符串字面值）是以 `f` 或 `F` 为前缀的字符串字面值。这种字符串字面值允许在替换字段中嵌入任意 Python 表达式的结果，替换字段以花括号（`{}`）分隔。每个替换字段必须包含一个表达式，其后可以可选地跟随：

- 调试说明符——等号 (`=`)；
- 转换说明符——`!s`、`!r` 或 `!a`；以及/或者
- 以冒号 (`:`) 为前缀的格式说明符。

有关这些字段语法的详细信息，请参阅 词法分析 一节中关于 f-字符串 的内容。

#### 调试说明符

3.8 版本新增。

如果调试说明符——等号 (`=`)——出现在替换字段表达式之后，则生成的 f-字符串将包含表达式的源码、等号以及表达式的值。这在调试时通常很有用：

```python
number = 14.3
f'{number=}'
'number=14.3'
```

表达式之前、之中和之后的空白，以及等号之后的空白都是有意义的——它们会保留在结果中：

```python
f'{ number  -  4  = }'
' number  -  4  = 10.3'
```

#### 转换说明符

默认情况下，替换字段表达式的值使用 `str()` 转换为字符串：

```python
from fractions import Fraction
one_third = Fraction(1, 3)
f'{one_third}'
'1/3'
```

当使用调试说明符而未使用格式说明符时，默认转换改为使用 `repr()`：

```python
f'{one_third = }'
'one_third = Fraction(1, 3)'
```

可以使用下列说明符之一来显式指定转换：

- `!s` 用于 `str()`
- `!r` 用于 `repr()`
- `!a` 用于 `ascii()`

例如：

```python
str(one_third)
'1/3'
repr(one_third)
'Fraction(1, 3)'

f'{one_third!s} is {one_third!r}'
'1/3 is Fraction(1, 3)'

string = "¡kočka 😸!"
ascii(string)
"'\\xa1ko\\u010dka \\U0001f638!'"

f'{string = !a}'
"string = '\\xa1ko\\u010dka \\U0001f638!'"
```

#### 格式说明符

在对表达式求值（并可能通过显式转换说明符进行转换）之后，将使用 `format()` 函数对其进行格式化。如果替换字段包含由冒号 (`:`) 引入的格式说明符，则该说明符将作为第二个参数传递给 `format()`。`format()` 的结果随后用作替换字段的最终值。例如：

```python
from fractions import Fraction
one_third = Fraction(1, 3)
f'{one_third:.6f}'
'0.333333'
f'{one_third:_^+10}'
'___+1/3___'
f'{one_third!r:_^20}'
'___Fraction(1, 3)___'
f'{one_third = :~>10}~'
'one_third = ~~~~~~~1/3~'
```

### 模板字符串字面值（t-字符串）

t-字符串（正式名称为模板字符串字面值）是以 `t` 或 `T` 为前缀的字符串字面值。

这些字符串遵循与格式化字符串字面值相同的语法和求值规则，但有以下区别：

- 模板字符串字面值不求值为 `str` 对象，而是求值为 `string.templatelib.Template` 对象。
- 不使用 `format()` 协议。取而代之的是，格式说明符和转换（如有）将被传递给为每个被求值的表达式创建的新 `Interpolation` 对象。如何处理格式说明符和转换，由处理所生成 `Template` 对象的代码来决定。
- 包含嵌套替换字段的格式说明符会在传递给 `Interpolation` 对象之前被立即求值。例如，形如 `{amount:.{precision}f}` 的插值将先对内部表达式 `{precision}` 求值以确定 `format_spec` 属性的值。如果 `precision` 为 2，则生成的格式说明符将是 `'.2f'`。
- 当插值表达式中提供了等号 `'='` 时，表达式的文本会被追加到相应插值之前的字面字符串中。这包括等号和周围的空白。该表达式的 `Interpolation` 实例将照常创建，只是 `conversion` 将被默认设置为 `'r'`（`repr()`）。如果提供了显式的转换或格式说明符，则会覆盖默认行为。

### printf 风格的字符串格式化

> **备注**：此处介绍的格式化操作具有多种怪异特性，可能导致许多常见错误（例如无法正确显示元组和字典）。
>
> 使用格式化字符串字面值、`str.format()` 接口或 `string.Template` 可能有助于避免这些错误。这些替代方案中的每一种都有自己的权衡利弊以及简单性、灵活性和/或可扩展性方面的优势。

字符串对象具有一个独一无二的内置操作：`%` 运算符（取模）。这也被称为字符串的格式化或插值运算符。给定 `format % values`（其中 `format` 是字符串），`format` 中的 `%` 转换标记符将被替换为 `values` 中零个或多个元素。其效果类似于在 C 语言中使用 `sprintf()`。例如：

```python
print('%s has %d quote types.' % ('Python', 2))
Python has 2 quote types.
```

如果 `format` 要求一个单独参数，则 `values` 可以为一个非元组对象。[5] 否则的话，`values` 必须或者是一个包含项数与格式字符串中指定的转换符项数恰好相同的元组，或者是一个单独映射对象（例如字典）。

转换标记符包含两个或更多的字符并具有以下组成，且必须遵循此处规定的顺序：

1. `'%'` 字符，用于标记转换符的起始。
2. 映射键（可选），由加圆括号的字符序列组成（例如 `(somename)`）。
3. 转换旗标（可选），用于影响某些转换类型的结果。
4. 最小字段宽度（可选）。如果指定为 `'*'`（星号），则实际宽度会从 `values` 元组的下一元素中读取，要转换的对象则为最小字段宽度和可选的精度之后的值。
5. 精度（可选），以在 `'.'`（点号）之后加精度值的形式给出。如果指定为 `'*'`（星号），则实际精度会从 `values` 元组的下一元素中读取，要转换的对象则为精度之后的值。
6. 长度修饰符（可选）。
7. 转换类型。

当右边的参数为一个字典（或其他映射类型）时，字符串中的格式必须包含圆括号插入的映射键，该键会紧跟在 `'%'` 字符之后被插入到该字典中。映射键将从映射中选取要格式化的值。例如：

```python
print('%(language)s has %(number)03d quote types.' %
      {'language': "Python", "number": 2})
Python has 002 quote types.
```

在此情况下格式中不能出现 `*` 标记符（因其需要一个序列类的参数列表）。

转换旗标为：

| 旗标 | 含义 |
| --- | --- |
| `'#'` | 值的转换将使用"替代形式"（具体定义见下文）。 |
| `'0'` | 转换将为数字值填充零字符。 |
| `'-'` | 转换值将靠左对齐（如果同时给出 `'0'` 转换，则将覆盖后者）。 |
| `' '` | （一个空格）在带符号的转换所产生的正数（或空字符串）之前留一个空格。 |
| `'+'` | 符号字符（`'+'` 或 `'-'`）将显示于转换结果的开头（覆盖"空格"旗标）。 |

可以给出长度修饰符（`h`、`l` 或 `L`），但会被忽略，因为对 Python 而言没有必要——所以例如 `%ld` 等价于 `%d`。

转换类型为：

| 转换符 | 含义 | 备注 |
| --- | --- | --- |
| `'d'` | 有符号十进制整数。 | |
| `'i'` | 有符号十进制整数。 | |
| `'o'` | 有符号八进制数。 | (1) |
| `'u'` | 过时类型——它等同于 `'d'`。 | (6) |
| `'x'` | 有符号十六进制数（小写）。 | (2) |
| `'X'` | 有符号十六进制数（大写）。 | (2) |
| `'e'` | 浮点指数格式（小写）。 | (3) |
| `'E'` | 浮点指数格式（大写）。 | (3) |
| `'f'` | 浮点十进制格式。 | (3) |
| `'F'` | 浮点十进制格式。 | (3) |
| `'g'` | 浮点格式。如果指数小于 -4 或不小于精度则使用小写指数格式，否则使用十进制格式。 | (4) |
| `'G'` | 浮点格式。如果指数小于 -4 或不小于精度则使用大写指数格式，否则使用十进制格式。 | (4) |
| `'c'` | 单个字符（接受整数或单个字符的字符串）。 | |
| `'r'` | 字符串（使用 `repr()` 转换任何 Python 对象）。 | (5) |
| `'s'` | 字符串（使用 `str()` 转换任何 Python 对象）。 | (5) |
| `'a'` | 字符串（使用 `ascii()` 转换任何 Python 对象）。 | (5) |
| `'%'` | 不转换参数，在结果中输出一个 `'%'` 字符。 | |

对于浮点格式，结果应正确舍入到小数点后给定的精度 p 位。舍入模式与 `round()` 内置函数一致。

备注：

1. 替代形式会在第一个数码之前插入标示八进制的前缀（`'0o'`）。
2. 替代形式会在第一个数码之前插入 `'0x'` 或 `'0X'` 前缀（取决于是使用 `'x'` 还是 `'X'` 格式）。
3. 替代形式总是使得结果中包含一个小数点，即使其后并没有数码。

   精度决定了小数点后的数码位数，默认为 6。

4. 替代形式总是使得结果中包含一个小数点，末尾的零不会被移除（在其他情况下会被移除）。

   精度决定了小数点前后的有效数码位数，默认为 6。

5. 如果精度为 N，输出将截短为 N 个字符。
6. 参见 PEP 237。

由于 Python 字符串显式地指明了长度，`%s` 转换不会将 `'\0'` 当做字符串的结尾。

> **在 3.1 版本发生变更**：绝对值超过 1e50 的数值的 `%f` 转换不会再被替换为 `%g` 转换。

## 二进制序列类型 --- bytes、bytearray、memoryview

操作二进制数据的核心内置类型是 `bytes` 和 `bytearray`。它们由 `memoryview` 提供支持，该对象使用 缓冲协议 来访问其他二进制对象所在内存，不需要创建对象的副本。

`array` 模块支持高效地存储基本数据类型，例如 32 位整数和 IEEE754 双精度浮点值。

### bytes 对象

bytes 对象是由单个字节构成的不可变序列。由于许多主要二进制协议都基于 ASCII 文本编码，因此 bytes 对象提供了一些仅在处理 ASCII 兼容数据时才有用的方法，并且在许多其他方面与字符串对象密切相关。

#### class bytes(source=b'')

#### class bytes(source, encoding, errors='strict')

首先，表示 bytes 字面值的语法与字符串字面值的大致相同，只是添加了一个 `b` 前缀：

- 单引号：`b'still allows embedded "double" quotes'`
- 双引号：`b"still allows embedded 'single' quotes"`
- 三引号：`b'''3 single quotes'''`、`b"""3 double quotes"""`

bytes 字面值中只允许 ASCII 字符（无论源代码声明的编码为何）。任何大于 127 的二进制值必须使用相应的转义序列形式加入 bytes 字面值。

像字符串字面值一样，bytes 字面值也可以使用 `r` 前缀来禁用转义序列处理。参见 字符串与字节串字面值 了解有关各种 bytes 字面值形式的详情，包括支持的转义序列。

虽然 bytes 字面值和表示法是基于 ASCII 文本的，但 bytes 对象的行为实际上就像是不可变的整数序列，序列中的每个值的大小被限制为 `0 <= x < 256`（如果违反此限制将引发 `ValueError`）。这种限制是有意设计用以强调以下事实：虽然许多二进制格式都包含基于 ASCII 的元素，可以通过某些面向文本的算法进行有用的操作，但情况对于任意二进制数据来说通常并非如此（盲目地对不兼容 ASCII 的二进制数据格式应用文本处理算法通常会导致数据损坏）。

除了字面值形式，bytes 对象还可以通过其他几种方式创建：

- 指定长度的以零值填充的 bytes 对象：`bytes(10)`
- 通过由整数组成的可迭代对象：`bytes(range(20))`
- 通过缓冲区协议复制现有的二进制数据：`bytes(obj)`

另请参阅 bytes 内置类型。

由于 2 个十六进制数码恰好对应于 1 个字节，因此十六进制数是描述二进制数据的常用格式。相应地，`bytes` 类型有一个从此种格式读取数据的附加类方法：

#### classmethod fromhex(string, /)

此 `bytes` 类方法返回一个 bytes 对象，解码给定的字符串对象。字符串必须包含每个字节两个十六进制数码，其中的 ASCII 空白符会被忽略。

```python
bytes.fromhex('2Ef0 F1f2  ')
b'.\xf0\xf1\xf2'
```

> **在 3.7 版本发生变更**：`bytes.fromhex()` 现在会跳过字符串中所有的 ASCII 空白符，而不只是空格符。
>
> **在 3.14 版本发生变更**：`bytes.fromhex()` 现在接受 ASCII bytes 和 bytes-like 对象作为输入。

存在一个反向转换函数，可将 bytes 对象转换为对应的十六进制表示。

#### hex(*, bytes_per_sep=1)

#### hex(sep, bytes_per_sep=1)

返回一个字符串对象，该对象包含实例中每个字节的两个十六进制数码。

```python
b'\xf0\xf1\xf2'.hex()
'f0f1f2'
```

如果你希望令十六进制字符串更易阅读，可以指定单个字符分隔符 `sep` 参数包含在输出中。默认情况下，该分隔符会放在每个字节之间。第二个可选的 `bytes_per_sep` 参数控制间距。正值从右侧计算分隔符位置，负值从左侧计算。

```python
value = b'\xf0\xf1\xf2'
value.hex('-')
'f0-f1-f2'
value.hex('_', 2)
'f0_f1f2'
b'UUDDLRLRAB'.hex(' ', -4)
'55554444 4c524c52 4142'
```

3.5 版本新增。

> **在 3.8 版本发生变更**：`bytes.hex()` 现在支持可选的 `sep` 和 `bytes_per_sep` 参数，以在十六进制输出中的字节之间插入分隔符。

由于 bytes 对象是由整数构成的序列（类似于元组），因此对于一个 bytes 对象 b，`b[0]` 将为一个整数，而 `b[0:1]` 将为一个长度为 1 的 bytes 对象。（这与文本字符串不同，文本字符串索引和切片均将产生一个长度为 1 的字符串。）

bytes 对象的表示使用字面值格式（`b'...'`），因为它通常都要比例如 `bytes([46, 46, 46])` 更有用。你总是可以使用 `list(b)` 将 bytes 对象转换为一个由整数构成的列表。

### bytearray 对象

`bytearray` 对象是 bytes 对象的可变对应物。

#### class bytearray(source=b'')

#### class bytearray(source, encoding, errors='strict')

bytearray 对象没有专属的字面值语法，它们总是通过调用构造器来创建：

- 创建一个空实例：`bytearray()`
- 创建一个指定长度的以零值填充的实例：`bytearray(10)`
- 通过由整数组成的可迭代对象：`bytearray(range(20))`
- 通过缓冲区协议复制现有的二进制数据：`bytearray(b'Hi!')`

由于 bytearray 对象是可变的，它们在 Bytes 和 Bytearray 操作 中描述的 bytes 和 bytearray 通用操作之外，还支持可变序列操作。

另请参阅 bytearray 内置类型。

由于 2 个十六进制数码恰好对应于 1 个字节，因此十六进制数是描述二进制数据的常用格式。相应地，`bytearray` 类型有一个从此种格式读取数据的附加类方法：

#### classmethod fromhex(string, /)

此 `bytearray` 类方法返回一个 bytearray 对象，解码给定的字符串对象。字符串必须包含每个字节两个十六进制数码，其中的 ASCII 空白符会被忽略。

```python
bytearray.fromhex('2Ef0 F1f2  ')
bytearray(b'.\xf0\xf1\xf2')
```

> **在 3.7 版本发生变更**：`bytearray.fromhex()` 现在会跳过字符串中所有的 ASCII 空白符，而不只是空格符。
>
> **在 3.14 版本发生变更**：`bytearray.fromhex()` 现在接受 ASCII bytes 和 bytes-like 对象作为输入。

存在一个反向转换函数，可将 bytearray 对象转换为对应的十六进制表示。

#### hex(*, bytes_per_sep=1)

#### hex(sep, bytes_per_sep=1)

返回一个字符串对象，该对象包含实例中每个字节的两个十六进制数码。

```python
bytearray(b'\xf0\xf1\xf2').hex()
'f0f1f2'
```

3.5 版本新增。

> **在 3.8 版本发生变更**：与 `bytes.hex()` 类似，`bytearray.hex()` 现在支持可选的 `sep` 和 `bytes_per_sep` 参数，以在十六进制输出中的字节之间插入分隔符。

#### resize(size, /)

调整 bytearray 的大小，使其包含 `size` 个字节。`size` 必须大于等于 0。

如果 bytearray 需要缩小，超出 `size` 的字节将被截断。

如果 bytearray 需要增大，所有新增的字节（即 `size` 之外的部分）将被设为零字节。

这等价于：

```python
def resize(ba, size):
    if len(ba) > size:
        del ba[size:]
    else:
        ba += b'\0' * (size - len(ba))
```

示例：

```python
shrink = bytearray(b'abc')
shrink.resize(1)
(shrink, len(shrink))
(bytearray(b'a'), 1)
grow = bytearray(b'abc')
grow.resize(5)
(grow, len(grow))
(bytearray(b'abc\x00\x00'), 5)
```

3.14 版本新增。

#### take_bytes(n=None, /)

从 bytearray 中移除前 `n` 个字节，并将其作为不可变的 `bytes` 返回。默认情况下（如果 `n` 为 `None`），返回所有字节并清空 bytearray。

如果 `n` 为负数，则从末尾开始索引，并取前 `len()` 加 `n` 个字节。如果 `n` 越界，则抛出 `IndexError`。

取出少于全部长度的字节会在 bytearray 中留下剩余字节，这需要一次拷贝。如果剩余字节应当被丢弃，请先使用 `resize()` 或 `del` 进行截断，再调用不带大小参数的 `take_bytes()`。

> **CPython 实现细节**：取出所有字节是一个零拷贝操作。

> **3.15 版本新增**：参见 What's New 条目以了解可通过 `bytearray.take_bytes()` 优化的常见代码模式。

由于 bytearray 对象是由整数构成的序列（类似于列表），因此对于一个 bytearray 对象 b，`b[0]` 将为一个整数，而 `b[0:1]` 将为一个长度为 1 的 bytearray 对象。（这与文本字符串不同，文本字符串索引和切片均将产生一个长度为 1 的字符串。）

bytearray 对象的表示使用 bytes 字面值格式（`bytearray(b'...')`），因为它通常都要比例如 `bytearray([46, 46, 46])` 更有用。你总是可以使用 `list(b)` 将 bytearray 对象转换为一个由整数构成的列表。

> **参见**：有关 bytearray 对象的线程安全保证的详细信息，请参阅 bytearray 对象的线程安全。

### bytes 和 bytearray 操作

bytes 和 bytearray 对象都支持通用序列操作。它们不仅能与相同类型的操作数一起使用，还能与任何 bytes-like 对象一起使用。由于这种灵活性，它们可以在运算中自由地混合而不会导致错误。但是，结果的返回类型可能取决于操作数的顺序。

> **备注**：bytes 和 bytearray 对象的方法不接受字符串作为其参数，正如字符串的方法不接受 bytes 作为其参数。例如，你必须这样写：

```python
a = "abc"
b = a.replace("a", "f")
```

以及：

```python
a = b"abc"
b = a.replace(b"a", b"f")
```

某些 bytes 和 bytearray 操作假定使用 ASCII 兼容的二进制格式，因此在处理任意二进制数据时应当避免使用。这些限制会在下文中说明。

> **备注**：使用这些基于 ASCII 的操作来处理未以基于 ASCII 的格式存储的二进制数据可能会导致数据损坏。

bytes 和 bytearray 对象的以下方法可以用于任意二进制数据。

#### bytes.count(sub[, start[, end]])

#### bytearray.count(sub[, start[, end]])

返回子序列 `sub` 在 `[start, end]` 范围内非重叠出现的次数。可选参数 `start` 与 `end` 会被解读为切片表示法。

要搜索的子序列可以是任意 bytes-like 对象或 0 到 255 范围内的整数。

如果 `sub` 为空，则返回字符之间的空切片数，即 bytes 对象的长度加一。

> **在 3.3 版本发生变更**：也接受 0 到 255 范围内的整数作为子序列。

#### bytes.removeprefix(prefix, /)

#### bytearray.removeprefix(prefix, /)

如果二进制数据以 `prefix` 字符串开头，返回 `bytes[len(prefix):]`。否则，返回原始二进制数据的副本：

```python
b'TestHook'.removeprefix(b'Test')
b'Hook'
b'BaseTestCase'.removeprefix(b'Test')
b'BaseTestCase'
```

`prefix` 可以是任意 bytes-like 对象。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

3.9 版本新增。

#### bytes.removesuffix(suffix, /)

#### bytearray.removesuffix(suffix, /)

如果二进制数据以 `suffix` 字符串结尾，并且 `suffix` 非空，返回 `bytes[:-len(suffix)]`。否则，返回原始二进制数据的副本：

```python
b'MiscTests'.removesuffix(b'Tests')
b'Misc'
b'TmpDirMixin'.removesuffix(b'Tests')
b'TmpDirMixin'
```

`suffix` 可以是任意 bytes-like 对象。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

3.9 版本新增。

#### bytes.decode(encoding='utf-8', errors='strict')

#### bytearray.decode(encoding='utf-8', errors='strict')

返回解码为 `str` 的字节串。

`encoding` 默认值为 `'utf-8'`；请参阅 标准编码 了解可用的值。

`errors` 控制如何处理解码错误。如果为 `'strict'`（默认值），则抛出 `UnicodeError` 异常。其他可能的值有 `'ignore'`、`'replace'` 以及任何其他通过 `codecs.register_error()` 注册的名称。有关详细信息，请参阅 错误处理方案。

出于性能原因，`errors` 的值只有在确实发生解码错误、启用了 Python 开发模式 或使用了调试构建时才会进行有效性检查。

> **备注**：将 `encoding` 参数传给 `str` 允许直接解码任何 bytes-like 对象，而无需创建临时的 bytes 或 bytearray 对象。

> **在 3.1 版本发生变更**：加入了对关键字参数的支持。
>
> **在 3.9 版本发生变更**：现在会在 Python 开发模式 和调试模式下检查 `errors` 参数的值。

#### bytes.endswith(suffix[, start[, end]])

#### bytearray.endswith(suffix[, start[, end]])

如果二进制数据以指定的 `suffix` 结束则返回 `True`，否则返回 `False`。`suffix` 也可以是由多个后缀构成的元组。如果有可选项 `start`，将从所指定位置开始检查。如果有可选项 `end`，将在所指定位置停止比较。

要搜索的后缀可以是任意 bytes-like 对象。

#### bytes.find(sub[, start[, end]])

#### bytearray.find(sub[, start[, end]])

返回子序列 `sub` 在数据中被找到的最小索引，`sub` 将包含于切片 `s[start:end]` 之内。可选参数 `start` 与 `end` 会被解读为切片表示法。如果 `sub` 未被找到则返回 `-1`。

要搜索的子序列可以是任意 bytes-like 对象或 0 到 255 范围内的整数。

> **备注**：`find()` 方法只应在需要知道 `sub` 的位置时使用。要检查 `sub` 是否为子串，请使用 `in` 运算符：

```python
b'Py' in b'Python'
True
```

> **在 3.3 版本发生变更**：也接受 0 到 255 范围内的整数作为子序列。

#### bytes.index(sub[, start[, end]])

#### bytearray.index(sub[, start[, end]])

类似于 `find()`，但在找不到子序列时会引发 `ValueError`。

要搜索的子序列可以是任意 bytes-like 对象或 0 到 255 范围内的整数。

> **在 3.3 版本发生变更**：也接受 0 到 255 范围内的整数作为子序列。

#### bytes.join(iterable, /)

#### bytearray.join(iterable, /)

返回一个由 `iterable` 中的二进制数据序列拼接而成的 bytes 或 bytearray 对象。如果 `iterable` 中存在任何不是 bytes-like 对象的值（包括 `str` 对象），则会引发 `TypeError`。提供此方法的 bytes 或 bytearray 对象的内容将作为元素之间的分隔符。

#### static bytes.maketrans(from, to, /)

#### static bytearray.maketrans(from, to, /)

此静态方法返回一个可供 `bytes.translate()` 使用的转换对照表，会把 `from` 中的每个字符映射为 `to` 中相同位置的字符；`from` 与 `to` 必须都是 bytes-like 对象并且具有相同的长度。

3.1 版本新增。

#### bytes.partition(sep, /)

#### bytearray.partition(sep, /)

在 `sep` 首次出现的位置拆分序列，返回一个 3 元组，其中包含分隔符之前的部分、分隔符本身或其 bytearray 副本，以及分隔符之后的部分。如果找不到分隔符，返回的 3 元组中包含原序列的副本，以及两个空的 bytes 或 bytearray 对象。

要搜索的分隔符可以是任意 bytes-like 对象。

#### bytes.replace(old, new, /, count=-1)

#### bytearray.replace(old, new, /, count=-1)

返回序列的副本，其中出现的所有子序列 `old` 都将被替换为 `new`。如果给出了 `count`，则只替换前 `count` 次出现。如果未指定 `count` 或为 `-1`，则全部替换。

要搜索的子序列及其替换值可以是任意 bytes-like 对象。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

> **在 3.15 版本发生变更**：`count` 现在支持作为关键字参数。

#### bytes.rfind(sub[, start[, end]])

#### bytearray.rfind(sub[, start[, end]])

返回子序列 `sub` 在序列内被找到的最大索引，这样 `sub` 将包含在 `s[start:end]` 之内。可选参数 `start` 与 `end` 会被解读为切片表示法。如果未找到则返回 `-1`。

要搜索的子序列可以是任意 bytes-like 对象或 0 到 255 范围内的整数。

> **在 3.3 版本发生变更**：也接受 0 到 255 范围内的整数作为子序列。

#### bytes.rindex(sub[, start[, end]])

#### bytearray.rindex(sub[, start[, end]])

类似于 `rfind()`，但在子序列 `sub` 未找到时会引发 `ValueError`。

要搜索的子序列可以是任意 bytes-like 对象或 0 到 255 范围内的整数。

> **在 3.3 版本发生变更**：也接受 0 到 255 范围内的整数作为子序列。

#### bytes.rpartition(sep, /)

#### bytearray.rpartition(sep, /)

在 `sep` 最后一次出现的位置拆分序列，返回一个 3 元组，其中包含分隔符之前的部分，分隔符本身或其 bytearray 副本，以及分隔符之后的部分。如果找不到分隔符，返回的 3 元组中包含两个空的 bytes 或 bytearray 对象，以及原序列的副本。

要搜索的分隔符可以是任意 bytes-like 对象。

#### bytes.startswith(prefix[, start[, end]])

#### bytearray.startswith(prefix[, start[, end]])

如果二进制数据以指定的 `prefix` 开始则返回 `True`，否则返回 `False`。`prefix` 也可以是由多个前缀构成的元组。如果有可选项 `start`，将从所指定位置开始检查。如果有可选项 `end`，将在所指定位置停止比较。

要搜索的前缀可以是任意 bytes-like 对象。

#### bytes.translate(table, /, delete=b'')

#### bytearray.translate(table, /, delete=b'')

返回原 bytes 或 bytearray 对象的副本，移除其中所有在可选参数 `delete` 中出现的字节，其余的字节将通过给定的转换表进行映射，该转换表必须是长度为 256 的 bytes 对象。

你可以使用 `bytes.maketrans()` 方法来创建转换表。

对于仅删除字符的转换，请将 `table` 参数设为 `None`：

```python
b'read this short text'.translate(None, b'aeiou')
b'rd ths shrt txt'
```

> **在 3.6 版本发生变更**：`delete` 现在支持作为关键字参数。

bytes 和 bytearray 对象的以下方法具有默认行为，假定使用 ASCII 兼容的二进制格式，但通过传入适当的参数仍然可以用于任意二进制数据。请注意，本节中所有的 bytearray 方法均非原地操作，而是产生新的对象。

#### bytes.center(width, fillbyte=b' ', /)

#### bytearray.center(width, fillbyte=b' ', /)

返回原对象的副本，在长度为 `width` 的序列内居中。使用指定的 `fillbyte` 填充两边的空位（默认使用 ASCII 空格符）。对于 bytes 对象，如果 `width` 小于等于 `len(s)` 则返回原序列。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.ljust(width, fillbyte=b' ', /)

#### bytearray.ljust(width, fillbyte=b' ', /)

返回原对象的副本，在长度为 `width` 的序列内靠左对齐。使用指定的 `fillbyte` 填充空位（默认使用 ASCII 空格符）。对于 bytes 对象，如果 `width` 小于等于 `len(s)` 则返回原序列。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.lstrip(bytes=None, /)

#### bytearray.lstrip(bytes=None, /)

返回原序列的副本，移除指定的前导字节。`bytes` 参数为指定要移除字节值集合的二进制序列。如果省略或为 `None`，则 `bytes` 参数默认移除 ASCII 空白符。`bytes` 参数并非指定单个前缀；而是会移除其所有组合的值：

```python
b'   spacious   '.lstrip()
b'spacious   '
b'www.example.com'.lstrip(b'cmowz.')
b'example.com'
```

要移除的字节值二进制序列可以是任意 bytes-like 对象。参见 `removeprefix()`，该方法将删除单个前缀字符串，而不是整个集合中的字符。例如：

```python
b'Arthur: three!'.lstrip(b'Arthur: ')
b'ee!'
b'Arthur: three!'.removeprefix(b'Arthur: ')
b'three!'
```

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.rjust(width, fillbyte=b' ', /)

#### bytearray.rjust(width, fillbyte=b' ', /)

返回原对象的副本，在长度为 `width` 的序列内靠右对齐。使用指定的 `fillbyte` 填充空位（默认使用 ASCII 空格符）。对于 bytes 对象，如果 `width` 小于等于 `len(s)` 则返回原序列。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.rsplit(sep=None, maxsplit=-1)

#### bytearray.rsplit(sep=None, maxsplit=-1)

将二进制序列拆分为相同类型的子序列，使用 `sep` 作为分隔字符串。如果给出了 `maxsplit`，则最多进行 `maxsplit` 次拆分，从最右边开始。如果 `sep` 未指定或为 `None`，任何只由 ASCII 空白符组成的子序列都会被作为分隔符。除了从右侧开始拆分，`rsplit()` 的行为类似于下文详细描述的 `split()`。

#### bytes.rstrip(bytes=None, /)

#### bytearray.rstrip(bytes=None, /)

返回原序列的副本，移除指定的末尾字节。`bytes` 参数为指定要移除字节值集合的二进制序列。如果省略或为 `None`，则 `bytes` 参数默认移除 ASCII 空白符。`bytes` 参数并非指定单个后缀；而是会移除其所有组合的值：

```python
b'   spacious   '.rstrip()
b'   spacious'
b'mississippi'.rstrip(b'ipz')
b'mississ'
```

要移除的字节值二进制序列可以是任意 bytes-like 对象。参见 `removesuffix()`，该方法将删除单个后缀字符串，而不是整个集合中的字符。例如：

```python
b'Monty Python'.rstrip(b' Python')
b'M'
b'Monty Python'.removesuffix(b' Python')
b'Monty'
```

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.split(sep=None, maxsplit=-1)

#### bytearray.split(sep=None, maxsplit=-1)

将二进制序列拆分为相同类型的子序列，使用 `sep` 作为分隔字符串。如果给出了 `maxsplit` 且不为负数，则最多进行 `maxsplit` 次拆分（因此，列表最多会有 `maxsplit+1` 个元素）。如果 `maxsplit` 未指定或为 `-1`，则不限制拆分次数（进行所有可能的拆分）。

如果给出了 `sep`，则连续的分隔符不会被组合在一起而是被视为分隔空子序列（例如 `b'1,,2'.split(b',')` 将返回 `[b'1', b'', b'2']`）。`sep` 参数可能为作为单个分隔符的多字节序列。使用指定的分隔符拆分空序列将返回 `[b'']` 或 `[bytearray(b'')]`，取决于被拆分对象的类型。`sep` 参数可以是任何 bytes-like 对象。

例如：

```python
b'1,2,3'.split(b',')
[b'1', b'2', b'3']
b'1,2,3'.split(b',', maxsplit=1)
[b'1', b'2,3']
b'1,2,,3,'.split(b',')
[b'1', b'2', b'', b'3', b'']
b'1<>2<>3<4'.split(b'<>')
[b'1', b'2', b'3<4']
```

如果 `sep` 未指定或为 `None`，则会应用另一种拆分算法：连续的 ASCII 空白符会被视为单个分隔符，如果序列包含前缀或后缀空白，则结果将不包含开头或末尾的空字符串。因而，在不指定分隔符的情况下拆分空序列或仅包含 ASCII 空白符的序列将返回 `[]`。

例如：

```python
b'1 2 3'.split()
[b'1', b'2', b'3']
b'1 2 3'.split(maxsplit=1)
[b'1', b'2 3']
b'   1   2   3   '.split()
[b'1', b'2', b'3']
```

#### bytes.strip(bytes=None, /)

#### bytearray.strip(bytes=None, /)

返回原序列的副本，移除指定的前导和末尾字节。`bytes` 参数为指定要移除字节值集合的二进制序列。如果省略或为 `None`，则 `bytes` 参数默认移除 ASCII 空白符。`bytes` 参数并非指定单个前缀或后缀；而是会移除其所有组合的值：

```python
b'   spacious   '.strip()
b'spacious'
b'www.example.com'.strip(b'cmowz.')
b'example'
```

要移除的字节值二进制序列可以是任意 bytes-like 对象。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

bytes 和 bytearray 对象的以下方法假定使用 ASCII 兼容的二进制格式，不应当被应用于任意二进制数据。请注意，本节中所有的 bytearray 方法均非原地操作，而是产生新的对象。

#### bytes.capitalize()

#### bytearray.capitalize()

返回原序列的副本，其中每个字节将都会被解读为 ASCII 字符，首个字节的字符大写而其余为小写。非 ASCII 字节值将保持原样。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.expandtabs(tabsize=8)

#### bytearray.expandtabs(tabsize=8)

返回序列的副本，其中所有的 ASCII 制表符会由一个或多个 ASCII 空格替换，具体位置取决于当前列位置和给定的制表符宽度。每 `tabsize` 个字节设一个制表位（默认值 8 时制表位在列 0、8、16 等依次递增）。要展开序列，当前列将被设为零并逐一检查序列中的每个字节。如果字节为 ASCII 制表符（`b'\t'`），则会在结果中插入一个或多个空格符，直到当前列等于下一个制表位。（制表符本身不会被复制。）如果当前字节为 ASCII 换行符（`b'\n'`）或回车符（`b'\r'`），它会被复制并将当前列重设为零。任何其他字节值将不加修改地复制并将当前列加一，不论该字节值在被打印时会如何显示：

```python
b'01\t012\t0123\t01234'.expandtabs()
b'01      012     0123    01234'
b'01\t012\t0123\t01234'.expandtabs(4)
b'01  012 0123    01234'
```

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.isalnum()

#### bytearray.isalnum()

如果序列中的所有字节都是字母类 ASCII 字符或 ASCII 十进制数码并且序列不为空，则返回 `True`，否则返回 `False`。字母类 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。ASCII 十进制数码就是字节值包含在序列 `b'0123456789'` 中的字符。

例如：

```python
b'ABCabc1'.isalnum()
True
b'ABC abc1'.isalnum()
False
```

#### bytes.isalpha()

#### bytearray.isalpha()

如果序列中的所有字节都是字母类 ASCII 字符并且序列不为空，则返回 `True`，否则返回 `False`。字母类 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

例如：

```python
b'ABCabc'.isalpha()
True
b'ABCabc1'.isalpha()
False
```

#### bytes.isascii()

#### bytearray.isascii()

如果序列为空或序列中的所有字节都是 ASCII，则返回 `True`，否则返回 `False`。ASCII 字节的范围是 0-0x7F。

3.7 版本新增。

#### bytes.isdigit()

#### bytearray.isdigit()

如果序列中的所有字节都是 ASCII 十进制数码并且序列不为空，则返回 `True`，否则返回 `False`。ASCII 十进制数码就是字节值包含在序列 `b'0123456789'` 中的字符。

例如：

```python
b'1234'.isdigit()
True
b'1.23'.isdigit()
False
```

#### bytes.islower()

#### bytearray.islower()

如果序列中至少有一个小写 ASCII 字符并且没有大写 ASCII 字符，则返回 `True`，否则返回 `False`。

例如：

```python
b'hello world'.islower()
True
b'Hello world'.islower()
False
```

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

#### bytes.isspace()

#### bytearray.isspace()

如果序列中的所有字节都是 ASCII 空白符并且序列不为空则返回 `True`，否则返回 `False`。ASCII 空白符就是字节值包含在序列 `b' \t\n\r\x0b\f'` 中的字符（空格、制表、换行、回车、垂直制表、换页）。

#### bytes.istitle()

#### bytearray.istitle()

如果序列为 ASCII 标题形式并且序列不为空，则返回 `True`，否则返回 `False`。关于 "titlecase" 的定义请参阅 `bytes.title()`。

例如：

```python
b'Hello World'.istitle()
True
b'Hello world'.istitle()
False
```

#### bytes.isupper()

#### bytearray.isupper()

如果序列中至少有一个大写字母类 ASCII 字符并且没有小写 ASCII 字符，则返回 `True`，否则返回 `False`。

例如：

```python
b'HELLO WORLD'.isupper()
True
b'Hello world'.isupper()
False
```

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

#### bytes.lower()

#### bytearray.lower()

返回原序列的副本，其中所有大写 ASCII 字符均转换为对应的小写形式。

例如：

```python
b'Hello World'.lower()
b'hello world'
```

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.splitlines(keepends=False)

#### bytearray.splitlines(keepends=False)

返回由原二进制序列中各行组成的列表，在 ASCII 行边界符的位置拆分。此方法使用 通用换行符 方式来拆分各行。结果列表中不包含换行符，除非给出了 `keepends` 且为真值。

例如：

```python
b'ab c\n\nde fg\rkl\r\n'.splitlines()
[b'ab c', b'', b'de fg', b'kl']
b'ab c\n\nde fg\rkl\r\n'.splitlines(keepends=True)
[b'ab c\n', b'\n', b'de fg\r', b'kl\r\n']
```

不同于 `split()`，当给出了分隔符 `sep` 时，对于空字符串此方法返回空列表，而末尾的换行不会产生额外的行：

```python
b"".split(b'\n'), b"Two lines\n".split(b'\n')
([b''], [b'Two lines', b''])
b"".splitlines(), b"One line\n".splitlines()
([], [b'One line'])
```

#### bytes.swapcase()

#### bytearray.swapcase()

返回原序列的副本，其中所有小写 ASCII 字符均转换为对应的大写形式，反之亦然。

例如：

```python
b'Hello World'.swapcase()
b'hELLO wORLD'
```

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

不同于 `str.swapcase()`，在二进制版本中 `bin.swapcase().swapcase() == bin` 总是成立。大小写转换在 ASCII 中是对称的，即使对于任意 Unicode 码位来说通常并不如此。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.title()

#### bytearray.title()

返回原二进制序列的标题版本，其中每个单词以大写 ASCII 字符为开头，其余字母为小写。不区分大小写的字节值将保持原样。

例如：

```python
b'Hello world'.title()
b'Hello World'
```

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。所有其他字节值都不区分大小写。

该算法使用一种简单的与语言无关的定义，将连续的字母组合视为单词。该定义在多数情况下都很有效，但它也意味着代表缩写形式与所有格的撇号也会成为单词边界，这可能导致不希望的结果：

```python
b"they're bill's friends from the UK".title()
b"They'Re Bill'S Friends From The Uk"
```

可以使用正则表达式来构造针对撇号的变通方案：

```python
import re
def titlecase(s):
    return re.sub(rb"[A-Za-z]+('[A-Za-z]+)?",
                  lambda mo: mo.group(0)[0:1].upper() +
                             mo.group(0)[1:].lower(),
                  s)

titlecase(b"they're bill's friends.")
b"They're Bill's Friends."
```

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.upper()

#### bytearray.upper()

返回原序列的副本，其中所有小写 ASCII 字符均转换为对应的大写形式。

例如：

```python
b'Hello World'.upper()
b'HELLO WORLD'
```

小写 ASCII 字符就是字节值包含在序列 `b'abcdefghijklmnopqrstuvwxyz'` 中的字符。大写 ASCII 字符就是字节值包含在序列 `b'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` 中的字符。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

#### bytes.zfill(width, /)

#### bytearray.zfill(width, /)

返回原序列的副本，在左边填充 ASCII `b'0'` 数码使其长度变为 `width`。正负值前缀（`b'+'`/`b'-'`）的处理方式是在正负符号之后填充而非在之前。对于 bytes 对象，如果 `width` 小于等于 `len(seq)` 则返回原序列。

例如：

```python
b"42".zfill(5)
b'00042'
b"-42".zfill(5)
b'-0042'
```

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

### printf 风格的字节串格式化

> **备注**：此处介绍的格式化操作具有多种怪异特性，可能导致许多常见错误（例如无法正确显示元组和字典）。如果要打印的值可能为元组或字典，请将其放入一个元组中。

字节串对象（`bytes`/`bytearray`）有一个独一无二的内置操作：`%` 运算符（取模）。这也被称为字节串的格式化或插值运算符。给定 `format % values`（其中 `format` 为一个字节串对象），`format` 中的 `%` 转换标记符将被替换为 `values` 中零个或多个元素。其效果类似于在 C 语言中使用 `sprintf()`。

如果 `format` 要求一个单独参数，则 `values` 可以为一个非元组对象。[5] 否则的话，`values` 必须或者是一个包含项数与格式字节串对象中指定的转换符项数恰好相同的元组，或者是一个单独映射对象（例如字典）。

转换标记符包含两个或更多的字符并具有以下组成，且必须遵循此处规定的顺序：

1. `'%'` 字符，用于标记转换符的起始。
2. 映射键（可选），由加圆括号的字符序列组成（例如 `(somename)`）。
3. 转换旗标（可选），用于影响某些转换类型的结果。
4. 最小字段宽度（可选）。如果指定为 `'*'`（星号），则实际宽度会从 `values` 元组的下一元素中读取，要转换的对象则为最小字段宽度和可选的精度之后的值。
5. 精度（可选），以在 `'.'`（点号）之后加精度值的形式给出。如果指定为 `'*'`（星号），则实际精度会从 `values` 元组的下一元素中读取，要转换的对象则为精度之后的值。
6. 长度修饰符（可选）。
7. 转换类型。

当右边的参数为一个字典（或其他映射类型）时，字节串对象中的格式必须包含圆括号插入的映射键，该键会紧跟在 `'%'` 字符之后被插入到该字典中。映射键将从映射中选取要格式化的值。例如：

```python
print(b'%(language)s has %(number)03d quote types.' %
      {b'language': b"Python", b"number": 2})
b'Python has 002 quote types.'
```

在此情况下格式中不能出现 `*` 标记符（因其需要一个序列类的参数列表）。

转换旗标为：

| 旗标 | 含义 |
| --- | --- |
| `'#'` | 值的转换将使用"替代形式"（具体定义见下文）。 |
| `'0'` | 转换将为数字值填充零字符。 |
| `'-'` | 转换值将靠左对齐（如果同时给出 `'0'` 转换，则将覆盖后者）。 |
| `' '` | （一个空格）在带符号的转换所产生的正数（或空字符串）之前留一个空格。 |
| `'+'` | 符号字符（`'+'` 或 `'-'`）将显示于转换结果的开头（覆盖"空格"旗标）。 |

可以给出长度修饰符（`h`、`l` 或 `L`），但会被忽略，因为对 Python 而言没有必要——所以例如 `%ld` 等价于 `%d`。

转换类型为：

| 转换符 | 含义 | 备注 |
| --- | --- | --- |
| `'d'` | 有符号十进制整数。 | |
| `'i'` | 有符号十进制整数。 | |
| `'o'` | 有符号八进制数。 | (1) |
| `'u'` | 过时类型——它等同于 `'d'`。 | (8) |
| `'x'` | 有符号十六进制数（小写）。 | (2) |
| `'X'` | 有符号十六进制数（大写）。 | (2) |
| `'e'` | 浮点指数格式（小写）。 | (3) |
| `'E'` | 浮点指数格式（大写）。 | (3) |
| `'f'` | 浮点十进制格式。 | (3) |
| `'F'` | 浮点十进制格式。 | (3) |
| `'g'` | 浮点格式。如果指数小于 -4 或不小于精度则使用小写指数格式，否则使用十进制格式。 | (4) |
| `'G'` | 浮点格式。如果指数小于 -4 或不小于精度则使用大写指数格式，否则使用十进制格式。 | (4) |
| `'c'` | 单个字节（接受整数或单个字节对象）。 | |
| `'b'` | 字节串（任何遵循缓冲区协议或是具有 `__bytes__()` 的对象）。 | (5) |
| `'s'` | `'s'` 是 `'b'` 的别名，只应在 Python2/3 代码库中使用。 | (6) |
| `'a'` | 字节串（使用 `repr(obj).encode('ascii', 'backslashreplace')` 转换任何 Python 对象）。 | (5) |
| `'r'` | `'r'` 是 `'a'` 的别名，只应在 Python2/3 代码库中使用。 | (7) |
| `'%'` | 不转换参数，在结果中输出一个 `'%'` 字符。 | |

备注：

1. 替代形式会在第一个数码之前插入标示八进制的前缀（`'0o'`）。
2. 替代形式会在第一个数码之前插入 `'0x'` 或 `'0X'` 前缀（取决于是使用 `'x'` 还是 `'X'` 格式）。
3. 替代形式总是使得结果中包含一个小数点，即使其后并没有数码。

   精度决定了小数点后的数码位数，默认为 6。

4. 替代形式总是使得结果中包含一个小数点，末尾的零不会被移除（在其他情况下会被移除）。

   精度决定了小数点前后的有效数码位数，默认为 6。

5. 如果精度为 N，输出将截短为 N 个字符。
6. `b'%s'` 已弃用，但在 3.x 系列中不会被移除。
7. `b'%r'` 已弃用，但在 3.x 系列中不会被移除。
8. 参见 PEP 237。

> **备注**：此方法的 bytearray 版本并非原地操作——它总是产生一个新对象，即便没有做任何改动。

> **参见**：PEP 461 - 为 bytes 和 bytearray 添加 % 格式化

3.5 版本新增。

### 内存视图

`memoryview` 对象允许 Python 代码访问一个对象的内部数据，只要该对象支持 缓冲协议 而无需进行拷贝。

#### class memoryview(object)

创建一个引用 `object` 的 memoryview。`object` 必须支持缓冲协议。支持缓冲协议的内置对象包括 `bytes` 和 `bytearray`。

memoryview 具有 元素 的概念，即由原始对象处理的基本内存单元。对于许多简单类型例如 `bytes` 和 `bytearray` 来说，一个元素就是一个字节，但是其他的类型例如 `array.array` 可能有更大的元素。

memoryviews 对其底层数据的类型是泛型的。

`len(view)` 等于 `tolist()` 的长度，即视图的嵌套列表表示。如果 `view.ndim == 1`，则其等于视图中元素的数量。

> **在 3.12 版本发生变更**：如果 `view.ndim == 0`，则 `len(view)` 现在会引发 `TypeError` 而不是返回 1。

`itemsize` 属性会给出单个元素所占的字节数。

memoryview 支持通过切片和索引访问其数据。一维切片会产生一个子视图：

```python
v = memoryview(b'abcefg')
v[1]
98
v[-1]
103
v[1:4]
<memory at 0x7f3ddc9f4350>
bytes(v[1:4])
b'bce'
```

如果 `format` 是 `struct` 模块中的原生格式说明符之一，则也支持使用整数或整数元组进行索引，并返回具有正确类型的单个元素。一维 memoryview 可以使用一个整数或一个单整数元组进行索引。多维 memoryview 可以使用恰好 `ndim` 个整数的元组进行索引，其中 `ndim` 是维数。零维 memoryview 可以使用空元组进行索引。

以下是一个使用非字节格式的例子：

```python
import array
a = array.array('l', [-11111111, 22222222, -33333333, 44444444])
m = memoryview(a)
m[0]
-11111111
m[-1]
44444444
m[::2].tolist()
[-11111111, -33333333]
```

如果底层对象是可写的，则 memoryview 支持一维切片赋值。改变大小则不被允许：

```python
data = bytearray(b'abcefg')
v = memoryview(data)
v.readonly
False
v[0] = ord(b'z')
data
bytearray(b'zbcefg')
v[1:4] = b'123'
data
bytearray(b'z123fg')
v[2:3] = b'spam'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: memoryview assignment: lvalue and rvalue have different structures
v[2:6] = b'spam'
data
bytearray(b'z1spam')
```

格式为 `'B'`、`'b'` 或 `'c'` 的可哈希（只读）类型的一维 memoryview 也是可哈希的。哈希定义为 `hash(m) == hash(m.tobytes())`：

```python
v = memoryview(b'abcefg')
hash(v) == hash(b'abcefg')
True
hash(v[2:4]) == hash(b'ce')
True
hash(v[::-2]) == hash(b'abcefg'[::-2])
True
```

> **在 3.3 版本发生变更**：一维 memoryview 现在可以被切片。格式为 `'B'`、`'b'` 或 `'c'` 的一维 memoryview 现在是可哈希的。
>
> **在 3.4 版本发生变更**：memoryview 现在会自动注册到 `collections.abc.Sequence`。
>
> **在 3.5 版本发生变更**：memoryview 现在可以使用整数元组进行索引。
>
> **在 3.14 版本发生变更**：memoryview 现在是一个泛型类型。

memoryview 具有几个方法：

#### __eq__(exporter)

如果 memoryview 与 PEP 3118 导出器的形状相等，并且当操作数各自的格式代码使用 `struct` 语法解释时所有对应值都相等，则它们相等。

对于 `tolist()` 目前支持的 `struct` 格式字符串子集，如果 `v.tolist() == w.tolist()`，则 `v` 和 `w` 相等：

```python
import array
a = array.array('I', [1, 2, 3, 4, 5])
b = array.array('d', [1.0, 2.0, 3.0, 4.0, 5.0])
c = array.array('b', [5, 3, 1])
x = memoryview(a)
y = memoryview(b)
x == a == y == b
True
x.tolist() == a.tolist() == y.tolist() == b.tolist()
True
z = y[::-2]
z == c
True
z.tolist() == c.tolist()
True
```

如果两边的格式字符串都不受 `struct` 模块支持，则对象将始终比较为不相等（即使格式字符串和缓冲区内容完全相同）：

```python
from ctypes import BigEndianStructure, c_long
class BEPoint(BigEndianStructure):
    _fields_ = [("x", c_long), ("y", c_long)]

point = BEPoint(100, 200)
a = memoryview(point)
b = memoryview(point)
a == point
False
a == b
False
```

请注意，与浮点数一样，对于 memoryview 对象，`v is w` 并不意味着 `v == w`。

> **在 3.3 版本发生变更**：此前的版本会比较原始内存而忽略条目格式与逻辑数组结构。

#### tobytes(order='C')

将缓冲区中的数据作为字节串返回。这相当于在 memoryview 上调用 `bytes` 构造器。

```python
m = memoryview(b"abc")
m.tobytes()
b'abc'
bytes(m)
b'abc'
```

对于非连续数组，结果等于将所有元素转换为 bytes 后的扁平化列表表示。`tobytes()` 支持所有的格式字符串，包括那些不在 `struct` 模块语法内的格式。

> **3.8 版本新增**：`order` 可以为 `{'C', 'F', 'A'}`。当 `order` 为 `'C'` 或 `'F'` 时，原始数组的数据会被转换为 C 或 Fortran 顺序。对于连续视图，`'A'` 会返回物理内存的精确副本。特别地，内存中的 Fortran 顺序会被保留。对于非连续视图，数据会先被转换为 C 顺序。`order=None` 与 `order='C'` 相同。

#### hex(*, bytes_per_sep=1)

#### hex(sep, bytes_per_sep=1)

返回一个字符串对象，其中分别以两个十六进制数码表示缓冲区里的每个字节。

```python
m = memoryview(b"abc")
m.hex()
'616263'
```

3.5 版本新增。

> **在 3.8 版本发生变更**：与 `bytes.hex()` 类似，`memoryview.hex()` 现在支持可选的 `sep` 和 `bytes_per_sep` 参数，以在十六进制输出中的字节之间插入分隔符。

#### tolist()

将缓冲区内的数据以一个元素列表的形式返回。

```python
memoryview(b'abc').tolist()
[97, 98, 99]
import array
a = array.array('d', [1.1, 2.2, 3.3])
m = memoryview(a)
m.tolist()
[1.1, 2.2, 3.3]
```

> **在 3.3 版本发生变更**：`tolist()` 现在支持 `struct` 模块语法中的所有单字符原生格式以及多维表示形式。

#### toreadonly()

返回 memoryview 对象的只读版本。原始的 memoryview 对象不会被改变。

```python
m = memoryview(bytearray(b'abc'))
mm = m.toreadonly()
mm.tolist()
[97, 98, 99]
mm[0] = 42
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot modify read-only memory
m[0] = 43
mm.tolist()
[43, 98, 99]
```

3.8 版本新增。

#### release()

释放由 memoryview 对象所公开的底层缓冲区。许多对象在被视图锁定时会执行特殊操作（例如，`bytearray` 会暂时禁止调整大小）；因此，调用 `release()` 可以方便地尽早移除这些限制（并释放任何悬挂的资源）。

在此方法被调用后，对视图的任何进一步操作都会引发 `ValueError`（`release()` 本身除外，它可以被调用多次）：

```python
m = memoryview(b'abc')
m.release()
m[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operation forbidden on released memoryview object
```

上下文管理协议也可用于达到类似的效果，通过 `with` 语句：

```python
with memoryview(b'abc') as m:
    m[0]

97
m[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operation forbidden on released memoryview object
```

3.2 版本新增。

#### cast(format, /)

#### cast(format, shape, /)

将 memoryview 转换为新的格式或形状。`shape` 默认为 `[byte_length//new_itemsize]`，这意味着结果视图将是一维的。返回值是一个新的 memoryview，但缓冲区本身不会被复制。支持的转换有 1D -> C 连续 和 C 连续 -> 1D。

目标格式被限制为 `struct` 语法中的单个元素原生格式。其中一种格式必须为字节格式（`'B'`、`'b'` 或 `'c'`）。结果的字节长度必须与原始长度相同。请注意，所有字节长度可能都依赖于操作系统。

将 1D/长整型转换为 1D/无符号字节：

```python
import array
a = array.array('l', [1,2,3])
x = memoryview(a)
x.format
'l'
x.itemsize
8
len(x)
3
x.nbytes
24
y = x.cast('B')
y.format
'B'
y.itemsize
1
len(y)
24
y.nbytes
24
```

将 1D/无符号字节转换为 1D/字符：

```python
b = bytearray(b'zyz')
x = memoryview(b)
x[0] = b'a'
Traceback (most recent call last):
  ...
TypeError: memoryview: invalid type for format 'B'
y = x.cast('c')
y[0] = b'a'
b
bytearray(b'ayz')
```

将 1D/字节转换为 3D/整数再转换为 1D/有符号字符：

```python
import struct
buf = struct.pack("i"*12, *list(range(12)))
x = memoryview(buf)
y = x.cast('i', shape=[2,2,3])
y.tolist()
[[[0, 1, 2], [3, 4, 5]], [[6, 7, 8], [9, 10, 11]]]
y.format
'i'
y.itemsize
4
len(y)
2
y.nbytes
48
z = y.cast('b')
z.format
'b'
z.itemsize
1
len(z)
48
z.nbytes
48
```

将 1D/无符号长整型转换为 2D/无符号长整型：

```python
buf = struct.pack("L"*6, *list(range(6)))
x = memoryview(buf)
y = x.cast('L', shape=[2,3])
len(y)
2
y.nbytes
48
y.tolist()
[[0, 1, 2], [3, 4, 5]]
```

3.3 版本新增。

> **在 3.5 版本发生变更**：当转换为字节视图时，源格式不再受限制。

#### count(value, /)

统计 `value` 出现的次数。

3.14 版本新增。

#### index(value, start=0, stop=sys.maxsize, /)

返回 `value` 第一次出现的索引（在索引 `start` 处或之后，且位于索引 `stop` 之前）。

如果找不到 `value`，则抛出 `ValueError`。

3.14 版本新增。

还有几个只读属性可用：

##### obj

memoryview 的底层对象：

```python
b  = bytearray(b'xyz')
m = memoryview(b)
m.obj is b
True
```

3.3 版本新增。

##### nbytes

`nbytes == product(shape) * itemsize == len(m.tobytes())`。这是数组在连续表示时将会占用的字节总空间。它不一定等于 `len(m)`：

```python
import array
a = array.array('i', [1,2,3,4,5])
m = memoryview(a)
len(m)
5
m.nbytes
20
y = m[::2]
len(y)
3
y.nbytes
12
len(y.tobytes())
12
```

多维数组：

```python
import struct
buf = struct.pack("d"*12, *[1.5*x for x in range(12)])
x = memoryview(buf)
y = x.cast('d', shape=[3,4])
y.tolist()
[[0.0, 1.5, 3.0, 4.5], [6.0, 7.5, 9.0, 10.5], [12.0, 13.5, 15.0, 16.5]]
len(y)
3
y.nbytes
96
```

3.3 版本新增。

##### readonly

一个表明内存是否只读的布尔值。

##### format

一个字符串，包含视图中每个元素的格式（表示为 `struct` 模块的样式）。memoryview 可以从具有任意格式字符串的导出器创建，但某些方法（例如 `tolist()`）被限制为原生单元素格式。

> **在 3.3 版本发生变更**：格式 `'B'` 现在会按照 `struct` 模块语法来处理。这意味着 `memoryview(b'abc')[0] == b'abc'[0] == 97`。

##### itemsize

memoryview 中每个元素的大小（以字节为单位）：

```python
import array, struct
m = memoryview(array.array('H', [32000, 32001, 32002]))
m.itemsize
2
m[0]
32000
struct.calcsize('H') == m.itemsize
True
```

##### ndim

一个整数，表示内存所代表的多维数组具有多少个维度。

##### shape

一个整数元组，长度为 `ndim`，表示内存作为一个 N 维数组的形状。

> **在 3.3 版本发生变更**：当 `ndim = 0` 时为空元组而不是 `None`。

##### strides

一个整数元组，长度为 `ndim`，给出以字节表示的大小，以便访问数组中每个维度上的每个元素。

> **在 3.3 版本发生变更**：当 `ndim = 0` 时为空元组而不是 `None`。

##### suboffsets

供 PIL 风格的数组内部使用。该值仅供参考。

##### c_contiguous

一个表明内存是否为 C 连续的布尔值。

3.3 版本新增。

##### f_contiguous

一个表明内存是否为 Fortran 连续的布尔值。

3.3 版本新增。

##### contiguous

一个表明内存是否连续的布尔值。

3.3 版本新增。

有关自由线程构建中 memoryview 对象的线程安全的信息，请参阅 memoryview 对象的线程安全。

## 集合类型 --- set、frozenset

`set` 对象是由具有唯一性的 hashable 对象所组成的无序多项集。常见的用途包括成员检测、从序列中去除重复项，以及数学中的集合类计算，例如交集、并集、差集与对称差集等等。（关于其他容器对象请参看 `dict`、`list` 与 `tuple` 等内置类，以及 `collections` 模块。）参见 内置类型操作的时间复杂度 以了解各种集合操作的开销。

与其他多项集一样，集合也支持 `x in set`、`len(set)` 和 `for x in set`。作为一种无序的多项集，集合并不记录元素位置或插入顺序。相应地，集合不支持索引、切片或其他序列类的操作。

目前有两种内置集合类型，`set` 和 `frozenset`。`set` 类型是可变的——其内容可以使用 `add()` 和 `remove()` 这样的方法来改变。由于是可变类型，它没有哈希值，且不能被用作字典的键或其他集合的元素。`frozenset` 类型是不可变并且为 hashable——其内容在被创建后不能再改变；因此它可以被用作字典的键或其他集合的元素。

除了可以使用 `set` 构造器，非空的 set（不是 frozenset）还可以通过将以逗号分隔的元素列表包含于花括号之内来创建，例如：`{'jack', 'sjoerd'}`。

两个类的构造器具有相同的作用方式：

#### class set(iterable=(), /)

#### class frozenset(iterable=(), /)

返回一个新的 `set` 或 `frozenset` 对象，其元素来自于 `iterable`。集合的元素必须为 hashable。要表示由集合对象构成的集合，所有的内层集合必须为 `frozenset` 对象。如果未指定 `iterable`，则将返回一个新的空集合。

集合可用多种方式来创建：

- 使用以逗号分隔的元素列表包含于花括号之内：`{'jack', 'sjoerd'}`
- 使用集合推导式：`{c for c in 'abracadabra' if c not in 'abc'}`
- 使用类型构造器：`set()`、`set('foobar')`、`set(['a', 'b', 'foo'])`

`set` 和 `frozenset` 的实例提供以下操作：

#### len(s)

返回集合 s 中的元素数量（即 s 的基数）。

#### x in s

检测 x 是否为 s 中的成员。

#### x not in s

检测 x 是否非 s 中的成员。

#### frozenset.isdisjoint(other, /)

#### set.isdisjoint(other, /)

如果集合中没有与 `other` 共有的元素则返回 `True`。当且仅当两个集合的交集为空集合时，两者为不相交集合。

#### frozenset.issubset(other, /)

#### set.issubset(other, /)

#### set <= other

检测是否集合中的每个元素都在 `other` 之中。

#### set < other

检测集合是否为 `other` 的真子集，即 `set <= other and set != other`。

#### frozenset.issuperset(other, /)

#### set.issuperset(other, /)

#### set >= other

检测是否 `other` 中的每个元素都在集合之中。

#### set > other

检测集合是否为 `other` 的真超集，即 `set >= other and set != other`。

#### frozenset.union(*others)

#### set.union(*others)

#### set | other | ...

返回一个新集合，其中元素来自原集合和所有 others。

#### frozenset.intersection(*others)

#### set.intersection(*others)

#### set & other & ...

返回一个新集合，其中元素为原集合和所有 others 共有的元素。

#### frozenset.difference(*others)

#### set.difference(*others)

#### set - other - ...

返回一个新集合，其中元素为原集合中所有不属于 others 的元素。

#### frozenset.symmetric_difference(other, /)

#### set.symmetric_difference(other, /)

#### set ^ other

返回一个新集合，其中的元素或属于原集合或属于 other，但不能同时属于两者。

#### frozenset.copy()

#### set.copy()

返回原集合的浅拷贝。

注意，`union()`、`intersection()`、`difference()`、`symmetric_difference()`、`issubset()` 和 `issuperset()` 方法的非运算符版本会接受任意可迭代对象作为参数。相比之下，它们所对应的运算符版本则要求其参数为集合。这就排除了容易出错的构造形式例如 `set('abc') & 'cbs'`，而推荐可读性更强的 `set('abc').intersection('cbs')`。

`set` 和 `frozenset` 均支持集合与集合的比较。两个集合当且仅当每个集合中的每个元素均包含于另一个集合之内（即各为另一个的子集）时则相等。一个集合当且仅当其为另一个集合的真子集（即为后者的子集但两者不相等）时才小于另一个集合。一个集合当且仅当其为另一个集合的真超集（即为后者的超集但两者不相等）时才大于另一个集合。

`set` 的实例与 `frozenset` 的实例之间基于它们的成员进行比较。例如 `set('abc') == frozenset('abc')` 返回 `True`，`set('abc') in set([frozenset('abc')])` 也一样。

子集与相等比较并不能推广为完全排序函数。例如，任意两个非空且不相交的集合不相等且互不为对方的子集，因此以下运算均返回 `False`：`a<b`、`a==b` 或者 `a>b`。

由于集合仅定义了部分排序（子集关系），因此 `list.sort()` 方法的输出对于集合的列表没有定义。

集合的元素，与字典的键类似，必须为 hashable。

混合了 `set` 实例与 `frozenset` 的二进制位运算将返回第一个操作数的类型。例如：`frozenset('ab') | set('bc')` 将返回 `frozenset` 的实例。

下表列出了可用于 `set` 而不能用于不可变的 `frozenset` 实例的操作：

#### set.update(*others)

#### set |= other | ...

更新集合，添加来自所有 others 的元素。

#### set.intersection_update(*others)

#### set &= other & ...

更新集合，只保留其中在所有 others 中也存在的元素。

#### set.difference_update(*others)

#### set -= other | ...

更新集合，移除其中也存在于 others 中的元素。

#### set.symmetric_difference_update(other, /)

#### set ^= other

更新集合，只保留存在于集合的一方而非共同存在的元素。

#### set.add(elem, /)

将元素 `elem` 添加到集合中。

#### set.remove(elem, /)

从集合中移除元素 `elem`。如果 `elem` 不存在于集合中则会引发 `KeyError`。

#### set.discard(elem, /)

如果元素 `elem` 存在于集合中则将其移除。

#### set.pop()

从集合中移除并返回任意一个元素。如果集合为空则会引发 `KeyError`。

#### set.clear()

从集合中移除所有元素。

注意，`update()`、`intersection_update()`、`difference_update()` 和 `symmetric_difference_update()` 方法的非运算符版本将接受任意可迭代对象作为参数。

注意，`__contains__()`、`remove()` 和 `discard()` 方法的 `elem` 参数可能是一个集合。为支持对一个等价的 `frozenset` 进行搜索，会根据 `elem` 创建一个临时的 frozenset。

set 和 frozenset 对其元素的类型是泛型的。

> **参见**：有关 set 对象的线程安全保证的详细信息，请参阅 set 对象的线程安全。

## 映射类型 --- dict、frozendict

映射对象会将 hashable 值映射到任意对象。目前有两种标准映射类型，即字典和 frozendict。（关于其他容器对象请参看内置 `list`、`set` 和 `tuple` 类，以及 `collections` 模块。）参见 内置类型操作的时间复杂度 以了解各种字典操作的开销。

字典的键几乎可以是任何值。非 hashable 的值，即包含列表、字典或其他可变类型（此类对象基于值而非对象标识进行比较）的值不可用作键。比较结果相等的值（如 `1`、`1.0` 和 `True` 等）可互换使用以索引同一字典条目。

#### class dict(**kwargs)

#### class dict(mapping, /, **kwargs)

#### class dict(iterable, /, **kwargs)

返回一个新的字典，基于可选的位置参数和可能为空的关键字参数集来初始化。

字典可用多种方式来创建：

- 使用花括号内以逗号分隔的 `键: 值` 对列表：`{'jack': 4098, 'sjoerd': 4127}` 或 `{4098: 'jack', 4127: 'sjoerd'}`
- 使用字典推导式：`{}`、`{x: x ** 2 for x in range(10)}`
- 使用类型构造器：`dict()`、`dict([('foo', 100), ('bar', 200)])`、`dict(foo=100, bar=200)`

如果没有给出位置参数，将创建一个空字典。如果给出一个位置参数并且其定义了 `keys()` 方法，则将通过以该方法返回的每个键对参数调用 `__getitem__()` 来创建字典。否则的话，位置参数必须为一个 `iterable` 对象。该可迭代对象中的每一项本身必须为一个刚好包含两个元素的可迭代对象。每一项中的第一个对象将成为新字典的一个键，第二个对象将成为其对应的值。如果一个键出现一次以上，该键的最后一个值将成为其在新字典中对应的值。

如果给出了关键字参数，则关键字参数及其值会被加入到基于位置参数创建的字典。如果要加入的键已存在，来自关键字参数的值将替换来自位置参数的值。

当且仅当字典具有相同的 `(键, 值)` 对时（不考虑排序）它们才比较相等。排序比较（`'<'`、`'<='`、`'>='`、`'>'`）会引发 `TypeError`。为了说明字典的创建与相等性，以下示例均返回与 `{"one": 1, "two": 2, "three": 3}` 相等的字典：

```python
a = dict(one=1, two=2, three=3)
b = {'one': 1, 'two': 2, 'three': 3}
c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))
d = dict([('two', 2), ('one', 1), ('three', 3)])
e = dict({'three': 3, 'one': 1, 'two': 2})
f = dict({'one': 1, 'three': 3}, two=2)
a == b == c == d == e == f
True
```

像第一个例子那样提供关键字参数的方式只适用于键是有效 Python 标识符的情况。否则的话，任何有效键都可以使用。

字典会保留插入顺序。请注意对已有键的更新不会影响顺序。删除后再次添加的键将被插入到末尾。

```python
d = {"one": 1, "two": 2, "three": 3, "four": 4}
d
{'one': 1, 'two': 2, 'three': 3, 'four': 4}
list(d)
['one', 'two', 'three', 'four']
list(d.values())
[1, 2, 3, 4]
d["one"] = 42
d
{'one': 42, 'two': 2, 'three': 3, 'four': 4}
del d["two"]
d["two"] = None
d
{'one': 42, 'three': 3, 'four': 4, 'two': None}
```

> **在 3.7 版本发生变更**：字典顺序会确保为插入顺序。此行为是自 3.6 版开始的 CPython 实现细节。

字典对两种类型是泛型的，分别表示字典键和值的类型。

以下是字典所支持的操作（因而自定义的映射类型也应当支持）：

#### list(d)

返回字典 d 中使用的所有键的列表。

#### len(d)

返回字典 d 中的项数。

#### d[key]

返回 d 中以 `key` 为键的项。如果映射中不存在 `key` 则会引发 `KeyError`。

如果字典的子类定义了方法 `__missing__()` 并且 `key` 不存在，则 `d[key]` 操作将调用该方法并附带键 `key` 作为参数。`d[key]` 随后将返回或引发 `__missing__(key)` 调用所返回或引发的任何内容。没有其他操作或方法会发起调用 `__missing__()`。如果未定义 `__missing__()`，则会引发 `KeyError`。`__missing__()` 必须是一个方法；它不能是一个实例变量：

```python
class Counter(dict):
    def __missing__(self, key):
        return 0

c = Counter()
c['red']
0
c['red'] += 1
c['red']
1
```

上面的例子显示了 `collections.Counter` 实现代码的局部。还有另一个不同的 `__missing__()` 方法是由 `collections.defaultdict` 所使用的。

#### d[key] = value

将 `d[key]` 设为 `value`。

#### del d[key]

将 `d[key]` 从 d 中移除。如果映射中不存在 `key` 则会引发 `KeyError`。

#### key in d

如果 d 中存在键 `key` 则返回 `True`，否则返回 `False`。

#### key not in d

等价于 `not key in d`。

#### iter(d)

返回以字典的键为元素的迭代器。这是 `iter(d.keys())` 的快捷方式。

#### clear()

移除字典中的所有元素。

#### copy()

返回原字典的浅拷贝。

#### classmethod fromkeys(iterable, value=None, /)

使用来自 `iterable` 的键创建一个新字典，并将键值设为 `value`。

`fromkeys()` 是一个返回新字典的类方法。`value` 默认为 `None`。所有值都只引用一个单独的实例，因此让 `value` 成为一个可变对象例如空列表通常是没有意义的。要获得不同的值，请改用字典推导式。

#### get(key, default=None, /)

如果 `key` 存在于字典中则返回 `key` 的值，否则返回 `default`。如果未给出 `default` 则默认为 `None`，因而此方法绝不会引发 `KeyError`。

#### items()

返回由字典项（`(键, 值)` 对）组成的一个新视图。参见 视图对象 的文档。

#### keys()

返回由字典键组成的一个新视图。参见 视图对象 的文档。

#### pop(key, /)

#### pop(key, default, /)

如果 `key` 存在于字典中则将其移除并返回其值，否则返回 `default`。如果 `default` 未给出且 `key` 不存在于字典中，则会引发 `KeyError`。

#### popitem()

从字典中移除并返回一个 `(键, 值)` 对。键值对会按 LIFO 的顺序被返回。

`popitem()` 适用于对字典进行消耗性的迭代，这在集合算法中经常被使用。如果字典为空，调用 `popitem()` 将引发 `KeyError`。

> **在 3.7 版本发生变更**：现在会确保采用 LIFO 顺序。在之前的版本中，`popitem()` 会返回一个任意的键/值对。

#### reversed(d)

返回一个逆序获取字典键的迭代器。这是 `reversed(d.keys())` 的快捷方式。

3.8 版本新增。

#### setdefault(key, default=None, /)

如果字典存在键 `key`，返回它的值。如果不存在，插入值为 `default` 的键 `key`，并返回 `default`。`default` 默认为 `None`。

#### update(**kwargs)

#### update(mapping, /, **kwargs)

#### update(iterable, /, **kwargs)

使用来自 `mapping` 或 `iterable` 和 kwargs 的键/值对更新字典，覆盖现有的键。返回 `None`。

`update()` 接受另一个具有 `keys()` 方法的对象（在此情况下会对从该方法返回的每个键调用 `__getitem__()`），或一个键/值对的可迭代对象（如元组或其他长度为二的可迭代对象）。如果给出了关键字参数，则会以其键/值对更新字典：`d.update(red=1, blue=2)`。

#### values()

返回由字典值组成的一个新视图。参见 视图对象 的文档。

两个 `dict.values()` 视图之间的相等性比较将总是返回 `False`。这在 `dict.values()` 与其自身比较时也同样成立：

```python
d = {'a': 1}
d.values() == d.values()
False
```

#### d | other

合并 d 与 `other` 中的键和值来创建一个新的字典，两者必须都是字典。当 d 和 `other` 有相同键时，`other` 的值优先。

3.9 版本新增。

#### d |= other

用 `other` 的键和值更新字典 d，`other` 可以是映射或键/值对的可迭代对象。当 d 和 `other` 有相同键时，`other` 的值优先。

3.9 版本新增。

字典和字典视图都是可反转的。

```python
d = {"one": 1, "two": 2, "three": 3, "four": 4}
d
{'one': 1, 'two': 2, 'three': 3, 'four': 4}
list(reversed(d))
['four', 'three', 'two', 'one']
list(reversed(d.values()))
[4, 3, 2, 1]
list(reversed(d.items()))
[('four', 4), ('three', 3), ('two', 2), ('one', 1)]
```

> **在 3.8 版本发生变更**：字典现在可反转。

> **参见**：`frozendict` 和 `types.MappingProxyType` 可用于创建 `dict` 的只读视图。
>
> **参见**：有关 dict 对象的线程安全保证的详细信息，请参阅 dict 对象的线程安全。

### 字典视图对象

由 `dict.keys()`、`dict.values()` 和 `dict.items()` 返回的对象是视图对象。它们提供字典条目的一个动态视图，这意味着当字典改变时，视图也会相应改变。

字典视图可以被迭代以产生与其对应的数据，并支持成员检测：

#### len(dictview)

返回字典中的条目数。

#### iter(dictview)

返回字典中的键、值或项（以 `(键, 值)` 为元素的元组表示）的迭代器。

键和值是按插入顺序进行迭代的。这样就允许使用 `zip()` 来创建 `(值, 键)` 对：`pairs = zip(d.values(), d.keys())`。另一个创建相同列表的方式是 `pairs = [(v, k) for (k, v) in d.items()]`。

在添加或删除字典中的条目期间对视图进行迭代可能引发 `RuntimeError` 或者无法完全迭代所有条目。

> **在 3.7 版本发生变更**：字典顺序会确保为插入顺序。

#### x in dictview

如果 x 是对应字典中存在的键、值或项（在最后一种情况下 x 应为一个 `(键, 值)` 元组）则返回 `True`。

#### reversed(dictview)

返回一个逆序获取字典键、值或项的迭代器。视图将按与插入相反的顺序进行迭代。

> **在 3.8 版本发生变更**：字典视图现在可反转。

#### dictview.mapping

返回 `types.MappingProxyType` 对象，封装了视图所引用的原始字典。

3.10 版本新增。

键视图在某种程度类似于集合，因为其条目是唯一的且可哈希的。项视图也包含集合操作，因为 `(键, 值)` 对是唯一的并且键是可哈希的。如果项视图中的所有值也都是可哈希的，则项视图也能与其他集合互操作。（值视图则不被视为类似于集合，因其条目通常不是唯一的。）对于类似于集合的视图，为抽象基类 `collections.abc.Set` 所定义的全部操作都是可用的（例如 `==`、`<` 或 `^`）。虽然使用集合运算符，但类似于集合的视图接受任何可迭代对象作为另一个操作数，而不像集合只接受集合本身作为输入。

一个使用字典视图的示例：

```python
dishes = {'eggs': 2, 'sausage': 1, 'bacon': 1, 'spam': 500}
keys = dishes.keys()
values = dishes.values()

# iteration
n = 0
for val in values:
    n += val

print(n)
504

# keys and values are iterated over in the same order (insertion order)
list(keys)
['eggs', 'sausage', 'bacon', 'spam']
list(values)
[2, 1, 1, 500]

# view objects are dynamic and reflect dict changes
del dishes['eggs']
del dishes['sausage']
list(keys)
['bacon', 'spam']

# set operations
keys & {'eggs', 'bacon', 'salad'}
{'bacon'}
keys ^ {'sausage', 'juice'} == {'juice', 'sausage', 'bacon', 'spam'}
True
keys | ['juice', 'juice', 'juice'] == {'bacon', 'spam', 'juice'}
True

# get back a read-only proxy for the original dictionary
values.mapping
mappingproxy({'bacon': 1, 'spam': 500})
values.mapping['spam']
500
```

### 冻结字典

#### class frozendict(**kwargs)

#### class frozendict(mapping, /, **kwargs)

#### class frozendict(iterable, /, **kwargs)

返回一个新的冻结字典，基于可选的位置参数和可能为空的关键字参数集来初始化。

`frozendict` 的 API 与 `dict` 的 API 类似，但有以下不同：

`dict` 比 `frozendict` 拥有更多的方法：

- `__delitem__()`
- `__setitem__()`
- `clear()`
- `pop()`
- `popitem()`
- `setdefault()`
- `update()`

如果所有的键和值都可哈希，则 `frozendict` 可以使用 `hash(frozendict)` 进行哈希。

`frozendict |= other` 不会原地修改 frozendict，而是创建一个新的冻结字典。

`frozendict` 不是 `dict` 的子类，而是直接继承自 `object`。

与字典一样，frozendict 对两种类型是泛型的，分别表示 frozendict 的键和值的类型。

#### classmethod fromkeys(iterable, value=None, /)

与 `dict.fromkeys()` 类似，但如果类型是 `frozendict` 的子类，或者构造器返回了一个 `frozendict`，则会再次使用初始化后的 frozendict 调用类型构造器。

3.15 版本新增。

## 上下文管理器类型

Python 的 `with` 语句支持由上下文管理器定义的运行时上下文这一概念。此对象的实现使用了一对专门方法，允许用户自定义类来定义运行时上下文，在语句体被执行前进入该上下文，并在语句执行完毕时退出该上下文：

#### contextmanager.__enter__()

进入运行时上下文并返回此对象或另一个与运行时上下文相关的对象。此方法的返回值会绑定到使用此上下文管理器的 `with` 语句的 `as` 子句中的标识符。

一个返回其自身的上下文管理器的例子是文件对象。文件对象会从 `__enter__()` 返回其自身，以允许 `open()` 被用作 `with` 语句中的上下文表达式。

一个返回相关对象的上下文管理器的例子是 `decimal.localcontext()` 所返回的对象。此种管理器会将活动的十进制上下文设为原始十进制上下文的一份副本并返回该副本。这允许对 `with` 语句的语句体中的当前十进制上下文进行更改，而不会影响 `with` 语句体以外的代码。

#### contextmanager.__exit__(exc_type, exc_val, exc_tb)

退出运行时上下文并返回一个布尔值旗标来表明所发生的任何异常是否应当被屏蔽。如果在执行 `with` 语句的语句体期间发生了异常，则参数会包含异常的类型、值以及回溯信息。在其他情况下三个参数均为 `None`。

从此方法返回一个真值将导致 `with` 语句屏蔽异常并继续执行紧随在 `with` 语句之后的语句。否则异常将在执行完此方法之后继续传播。

如果此方法在处理 `with` 语句体先前引发的异常时又引发了异常，则会引发新的异常，而原始异常会被存储在新异常的 `__context__` 属性中。

传入的异常绝对不应当被显式地重新引发——相反，此方法应当返回一个假值以表明此方法已成功完成并且不希望屏蔽被引发的异常。这允许上下文管理代码方便地检测 `__exit__()` 方法是否确实已失败。

Python 定义了多个上下文管理器以方便实现线程同步、文件或其他对象的快速关闭，以及对于活动十进制算术上下文的简化操作。除了实现上下文管理协议之外，不会对这些特定类型做任何特殊处理。请参阅 `contextlib` 模块查看一些示例。

Python 的生成器和 `contextlib.contextmanager` 装饰器提供了实现这些协议的便捷方式。如果生成器函数带有 `contextlib.contextmanager` 装饰器，它将返回一个实现必要的 `__enter__()` 和 `__exit__()` 方法的上下文管理器，而不再是由未经装饰的生成器函数所产生的迭代器。

请注意在 Python/C API 中 Python 对象的类型结构中并没有针对这些方法的专门槽位。想要定义这些方法的扩展类型必须将它们作为普通的 Python 可访问方法来提供。与设置运行时上下文的开销相比，单个类字典查找的开销可以忽略。

## 类型注解类型 --- 泛型别名、联合

类型注解的核心内置类型为 泛型别名 与 联合。

### 泛型别名类型

`GenericAlias` 对象通常是通过抽取一个类来创建的。它们最常与 `list` 或 `dict` 这样的容器类一起使用。例如，`list[int]` 这个 `GenericAlias` 对象是通过以 `int` 参数抽取 `list` 类而创建的。`GenericAlias` 对象主要是为类型注解而设计的。

> **备注**：通常一个类只有在实现了特殊方法 `__class_getitem__()` 时才能被抽取。

`GenericAlias` 对象充当泛型类型的代理，实现了 参数化泛型。

对于容器类，提供给类抽取操作的参数可以指明对象所包含的元素类型。例如，`set[bytes]` 可以在类型注解中用来表示一个集合中的所有元素均为 `bytes` 类型。

对于定义了 `__class_getitem__()` 但不是容器的类，提供给类抽取操作的参数往往会指明对象上定义的一个或多个方法的返回类型。例如，正则表达式可以在 `str` 数据类型和 `bytes` 数据类型上使用：

- 如果 `x = re.search('foo', 'foo')`，则 x 将为一个 `re.Match` 对象，其中 `x.group(0)` 和 `x[0]` 的返回值都将是 `str` 类型。我们可以在类型注解中使用 GenericAlias `re.Match[str]` 来表示这种对象。
- 如果 `y = re.search(b'bar', b'bar')`（注意表示 bytes 的 `b`），则 y 也将是一个 `re.Match` 的实例，但 `y.group(0)` 和 `y[0]` 的返回值都将是 `bytes` 类型。在类型注解中，我们将使用 `re.Match[bytes]` 来表示这种 `re.Match` 对象。

`GenericAlias` 对象是 `types.GenericAlias` 类的实例，该类也可用于直接创建 `GenericAlias` 对象。用户自定义泛型类的特化可能不是 `types.GenericAlias` 的实例，但它们提供类似的功能。

#### T[X, Y, ...]

创建一个表示由类型 X、Y 等参数化的类型 T 的 `GenericAlias`，具体取决于所使用的 T。例如，一个期望包含浮点元素的 list 的函数：

```python
def average(values: list[float]) -> float:
    return sum(values) / len(values)
```

另一个例子是关于映射对象的，使用 `dict`，它是一个期望两个分别表示键类型和值类型的类型参数的泛型类型。在此示例中，函数期望得到一个键类型为 `str`、值类型为 `int` 的 `dict`：

```python
def send_post_request(url: str, body: dict[str, int]) -> None:
    ...
```

内置函数 `isinstance()` 和 `issubclass()` 不接受作为其第二个参数的 `GenericAlias` 类型：

```python
isinstance([1, 2], list[str])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: isinstance() argument 2 cannot be a parameterized generic
```

Python 运行时并不强制要求类型注解。这同样适用于泛型类型及其类型形参。当从 `GenericAlias` 创建容器对象时，容器中的元素并不会按类型进行检查。例如，以下代码虽然不被鼓励，但运行时并不会出错：

```python
t = list[str]
t([1, 2, 3])
[1, 2, 3]
```

此外，参数化泛型会在对象创建过程中抹除类型形参：

```python
t = list[str]
type(t)
<class 'types.GenericAlias'>

l = t()
type(l)
<class 'list'>
```

`GenericAlias` 的实例在运行时并不是类，即使它们的行为类似于类（它们可以被实例化，也可以被继承）：

```python
import inspect
inspect.isclass(list[int])
False
```

这对于用户自定义泛型同样适用。

在泛型上调用 `repr()` 或 `str()` 会显示参数化类型：

```python
repr(list[int])
'list[int]'

str(list[int])
'list[int]'
```

泛型容器的 `__getitem__()` 方法将引发异常以禁止例如 `dict[str][str]` 这样的错误：

```python
dict[str][str]
Traceback (most recent call last):
  ...
TypeError: dict[str] is not a generic class
```

但是，当使用了类型变量时此种表达式是有效的。索引必须具有与 `GenericAlias` 对象的 `__args__` 中的类型变量条目数相同的元素数。

```python
from typing import TypeVar
Y = TypeVar('Y')
dict[str, Y][int]
dict[str, int]
```

### 标准泛型类

以下标准库类支持参数化泛型。此列表并非详尽无遗。

- `tuple`
- `list`
- `dict`
- `set`
- `frozendict`
- `frozenset`
- `type`
- `asyncio.Future`
- `asyncio.Task`
- `collections.deque`
- `collections.defaultdict`
- `collections.OrderedDict`
- `collections.Counter`
- `collections.ChainMap`
- `collections.abc.Awaitable`
- `collections.abc.Coroutine`
- `collections.abc.AsyncIterable`
- `collections.abc.AsyncIterator`
- `collections.abc.AsyncGenerator`
- `collections.abc.Iterable`
- `collections.abc.Iterator`
- `collections.abc.Generator`
- `collections.abc.Reversible`
- `collections.abc.Container`
- `collections.abc.Collection`
- `collections.abc.Callable`
- `collections.abc.Set`
- `collections.abc.MutableSet`
- `collections.abc.Mapping`
- `collections.abc.MutableMapping`
- `collections.abc.Sequence`
- `collections.abc.MutableSequence`
- `collections.abc.ByteString`
- `collections.abc.MappingView`
- `collections.abc.KeysView`
- `collections.abc.ItemsView`
- `collections.abc.ValuesView`
- `contextlib.AbstractContextManager`
- `contextlib.AbstractAsyncContextManager`
- `dataclasses.Field`
- `functools.cached_property`
- `functools.partialmethod`
- `os.PathLike`
- `queue.LifoQueue`
- `queue.Queue`
- `queue.PriorityQueue`
- `queue.SimpleQueue`
- `re.Pattern`
- `re.Match`
- `shelve.BsdDbShelf`
- `shelve.DbfilenameShelf`
- `shelve.Shelf`
- `types.MappingProxyType`
- `weakref.WeakKeyDictionary`
- `weakref.WeakMethod`
- `weakref.WeakSet`
- `weakref.WeakValueDictionary`

### GenericAlias 对象的特殊属性

所有的参数化泛型都实现了特殊的只读属性。

#### genericalias.__origin__

本属性指向未参数化的泛型类：

```python
list[int].__origin__
<class 'list'>
```

#### genericalias.__args__

本属性是传给泛型类原始 `__class_getitem__()` 的泛型类型（可能有长度为 1 的）元组：

```python
dict[str, list[int]].__args__
(<class 'str'>, list[int])
```

#### genericalias.__parameters__

本属性是延迟计算的 `__args__` 中找到的唯一类型变量（可能为空）的元组：

```python
from typing import TypeVar

T = TypeVar('T')
list[T].__parameters__
(~T,)
```

> **备注**：带有 `typing.ParamSpec` 参数的 `GenericAlias` 对象在替换之后可能不会有正确的 `__parameters__`，因为 `typing.ParamSpec` 主要是为静态类型检查而设计的。

#### genericalias.__unpacked__

一个布尔值，如果该别名已使用 `*` 运算符进行解包则为真（参见 `TypeVarTuple`）。

3.11 版本新增。

> **参见**：
>
> - **PEP 484 - Type Hints**：介绍 Python 的类型注解框架。
> - **PEP 585 - Type Hinting Generics In Standard Collections**：介绍原生参数化标准库类的能力，前提是它们实现了特殊类方法 `__class_getitem__()`。
> - **泛型（Generic）、用户自定义泛型与 typing.Generic**：关于如何实现可在运行时被参数化并被静态类型检查器所理解的泛型类的文档。

3.9 版本新增。

### 联合类型

联合对象包含了在多个 类型对象 上执行 `|`（按位或）运算后的值。这些类型主要用于类型注解。与下标 `typing.Union` 相比，联合类型表达式可以实现更简洁的类型提示语法。

#### X | Y | ...

定义包含 X、Y 等类型的联合对象。`X | Y` 表示 X 或 Y。它等价于 `typing.Union[X, Y]`。例如，以下函数应接受 `int` 或 `float` 类型的参数：

```python
def square(number: int | float) -> int | float:
    return number ** 2
```

> **备注**：`|` 运算符不能在运行时用于定义其中一方或多方成员为前向引用的联合。例如 `int | "Foo"`，其中 `"Foo"` 是对尚未定义的类的引用，将在运行时失败。对于包含前向引用的联合，请将整个表达式用字符串表示，例如 `"int | Foo"`。

#### union_object == other

联合对象可与其他联合对象进行相等性测试。详细说明：

联合的联合会被扁平化：

```python
(int | str) | float == int | str | float
```

冗余的类型会被移除：

```python
int | str | int == int | str
```

在比较联合时，顺序会被忽略：

```python
int | str == str | int
```

它会创建 `typing.Union` 的实例：

```python
int | str == typing.Union[int, str]
type(int | str) is typing.Union
```

可选类型可表示为与 `None` 的联合：

```python
str | None == typing.Optional[str]
```

#### isinstance(obj, union_object)

#### issubclass(obj, union_object)

也支持以联合对象为参数调用 `isinstance()` 和 `issubclass()`：

```python
isinstance("", int | str)
True
```

但是，联合对象中的参数化泛型无法被检查：

```python
isinstance(1, int | list[int])  # short-circuit evaluation
True
isinstance([1], int | list[int])
Traceback (most recent call last):
  ...
TypeError: isinstance() argument 2 cannot be a parameterized generic
```

联合对象暴露给用户的类型可以从 `typing.Union` 访问，并可用于 `isinstance()` 检查：

```python
import typing
isinstance(int | str, typing.Union)
True
typing.Union()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot create 'typing.Union' instances
```

> **备注**：类型对象的 `__or__()` 方法是为支持 `X | Y` 语法而添加的。如果元类实现了 `__or__()`，联合操作可能会覆盖它：

```python
class M(type):
    def __or__(self, other):
        return "Hello"

class C(metaclass=M):
    pass

C | int
'Hello'
int | C
int | C
```

> **参见**：PEP 604 -- 提出 `X | Y` 语法和联合类型的 PEP。

3.10 版本新增。

> **在 3.14 版本发生变更**：联合对象现在是 `typing.Union` 的实例。此前，它们是 `types.UnionType` 的实例，后者仍然是 `typing.Union` 的别名。

## 其他内置类型

解释器支持一些其他种类的对象。这些对象大都仅支持一两种操作。

### 模块

模块唯一的特殊操作是属性访问：`m.name`，这里 m 是一个模块而 `name` 访问定义在 m 的符号表中的一个名称。模块属性可以被赋值。（请注意 `import` 语句严格来说也是对模块对象的一种操作；`import foo` 不要求存在一个名为 foo 的模块对象，而是要求存在一个对于名为 foo 的模块的（外部）定义。）

每个模块都有一个特殊属性 `__dict__`。这是包含模块的符号表的字典。修改此字典实际上会改变模块的符号表，但是无法直接对 `__dict__` 属性赋值（你可以写 `m.__dict__['a'] = 1`，这会将 `m.a` 定义为 1，但是你不能写 `m.__dict__ = {}`）。不建议直接修改 `__dict__`。

内置于解释器中的模块会写成这样：`<module 'sys' (built-in)>`。如果是从一个文件加载，则会写成 `<module 'os' from '/usr/local/lib/pythonX.Y/os.pyc'>`。

### 类与类实例

关于这些请参看 对象、值与类型 和 类定义。

### 函数

函数对象是通过函数定义创建的。对函数对象的唯一操作是调用它：`func(argument-list)`。

函数对象实际上分为两类：内置函数和用户自定义函数。两者支持同样的操作（调用函数），但实现方式不同，因此对象类型也不同。

更多信息请参看 函数定义。

### 方法

方法是在属性表示法下被调用的函数。此处存在两种类型：内置方法（例如列表的 `append()` 方法）和类实例方法。内置方法由支持它们的类型来描述。

如果你通过一个实例来访问方法（即定义在类命名空间内的函数），你将得到一个特殊对象：绑定方法（或称 实例方法）对象。当被调用时，它会将 `self` 参数添加到参数列表。绑定方法具有两个特殊的只读属性：`m.__self__` 是操作该方法的对象，`m.__func__` 是实现该方法的函数。调用 `m(arg-1, arg-2, ..., arg-n)` 完全等价于调用 `m.__func__(m.__self__, arg-1, arg-2, ..., arg-n)`。

与函数对象类似，绑定方法对象也支持获取任意属性。但是，由于方法属性实际上保存于下层函数对象（`method.__func__`），因此不允许设置绑定方法的方法属性。尝试设置方法的属性将会导致引发 `AttributeError`。为了设置方法属性，你需要显式地在其下层函数对象上设置：

```python
class C:
    def method(self):
        pass

c = C()
c.method.whoami = 'my name is method'  # can't set on the method
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'method' object has no attribute 'whoami'
c.method.__func__.whoami = 'my name is method'
c.method.whoami
'my name is method'
```

更多信息请参看 实例方法。

### 代码对象

代码对象被实现用来表示"伪编译"的可执行 Python 代码，例如一个函数体。它们不同于函数对象，因为它们不包含对其全局执行环境的引用。代码对象由内置的 `compile()` 函数返回，并可通过从函数对象的 `__code__` 属性从中提取。另请参阅 `code` 模块。

访问 `__code__` 会触发一个审计事件 `object.__getattr__`，附带参数 `obj` 和 `"__code__"`。

可以通过将代码对象（而非源码字符串）传给 `exec()` 或 `eval()` 内置函数来执行或求值。

更多信息请参看 标准类型层级结构。

### 类型对象

类型对象表示各种对象类型。对象的类型可通过内置函数 `type()` 来访问。类型没有特殊的操作。标准库模块 `types` 定义了所有标准内置类型的名称。

类型以这样的写法来表示：`<class 'int'>`。

### 空对象

此对象会由不显式地返回值的函数所返回。它不支持任何特殊的操作。空对象恰好只有一个，名为 `None`（内置名称）。`type(None)()` 会生成同一个单例。

该对象写为 `None`。

### 省略号对象

此对象通常用于表示某个内容被省略。它不支持任何特殊的操作。省略号对象恰好只有一个，名为 `Ellipsis`（内置名称）。`type(Ellipsis)()` 会生成 `Ellipsis` 单例。

它写为 `Ellipsis` 或 `...`。

在典型用法中，作为省略号对象的 `...` 会出现在少数几个不同的场合，例如：

- 在类型注解中，如可调用参数或元组元素。
- 作为函数体而非 `pass` 语句。
- 在第三方库中，如 Numpy 的切片和步进。

Python 也会以并非省略号对象的方式使用三个点，例如：

- Doctest 的 `ELLIPSIS`，作为缺失内容的模式。
- 当输入不完整时，交互式 shell 的默认 Python 提示符。

最后，Python 文档经常以惯常的英语用法使用三个点来表示被省略的内容，即便是在也将其用作省略号的代码示例中也是如此。

### NotImplemented 对象

此对象会在比较和二元运算被应用于它们所不支持的类型时返回。更多信息请参看 比较运算。`NotImplemented` 对象恰好只有一个。`type(NotImplemented)()` 会生成这个单例实例。

它写为 `NotImplemented`。

### 内部对象

有关此对象的信息请参看 标准类型层级结构。其中描述了栈帧对象、回溯对象以及切片对象。

## 特殊属性

语言实现对某些对象类型添加了一些特殊的只读属性（只要具有相关性）。其中一些并不会被 `dir()` 内置函数所报告。

#### definition.__name__

类、函数、方法、描述器或生成器实例的名称。

#### definition.__qualname__

类、函数、方法、描述器或生成器实例的限定名称。

3.3 版本新增。

#### definition.__module__

类或函数所属模块的名称。

#### definition.__doc__

类或函数的文档字符串，如果未定义则为 `None`。

#### definition.__type_params__

泛型类、函数和类型别名的类型参数。对于非泛型的类和函数，这将是一个空元组。

3.12 版本新增。

## 整数与字符串转换的长度限制

CPython 对于 `int` 与 `str` 之间的转换有一个全局限制，以缓解拒绝服务攻击。此限制仅适用于十进制或其他非 2 的幂次基数。十六进制、八进制和二进制的转换不受限制。该限制可以被配置。

CPython 中的 `int` 类型是以二进制形式存储的任意长度数字（通常被称为"大数"）。不存在能以线性时间将一个字符串转换为二进制整数或将二进制整数转换为字符串的算法，除非基数是 2 的幂。即便是已知最好的以 10 为基数的算法也具有次二次方的复杂度。转换一个大值如 `int('1' * 500_000)` 在快速的 CPU 上也可能花费超过一秒钟。

限制转换大小提供了一种务实的方法来避免 CVE 2020-10735。

该限制会应用于当采用非线性转换算法时输入或输出字符串中数码字符的数量。下划线和符号不会被计入限制。

当某个操作将超出限制时，会引发 `ValueError`：

```python
import sys
sys.set_int_max_str_digits(4300)  # Illustrative, this is the default.
_ = int('2' * 5432)
Traceback (most recent call last):
...
ValueError: Exceeds the limit (4300 digits) for integer string conversion: value has 5432 digits; use sys.set_int_max_str_digits() to increase the limit
i = int('2' * 4300)
len(str(i))
4300
i_squared = i*i
len(str(i_squared))
Traceback (most recent call last):
...
ValueError: Exceeds the limit (4300 digits) for integer string conversion; use sys.set_int_max_str_digits() to increase the limit
len(hex(i_squared))
7144
assert int(hex(i_squared), base=16) == i*i  # Hexadecimal is unlimited.
```

默认限制是 4300 位数，如 `sys.int_info.default_max_str_digits` 所提供。可以配置的最低限制是 640 位数，如 `sys.int_info.str_digits_check_threshold` 所提供。

验证：

```python
import sys
assert sys.int_info.default_max_str_digits == 4300, sys.int_info
assert sys.int_info.str_digits_check_threshold == 640, sys.int_info
msg = int('578966293710682886880994035146873798396722250538762761564'
          '9252925514383915483333812743580549779436104706260696366600'
          '571186405732').to_bytes(53, 'big')
```

3.11 版本新增。

### 受影响的 API

该限制仅适用于 `int` 与 `str` 或 `bytes` 之间可能较慢的转换：

- `int(string)` 使用默认的 10 进制。
- `int(string, base)` 对所有不是 2 的幂的进制。
- `str(integer)`。
- `repr(integer)`。
- 任何其他以 10 为基数的字符串转换，例如 `f"{integer}"`、`"{}".format(integer)` 或 `b"%d" % integer`。

该限制不适用于具有线性算法的函数：

- `int(string, base)` 使用 2、4、8、16 或 32 进制。
- `int.from_bytes()` 和 `int.to_bytes()`。
- `hex()`、`oct()`、`bin()`。
- 十六进制、八进制和二进制数的格式规格迷你语言。
- str 转 float。
- str 转 decimal.Decimal。

### 配置限制

在 Python 启动之前，你可以使用环境变量或解释器命令行旗标来配置该限制：

- `PYTHONINTMAXSTRDIGITS`，例如 `PYTHONINTMAXSTRDIGITS=640 python3` 会将限制设置为 640，或者 `PYTHONINTMAXSTRDIGITS=0 python3` 会禁用该限制。
- `-X int_max_str_digits`，例如 `python3 -X int_max_str_digits=640`

`sys.flags.int_max_str_digits` 包含 `PYTHONINTMAXSTRDIGITS` 或 `-X int_max_str_digits` 的值。如果同时设置了环境变量和 `-X` 选项，则 `-X` 选项优先。值为 `-1` 表示两者均未设置，因此在初始化期间使用了 `sys.int_info.default_max_str_digits` 的值。

在代码中，你可以使用以下 `sys` API 检查当前限制并设置新的限制：

- `sys.get_int_max_str_digits()` 和 `sys.set_int_max_str_digits()` 是解释器范围内限制的获取器和设置器。子解释器有其各自的限制。

关于默认值和最小值的信息可以在 `sys.int_info` 中找到：

- `sys.int_info.default_max_str_digits` 是编译时默认限制。
- `sys.int_info.str_digits_check_threshold` 是限制的最低可接受值（0 除外，它表示禁用）。

3.11 版本新增。

> **小心**：设置较低的限制可能会导致问题。虽然罕见，但确实存在源码中的十进制整数常量超过最低阈值的代码。设置限制的后果是，Python 源码中若包含超过限制长度的十进制整数字面值，将会在解析时遇到错误，通常是在启动时、导入时甚至安装时——即当代码尚无最新的 .pyc 文件存在的任何时刻。对于包含此类大常量的源码，一个解决办法是将其转换为 `0x` 十六进制形式，因为十六进制没有限制。
>
> 如果你使用较低的限制，请彻底地测试你的应用程序。请确保你的测试在早期就通过环境变量或旗标设置了限制，以便在启动期间乃至任何可能调用 Python 将 .py 源码预编译为 .pyc 文件的安装步骤中都能生效。

### 推荐配置

默认的 `sys.int_info.default_max_str_digits` 预计对于大多数应用程序来说都是合理的。如果你的应用程序需要不同的限制，请从你的主入口点使用与 Python 版本无关的代码进行设置，因为这些 API 是在 3.12 之前的版本的安全补丁发布中加入的。

示例：

```python
import sys
if hasattr(sys, "set_int_max_str_digits"):
    upper_bound = 68000
    lower_bound = 4004
    current_limit = sys.get_int_max_str_digits()
    if current_limit == 0 or current_limit > upper_bound:
        sys.set_int_max_str_digits(upper_bound)
    elif current_limit < lower_bound:
        sys.set_int_max_str_digits(lower_bound)
```

如果你需要完全禁用，请将其设为 0。

## 脚注

[1] 关于这些特殊方法的附加信息可以在 Python 参考手册（基本定制）中找到。

[2] 作为这种比较的后果，列表 `[1, 2]` 被认为与 `[1.0, 2.0]` 相等，元组也是如此。

[3] 它们必须具有这样的优先级，因为解析器无法判断操作数的类型。

[4] 区分大小写的字符是指通用类别属性为 `"Lu"`（Letter, uppercase）、`"Ll"`（Letter, lowercase）或 `"Lt"`（Letter, titlecase）之一的字符。

[5] 如果只想格式化一个元组，那么应当提供一个单例元组，其唯一的元素就是要进行格式化的那个元组。
