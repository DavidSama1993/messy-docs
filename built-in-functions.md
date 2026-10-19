# 内置函数

Python 解释器内置了许多始终可用的函数和类型。它们按字母顺序在此列出。

## 内置函数列表

**A** — `abs()` `aiter()` `all()` `anext()` `any()` `ascii()`

**B** — `bin()` `bool()` `breakpoint()` `bytearray()` `bytes()`

**C** — `callable()` `chr()` `classmethod()` `compile()` `complex()`

**D** — `delattr()` `dict()` `dir()` `divmod()`

**E** — `enumerate()` `eval()` `exec()`

**F** — `filter()` `float()` `format()` `frozendict()` `frozenset()`

**G** — `getattr()` `globals()`

**H** — `hasattr()` `hash()` `help()` `hex()`

**I** — `id()` `input()` `int()` `isinstance()` `issubclass()` `iter()`

**L** — `len()` `list()` `locals()`

**M** — `map()` `max()` `memoryview()` `min()`

**N** — `next()`

**O** — `object()` `oct()` `open()` `ord()`

**P** — `pow()` `print()` `property()`

**R** — `range()` `repr()` `reversed()` `round()`

**S** — `set()` `setattr()` `sentinel()` `slice()` `sorted()` `staticmethod()` `str()` `sum()` `super()`

**T** — `tuple()` `type()`

**V** — `vars()`

**Z** — `zip()`

**_** — `__import__()`

## abs(number, /)

返回一个数的绝对值。参数可以是整数、浮点数或实现了 `__abs__()` 的对象。如果参数是复数，则返回其模（大小）。

## aiter(async_iterable, /)

返回异步可迭代对象的异步迭代器。等价于调用 `x.__aiter__()`。

> **注意**：与 `iter()` 不同，`aiter()` 没有双参数形式。

> **3.10 版新增。**

## all(iterable, /)

如果可迭代对象的所有元素都为真（或可迭代对象为空），则返回 `True`。等价于：

```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```

## awaitable anext(async_iterator, /)

## awaitable anext(async_iterator, default, /)

被等待（await）时，从给定的异步迭代器返回下一项；如果迭代器已耗尽且给出了 default，则返回 default。

这是内置函数 `next()` 的异步版本，行为类似。

它调用 async_iterator 的 `__anext__()` 方法，返回一个可等待对象。等待该对象将返回迭代器的下一个值。如果给出了 default，则在迭代器耗尽时返回 default，否则引发 `StopAsyncIteration`。

> **3.10 版新增。**

## any(iterable, /)

如果可迭代对象的任一元素为真，则返回 `True`。如果可迭代对象为空，则返回 `False`。等价于：

```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
```

## ascii(object, /)

与 `repr()` 类似，返回一个包含对象可打印表示形式的字符串，但会使用 `\x`、`\u` 或 `\U` 转义来转义 `repr()` 返回的字符串中的非 ASCII 字符。这会生成一个与 Python 2 中 `repr()` 返回结果类似的字符串。

## bin(integer, /)

将整数转换为以 `"0b"` 为前缀的二进制字符串。结果是一个合法的 Python 表达式。如果 integer 不是 Python 的 `int` 对象，则它必须定义返回整数的 `__index__()` 方法。一些示例：

```python
bin(3)
'0b11'
bin(-10)
'-0b1010'
```

无论是否需要 `"0b"` 前缀，都可以使用以下任一方式：

```python
format(14, '#b'), format(14, 'b')
('0b1110', '1110')
f'{14:#b}', f'{14:b}'
('0b1110', '1110')
```

另请参阅 `enum.bin()`，它可以用二进制补码表示负值。

另请参阅 `format()` 了解更多信息。

## class bool(object=False, /)

返回布尔值，即 `True` 或 `False` 之一。参数使用标准的真值测试过程进行转换。如果参数为假或省略，则返回 `False`；否则返回 `True`。`bool` 类是 `int` 的子类（参见"数字类型 — int, float, complex"）。它不能再被继承。它只有 `False` 和 `True` 两个实例（参见"布尔类型 - bool"）。

> **3.7 版更改**：该参数现在是仅限位置参数。

## breakpoint(*args, **kws)

此函数会在调用点将你带入调试器。具体来说，它调用 `sys.breakpointhook()`，将 args 和 kws 直接传递过去。默认情况下，`sys.breakpointhook()` 调用 `pdb.set_trace()`，且不期望任何参数。在这种情况下，它纯粹是一个便捷函数，让你不必显式导入 `pdb` 或输入太多代码就能进入调试器。但是，`sys.breakpointhook()` 可以被设置为其他函数，`breakpoint()` 将自动调用它，使你能够进入所选择的调试器。如果 `sys.breakpointhook()` 不可访问，此函数将引发 `RuntimeError`。

默认情况下，`breakpoint()` 的行为可以通过 `PYTHONBREAKPOINT` 环境变量来更改。有关用法详细信息，请参阅 `sys.breakpointhook()`。

请注意，如果 `sys.breakpointhook()` 已被替换，则上述行为无法保证。

以参数 breakpointhook 引发审计事件 `builtins.breakpoint`。

> **3.7 版新增。**

## class bytearray(source=b'')

## class bytearray(source, encoding, errors='strict')

返回一个新的字节数组。`bytearray` 类是整数范围在 `0 <= x < 256` 内的可变序列。它具有"可变序列类型"中描述的大多数可变序列的常用方法，以及 `bytes` 类型的大部分方法，参见"字节和字节数组操作"。

可选的 source 参数可以通过几种不同的方式来初始化数组：

- 如果是字符串，则必须同时给出 encoding（以及可选的 errors）参数；然后 `bytearray()` 会使用 `str.encode()` 将字符串转换为字节。
- 如果是整数，则数组将具有该大小，并以空字节初始化。
- 如果是符合缓冲区接口的对象，则使用该对象的只读缓冲区来初始化字节数组。
- 如果是可迭代对象，则它必须是 `0 <= x < 256` 范围内的整数的可迭代对象，这些整数将用作数组的初始内容。

不带参数时，将创建大小为 0 的数组。

另请参阅"二进制序列类型 — bytes, bytearray, memoryview"和"Bytearray 对象"。

## class bytes(source=b'')

## class bytes(source, encoding, errors='strict')

返回一个新的 `"bytes"` 对象，它是 `0 <= x < 256` 范围内的整数的不可变序列。`bytes` 是 `bytearray` 的不可变版本——它具有相同的非变异方法和相同的索引与切片行为。

因此，构造函数的参数按 `bytearray()` 的方式解释。

字节对象也可以用字面量创建，参见"字符串和字节字面量"。

另请参阅"二进制序列类型 — bytes, bytearray, memoryview"、"Bytes 对象"和"字节和字节数组操作"。

## callable(object, /)

如果 object 参数看起来可调用，则返回 `True`，否则返回 `False`。如果返回 `True`，调用仍有可能失败；但如果返回 `False`，调用 object 永远不会成功。请注意，类是可调用的（调用类会返回一个新实例）；如果实例的类有 `__call__()` 方法，则实例是可调用的。

> **3.2 版新增**：此函数在 Python 3.0 中首次被移除，随后在 Python 3.2 中重新引入。

## chr(codepoint, /)

返回表示指定 Unicode 码位字符的字符串。例如，`chr(97)` 返回字符串 `'a'`，而 `chr(8364)` 返回字符串 `'€'`。这是 `ord()` 的逆操作。

参数的有效范围是 0 到 1,114,111（十六进制的 0x10FFFF）。超出该范围将引发 `ValueError`。

## @classmethod

将方法转换为类方法。

类方法将类作为隐式的第一个参数接收，就像实例方法接收实例一样。要声明类方法，请使用以下惯用法：

```python
class C:
    @classmethod
    def f(cls, arg1, arg2): ...
```

`@classmethod` 形式是一个函数装饰器——详细信息参见"函数定义"。

类方法可以在类上调用（如 `C.f()`），也可以在实例上调用（如 `C().f()`）。除了其所属的类之外，实例会被忽略。如果为派生类调用类方法，则派生类对象将作为隐含的第一个参数传入。

类方法与 C++ 或 Java 的静态方法不同。如果你想要的是那些，请参阅本节的 `staticmethod()`。有关类方法的更多信息，请参见"标准类型层级结构"。

> **3.9 版更改**：类方法现在可以包装其他描述符，如 `property()`。
>
> **3.10 版更改**：类方法现在继承方法属性（`__module__`、`__name__`、`__qualname__`、`__doc__` 和 `__annotations__`），并新增了 `__wrapped__` 属性。
>
> **3.11 版起弃用，3.13 版移除**：类方法不再能包装其他描述符，如 `property()`。

## compile(source, filename, mode, flags=0, dont_inherit=False, optimize=-1, *, module=None)

将 source 编译为代码或 AST 对象。代码对象可以由 `exec()` 或 `eval()` 执行。source 可以是普通字符串、字节字符串或 AST 对象。有关如何使用 AST 对象的信息，请参阅 `ast` 模块文档。

filename 参数应给出代码来源的文件名；如果代码不是从文件中读取的，则传递一些可识别的值（常用 `'<string>'`）。

mode 参数指定必须编译哪种代码；如果 source 由一系列语句组成，则为 `'exec'`；如果由单个表达式组成，则为 `'eval'`；如果由单个交互式语句组成，则为 `'single'`（在后一种情况下，求值结果不为 `None` 的表达式语句会被打印出来）。

可选参数 flags 和 dont_inherit 控制应激活哪些编译器选项以及允许哪些 future 特性。如果两者都未提供（或都为零），则使用与调用 `compile()` 的代码相同的 flags 来编译代码。如果给出了 flags 参数而未给出 dont_inherit（或为零），则除了原本会使用的选项之外，还会使用 flags 参数指定的编译器选项和 future 语句。如果 dont_inherit 是非零整数，则完全以 flags 参数为准——周围代码中的 flags（future 特性和编译器选项）被忽略。

编译器选项和 future 语句由比特位指定，可以按位 OR 组合来指定多个选项。指定某个 future 特性所需的位字段可以在 `__future__` 模块中 `_Feature` 实例的 `compiler_flag` 属性中找到。编译器标志可以在 `ast` 模块中找到，前缀为 `PyCF_`。

optimize 参数指定编译器的优化级别；默认值 -1 选择由 `-O` 选项给出的解释器优化级别。显式级别为 0（不优化；`__debug__` 为真）、1（移除断言，`__debug__` 为假）或 2（同时移除文档字符串）。

可选参数 module 指定模块名称。这是为了能够按模块名称明确地过滤语法警告所必需的。

如果编译的 source 无效（包括包含空字符或无法解码的 source），此函数将引发 `SyntaxError`；如果 mode 或 flags 无效，或字符串 source 包含代理字符（surrogate），则引发 `ValueError`；如果 source 过于复杂而无法解析或编译（例如嵌套了数千个运算符的表达式），则引发 `MemoryError` 或 `RecursionError`；如果 source 过大，则引发 `OverflowError`。

如果要将 Python 代码解析为其 AST 表示，请参见 `ast.parse()`。

以参数 source 和 filename 引发审计事件 `compile`。隐式编译也可能引发此事件。

> **注意**：在 `'single'` 或 `'eval'` 模式下编译包含多行代码的字符串时，输入必须以至少一个换行符结尾。这是为了便于在 `code` 模块中检测不完整和完整的语句。
>
> **警告**：由于 Python 的 AST 编译器存在栈深度限制，编译到 AST 对象时，使用足够大/复杂的字符串有可能使 Python 解释器崩溃。
>
> **3.2 版更改**：允许使用 Windows 和 Mac 换行符。此外，`'exec'` 模式下的输入不再必须以换行符结尾。新增了 optimize 参数。
>
> **3.5 版更改**：此前，当 source 中出现空字节时会引发 `TypeError`。
>
> **3.8 版更改**：现在可以在 flags 中传入 `ast.PyCF_ALLOW_TOP_LEVEL_AWAIT`，以支持顶层 `await`、`async for` 和 `async with`。
>
> **3.12 版更改**：当 source 中出现空字节时，引发 `SyntaxError` 而不是 `ValueError`。
>
> **3.15 版更改**：新增了 module 参数。

## class complex(number=0, /)

## class complex(string, /)

## class complex(real=0, imag=0)

将单个字符串或数字转换为复数，或从实部和虚部创建复数。

示例：

```python
complex('+1.23')
(1.23+0j)
complex('-4.5j')
-4.5j
complex('-1.23+4.5j')
(-1.23+4.5j)
complex('\t( -1.23+4.5J )\n')
(-1.23+4.5j)
complex('-Infinity+NaNj')
(-inf+nanj)
complex(1.23)
(1.23+0j)
complex(imag=-4.5)
-4.5j
complex(-1.23, 4.5)
(-1.23+4.5j)
```

如果参数是字符串，则它必须包含实部（格式与 `float()` 相同）或虚部（格式相同，但带 `'j'` 或 `'J'` 后缀），或同时包含实部和虚部（这种情况下虚部的符号是必需的）。字符串可以被空白字符和圆括号 `'('`、`')'` 包围，这些会被忽略。字符串中 `'+'`、`'-'`、`'j'` 或 `'J'` 后缀与十进制数字之间不得包含空白字符。例如，`complex('1+2j')` 是合法的，但 `complex('1 + 2j')` 会引发 `ValueError`。更准确地说，在移除括号和首尾空白字符之后，输入必须符合以下语法中的 complexvalue 产生式：

```
complexvalue: floatvalue |
              floatvalue ("j" | "J") |
              floatvalue sign absfloatvalue ("j" | "J")
```

如果参数是数字，构造函数充当与 `int` 和 `float` 类似的数值转换。对于一般的 Python 对象 x，`complex(x)` 委托给 `x.__complex__()`。如果未定义 `__complex__()`，则回退到 `__float__()`。如果未定义 `__float__()`，则回退到 `__index__()`。

如果提供两个参数或使用关键字参数，每个参数可以是任何数值类型（包括复数）。如果两个参数都是实数，则返回实部为 real、虚部为 imag 的复数。如果两个参数都是复数，则返回实部为 `real.real - imag.imag`、虚部为 `real.imag + imag.real` 的复数。如果其中一个参数是实数，则在上述表达式中仅使用其实部。

另请参阅 `complex.from_number()`，它只接受单个数值参数。

如果所有参数都省略，则返回 `0j`。

复数类型在"数字类型 — int, float, complex"中有描述。

> **3.6 版更改**：允许像代码字面量一样使用下划线对数字分组。
>
> **3.8 版更改**：如果未定义 `__complex__()` 和 `__float__()`，则回退到 `__index__()`。
>
> **3.14 版起弃用**：将复数作为 real 或 imag 参数传入现已弃用；它只应作为单个位置参数传入。

## delattr(object, name, /)

这是 `setattr()` 的相关函数。参数是一个对象和一个字符串。字符串必须是该对象某个属性的名称。只要对象允许，该函数会删除指定名称的属性。例如，`delattr(x, 'foobar')` 等价于 `del x.foobar`。name 不必是 Python 标识符（参见 `setattr()`）。

## class dict(**kwargs)

## class dict(mapping, /, **kwargs)

## class dict(iterable, /, **kwargs)

创建一个新字典。`dict` 对象是字典类。另请参阅"映射类型 — dict, frozendict"中关于此类的文档。

关于其他容器，请参阅内置的 `frozendict`、`list`、`set` 和 `tuple` 类，以及 `collections` 模块。

## dir()

## dir(object, /)

不带参数时，返回当前局部作用域中的名称列表。带参数时，尝试返回该对象的有效属性列表。

如果对象有名为 `__dir__()` 的方法，则调用该方法，它必须返回属性列表。这允许实现了自定义 `__getattr__()` 或 `__getattribute__()` 函数的对象自定义 `dir()` 报告其属性的方式。

如果对象不提供 `__dir__()`，该函数会尽力从对象的 `__dict__` 属性（如果已定义）及其类型对象中收集信息。结果列表不一定完整，当对象有自定义 `__getattr__()` 时可能不准确。

默认的 `dir()` 机制对不同类型对象的行为不同，因为它试图产生最相关而非最完整的信息：

- 如果对象是模块对象，列表包含模块属性的名称。
- 如果对象是类型或类对象，列表包含其属性的名称，并递归地包含其基类属性的名称。
- 否则，列表包含对象的属性名称、其类的属性名称，并递归地包含其类的基类属性的名称。

结果列表按字母顺序排序。例如：

```python
import struct
dir()   # 显示模块命名空间中的名称
['__builtins__', '__name__', 'struct']
dir(struct)   # 显示 struct 模块中的名称
['Struct', '__all__', '__builtins__', '__doc__', '__file__',
 '__initializing__', '__loader__', '__name__', '__package__',
 '_clearcache', 'calcsize', 'error', 'pack', 'pack_into',
 'unpack', 'unpack_from']
class Shape:
    def __dir__(self):
        return ['area', 'perimeter', 'location']

s = Shape()
dir(s)
['area', 'location', 'perimeter']
```

> **注意**：由于 `dir()` 主要是为了在交互式提示符下方便使用而提供的，它更倾向于提供一组有趣的名称，而不是一组严格或一致定义的名称，并且其详细行为可能在不同版本间发生变化。例如，当参数是类时，元类属性不在结果列表中。

## divmod(a, b, /)

以两个（非复数）数字为参数，在使用整数除法时返回由它们的商和余数组成的一对数字。对于混合操作数类型，适用二元算术运算符的规则。对于整数，结果与 `(a // b, a % b)` 相同。对于浮点数，结果是 `(q, a % b)`，其中 q 通常是 `math.floor(a / b)`，但可能比它小 1。无论如何，`q * b + a % b` 都非常接近 a；如果 `a % b` 非零，则它与 b 同号，且 `0 <= abs(a % b) < abs(b)`。

## enumerate(iterable, start=0)

返回一个枚举对象。iterable 必须是序列、迭代器或其他支持迭代的对象。`enumerate()` 返回的迭代器的 `__next__()` 方法返回一个元组，其中包含计数（从 start 开始，默认为 0）和通过迭代 iterable 获得的值。

```python
seasons = ['Spring', 'Summer', 'Fall', 'Winter']
list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
list(enumerate(seasons, start=1))
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

等价于：

```python
def enumerate(iterable, start=0):
    n = start
    for elem in iterable:
        yield n, elem
        n += 1
```

## eval(source, /, globals=None, locals=None)

参数：

- **source** (`str | code object`) – 一个 Python 表达式。
- **globals** (`dict | frozendict | None`) – 全局命名空间（默认值：`None`）。
- **locals** (`mapping | None`) – 局部命名空间（默认值：`None`）。

返回：

- 表达式求值的结果。

引发：

- 语法错误会作为异常报告。

> **警告**：此函数执行任意代码。使用不受信任的用户输入调用它会导致安全漏洞。

source 参数被解析并作为 Python 表达式（严格来说，是表达式列表）求值，使用 globals 和 locals 映射作为全局和局部命名空间。如果 globals 字典存在且不包含键 `__builtins__` 的值，则在解析 source 之前，会在该键下插入对内置模块 `builtins` 字典的引用。覆盖 `__builtins__` 可以用来限制或更改可用的名称，但这不是一种安全机制：被执行的代码仍然可以访问所有内置名称。如果省略 locals 映射，则默认使用 globals 字典。如果两个映射都省略，则使用调用 `eval()` 的环境中的 globals 和 locals 执行 source。请注意，只有在封闭环境中的嵌套作用域（non-local）已经在调用 `eval()` 的作用域中被引用时（例如通过 `nonlocal` 语句），`eval()` 才能访问它们。

示例：

```python
x = 1
eval('x+1')
2
eval("1, 2")
(1, 2)
```

此函数也可用于执行任意代码对象（例如由 `compile()` 创建的对象）。在这种情况下，请传入代码对象而不是字符串。如果代码对象是以 `'exec'` 作为 mode 参数编译的，则 `eval()` 的返回值将为 `None`。

提示：`exec()` 函数支持动态执行语句。`globals()` 和 `locals()` 函数分别返回当前全局和局部字典，它们在传递给 `eval()` 或 `exec()` 使用时可能会很有用。

如果给定的 source 是字符串，则会去除首尾的空格和制表符。

请参见 `ast.literal_eval()`，它用于对仅包含字面量的表达式字符串求值。

以代码对象为参数引发审计事件 `exec`。也可能引发代码编译事件。

> **3.13 版更改**：globals 和 locals 参数现在可以作为关键字传递。
>
> **3.13 版更改**：默认 locals 命名空间的语义已按照 `locals()` 内置函数的说明进行了调整。
>
> **3.15 版更改**：globals 现在可以是 `frozendict`。

## exec(source, /, globals=None, locals=None, *, closure=None)

> **警告**：此函数执行任意代码。使用不受信任的用户输入调用它会导致安全漏洞。

此函数支持动态执行 Python 代码。source 必须是字符串或代码对象。如果是字符串，则将其解析为一组 Python 语句，然后执行（除非发生语法错误）。[1] 如果是代码对象，则直接执行。在所有情况下，被执行的代码都应作为文件输入有效（参见参考手册中的"文件输入"一节）。请注意，`nonlocal`、`yield` 和 `return` 语句不能在函数定义之外使用，即使在传递给 `exec()` 函数的代码上下文中也是如此。返回值为 `None`。

在所有情况下，如果省略了可选部分，代码将在当前作用域中执行。如果只提供 globals，则它必须是一个字典（而不是字典的子类），将同时用于全局和局部变量。如果同时给出 globals 和 locals，则分别用于全局和局部变量。如果提供 locals，它可以是任何映射对象。请记住，在模块层级，globals 和 locals 是同一个字典。

> **注意**：当 exec 获得两个独立的对象作为 globals 和 locals 时，代码将像嵌入在类定义中一样执行。这意味着在被执行代码中定义的函数和类将无法访问在顶层赋值的变量（因为"顶层"变量在类定义中被视为类变量）。

如果 globals 字典不包含键 `__builtins__` 的值，则在该键下插入对内置模块 `builtins` 字典的引用。覆盖 `__builtins__` 可以用来限制或更改可用的名称，但这不是一种安全机制：被执行的代码仍然可以访问所有内置名称。

closure 参数指定一个闭包——由单元变量（cellvars）组成的元组。只有当对象是包含自由（闭包）变量的代码对象时才有效。元组的长度必须与代码对象的 `co_freevars` 属性的长度完全匹配。

以代码对象为参数引发审计事件 `exec`。也可能引发代码编译事件。

> **注意**：内置函数 `globals()` 和 `locals()` 分别返回当前的全局和局部命名空间，可以将它们传递出去，用作 `exec()` 的第二和第三个参数。
>
> **注意**：默认的 locals 行为如下面的函数 `locals()` 所述。如果需要在 `exec()` 函数返回后查看代码对 locals 的影响，请传入显式的 locals 字典。

> **3.11 版更改**：新增了 closure 参数。
>
> **3.13 版更改**：globals 和 locals 参数现在可以作为关键字传递。
>
> **3.13 版更改**：默认 locals 命名空间的语义已按照 `locals()` 内置函数的说明进行了调整。
>
> **3.15 版更改**：globals 现在可以是 `frozendict`。

## filter(function, iterable, /)

从 iterable 中 function 为真的那些元素构造一个迭代器。iterable 可以是序列、支持迭代的容器或迭代器。如果 function 为 `None`，则假定使用恒等函数，即删除 iterable 中所有为假的元素。

请注意，如果 function 不为 `None`，则 `filter(function, iterable)` 等价于生成器表达式 `(item for item in iterable if function(item))`；如果 function 为 `None`，则等价于 `(item for item in iterable if item)`。

请参阅 `itertools.filterfalse()`，它是返回 iterable 中 function 为假的元素的互补函数。

## class float(number=0.0, /)

## class float(string, /)

返回由数字或字符串构造的浮点数。

示例：

```python
float('+1.23')
1.23
float('   -12345\n')
-12345.0
float('1e-003')
0.001
float('+1E6')
1000000.0
float('-Infinity')
-inf
```

如果参数是字符串，它应包含一个十进制数字，可选地以符号开头，并可选地被空白字符包围。可选的符号可以是 `'+'` 或 `'-'`；`'+'` 符号对产生的值没有影响。参数也可以是表示 NaN（非数字）或正负无穷大的字符串。更准确地说，在移除首尾空白字符之后，输入必须符合以下语法中的 floatvalue 产生式：

```
sign:          "+" | "-"
infinity:      "Infinity" | "inf"
nan:           "nan"
digit:         <Unicode 十进制数字，即 Unicode 通用类别 Nd 中的字符>
digitpart:     digit (["_"] digit)*
number:        [digitpart] "." digitpart | digitpart ["."]
exponent:      ("e" | "E") [sign] digitpart
floatnumber:   number [exponent]
absfloatvalue: floatnumber | infinity | nan
floatvalue:    [sign] absfloatvalue
```

大小写不重要，因此例如 `"inf"`、`"Inf"`、`"INFINITY"` 和 `"iNfINity"` 都是正无穷大的可接受拼写。

否则，如果参数是整数或浮点数，则返回具有相同值（在 Python 浮点精度范围内）的浮点数。如果参数超出 Python 浮点数的范围，将引发 `OverflowError`。

对于一般的 Python 对象 x，`float(x)` 委托给 `x.__float__()`。如果未定义 `__float__()`，则回退到 `__index__()`。

另请参阅 `float.from_number()`，它只接受数值参数。

如果未给出参数，则返回 `0.0`。

浮点类型在"数字类型 — int, float, complex"中有描述。

> **3.6 版更改**：允许像代码字面量一样使用下划线对数字分组。
>
> **3.7 版更改**：该参数现在是仅限位置参数。
>
> **3.8 版更改**：如果未定义 `__float__()`，则回退到 `__index__()`。

## format(value, format_spec='', /)

将值转换为由 format_spec 控制的"格式化"表示形式。format_spec 的解释取决于 value 参数的类型；不过，大多数内置类型都使用一种标准格式化语法：格式规格迷你语言。

默认的 format_spec 是空字符串，通常与调用 `str(value)` 的效果相同。

对 `format(value, format_spec)` 的调用会被转换为 `type(value).__format__(value, format_spec)`，它在查找值的 `__format__()` 方法时会绕过实例字典。如果方法查找一直到达 `object` 而 format_spec 非空，或者 format_spec 或返回值不是字符串，则会引发 `TypeError` 异常。

> **3.4 版更改**：如果 format_spec 不是空字符串，`object().__format__(format_spec)` 会引发 `TypeError`。

## class frozendict(**kwargs)

## class frozendict(mapping, /, **kwargs)

## class frozendict(iterable, /, **kwargs)

创建一个新的冻结字典。`frozendict` 对象是一个内置类。另请参阅"映射类型 — dict, frozendict"中关于此类的文档。

关于其他容器，请参阅内置的 `dict`、`list`、`set` 和 `tuple` 类，以及 `collections` 模块。

> **3.15 版新增。**

## class frozenset(iterable=(), /)

返回一个新的 `frozenset` 对象，可选地从 iterable 中获取元素。`frozenset` 是一个内置类。另请参阅"集合类型 — set, frozenset"中关于此类的文档。

关于其他容器，请参阅内置的 `set`、`list`、`tuple` 和 `dict` 类，以及 `collections` 模块。

## getattr(object, name, /)

## getattr(object, name, default, /)

返回 object 的指定名称属性的值。name 必须是字符串。如果该字符串是对象某个属性的名称，则结果就是该属性的值。例如，`getattr(x, 'foobar')` 等价于 `x.foobar`。如果指定名称的属性不存在，则返回 default（如果提供），否则引发 `AttributeError`。name 不必是 Python 标识符（参见 `setattr()`）。

> **注意**：由于私有名称改写（name mangling）发生在编译时，要使用 `getattr()` 获取私有属性（以两个下划线开头的属性）的值，必须手动改写其名称。

## globals()

返回实现当前模块命名空间的字典。对于函数内部的代码，它在函数定义时被设置，并且无论函数在哪里被调用都保持不变。

## hasattr(object, name, /)

参数是一个对象和一个字符串。如果字符串是对象某个属性的名称，则结果为 `True`，否则为 `False`。（这是通过调用 `getattr(object, name)` 并检查它是否引发 `AttributeError` 来实现的。）

## hash(object, /)

返回对象的哈希值（如果有的话）。哈希值是整数。它们用于在字典查找时快速比较字典键。比较相等的数值具有相同的哈希值（即使它们类型不同，例如 1 和 1.0 的情况）。

> **注意**：对于具有自定义 `__hash__()` 方法的对象，请注意 `hash()` 会根据宿主机的位宽截断返回值。

## help()

## help(request)

调用内置帮助系统。（此函数用于交互式使用。）如果未给出参数，则交互式帮助系统在解释器控制台上启动。如果参数是字符串，则将该字符串作为模块、函数、类、方法、关键字或文档主题的名称进行查找，并在控制台上打印帮助页面。如果参数是任何其他类型的对象，则会生成关于该对象的帮助页面。

请注意，如果在调用 `help()` 时函数的参数列表中出现斜杠（`/`），则表示斜杠之前的参数是仅限位置参数。有关更多信息，请参阅关于仅限位置参数的 FAQ 条目。

此函数由 `site` 模块添加到内置命名空间中。

> **3.4 版更改**：`pydoc` 和 `inspect` 的更改意味着报告的可调用对象签名现在更加全面和一致。

## hex(integer, /)

将整数转换为以小写 `"0x"` 为前缀的十六进制字符串。如果 integer 不是 Python 的 `int` 对象，则它必须定义返回整数的 `__index__()` 方法。一些示例：

```python
hex(255)
'0xff'
hex(-42)
'-0x2a'
```

如果要将整数转换为带前缀或不带前缀的大写或小写十六进制字符串，可以使用以下任一方式：

```python
'%#x' % 255, '%x' % 255, '%X' % 255
('0xff', 'ff', 'FF')
format(255, '#x'), format(255, 'x'), format(255, 'X')
('0xff', 'ff', 'FF')
f'{255:#x}', f'{255:x}', f'{255:X}'
('0xff', 'ff', 'FF')
```

另请参阅 `format()` 了解更多信息。

另请参阅 `int()`，了解如何使用基数 16 将十六进制字符串转换为整数。

> **注意**：要获取浮点数的十六进制字符串表示形式，请使用 `float.hex()` 方法。

## id(object, /)

返回对象的"标识"。这是一个整数，保证在该对象的生命周期内唯一且恒定。两个生命周期不重叠的对象可能具有相同的 `id()` 值。

> **CPython 实现细节**：这是对象在内存中的地址。

以参数 id 引发审计事件 `builtins.id`。

## input()

## input(prompt, /)

如果存在 prompt 参数，则将其写入标准输出，不带尾随换行符。然后该函数从输入中读取一行，将其转换为字符串（去除尾随换行符），并返回该字符串。当读取到 EOF 时，引发 `EOFError`。示例：

```python
s = input('--> ')
--> Monty Python's Flying Circus
s
"Monty Python's Flying Circus"
```

如果加载了 `readline` 模块，则 `input()` 将使用它来提供精细的行编辑和历史记录功能。

在读取输入之前，以参数 prompt 引发审计事件 `builtins.input`。

成功读取输入后，以结果引发审计事件 `builtins.input/result`。

## class int(number=0, /)

## class int(string, /, base=10)

返回由数字或字符串构造的整数对象，如果未给出参数则返回 0。

示例：

```python
int(123.45)
123
int('123')
123
int('   -12_345\n')
-12345
int('FACE', 16)
64206
int('0xface', 0)
64206
int('01110011', base=2)
115
```

如果参数定义了 `__int__()`，则 `int(x)` 返回 `x.__int__()`。如果参数定义了 `__index__()`，则返回 `x.__index__()`。对于浮点数，这会向零截断。

如果参数不是数字，或者给出了 base，则它必须是表示以 base 为基数的整数的字符串、`bytes` 或 `bytearray` 实例。字符串前面可以有 `+` 或 `-`（中间不能有空格），可以有前导零，可以被空白字符包围，并且数字之间可以穿插单个下划线。

以 base 为基数的整数字符串包含数字，每个数字表示从 0 到 n-1 的值。0–9 的值可以用任何 Unicode 十进制数字表示。10–35 的值可以用 a 到 z（或 A 到 Z）表示。默认基数为 10。允许的基数为 0 和 2–36。基数 2、8、16 的字符串可以像代码中的整数字面量一样，可选地加上前缀 `0b/0B`、`0o/0O` 或 `0x/0X`。对于基数 0，字符串的解释方式与代码中的整数字面量类似，实际基数由前缀决定，即 2、8、10 或 16。基数 0 还禁止前导零：`int('010', 0)` 不合法，而 `int('010')` 和 `int('010', 8)` 是合法的。

整数类型在"数字类型 — int, float, complex"中有描述。

> **3.4 版更改**：如果 base 不是 `int` 的实例，并且 base 对象有 `base.__index__` 方法，则调用该方法来获取基数的整数。以前的版本使用 `base.__int__` 而不是 `base.__index__`。
>
> **3.6 版更改**：允许像代码字面量一样使用下划线对数字分组。
>
> **3.7 版更改**：第一个参数现在是仅限位置参数。
>
> **3.8 版更改**：如果未定义 `__int__()`，则回退到 `__index__()`。
>
> **3.11 版更改**：可以限制 int 字符串输入和字符串表示的长度，以帮助避免拒绝服务攻击。将字符串转换为 int 时超出限制，或将 int 转换为字符串时会超出限制，都会引发 `ValueError`。请参阅整数字符串转换长度限制文档。
>
> **3.14 版更改**：`int()` 不再委托给 `__trunc__()` 方法。

## isinstance(object, classinfo, /)

如果 object 参数是 classinfo 参数的实例，或者是其（直接、间接或虚拟）子类的实例，则返回 `True`。如果 object 不是给定类型的对象，则该函数始终返回 `False`。如果 classinfo 是类型对象的元组（或递归地，其他此类元组）或多种类型的 Union 类型，则当 object 是其中任一类型的实例时返回 `True`。如果 classinfo 不是类型或类型元组及此类元组，则引发 `TypeError` 异常。如果先前的检查成功，则可能不会对无效类型引发 `TypeError`。

> **3.10 版更改**：classinfo 可以是 Union 类型。

## issubclass(cls, classinfo, /)

如果 cls 是 classinfo 的（直接、间接或虚拟）子类，则返回 `True`。一个类被视为其自身的子类。classinfo 可以是类对象的元组（或递归地，其他此类元组）或 Union 类型，在这种情况下，如果 cls 是 classinfo 中任何一项的子类，则返回 `True`。在任何其他情况下，引发 `TypeError` 异常。

> **3.10 版更改**：classinfo 可以是 Union 类型。

## iter(iterable, /)

## iter(callable, sentinel, /)

返回一个迭代器对象。第一个参数的解释因第二个参数是否存在而大不相同。没有第二个参数时，单个参数必须是支持可迭代协议（`__iter__()` 方法）的集合对象，或者必须支持序列协议（`__getitem__()` 方法，整数参数从 0 开始）。如果这两种协议都不支持，则引发 `TypeError`。如果给出了第二个参数 sentinel，则第一个参数必须是可调用对象。这种情况下创建的迭代器在每次调用其 `__next__()` 方法时都会不带参数地调用 callable；如果返回值等于 sentinel，则引发 `StopIteration`，否则返回该值。

另请参阅"与迭代相关的类型"。

`iter()` 第二种形式的一个有用应用是构建块读取器（block-reader）。例如，从二进制数据库文件中读取固定宽度的块，直到文件结束：

```python
from functools import partial
with open('mydata.db', 'rb') as f:
    for block in iter(partial(f.read, 64), b''):
        process_block(block)
```

## len(object, /)

返回对象的长度（项数）。参数可以是序列（如字符串、bytes、元组、列表或 range）或集合（如字典、集合或冻结集合）。

> **CPython 实现细节**：对于长度大于 `sys.maxsize` 的对象（如 `range(2 ** 100)`），`len` 会引发 `OverflowError`。

## class list(iterable=(), /)

`list` 实际上不是函数，而是一种可变序列类型，如"列表和序列类型 — list, tuple, range"中所述。

## locals()

返回表示当前局部符号表的映射对象，以变量名为键，以其当前绑定的引用为值。

在模块作用域，以及使用带单个命名空间的 `exec()` 或 `eval()` 时，此函数返回与 `globals()` 相同的命名空间。

在类作用域，它返回将传递给元类构造函数的命名空间。

当使用带独立的局部和全局参数的 `exec()` 或 `eval()` 时，它返回传入函数调用的局部命名空间。

在上述所有情况中，在给定的执行帧中每次调用 `locals()` 都会返回同一个映射对象。通过 `locals()` 返回的映射对象所做的更改将可见地表现为局部变量的赋值、重新赋值或删除，而对局部变量的赋值、重新赋值或删除会立即影响所返回映射对象的内容。

在优化作用域中（包括函数、生成器和协程），每次调用 `locals()` 反而返回一个包含函数局部变量当前绑定以及任何 nonlocal 单元引用的新字典。在这种情况下，通过返回的字典进行的名称绑定更改不会写回相应的局部变量或 nonlocal 单元引用，而对局部变量和 nonlocal 单元引用的赋值、重新赋值或删除不会影响之前返回的字典的内容。

在函数、生成器或协程中，作为推导式的一部分调用 `locals()` 等价于在包含作用域中调用它，不同之处在于会包含推导式已初始化的迭代变量。在其他作用域中，它的行为如同推导式作为嵌套函数运行。

作为生成器表达式的一部分调用 `locals()` 等价于在嵌套的生成器函数中调用它。

> **3.12 版更改**：`locals()` 在推导式中的行为已按 PEP 709 的描述更新。
>
> **3.13 版更改**：作为 PEP 667 的一部分，现在已定义修改此函数返回的映射对象的语义。优化作用域中的行为如上所述。除了被明确定义之外，其他作用域中的行为与之前版本保持不变。

## map(function, iterable, /, *iterables, strict=False)

返回一个迭代器，该迭代器将 function 应用于 iterable 的每一项并产出结果。如果传入了额外的 iterables 参数，则 function 必须接受同样数量的参数，并将其并行应用于所有可迭代对象的项。有多个可迭代对象时，迭代器在最短的可迭代对象耗尽时停止。如果 strict 为 `True` 并且其中一个可迭代对象先于其他对象耗尽，则引发 `ValueError`。对于函数输入已经排列为参数元组的情况，请参阅 `itertools.starmap()`。

> **3.14 版更改**：新增了 strict 参数。

## max(iterable, /, *, key=None)

## max(iterable, /, *, default, key=None)

## max(arg1, arg2, /, *args, key=None)

返回可迭代对象中的最大项，或两个或多个参数中最大的一个。

如果提供了一个位置参数，则它应当是一个可迭代对象。返回该可迭代对象中的最大项。如果提供了两个或多个位置参数，则返回其中最大的位置参数。

有两个可选的仅限关键字参数。key 参数指定一个单参数排序函数，类似于 `list.sort()` 使用的函数。default 参数指定当提供的可迭代对象为空时要返回的对象。如果可迭代对象为空且未提供 default，则引发 `ValueError`。

如果有多个最大项，该函数返回最先遇到的那一个。这与其他保持排序稳定性的工具一致，如 `sorted(iterable, key=keyfunc, reverse=True)[0]` 和 `heapq.nlargest(1, iterable, key=keyfunc)`。

> **3.4 版更改**：新增了 default 仅限关键字参数。
>
> **3.8 版更改**：key 可以为 `None`。

## class memoryview(object)

返回由给定参数创建的"内存视图"对象。更多信息请参见"内存视图"。

## min(iterable, /, *, key=None)

## min(iterable, /, *, default, key=None)

## min(arg1, arg2, /, *args, key=None)

返回可迭代对象中的最小项，或两个或多个参数中最小的一个。

如果提供了一个位置参数，则它应当是一个可迭代对象。返回该可迭代对象中的最小项。如果提供了两个或多个位置参数，则返回其中最小的位置参数。

有两个可选的仅限关键字参数。key 参数指定一个单参数排序函数，类似于 `list.sort()` 使用的函数。default 参数指定当提供的可迭代对象为空时要返回的对象。如果可迭代对象为空且未提供 default，则引发 `ValueError`。

如果有多个最小项，该函数返回最先遇到的那一个。这与其他保持排序稳定性的工具一致，如 `sorted(iterable, key=keyfunc)[0]` 和 `heapq.nsmallest(1, iterable, key=keyfunc)`。

> **3.4 版更改**：新增了 default 仅限关键字参数。
>
> **3.8 版更改**：key 可以为 `None`。

## next(iterator, /)

## next(iterator, default, /)

通过调用迭代器的 `__next__()` 方法获取下一项。如果给出了 default，则在迭代器耗尽时返回 default，否则引发 `StopIteration`。

## class object

这是所有其他类的终极基类。它具有 Python 类所有实例共有的方法。调用构造函数时，它返回一个没有特性的新对象。构造函数不接受任何参数。

> **注意**：`object` 实例没有 `__dict__` 属性，因此你无法为 `object` 的实例分配任意属性。

## oct(integer, /)

将整数转换为以 `"0o"` 为前缀的八进制字符串。结果是一个合法的 Python 表达式。如果 integer 不是 Python 的 `int` 对象，则它必须定义返回整数的 `__index__()` 方法。例如：

```python
oct(8)
'0o10'
oct(-56)
'-0o70'
```

如果要将整数转换为带或不带 `"0o"` 前缀的八进制字符串，可以使用以下任一方式：

```python
'%#o' % 10, '%o' % 10
('0o12', '12')
format(10, '#o'), format(10, 'o')
('0o12', '12')
f'{10:#o}', f'{10:o}'
('0o12', '12')
```

另请参阅 `format()` 了解更多信息。

## open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)

打开文件并返回相应的文件对象。如果文件无法打开，则引发 `OSError`。有关如何使用此函数的更多示例，请参见"读写文件"。

file 是一个路径类（path-like）对象，给出要打开的文件的路径名（绝对路径或相对于当前工作目录），或者是要包装的文件的整数文件描述符。（如果给出文件描述符，则当返回的 I/O 对象关闭时，它也会被关闭，除非将 closefd 设置为 `False`。）

mode 是一个可选字符串，指定文件打开的模式。它默认为 `'r'`，即以文本模式打开用于读取。其他常用值是 `'w'`（写入，如果文件已存在则截断）、`'x'`（独占创建）和 `'a'`（追加；在某些 Unix 系统上，这意味着无论当前查找位置如何，所有写入都会追加到文件末尾）。在文本模式下，如果未指定 encoding，则默认使用 UTF-8；如果禁用了 Python UTF-8 模式，则调用 `locale.getencoding()` 获取当前区域设置编码。（要读写原始字节，请使用二进制模式并保持 encoding 未指定。）可用的模式有：

| 字符 | 含义 |
| --- | --- |
| `'r'` | 打开用于读取（默认） |
| `'w'` | 打开用于写入，先截断文件 |
| `'x'` | 打开用于独占创建，如果文件已存在则失败 |
| `'a'` | 打开用于写入，如果文件存在则追加到文件末尾 |
| `'b'` | 二进制模式 |
| `'t'` | 文本模式（默认） |
| `'+'` | 打开用于更新（读写） |

默认模式是 `'r'`（打开用于读取文本，是 `'rt'` 的同义词）。模式 `'w+'` 和 `'w+b'` 会打开并截断文件。模式 `'r+'` 和 `'r+b'` 打开文件时不截断。

如"概述"中所述，Python 区分二进制 I/O 和文本 I/O。以二进制模式打开的文件（mode 参数中包含 `'b'`）以 `bytes` 对象返回内容，不做任何解码。在文本模式下（默认，或 mode 参数中包含 `'t'`），文件内容以 `str` 返回，字节先用默认编码（或指定编码，如果给出的话）解码。

> **注意**：Python 不依赖底层操作系统对文本文件的概念；所有处理都由 Python 自身完成，因此是平台无关的。

buffering 是一个可选整数，用于设置缓冲策略。传入 0 以关闭缓冲（仅在二进制模式下允许），1 表示选择行缓冲（仅在文本模式下写入时可用），大于 1 的整数表示固定大小块缓冲区的字节大小。请注意，以这种方式指定缓冲区大小适用于二进制缓冲 I/O，但 `TextIOWrapper`（即以 `mode='r+'` 打开的文件）会有另一种缓冲。要禁用 `TextIOWrapper` 中的缓冲，可以考虑为 `io.TextIOWrapper.reconfigure()` 使用 `write_through` 标志。当未给出 buffering 参数时，默认缓冲策略如下：

- 二进制文件以固定大小的块进行缓冲；当设备块大小可用时，缓冲区大小为 `max(min(blocksize, 8 MiB), DEFAULT_BUFFER_SIZE)`。在大多数系统上，缓冲区通常为 128 KB 长。
- "交互式"文本文件（`isatty()` 返回 `True` 的文件）使用行缓冲。其他文本文件使用上述二进制文件的策略。

encoding 是用于解码或编码文件的编码名称。它只应在文本模式下使用。默认编码是 UTF-8；如果禁用了 Python UTF-8 模式，则默认值取决于平台（即 `locale.getencoding()` 返回的值）。可以使用 Python 支持的任何文本编码，`encoding="locale"` 显式指定当前区域设置编码。有关支持的编码列表，请参见 `codecs` 模块。

errors 是一个可选字符串，指定如何处理编码和解码错误——不能在二进制模式下使用。可以使用多种标准错误处理器，任何已在 `codecs.register_error()` 中注册的错误处理名称也是有效的。标准名称可以在"错误处理器"中找到。

newline 决定如何解析流中的换行符。它可以是 `None`、`''`、`'\n'`、`'\r'` 和 `'\r\n'`。它的工作方式如下：

- 从流中读取输入时，如果 newline 为 `None`，则启用通用换行模式。输入中的行可以以 `'\n'`、`'\r'` 或 `'\r\n'` 结尾，这些会在返回给调用者之前被转换为 `'\n'`。如果为 `''`，则启用通用换行模式，但行结尾原样返回给调用者。如果是任何其他合法值，则输入行仅由给定的字符串终止，行结尾原样返回给调用者。
- 向流写入输出时，如果 newline 为 `None`，则写入的任何 `'\n'` 字符都会被转换为系统默认行分隔符 `os.linesep`。如果 newline 为 `''` 或 `'\n'`，则不进行转换。如果 newline 是任何其他合法值，则写入的任何 `'\n'` 字符都会被转换为给定的字符串。

如果 closefd 为 `False` 并且给出的是文件描述符而不是文件名，则当文件关闭时，底层文件描述符将保持打开。如果给出的是文件名，则 closefd 必须为 `True`（默认值）；否则将引发错误。

可以通过将可调用对象作为 opener 传入来使用自定义 opener。然后通过用 `(file, flags)` 调用 opener 来获取文件对象的底层文件描述符。opener 必须返回一个打开的文件描述符（将 `os.open` 作为 opener 传入，其功能类似于传入 `None`）。

新创建的文件是不可继承的。

以下示例使用 `os.open()` 函数的 dir_fd 参数打开相对于给定目录的文件：

```python
import os
dir_fd = os.open('somedir', os.O_RDONLY)
def opener(path, flags):
    return os.open(path, flags, dir_fd=dir_fd)

with open('spamspam.txt', 'w', opener=opener) as f:
    print('This will be written to somedir/spamspam.txt', file=f)

os.close(dir_fd)  # 不要泄漏文件描述符
```

`open()` 函数返回的文件对象类型取决于模式。当 `open()` 用于以文本模式打开文件（`'w'`、`'r'`、`'wt'`、`'rt'` 等）时，它返回 `io.TextIOBase` 的子类（具体是 `io.TextIOWrapper`）。当用于以带缓冲的二进制模式打开文件时，返回的类是 `io.BufferedIOBase` 的子类。具体类别各不相同：在读取二进制模式下，它返回 `io.BufferedReader`；在写入二进制和追加二进制模式下，它返回 `io.BufferedWriter`；在读写模式下，它返回 `io.BufferedRandom`。当缓冲被禁用时，返回原始流，即 `io.RawIOBase` 的子类 `io.FileIO`。

另请参阅文件处理模块，如 `fileinput`、`io`（`open()` 在其中声明）、`os`、`os.path`、`tempfile` 和 `shutil`。

以参数 path、mode、flags 引发审计事件 `open`。

mode 和 flags 参数可能已被修改或从原始调用中推断出来。

> **3.3 版更改**：
>
> - 新增了 opener 参数。
> - 新增了 `'x'` 模式。
> - 过去会引发 `IOError`，它现在是 `OSError` 的别名。
> - 如果以独占创建模式（`'x'`）打开的文件已存在，现在会引发 `FileExistsError`。
>
> **3.4 版更改**：
>
> - 文件现在不可继承。
>
> **3.5 版更改**：
>
> - 如果系统调用被中断且信号处理程序不引发异常，该函数现在会重试系统调用，而不是引发 `InterruptedError` 异常（原因参见 PEP 475）。
> - 新增了 `'namereplace'` 错误处理器。
>
> **3.6 版更改**：
>
> - 新增了对实现了 `os.PathLike` 的对象的支持。
> - 在 Windows 上，打开控制台缓冲区可能返回 `io.RawIOBase` 的除 `io.FileIO` 之外的子类。
>
> **3.11 版更改**：`'U'` 模式已被移除。
>
> **3.15 版更改**：UTF-8 现在是默认编码，取代了平台相关的区域设置编码（PEP 686）。

## ord(character, /)

返回字符的序数值。

如果参数是单字符字符串，则返回该字符的 Unicode 码位。例如，`ord('a')` 返回整数 97，`ord('€')`（欧元符号）返回 8364。这是 `chr()` 的逆操作。

如果参数是长度为 1 的 `bytes` 或 `bytearray` 对象，则返回其单个字节值。例如，`ord(b'a')` 返回整数 97。

## pow(base, exp, mod=None)

返回 base 的 exp 次幂；如果存在 mod，则返回 base 的 exp 次幂对 mod 取模（比 `pow(base, exp) % mod` 计算得更高效）。双参数形式 `pow(base, exp)` 等价于使用幂运算符：`base**exp`。

当参数是混合操作数类型的内置数值类型时，适用二元算术运算符的强制转换规则。对于 `int` 操作数，结果与操作数类型相同（强制转换后），除非第二个参数为负数；在这种情况下，所有参数都转换为 `float` 并返回浮点结果。例如，`pow(10, 2)` 返回 100，但 `pow(10, -2)` 返回 0.01。对于类型为 `int` 或 `float` 的负底数和非整数指数，返回复数结果。例如，`pow(-9, 0.5)` 返回接近 `3j` 的值。而对于类型为 `int` 或 `float` 的负底数和整数指数，返回浮点结果。例如，`pow(-9, 2.0)` 返回 `81.0`。

对于 `int` 操作数 base 和 exp，如果存在 mod，则 mod 也必须是整数类型且 mod 必须非零。如果存在 mod 且 exp 为负，则 base 必须与 mod 互质。在这种情况下，返回 `pow(inv_base, -exp, mod)`，其中 inv_base 是 base 对 mod 取模的逆元。

以下示例计算 38 对 97 取模的逆元：

```python
pow(38, -1, mod=97)
23
23 * 38 % 97 == 1
True
```

> **3.8 版更改**：对于 `int` 操作数，三参数形式的 `pow` 现在允许第二个参数为负，从而可以计算模逆元。
>
> **3.8 版更改**：允许使用关键字参数。以前只支持位置参数。

## print(*objects, sep=' ', end='\n', file=None, flush=False)

将对象打印到文本流 file 中，以 sep 分隔，并以 end 结尾。sep、end、file 和 flush（如果存在）必须作为关键字参数给出。

所有非关键字参数都会像 `str()` 那样转换为字符串并写入流中，以 sep 分隔并以 end 结尾。sep 和 end 都必须是字符串；它们也可以是 `None`，表示使用默认值。如果没有给出 objects，`print()` 只会写入 end。

file 参数必须是具有 `write(string)` 方法的对象；如果不存在或为 `None`，则使用 `sys.stdout`。由于打印的参数会被转换为文本字符串，`print()` 不能用于二进制模式的文件对象。对于这些情况，请改用 `file.write(...)`。

输出缓冲通常由 file 决定。但是，如果 flush 为真，则会强制刷新流。

> **3.3 版更改**：新增了 flush 关键字参数。

## class property(fget=None, fset=None, fdel=None, doc=None)

返回一个 property 属性。

fget 是获取属性值的函数。fset 是设置属性值的函数。fdel 是删除属性值的函数。doc 为该属性创建文档字符串。

典型用法是定义一个受管属性 x：

```python
class C:
    def __init__(self):
        self._x = None

    def getx(self):
        return self._x

    def setx(self, value):
        self._x = value

    def delx(self):
        del self._x

    x = property(getx, setx, delx, "I'm the 'x' property.")
```

如果 c 是 C 的实例，则 `c.x` 将调用 getter，`c.x = value` 将调用 setter，`del c.x` 将调用 deleter。

如果给出 doc，它将是 property 属性的文档字符串。否则，property 将复制 fget 的文档字符串（如果存在）。这使得使用 `@property` 作为装饰器可以轻松创建只读属性：

```python
class Parrot:
    def __init__(self):
        self._voltage = 100000

    @property
    def voltage(self):
        """Get the current voltage."""
        return self._voltage
```

`@property` 装饰器将 `voltage()` 方法转换为同名只读属性的"getter"，并将 voltage 的文档字符串设置为 "Get the current voltage."。

### @getter、@setter 和 @deleter

property 对象具有 getter、setter 和 deleter 方法，可用作装饰器，创建 property 的副本，并将相应的访问器函数设置为被装饰的函数。最好通过示例来解释：

```python
class C:
    def __init__(self):
        self._x = None

    @property
    def x(self):
        """I'm the 'x' property."""
        return self._x

    @x.setter
    def x(self, value):
        self._x = value

    @x.deleter
    def x(self):
        del self._x
```

这段代码与第一个示例完全等价。请务必为附加函数指定与原始 property 相同的名称（本例中为 x）。

返回的 property 对象还具有与构造函数参数相对应的属性 fget、fset 和 fdel。

> **3.5 版更改**：property 对象的文档字符串现在是可写的。

### __name__

保存 property 名称的属性。property 的名称可以在运行时更改。

> **3.13 版新增。**

## class range(stop, /)

## class range(start, stop, step=1, /)

`range` 实际上不是函数，而是一种不可变序列类型，如"Range 和序列类型 — list, tuple, range"中所述。

## repr(object, /)

返回包含对象可打印表示形式的字符串。对于许多类型，此函数会尝试返回一个字符串，当传递给 `eval()` 时会产生具有相同值的对象；否则，表示形式是一个用尖括号括起来的字符串，其中包含对象类型的名称以及其他信息，通常包括对象的名称和地址。类可以通过定义 `__repr__()` 方法来控制此函数为其实例返回的内容。如果 `sys.displayhook()` 不可访问，此函数将引发 `RuntimeError`。

这个类有可以求值的自定义表示形式：

```python
class Person:
   def __init__(self, name, age):
      self.name = name
      self.age = age

   def __repr__(self):
      return f"Person({self.name!r}, {self.age!r})"
```

## reversed(object, /)

返回一个反向迭代器。参数必须是具有 `__reversed__()` 方法的对象，或者支持序列协议（`__len__()` 方法和整数参数从 0 开始的 `__getitem__()` 方法）的对象。

## round(number, ndigits=None)

返回 number 舍入到小数点后 ndigits 位精度的值。如果省略 ndigits 或为 `None`，则返回最接近输入的整数。

对于支持 `round()` 的内置类型，值会被舍入到最接近的 10 的负 ndigits 次幂的倍数；如果两个倍数同样接近，则向偶数方向舍入（例如，`round(0.5)` 和 `round(-0.5)` 都是 0，`round(1.5)` 是 2）。ndigits 可以是任何整数值（正数、零或负数）。如果省略 ndigits 或为 `None`，则返回值是整数。否则，返回值的类型与 number 相同。

对于一般的 Python 对象 number，`round` 委托给 `number.__round__`。

> **注意**：`round()` 对浮点数的行为可能令人惊讶：例如，`round(2.675, 2)` 给出 2.67 而不是预期的 2.68。这不是 bug：这是因为大多数十进制小数无法精确表示为浮点数。更多信息请参见"浮点算术：问题与局限"。

## class set(iterable=(), /)

返回一个新的 `set` 对象，可选地从 iterable 中获取元素。`set` 是一个内置类。另请参阅"集合类型 — set, frozenset"中关于此类的文档。

关于其他容器，请参阅内置的 `frozenset`、`list`、`tuple` 和 `dict` 类，以及 `collections` 模块。

## setattr(object, name, value, /)

这是 `getattr()` 的对应函数。参数是一个对象、一个字符串和一个任意值。字符串可以指定一个现有属性或新属性。只要对象允许，该函数就会将值赋给该属性。例如，`setattr(x, 'foobar', 123)` 等价于 `x.foobar = 123`。

name 不必是"名称（标识符和关键字）"中定义的 Python 标识符，除非对象选择强制要求，例如通过自定义的 `__getattribute__()` 或通过 `__slots__`。名称不是标识符的属性无法使用点号表示法访问，但可以通过 `getattr()` 等访问。

> **注意**：由于私有名称改写（name mangling）发生在编译时，要使用 `setattr()` 设置私有属性（以两个下划线开头的属性），必须手动改写其名称。

## class sentinel(name, /, *, repr=None)

返回一个新的唯一哨兵对象。name 必须是 `str`，默认用作返回对象的表示形式：

```python
MISSING = sentinel("MISSING")
MISSING
MISSING
```

可选的 repr 参数可用于指定不同的表示形式：

```python
MISSING = sentinel("MISSING", repr="<MISSING>")
MISSING
<MISSING>
```

哨兵对象为真值（truthy），且仅与自身比较相等。它们用于与 `is` 运算符进行比较。

`sentinel` 不支持子类化。

哨兵对象的浅拷贝和深拷贝返回对象本身。

哨兵通常被赋值给一个名称匹配的变量。以这种方式定义的哨兵可以在类型提示中使用：

```python
MISSING = sentinel("MISSING")

def next_value(default: int | MISSING = MISSING):
    ...
```

哨兵对象支持 `|` 运算符，用于类型表达式。

对于放置在模块全局作用域中且名称与哨兵名称匹配的哨兵对象，以及放置在类作用域中且名称与哨兵的限定名称匹配的哨兵，支持序列化（pickle）。其他哨兵（例如定义在函数作用域中的）不可序列化。序列化后哨兵的标识保持不变：

```python
import pickle

PICKLABLE = sentinel("PICKLABLE")

assert pickle.loads(pickle.dumps(PICKLABLE)) is PICKLABLE

class Cls:
    PICKLABLE = sentinel("Cls.PICKLABLE")

assert pickle.loads(pickle.dumps(Cls.PICKLABLE)) is Cls.PICKLABLE
```

哨兵对象具有以下属性：

### __name__

哨兵的名称。

### __module__

创建哨兵的模块名称。此属性是可写的。

> **3.15 版新增。**

## class slice(stop, /)

## class slice(start, stop, step=None, /)

返回一个切片对象，表示 `range(start, stop, step)` 指定的索引集合。start 和 step 参数默认为 `None`。

使用切片语法时也会生成切片对象。例如：`a[start:stop:step]` 或 `a[start:stop, i]`。

有关返回迭代器的替代版本，请参见 `itertools.islice()`。

### start、stop、step

这些只读属性设置为参数值（或其默认值）。它们没有其他显式功能；不过，NumPy 和其他第三方包会使用它们。

> **3.12 版更改**：切片对象现在可哈希（前提是 start、stop 和 step 可哈希）。

## sorted(iterable, /, *, key=None, reverse=False)

从 iterable 的项中返回一个新的已排序列表。

有两个可选参数，必须作为关键字参数指定。

key 指定一个单参数函数，用于从 iterable 的每个元素中提取比较键（例如，`key=str.lower`）。默认值为 `None`（直接比较元素）。

reverse 是一个布尔值。如果设置为 `True`，则列表元素将按照每次比较都反转的方式排序。

使用 `functools.cmp_to_key()` 将旧式的 cmp 函数转换为 key 函数。

内置的 `sorted()` 函数保证是稳定的。如果排序保证不改变比较相等的元素的相对顺序，则该排序是稳定的——这有助于多遍排序（例如，先按部门排序，再按薪级排序）。

排序算法仅在项之间使用 `<` 比较。虽然定义 `__lt__()` 方法就足以进行排序，但 PEP 8 建议实现全部六个富比较。这有助于避免在使用同一数据与其他排序工具（如依赖不同底层方法的 `max()`）时出现错误。实现全部六个比较也有助于避免混合类型比较的混淆，这些比较可能会调用反射的 `__gt__()` 方法。

有关排序示例和简短的排序教程，请参见"排序技巧"。

## @staticmethod

将方法转换为静态方法。

静态方法不接收隐式的第一个参数。要声明静态方法，请使用以下惯用法：

```python
class C:
    @staticmethod
    def f(arg1, arg2, argN): ...
```

`@staticmethod` 形式是一个函数装饰器——详细信息参见"函数定义"。

静态方法可以在类上调用（如 `C.f()`），也可以在实例上调用（如 `C().f()`）。此外，静态方法描述符本身也是可调用的，因此可以在类定义中使用（如 `f()`）。

Python 中的静态方法类似于 Java 或 C++ 中的静态方法。另请参阅 `@classmethod`，它是用于创建备用类构造函数的有用变体。

与所有装饰器一样，也可以将 `staticmethod` 作为常规函数调用并对其结果做一些处理。在某些情况下这是必要的，例如当你需要从类体中引用一个函数，并且想避免自动转换为实例方法时。对于这些情况，请使用以下惯用法：

```python
def regular_function():
    ...

class C:
    method = staticmethod(regular_function)
```

有关静态方法的更多信息，请参见"标准类型层级结构"。

> **3.10 版更改**：静态方法现在继承方法属性（`__module__`、`__name__`、`__qualname__`、`__doc__` 和 `__annotations__`），新增了 `__wrapped__` 属性，并且现在可以作为常规函数调用。

## class str(*, encoding='utf-8', errors='strict')

## class str(object)

## class str(object, encoding, errors='strict')

## class str(object, *, errors)

返回 object 的 `str` 版本。详细信息参见 `str()`。

`str` 是内置的字符串类。有关字符串的一般信息，请参见"文本序列类型 — str"。

## sum(iterable, /, start=0)

从左到右对 start 和 iterable 的项求和并返回总和。iterable 的项通常是数字，start 值不允许是字符串。

对于某些用例，`sum()` 有很好的替代方案。连接字符串序列的首选快速方法是调用 `''.join(sequence)`。要以扩展精度添加浮点值，请参见 `math.fsum()`。要连接一系列可迭代对象，请考虑使用 `itertools.chain()`。

> **3.8 版更改**：start 参数可以指定为关键字参数。
>
> **3.12 版更改**：浮点求和切换为一种在大多数构建上具有更高精度和更好交换性的算法。
>
> **3.14 版更改**：新增了对复数求和的专门化，使用与浮点求和相同的算法。

## class super

## class super(type, object_or_type=None, /)

返回一个代理对象，将方法调用委托给 type 的父类或兄弟类。这对于访问在类中被覆盖的继承方法很有用。

object_or_type 决定要搜索的方法解析顺序（MRO）。搜索从 type 之后紧跟的类开始。

例如，如果 object_or_type 的 `__mro__` 是 `D -> B -> C -> A -> object`，且 type 的值是 B，则 `super()` 搜索 `C -> A -> object`。

与 object_or_type 对应的类的 `__mro__` 属性列出了 `getattr()` 和 `super()` 使用的方法解析搜索顺序。该属性是动态的，每当继承层级更新时都可能改变。

如果省略第二个参数，则返回的 super 对象是未绑定的。如果第二个参数是对象，则 `isinstance(obj, type)` 必须为真。如果第二个参数是类型，则 `issubclass(type2, type)` 必须为真（这对类方法很有用）。

当在类的普通方法中直接调用时，两个参数都可以省略（"零参数 super()"）。在这种情况下，type 将是外围类，obj 将是紧邻的外围函数的第一个参数（通常是 `self`）。（这意味着零参数 `super()` 在嵌套函数中不会按预期工作，包括隐式创建嵌套函数的生成器表达式。）

super 有两种典型用例。在单继承的类层级中，super 可用于引用父类而无需显式命名，从而使代码更易于维护。这种用法与其他编程语言中 super 的用法非常相似。

第二种用例是在动态执行环境中支持协作式多重继承。这种用例是 Python 独有的，在静态编译语言或仅支持单继承的语言中不存在。这使得实现"菱形图"成为可能，即多个基类实现同一个方法。良好的设计要求此类实现在每种情况下都具有相同的调用签名（因为调用顺序在运行时确定，因为该顺序会适应类层级的变化，并且因为该顺序可能包含运行时之前未知的兄弟类）。

对于这两种用例，典型的父类调用如下所示：

```python
class C(B):
    def method(self, arg):
        super().method(arg)    # 这与以下代码效果相同：
                               # super(C, self).method(arg)
```

除了方法查找之外，`super()` 也适用于属性查找。一个可能的用例是在父类或兄弟类中调用描述符。

请注意，`super()` 是作为显式点号属性查找（如 `super().__getitem__(name)`）绑定过程的一部分实现的。它通过实现自己的 `__getattribute__()` 方法来以支持协作式多重继承的可预测顺序搜索类。因此，`super()` 对于使用语句或运算符（如 `super()[name]`）的隐式查找是未定义的。

还要注意，除了零参数形式之外，`super()` 不限于在方法内部使用。双参数形式精确指定参数并建立适当的引用。零参数形式只能在类定义内部使用，因为编译器会填入必要的细节以正确获取正在定义的类，以及为普通方法访问当前实例。

关于如何使用 `super()` 设计协作类的实用建议，请参见"使用 super() 的指南"。

> **3.14 版更改**：super 对象现在可以被序列化和复制。

## class tuple(iterable=(), /)

`tuple` 实际上不是函数，而是一种不可变序列类型，如"元组和序列类型 — list, tuple, range"中所述。

## class type(object, /)

## class type(name, bases, dict, /, **kwargs)

使用一个参数时，返回对象的类型。返回值是一个类型对象，通常与 `object.__class__` 返回的对象相同。

建议使用内置函数 `isinstance()` 来测试对象的类型，因为它考虑了子类。

使用三个参数时，返回一个新的类型对象。这本质上是 `class` 语句的动态形式。name 字符串是类名，并成为 `__name__` 属性。bases 元组包含基类，并成为 `__bases__` 属性；如果为空，则添加所有类的终极基类 `object`。dict 字典包含类体的属性和方法定义；它可能在被复制或包装后成为 `__dict__` 属性。以下两条语句创建相同的类型对象：

```python
class X:
    a = 1

X = type('X', (), dict(a=1))
```

另请参阅：

- 关于类的属性和方法的文档。
- 类型对象。

提供给三参数形式的关键字参数会传递给相应的元类机制（通常是 `__init_subclass__()`），就像类定义中的关键字（metaclass 除外）那样。

与 `class` 语句不同，三参数形式不会调用元类的 `__prepare__` 方法（参见"准备类命名空间"）。使用 `types.new_class()` 可以用适当的元类动态创建类。

另请参阅"自定义类创建"。

> **3.6 版更改**：未覆盖 `type.__new__` 的 `type` 子类不能再使用单参数形式来获取对象的类型。
>
> **3.15 版更改**：dict 现在可以是 `frozendict`。

## vars()

## vars(object, /)

返回模块、类、实例或任何其他具有 `__dict__` 属性的对象的 `__dict__` 属性。

模块和实例等对象具有可更新的 `__dict__` 属性；但是，其他对象可能对其 `__dict__` 属性有写入限制（例如，类使用 `types.MappingProxyType` 来防止直接更新字典）。

不带参数时，`vars()` 的行为类似于 `locals()`。

如果指定了对象但它没有 `__dict__` 属性（例如，其类定义了 `__slots__` 属性），则引发 `TypeError` 异常。

> **3.13 版更改**：不带参数调用此函数的结果已按照 `locals()` 内置函数的说明进行了更新。

## zip(*iterables, strict=False)

并行迭代多个可迭代对象，生成由每个对象各取一项组成的元组。

示例：

```python
for item in zip([1, 2, 3], ['sugar', 'spice', 'everything nice']):
    print(item)

(1, 'sugar')
(2, 'spice')
(3, 'everything nice')
```

更正式地说：`zip()` 返回一个元组迭代器，其中第 i 个元组包含每个参数可迭代对象的第 i 个元素。

思考 `zip()` 的另一种方式是：它将行变为列，将列变为行。这类似于矩阵转置。

`zip()` 是惰性的：在可迭代对象被迭代之前（例如通过 for 循环或包装在 list 中），元素不会被处理。

需要考虑的一点是，传递给 `zip()` 的可迭代对象可能长度不同；有时是故意如此，有时是因为准备这些可迭代对象的代码中存在错误。Python 提供了三种不同的方法来处理此问题：

默认情况下，`zip()` 在最短的可迭代对象耗尽时停止。它会忽略较长可迭代对象中的剩余项，将结果截断为最短可迭代对象的长度：

```python
list(zip(range(3), ['fee', 'fi', 'fo', 'fum']))
[(0, 'fee'), (1, 'fi'), (2, 'fo')]
```

`zip()` 常用于假定可迭代对象等长的情况。在这种情况下，建议使用 `strict=True` 选项。其输出与常规 `zip()` 相同：

```python
list(zip(('a', 'b', 'c'), (1, 2, 3), strict=True))
[('a', 1), ('b', 2), ('c', 3)]
```

与默认行为不同，如果一个可迭代对象先于其他对象耗尽，它会引发 `ValueError`：

```python
for item in zip(range(3), ['fee', 'fi', 'fo', 'fum'], strict=True):
    print(item)

(0, 'fee')
(1, 'fi')
(2, 'fo')
Traceback (most recent call last):
  ...
ValueError: zip() argument 2 is longer than argument 1
```

如果没有 `strict=True` 参数，任何导致可迭代对象长度不同的错误都会被静默掩盖，可能表现为程序其他部分中难以发现的错误。

可以通过用常量值填充较短的可迭代对象来使所有可迭代对象具有相同的长度。这由 `itertools.zip_longest()` 完成。

边界情况：使用单个可迭代对象参数时，`zip()` 返回一个 1 元组迭代器。不带参数时，它返回一个空迭代器。

提示与技巧：

- 可迭代对象从左到右的求值顺序是有保证的。这使得可以用 `zip(*[iter(s)]*n, strict=True)` 这个惯用法将数据序列聚合成 n 长度的组。它将同一个迭代器重复 n 次，使每个输出元组包含对该迭代器 n 次调用的结果。其效果是将输入划分为 n 长度的块。

`zip()` 与 `*` 运算符结合可以用来解压（unzip）列表：

```python
x = [1, 2, 3]
y = [4, 5, 6]
list(zip(x, y))
[(1, 4), (2, 5), (3, 6)]
x2, y2 = zip(*zip(x, y))
x == list(x2) and y == list(y2)
True
```

> **3.10 版更改**：新增了 strict 参数。

## __import__(name, globals=None, locals=None, fromlist=(), level=0)

> **注意**：这是一个高级函数，日常 Python 编程中不需要，不像 `importlib.import_module()`。

此函数由 `import` 语句调用。它可以被替换（通过导入 `builtins` 模块并赋值给 `builtins.__import__`）以更改 `import` 语句的语义，但强烈不建议这样做，因为通常使用导入钩子（参见 PEP 302）实现同样的目标更简单，并且不会给假定使用默认导入实现的代码带来问题。也建议不要直接使用 `__import__()`，而应使用 `importlib.import_module()`。

该函数导入名为 name 的模块，可能使用给定的 globals 和 locals 来确定如何在包上下文中解释该名称。fromlist 给出应从 name 指定的模块导入的对象或子模块的名称。标准实现完全不使用其 locals 参数，仅使用 globals 来确定 `import` 语句的包上下文。

level 指定使用绝对导入还是相对导入。0（默认值）表示只执行绝对导入。level 的正值表示相对于调用 `__import__()` 的模块所在目录向上搜索的父目录层数（详细信息参见 PEP 328）。

当 name 变量的形式为 `package.module` 时，通常返回顶层包（第一个点之前的名称），而不是 name 指定的模块。但是，当给出非空的 fromlist 参数时，返回 name 指定的模块。

例如，语句 `import spam` 产生的字节码类似于以下代码：

```python
spam = __import__('spam', globals(), locals(), [], 0)
```

语句 `import spam.ham` 产生此调用：

```python
spam = __import__('spam.ham', globals(), locals(), [], 0)
```

注意这里 `__import__()` 返回顶层模块，因为这是 `import` 语句绑定到名称的对象。

另一方面，语句 `from spam.ham import eggs, sausage as saus` 产生：

```python
_temp = __import__('spam.ham', globals(), locals(), ['eggs', 'sausage'], 0)
eggs = _temp.eggs
saus = _temp.sausage
```

这里，从 `__import__()` 返回 `spam.ham` 模块。从这个对象中检索要导入的名称，并赋值给它们各自的名称。

如果只是想按名称导入模块（可能在包内），请使用 `importlib.import_module()`。

> **3.3 版更改**：不再支持 level 的负值（这也将默认值更改为 0）。
>
> **3.9 版更改**：当使用命令行选项 `-E` 或 `-I` 时，现在会忽略环境变量 `PYTHONCASEOK`。

## 脚注

[1] 请注意，解析器只接受 Unix 风格的行尾约定。如果从文件中读取代码，请确保使用换行转换模式来转换 Windows 或 Mac 风格的换行符。
