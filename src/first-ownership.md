# 所有权初探

> 原文链接：[first-ownership.md](https://github.com/rust-unofficial/too-many-lists/blob/master/src/first-ownership.md) <br>
> 翻译基准：[commit b57202a](https://github.com/rust-unofficial/too-many-lists/blob/b57202a5e01b50e4217b85af3d89f49f612dcbae/src/first-ownership.md)

现在可以开始构造我们的链表了。我们将使用普通的（非静态的）方法来与它打交道。在 Rust 中，方法就是一类特殊的函数。它们拥有无须指明类型的 `self` 参数。

```rust, ignore
fn foo(self, arg2: Type2) -> ReturnType {
    // 函数体
}
```

`self` 参数主要有三种形式：`self`、`&mut self`、`&self`。它们分别代表了 Rust 中主要的三种所有权：

* `self` ——值
* `&mut self` ——可变引用
* `&self` ——共享引用
