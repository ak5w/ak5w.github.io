[返回](/docs/dev/base/index.md)

对于任何编程语言在实例中学习效果都要比空洞的直接学习概念好。正所谓“talk is cheap, show me code.”是也。

本篇，以猜数字游戏为例，对 `rust` 语言中涉及到的概念进行讲解，尽可能做到深入浅出。

## 1. 猜数字游戏的逻辑

 1. 程序将会随机生成一个 1 到 100 之间的随机整数。
 2. 接着请玩家猜一个数并输入。
 3. 提示猜测是大了还是小了。如果猜对了，它会打印祝贺信息并退出。

## 2. 创建新项目
主要通过
```shell
cargo new guessing_game
cargo run
```
来创建新的项目，运行结果如下：

```shell
➜  tmp git:(master) ✗ cargo new guessing_game  
     Created binary (application) `guessing_game` package
➜  tmp git:(master) ✗ ls
guessing_game
➜  tmp git:(master) ✗ cd guessing_game       
➜  guessing_game git:(master) ✗ cargo run              
   Compiling guessing_game v0.1.0 (/home/ak3/wspaces/dev/tmp/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30s
     Running `target/debug/guessing_game`
Hello, world!
```

## 3. 猜数字游戏实现
拆分为以下三个步骤来实现，具体概念穿插讲解，在最后再进行总结。
### 3.1 获取用户输入并打印

#### a. 标准库引入
获取用户输入的功能，需要**引入标准库内容**来完成，如下：
```rust
use std::io;
```
在 [标准读写库文档](https://doc.rust-lang.org/std/io/index.html) 就能看到从控制台读写的案例
代码如下：
```rust
use std::io;

fn main() -> io::Result<()> {
    let mut input = String::new();

    io::stdin().read_line(&mut input)?;

    println!("You typed: {}", input.trim());
    Ok(())
}
```
执行如下：
```shell
➜  guessing_game git:(master) ✗ cargo run
   Compiling guessing_game v0.1.0 (/home/ak3/wspaces/dev/tmp/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 0.22s
     Running `target/debug/guessing_game`
11
You typed: 11
```
#### b. println! 宏
 println! 是一个在屏幕上打印字符串的**宏**

#### c. 可变与不可变 变量
 上面的`input` 变量使用了可变变量的定义。`Rust`中默认定义的变量具有**不可变性**，可变变量需要显示用 `let mut`来定义。

#### d. string::new 与io::stdin().read_line(&mut input)
### 3.2 生成指定范围内的随机数
> Rust 标准库中尚未包含随机数功能。然而，Rust 团队还是提供了一个包含上述功能的 rand crate。

在 [crates.io](https://crates.io/) 搜索rand 得到 rand crate的相关信息,然后在执行如下命令添加 rand crate
```shell
cargo add rand
```
对比执行上述命令前后差异，是在 Cargo.toml中增加了一行
```rust
rand = "0.8.5"
```
在 [rand](https://crates.io/crates/rand)右侧进入其[rand documention](https://docs.rs/rand/0.8.5/rand/)

在原来代码基础上新增 如下代码
```rust
use rand::Rng;

let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("The secret number is: {secret_number}");
```
执行 `cargo run` 显示如下：
```shell
  Downloaded libc v0.2.148
  Downloaded getrandom v0.2.10
  Downloaded 3 crates (748.2 KB) in 29.36s
   Compiling libc v0.2.148
   Compiling cfg-if v1.0.0
   Compiling ppv-lite86 v0.2.17
   Compiling getrandom v0.2.10
   Compiling rand_core v0.6.4
   Compiling rand_chacha v0.3.1
   Compiling rand v0.8.5
   Compiling guessing_game v0.1.0 (/home/ak3/wspaces/dev/tmp/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 30.82s
     Running `target/debug/guessing_game`
The secret number is: 66
12
You typed: 12
```
在执行cargo run的过程中，会下载 rand 相关的 crate lib.



### 3.3 随机数与猜测数的比较
这部分的主要是循环读取用户输入，与随机数比较给出输出直至猜中结束游戏。

#### a. 字符串转数字
由于从控制台输入的数据最先取得的都是字符串，需要将字符串转换为相应类型，才能进行对应的操作。


```rust
// 此处guess 覆盖了上面的 guess ，Rust 允许同名覆盖变量,隐藏 （Shadowing） guess 之前的值
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```

parse 方法返回一个 **Result** 类型。像之前 “使用 Result 类型来处理潜在的错误” 讨论的 read_line 方法那样，再次按部就班的用 expect 方法处理即可。如果 parse 不能从字符串生成一个数字，返回一个 Result 的 Err 成员时，expect 会使游戏崩溃并打印附带的信息。

#### b. 数字比较大小

```rust
use std::cmp::Ordering;
```

增加 use 声明，从标准库引入了一个叫做 std::cmp::Ordering 的类型到作用域中。 Ordering 也是一个枚举，不过它的成员是 Less、Greater 和 Equal。这是比较两个值时可能出现的三种结果。

```rust
 match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }

```
使用一个 match 表达式，根据对 guess 和 secret_number 调用 cmp 返回的 Ordering 成员来决定接下来做什么。

一个 match 表达式由 分支（arms） 构成。一个分支包含一个 模式（pattern）和表达式开头的值与分支模式相匹配时应该执行的代码。Rust 获取提供给 match 的值并挨个检查每个分支的模式。

#### c. 循环处理多次猜想
直接通过 `loop` 关键字进行循环读入比较，正确则推出（`break` 关键字退出循环)。
代码如下：
```rust
use std::io;
use rand::Rng;
use std::cmp::Ordering;

fn main(){
    

    let secret_number = rand::thread_rng().gen_range(1..=100);

    // println!("The secret number is: {secret_number}");

    loop {
        let mut guess = String::new();
        println!("Please input your guess.");
        io::stdin().read_line(&mut guess);
        
        // 此处guess 覆盖了上面的 guess ，Rust 允许同名覆盖变量,隐藏 （Shadowing） guess 之前的值
        let guess: u32 = guess.trim().parse().expect("Please type a number!");

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;  // 跳出循环，结束游戏。
            }
        }
    }

}
```

至此，我们的猜数字游戏完成了。但也存在一些对输入数据异常的 case 处理。
在后续学习中逐步完善。

## 4. 总结

虽然猜数字游戏较小，但从中能看到一些 `rust` 的特性：
1. 使用 `use` 关键字引入库。
2. println! 是宏。
3. match 表达式。
4. Result 枚举类型。

更多详情，且看后续笔记。

本文参考 [Rust 程序设计语言：写个猜数字游戏](https://kaisery.github.io/trpl-zh-cn/ch02-00-guessing-game-tutorial.html)

