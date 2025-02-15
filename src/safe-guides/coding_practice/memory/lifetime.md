# 生存期

生存期（lifetime），也被叫做 生命周期。

---

## P.MEM.Lifetime.01 生命周期参数命名尽量简单

**【描述】**

生命周期参数的命名应该尽量简单，用 `'a/ 'b/ 'c` 即可，不要将其命名为带语义的名称。

因为生命周期参数的目的是给编译器使用，用于防止函数中出现悬垂引用。

生命周期参数越简单，越没有语义，就越不会影响到其他业务代码，作为开发者可以做到无视这些生命周期参数，而只有在需要的时候才检查它们。

如果加上语义的话，就会影响代码可读性。

【正例】

```rust
fn the_longest<'a>(s1: &'a str, s2: &'a str) -> &'a str {
    if s1.len() > s2.len() { s1 } else { s2 }
}
```

【反例】

```rust
fn the_longest<'strlife>(s1: &'strlife str, s2: &'strlife str) -> &'strlife str {
    if s1.len() > s2.len() { s1 } else { s2 }
}
```

## P.MEM.Lifetime.02  在需要的时候，最好显式地标注生命周期，而非利用编译器推断

**【描述】**

编译器可以推断你的代码做了什么，但它不知道你的意图是什么。

编译器对生命周期参数有两种单态化方式（生命周期参数也是一种泛型）：

- Early bound。一般情况下，`'a: 'b` 以及 `impl<'a'>`  这种方式是 early bound，意味着这些生命周期参数会在当前作用域单态化生命周期实例。
- Late bound。默认的 `'a`   或 `for<'a>` 是在实际调用它们的地方才单态化生命周期实例。

在不同的场景下，需要指定合适的单态化方式，才能让编译器明白你的意图。

在使用匿名生命周期 `'_` 的时候需要注意，如果有多个匿名生命周期，比如 `('_ ，'_)` ，每个匿名生命周期都会有自己的单独实例。

