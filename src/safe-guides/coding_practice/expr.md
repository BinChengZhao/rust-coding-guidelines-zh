# 表达式

Rust 中几乎一切皆表达式。

---

## P.EXP.01    

【描述】



---


## G.EXP.01 当需要对表达式求值之后重新赋值时尽量使用复合赋值模式

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [assign_op_pattern](https://rust-lang.github.io/rust-clippy/master/#assign_op_pattern) | yes| no | style | warn |

### 【描述】

【正例】

```rust
let mut a = 5;
let b = 0;
a += b;
```

【反例】

```rust
let mut a = 5;
let b = 0;
a = a + b;
```


## G.EXP.02  避免在比较中使用不兼容的位掩码

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [bad_bit_mask](https://rust-lang.github.io/rust-clippy/master/#bad_bit_mask) | yes| no | correctness | deny |

### 【描述】

可以对照下面表格进行检查。

|Comparison	| Bit Op |Example	| is always	| Formula |
| ------ | ---- | --------- | ------ | ------ | 
|== or != |	& | x & 2 == 3 | false | c & m != c|
|< or >= |	& | x & 2 < 3 | true | m < c|
|> or <= |	& | x & 1 > 1 | false | m <= c|
|== or != |	| | x | 1 == 0 | false | c | m != c|
|< or >= |	| | x | 1 < 1 | false | m >= c|
|<= or > |	| | x | 1 > 0 | true | m > c|

## G.EXP.03   不要使用子表达式调用

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [unnecessary_operation](https://rust-lang.github.io/rust-clippy/master/#unnecessary_operation) | yes           | no           | **complexity** | warn  |

### 【描述】

这样会影响代码可读性。

【正例】

```rust
let arr = compute_array();
let first = arr[0];
```

【反例】

```rust
let first = compute_array()[0];
```

## G.EXP.04    不要使用无效表达式语句

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [no_effect](https://rust-lang.github.io/rust-clippy/master/#no_effect) | yes           | no           | **complexity** | warn  |

### 【描述】

无效的表达式语句，虽然会执行，但实际并没有起到什么效果。

也有例外情况存在。

【正例】

```rust
let a = 41;
let a = a+1;
```

【反例】

```rust
a+1;
```

【例外】

像在下面代码中，为了确保常量函数 `new` 可以在输入参数超出 MAX 限制的情况下 panic，使用了一个数组的技巧：` ["tag number out of range"][(byte > Self::MAX) as usize];` 。因为目前 在常量上下文中还无法直接使用 `panic!`，等 `const_panic` 功能稳定就可以了。

如果不加 `#[allow(clippy::no_effect)]` ，Clippy 会有警告。 

```rust
// From: https://docs.rs/crate/der/0.4.1/source/src/tag/number.rs

#[derive(Copy, Clone, Debug, Eq, PartialEq, PartialOrd, Ord)]
pub struct TagNumber(pub(super) u8);

impl TagNumber {
    /// Maximum tag number supported (inclusive).
    pub const MAX: u8 = 30;

    /// Create a new tag number (const-friendly).
    ///
    /// Panics if the tag number is greater than [`TagNumber::MAX`]. For a fallible
    /// conversion, use [`TryFrom`] instead.
    #[allow(clippy::no_effect)]
    pub const fn new(byte: u8) -> Self {
        // TODO(tarcieri): hax! use const panic when available
        ["tag number out of range"][(byte > Self::MAX) as usize];
        Self(byte)
    }
	// ...
    
}

```

## G.EXP.05    使用 `+=`/`-=` 等操作来代替 `++i` / `--i` 

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [double_neg](https://rust-lang.github.io/rust-clippy/master/#double_neg) | yes           | no           | style      | warn |

### 【描述】

`++i` 这种操作编译会失败，但是 `--i` 编译不会出错。有些C/Cpp等其他语言新手容易犯此错误。

【正例】

```rust
let mut x = 3;
x -= 1;
```



【反例】

```rust
let mut x = 3;
--x;
```



## G.EXP.06    表达式操作最好使用括号来表达清楚优先级顺序  

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [precedence](https://rust-lang.github.io/rust-clippy/master/#precedence) | yes           | no           | complexity | warn  |

### 【描述】

并不是每个人都能记得住优先级，所以最好使用括号把优先级顺序区分出来，增加可读性。

【正例】

```rust
(1 << 2) + 3
(-1i32).abs()
```

【反例】

```rust
1 << 2 + 3
-1i32.abs()
```





