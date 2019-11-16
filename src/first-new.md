# New 方法

> 原文链接：[first-new.md](https://github.com/rust-unofficial/too-many-lists/blob/master/src/first-new.md) <br>
> 翻译基准：[commit b57202a](https://github.com/rust-unofficial/too-many-lists/blob/b57202a5e01b50e4217b85af3d89f49f612dcbae/src/first-new.md)

为了将代码与类型关联起来，我们需要用 `impl` 块。

```rust, ignore
impl List {
    // TODO: 实现相关功能的代码
}
```

现在我们得要知道如何写代码，Rust 用如下格式定义函数：

```rust, ignore
fn func_name(arg1: Type1, arg2: Type2) -> ReturnType {
    // 函数体
}
```

首先要考虑如何**创建**链表。既然要隐藏内部的实现细节，就要对外提供函数接口。在 Rust 中，常常用静态方法（static method）完成这个任务。其实就是 `impl` 块中的一个普通函数：

```rust, ignore
impl List {
    pub fn new() -> Self {
        List { head: Link::Empty }
    }
}
```

有几个要点：

* `Self` 指代 `impl` 后面跟的那个类型。这样就避免重复了。

* 我们构造结构体实例的方式几乎与声明它的方式一样。唯一的不同点在于跟在字段后边的不是类型，而是初始化它们所用的值。

* 我们用作用域符 `::` 来表示枚举类型的某个变体。

* 函数中的最后一个表达式会隐式地被返回。这样做可以使代码更整洁。同时与其他 C-like 语言一样，你仍然可以用 `return` 语句来提早返回。
