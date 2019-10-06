# 创建方法

> 原文链接：[first-new.md](https://github.com/rust-unofficial/too-many-lists/blob/master/src/first-new.md)
> <br>
> 翻译基准：[commit b57202a](https://github.com/rust-unofficial/too-many-lists/blob/b57202a5e01b50e4217b85af3d89f49f612dcbae/src/first-new.md)

为了将代码与类型关联起来，我们需要用 `impl` 块。

```rust, ignore
impl List {
    // TODO: 使功能奏效的代码
}
```

现在我们要搞明白如何真正地写一行代码，Rust 用如下地格式定义函数：

```rust, ignore
fn func_name(arg1: Type1, arg2: Type2) -> ReturnType {
    // 函数体
}
```
