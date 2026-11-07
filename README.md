# fraction.vt —— Vortex 精确有理数库

> 一个单文件的 Vortex 脚本语言精确有理数库。基于 `long` 实现，自动约分，同时兼容 **解释器**（`vortex`）与 **LLVM 原生编译器**（`vtcc`）。
> I doesn't has the time to translate the file.
> sorry about that.

![License](https://img.shields.io/badge/License-MIT-blue)
![Platform](https://img.shields.io/badge/Backend-interpreter%20%2B%20vtcc-green)

---

## 特性

- ✅ **精确算术** —— `+ - * /`、一元 `-`、整数幂 `**`，无浮点误差
- ✅ **自动约分** —— 每个 `Fraction` 始终保持最简形式，分母恒为正
- ✅ **运算符重载** —— 自然语法：`a + b`、`-a`（通过顶层 `operator` 声明）
- ✅ **精确比较** —— `eq / ne / lt / le / gt / ge / cmp`
- ✅ **解析与格式化** —— `parse("22/7")`、`to_str()`（`"3/4"`、`"7"`）、`to_float()`
- ✅ **整数混合运算** —— `add_int / sub_int / mul_int / div_int`
- ✅ **类型化异常** —— 分母为零抛 `ValueError`，`div`/`inv` 对零抛 `ZeroDivisionError`
- ✅ **单文件** —— 无需构建，`import fraction;` 即可
- ✅ **双后端一致** —— `vortex` 与 `vtcc` 行为完全相同

---

## 快速开始

`fraction.vt` 是**单文件模块**，放到脚本同级目录即可：

```
my_project/
    main.vt
    fraction.vt
```

```vt
import fraction;

auto a = fraction.frac(1, 3);
auto b = fraction.frac(1, 6);

print(a.to_str());                  # 1/3
print((a + b).to_str());            # 1/2
print((a * b).to_str());            # 1/18
print((-a).to_str());               # -1/3
print(a.to_float());                # 0.333333
```

---

## API 参考

### 构造

| 函数 | 返回 | 说明 |
| --- | --- | --- |
| `Fraction(n, d)` | `Fraction` | 构造。自动约分、规范化符号；`d == 0` 抛 `ValueError` |
| `frac(n, d)` | `Fraction` | `Fraction(n, d)` 的简写 |
| `from_int(n)` | `Fraction` | 整数 → 有理数（`n/1`） |
| `from_float(x)` | `Fraction` | 从 double 近似构造，保留 6 位小数 |
| `from_float_prec(x, prec)` | `Fraction` | 从 double 近似构造，保留 `prec` 位小数 |

```vt
auto f = fraction.frac(6, 8);          # 3/4（自动约分）
auto g = fraction.from_int(5);         # 5
auto h = fraction.from_float(0.25);    # 1/4
```

### 访问器

| 函数 | 返回 | 说明 |
| --- | --- | --- |
| `numerator(f)` | `long` | 分子（承载符号） |
| `denominator(f)` | `long` | 分母（恒为正） |

也可以直接读取字段：

```vt
auto f = fraction.frac(3, 4);
print(f.num);    # 3
print(f.den);    # 4
```

### 算术（模块级）

| 函数 | 返回 | 说明 |
| --- | --- | --- |
| `add(a, b)` | `Fraction` | `a + b` |
| `sub(a, b)` | `Fraction` | `a - b` |
| `mul(a, b)` | `Fraction` | `a * b` |
| `div(a, b)` | `Fraction` | `a / b`，`b == 0` 抛 `ZeroDivisionError` |
| `neg(a)` | `Fraction` | `-a` |
| `abs_(a)` | `Fraction` | `\|a\|` |
| `inv(a)` | `Fraction` | `1 / a`，`a == 0` 抛 `ZeroDivisionError` |
| `pow(a, n)` | `Fraction` | `a ** n`（整数指数，允许负数） |

### 与整数混合

| 函数 | 返回 | 说明 |
| --- | --- | --- |
| `add_int(a, k)` | `Fraction` | `a + k` |
| `sub_int(a, k)` | `Fraction` | `a - k` |
| `mul_int(a, k)` | `Fraction` | `a * k` |
| `div_int(a, k)` | `Fraction` | `a / k`，`k == 0` 抛 `ZeroDivisionError` |

### 比较

| 函数 | 返回 | 说明 |
| --- | --- | --- |
| `eq(a, b)` | `bool` | `a == b` |
| `ne(a, b)` | `bool` | `a != b` |
| `lt(a, b)` | `bool` | `a < b` |
| `le(a, b)` | `bool` | `a <= b` |
| `gt(a, b)` | `bool` | `a > b` |
| `ge(a, b)` | `bool` | `a >= b` |
| `cmp(a, b)` | `int` | `-1`、`0`、`1` |

### 转换

| 函数 | 返回 | 说明 |
| --- | --- | --- |
| `to_str(f)` | `str` | `"3/4"`；分母为 `1` 时返回 `"7"` |
| `to_float(f)` | `float` | 近似 double 值 |
| `parse(s)` | `Fraction` | 解析 `"n/d"` 或 `"n"`，容忍首尾空格 |

### 常量

| 名称 | 值 |
| --- | --- |
| `fraction.ZERO` | `0/1` |
| `fraction.ONE` | `1/1` |

### 运算符重载

得益于 Vortex 的顶层 `operator` 声明，以下运算可直接作用于 `Fraction`：

| 表达式 | 结果 |
| --- | --- |
| `a + b` | `Fraction` |
| `a - b` | `Fraction` |
| `a * b` | `Fraction` |
| `a / b` | `Fraction` |
| `-a` | `Fraction` |

---

## 示例

### 基础算术

```vt
import fraction;

auto a = fraction.frac(1, 2);
auto b = fraction.frac(1, 3);

print((a + b).to_str());            # 5/6
print((a - b).to_str());            # 1/6
print((a * b).to_str());            # 1/6
print((a / b).to_str());            # 3/2
print((-a).to_str());               # -1/2
```

### 精确幂运算

```vt
import fraction;

auto f = fraction.frac(2, 3);
print(fraction.pow(f, 3).to_str());     # 8/27
print(fraction.pow(f, -2).to_str());    # 9/4
```

### 解析与比较

```vt
import fraction;

auto p = fraction.parse("22/7");
auto q = fraction.frac(355, 113);

print("22/7 =",   p.to_float());        # 3.142857...
print("355/113 =", q.to_float());       # 3.141592...
print("355/113 > 22/7 ?", fraction.gt(q, p));   # true
print("cmp =", fraction.cmp(q, p));     # 1
```

### 与整数混合

```vt
import fraction;

auto f = fraction.frac(3, 4);
print(fraction.add_int(f, 1).to_str());     # 7/4
print(fraction.mul_int(f, 2).to_str());     # 3/2
```

### 错误处理

```vt
import fraction;

try {
    fraction.frac(1, 0);
} catch (ValueError e) {
    print("caught:", e);        # caught: Fraction: denominator must not be zero
}

try {
    fraction.div(fraction.frac(1, 2), fraction.ZERO);
} catch (ZeroDivisionError e) {
    print("caught:", e);        # caught: Fraction: division by zero
}
```

### 精确求和（调和级数）

```vt
import fraction;

auto s = fraction.ZERO;
long n = 10;
for (i in range(1, n + 1)) {
    s = s + fraction.frac(1, i);
}
print(s.to_str());              # 7381/2520
print(s.to_float());            # 2.928968...
```

### 用运算符重载构造连分数

```vt
import fraction;

# [1; 2, 2, 2, ...] —— 收敛到 sqrt(2)
auto x = fraction.from_int(1);
long terms = 10;
for (i in range(0, terms)) {
    x = fraction.from_int(1) + fraction.div(fraction.ONE, fraction.from_int(1) + x);
}
print(x.to_str());              # sqrt(2) 的有理数近似
print(x.to_float());            # ≈ 1.41421356...
```

---

## 设计要点

### 不变量

本库产生的每个 `Fraction` 都满足：

1. **最简形式** —— `gcd(num, den) == 1`
2. **分母为正** —— `den > 0`，符号由 `num` 承载

`init` 构造函数强制保证这两点，且所有算术运算都返回新构造的 `Fraction`，因此外部无法破坏该不变量。

### 溢出

内部 `num` 与 `den` 均为 `long`。中间乘积（如 `add` 或 `lt` 中的 `num * den`）在输入较大时可能超出 `long` 范围，此时按二补码回绕（模 2⁶⁴），与 Vortex 的整数语义一致。

若需要处理更大的分子/分母，可考虑使用 `extfloat`（Vortex 的精确十进制类型），或在组合前预先约分。

### 异常

| 场景 | 抛出 |
| --- | --- |
| `Fraction(n, 0)` | `ValueError("Fraction: denominator must not be zero")` |
| `div(a, ZERO)` | `ZeroDivisionError("Fraction: division by zero")` |
| `inv(ZERO)` | `ZeroDivisionError("Fraction: inverse of zero")` |

全部为**类型化异常**——可用 `catch (ValueError e)` / `catch (ZeroDivisionError e)` 过滤，未匹配的异常会向外层传播，与 Vortex 其余部分一致。

### 兼容性

- ✅ `vortex`（解释器）
- ✅ `vtcc`（原生编译器）
- ✅ `--debug` 构建
- ✅ 可通过 `vtpm pack` 打包（单文件，无 `extdef` DLL）

仅依赖内建模块：

- `math` —— 用于 `math.gcd`
- `text` —— `parse` 中使用 `text.find / len / char_at / trim`

---

## 许可证

MIT License —— 见 [LICENSE](LICENSE)。

---

## 贡献

欢迎提交 issue 和 PR。提交时请：

1. 保持 `fraction.vt` 为**单文件**（这是本库的全部意义所在）
2. 在 `tests/` 中添加测试（`foo.vt` + `foo.expected` 对，参见 Vortex 的[回归测试](https://www.github.com/i8doney/vortex/blob/main/USAGE.md#6-regression-testing)）
3. 验证 `vtcc` 一致性（`runtest.ps1` 会对缺少 `.expected` 的 `.vt` 自动做此检查）

---

## 另见

- [Vortex 主仓库](https://www.github.com/i8doney/vortex)
- [Vortex 语言参考](https://www.github.com/i8doney/vortex/blob/main/README.md#language-reference)
- [Vortex 使用指南](https://www.github.com/i8doney/vortex/blob/main/USAGE.md)
- [vortex-libs 库仓库](https://www.github.com/i8doney/vortex-libs)