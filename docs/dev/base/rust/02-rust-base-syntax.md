[返回](/docs/dev/base/index.md)

[toc]

对于目前的高级编程语言，基本都绕不开 `变量`、`数据类型`、`函数`和`控制流` 这些基本的语法概念。`Rust`中也是基本跟正常别的语言在这些上无太大差异。

## 1 变量与可变性

Rust 语言为了高并发安全而做的设计：在语言层面尽量少的让变量的值可以改变。所以 a 的值不可变。但这不意味着 a 不是"变量"（英文中的 variable），官方文档称 a 这种变量为"不可变变量"。

如果我们编写的程序的一部分在假设值永远不会改变的情况下运行，而我们代码的另一部分在改变该值，那么代码的第一部分可能就不会按照设计的意图去运转。由于这种原因造成的错误很难在事后找到。这是 Rust 语言设计这种机制的原因。

### 变量定义

Rust 中**默认定义**的变量**具有不可变性**，如下示例：

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

编译程序时会出错：

```shell
error[E0384]: cannot assign twice to immutable variable `x`
```

错误信息指出错误的原因是 `不能对不可变变量 x 二次赋值`（`cannot assign twice to immutable variable `x` `），因为对不可变变量 `x` 赋第二个值。

不过可变性也是非常有用的，可以用来更方便地编写代码。尽管变量默认是不可变的，你仍然可以在**变量名前添加 `mut` 来使其可变**

使用 let mut 声明“纯正”变量。

<u>**Rust 是强类型语言，但具有自动判断变量类型的能力。**</u>

```rust
let a = 123;

a = "abc";   // error[E0308]: mismatched types  ^^^^^ expected integer, found `&str`
a = 4.56 ;   // error[E0308]: mismatched types  ^^^^ expected integer, found floating-point number
a = 456;     // error[E0384]: cannot assign twice to immutable variable `a`

// 针对上面 a = 456 编译错误更清晰地说明 let 声明的是不可变变量
2 |     let a = 123;
  |         -
  |         |
  |         first assignment to `a`
  |         help: consider making this binding mutable: `mut a`
3 |     
4 |     a = 456;
  |     ^^^^^^^ cannot assign twice to immutable variable
```

### 常量

常量只能被设置为常量表达式，而不可以是其他任何只能在运行时计算出的值。

### [Shadowing](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html#shadowing)

定义一个与之前变量同名（数据类型也必须相同）的新变量，称之为第一个变量被第二个 **隐藏（Shadowing）** 了。

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");
}
```

输出如下：

```shell
 cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6
```

## 2. 数据类型

Rust 是 **静态类型**（*statically typed*）语言，也就是说在编译时就必须知道所有变量的类型。根据值及其使用方式，编译器通常可以推断出我们想要用的类型。当多种类型均有可能时，须添加类型注解来指定数据类型。

两类数据类型子集：标量（scalar）和复合（compound）。

### 2.1 标量类型

### 2.2 复合类型

***复合类型***（**Compound types**）可以将多个值组合成一个类型。Rust 有两个原生的复合类型：元组（tuple）和数组（array）。

元组与数组都是声明后长度固定，所以称其为 ***\*固长数组\****。vector 类型是标准库提供的一个 ***\*允许\**** 增长和缩小长度的类似数组的集合类型。

#### 元组 tuple - 多类型固长数组

```rust
fn main() {
	let tup = (500, 6.4, 1);
	// 使用模式匹配（pattern matching）来解构（destructure）元组值
    // 使用了 let 和一个模式将 tup 分成了三个不同的变量，x、y 和 z。这叫做 解构（destructuring），因为它将一个元组拆成了三个部分。
    let (x, y, z) = tup;

    println!("The value of y is: {}", y);	
    
    // -----
    
    let x: (i32, f64, u8) = (500, 6.4, 1);
	// 使用点号（.）后跟值的索引来直接访问它们，第一个索引为 0 
    let five_hundred = x.0;
    let six_point_four = x.1;
    let one = x.2;
}
```

#### 数组 array - 同类型固长数组

```rust
// 在方括号中包含每个元素的类型，后跟分号，再后跟数组元素的数量。
let a: [i32; 5] = [1, 2, 3, 4, 5];

// 通过索引访问
let first = a[0];
let second = a[1];

// 等价于
let b = [3; 5];
```

数组越界访问，会在 ***运行时 (runtime) 错误*** 并且不会成功退出。

## 3. 函数

```rust
fn <函数名> ( <参数> ) {

	<函数体>
	
}
```

其中 Rust 函数名称的命名风格是小写字母以下划线分割：

在函数签名中，**必须**声明每个参数的类型。这是 Rust 设计中一个经过慎重考虑的决定：要求在函数定义中提供类型注解，意味着编译器不需要你在代码的其他地方注明类型来指出你的意图。

### 语句 和表达式 以及函数体

因为 Rust 是一门基于表达式（expression-based）的语言，***语句***（**Statements**）是执行一些操作但不返回值的指令。表达式（**Expressions**）计算并产生一个值。

```rust
fn main() {
    // 这是一个语句
    let m = 6;
	
    // 由于上述定义变量是语句，无返回值，所以会在编译期报错： note: variable declaration using `let` is a statement
    // 这与其他语言不同，例如 C 和 Ruby，它们的赋值语句会返回所赋的值
    let a = (let b = 6);
    
    let y = {
        let x = 3;
        x + 1			// 表达式没有分号，有点比习惯。。。如果在表达式的结尾加上分号，它就变成了语句，而语句不会返回值
    };
}
```

### 返回值

```rust
fn five() -> i32 {
    5
}

fn plus_one(x: i32) -> i32 {
    x + 1		// 如果在这行末加分号，则变成语句，没有返回值，则报编译错误：
    			// implicitly returns `()` as its body has no tail or `return` expression
}

fn addsub(x: isize, y: isize) -> (isize, isize) {
    (x + y, x - y) // use tuple to simulate it
}

let (a, b) = addsub(1, 2); // 通过元组来接收函数的多返回值
```

## 4. 控制流

> 根据条件是否为真来决定是否执行某些代码，以及根据条件是否为真来重复运行一段代码是大部分编程语言的基本组成部分。

Rust 中 ***强烈限定*** 条件**表达式处的数据类型必须为 bool**, 否则会报 ^^^^^^ expected bool 的编译错误

### if else

Rust 只会执行第一个条件为真的代码块，并且一旦它找到一个以后，甚至都不会检查剩下的条件。

使用过多的 `else if` 表达式会使代码显得杂乱无章，所以如果有多于一个 `else if` 表达式，最好重构代码。可使用强大的 Rust 分支结构（branching construct），叫做 `match`。

```rust
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
    
    // 在 let 语句中使用 if 处理返回值
    // if 分支和 else 分支的返回类型不匹配，则会出现一个错误：
    let number = if condition {
        5
    } else {
        6
    };
}
```

### loop / while / for

```rust
fn main() {
    let mut counter = 0;

    // 1. -------- loop 
    let result = loop {
        counter += 1;
        if counter == 10 {
            // 使用 break 关键字来告诉程序何时停止循环
            break counter * 2;
        }
    };
    
    // 2. -------- while
    // while 结构消除了很多使用 loop、if、else 和 break 时所必须的嵌套，这样更加清晰。当条件为真就执行，否则退出循环
    let mut number = 3;
	while number != 0 {
        println!("{}!", number);

        number = number - 1;
    }
    
    // 3. -------- for ---- 推荐使用，更简约
    let a = [10, 20, 30, 40, 50];

    for element in a.iter() {
        println!("the value is: {}", element);
    }
    
    // 后续
    for number in (1..4).rev() {
        println!("{}!", number);
    }
}
```
