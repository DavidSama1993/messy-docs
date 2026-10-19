# 内置常量

内置命名空间中存在少量常量，它们是：

## False

`bool` 类型的假值。对 `False` 赋值是非法的，会引发 `SyntaxError`。

## True

`bool` 类型的真值。对 `True` 赋值是非法的，会引发 `SyntaxError`。

## None

一个常用于表示"值缺失"的对象，例如当默认参数没有传递给函数时。对 `None` 赋值是非法的，会引发 `SyntaxError`。`None` 是 `NoneType` 类型的唯一实例。

## NotImplemented

一个特殊值，应由二元特殊方法（如 `__eq__()`、`__lt__()`、`__add__()`、`__rsub__()` 等）返回，以表示该操作未针对另一个类型实现；就地二元特殊方法（如 `__imul__()`、`__iand__()` 等）也可以出于同样的目的返回它。它不应在布尔上下文中求值。`NotImplemented` 是 `types.NotImplementedType` 类型的唯一实例。

> **注意**：当二元（或就地）方法返回 `NotImplemented` 时，解释器会尝试在另一个类型上执行反射操作（或根据运算符采用其他回退方式）。如果所有尝试都返回 `NotImplemented`，解释器将引发适当的异常。错误地返回 `NotImplemented` 会导致误导性的错误消息，或将 `NotImplemented` 值返回给 Python 代码。
>
> 示例请参阅"实现算术运算"。

> **警告**：`NotImplemented` 与 `NotImplementedError` 不可互换。该常量只能按上述方式使用；关于该异常的正确用法，请参阅 `NotImplementedError`。

> **3.9 版更改**：在布尔上下文中对 `NotImplemented` 求值已被弃用。
>
> **3.14 版更改**：在布尔上下文中对 `NotImplemented` 求值现在会引发 `TypeError`。此前（自 Python 3.9 起）它会求值为 `True` 并发出 `DeprecationWarning`。

## Ellipsis

与省略号字面量 `"..."` 相同，是一个常用于表示"某处被省略"的对象。可以对 `Ellipsis` 赋值，但对 `...` 赋值会引发 `SyntaxError`。`Ellipsis` 是 `types.EllipsisType` 类型的唯一实例。

## __debug__

如果 Python 不是以 `-O` 选项启动的，则此常量为真。另请参阅 `assert` 语句。

> **注意**：名称 `None`、`False`、`True` 和 `__debug__` 不能被重新赋值（对它们赋值，即使作为属性名，也会引发 `SyntaxError`），因此可以将它们视为"真正的"常量。

## site 模块添加的常量

`site` 模块（在启动时自动导入，除非给出了 `-S` 命令行选项）会向内置命名空间添加几个常量。它们对交互式解释器 shell 很有用，但不应在程序中使用。

### quit(code=None)

### exit(code=None)

打印时，会打印类似"使用 quit() 或 Ctrl-D（即 EOF）退出"消息的对象；在交互式解释器中直接访问或作为函数调用时，会以指定的退出码引发 `SystemExit`。

### help

打印时，会打印"输入 help() 获取交互式帮助，或输入 help(object) 获取关于对象的帮助。"消息的对象；在交互式解释器中直接访问时，会调用内置帮助系统（参见 `help()`）。

### copyright

### credits

打印或调用时，分别打印版权或致谢文本的对象。

### license

打印时，会打印"输入 license() 查看完整许可文本"消息的对象；调用时，会以类似分页的方式（一次一屏）显示完整的许可文本。
