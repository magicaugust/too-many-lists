# 内存布局

> 原文链接：[first.md](https://github.com/rust-unofficial/too-many-lists/blob/master/src/first-layout.md)
> <br>
> 翻译基准：[commit 0f4c26b](https://github.com/rust-unofficial/too-many-lists/blob/0f4c26bfe58f81d3667eb214398a3cf99f66a7f2/src/first-layout.md)

那么，什么是链表呢？说的简单一点，就是堆上的一系列数据，按顺序指向别的数据形成
的结构。对使用过程式语言的程序员来说，链表是个大坑。不过函数式的拥趸却觉得用它
可以构造一切。你要是问他们链表是什么，他们可能会给你如下的定义：

```haskell
List a = Empty | Elem a (List a)
```

意思是，一个链表要么是空表，要么是一个元素跟着一个链表。这是一种递归定义的
**和类型**（*sum type*），即「可以具有不同类型的值的类型」。在 Rust 中，和类型
被称作枚举类型 `enum`。如果你曾是 C-like 语言的程序员，应该会很熟悉这个名字。下
面我们用 Rust 写出这个函数式版的定义：

文件名：src/first.rs

```rust, ignore
// pub 能让别人可以在模块外调用 List 类型
pub enum List {
    Empty,
    Elem(i32, List),
}
```

写完了！编译一下吧。

```text
$ cargo build
   Compiling lists v0.1.0
error[E0072]: recursive type `first::List` has infinite size
 --> src/first.rs:2:1
  |
2 | pub enum List {
  | ^^^^^^^^^^^^^ recursive type has infinite size
3 |     Empty,
4 |     Elem(i32, List),
  |               ---- recursive without indirection
  |
  = help: insert indirection (e.g., a `Box`, `Rc`, or `&`) at some point to make
 `first::List` representable
```

呃，我不知道你怎么想，反正我觉得我被函数式社区给坑了。

如果仔细读一下出错信息，其实可以发现 rustc 编译器已经告诉了我们解决之道。

> 提示：在某处添加间接修饰符，比如：`Box`，`Rc`，`&`，可以让类型 `first::List` 成立。

好吧，不过 `Box` 是什么东西？搜一下吧：

> [std::boxed::Box - Rust][box-doc]

摘录如下：

> 【已翻译】
>
> Struct std::boxed::Box
>
> ```rust, ignore
> pub struct Box<T>(_)
>  where
>     T: ?Sized;
> ```
>
> 一种用于堆分配内存的指针类型。
>
> 详细内容请参考[相关模块文档][box-doc-more]。

点击链接：

> 【已翻译】
>
> Module std::boxed
>
> 一种用于堆分配内存的指针类型。
>
> `Box<T>`，简称 box，提供了 Rust 中最简单的堆分配方式。在分配时获得所有权，并在
> 离开作用域时释放内存。
>
> 例子
>
> 通过装箱，把数据从栈中转移到堆中：
>
> ```rust, ignore
> let val: u8 = 5;
> let boxed: Box<u8> = Box::new(val);
> ```
>
> 通过[解引用][deref-doc]，把数据从堆中转移到栈中：
>
> ```rust, ignore
> let boxed: Box<u8> = Box::new(5);
> let val: u8 = *boxed;
> ```
>
> 构造递归定义的数据结构：
>
> ```rust
> #[derive(Debug)]
> enum List<T> {
>     Cons(T, Box<List<T>>),
>     Nil,
> }
>
> fn main() {
>     let list: List<i32> =
>         List::Cons(1, Box::new(List::Cons(2, Box::new(List::Nil))));
>     println!("{:?}", list);
> }
> ```
>
> 程序会输出 `Cons(1, Cons(2, Nil))`。
>
> 递归类型必须被装箱，不然如果像下面这样定义 `Cons`：
>
> ```rust, ignore
> # enum List<T> {
>     Cons(T, List<T>),
> #     Nil,
> # }
> ```
>
> 就会出错。因为 `List` 类型的大小依赖于内部元素的大小，但我们无法得知要给
> `Cons` 分配多大内存。通过引入确定大小的 `Box<T>`，解决了 `Cons` 大小不定的问
> 题。

哇！这可能是我见过最有帮助的文档了。开篇第一个内容就恰好是我们正尝试在写的程序，包括它可能错误的原因以及修正错误的办法。

那我们就照办吧：

```rust, ignore
pub enum List {
    Empty,
    Elem(i32, Box<List>),
}
```

```text
$ cargo build
   Compiling lists v0.1.0
    Finished dev [unoptimized + debuginfo] target(s) in 0.45s
```

成功编译过了！

[box-doc]: https://doc.rust-lang.org/std/boxed/struct.Box.html
[box-doc-more]: https://doc.rust-lang.org/std/boxed/
[deref-doc]: https://doc.rust-lang.org/std/ops/trait.Deref.html
