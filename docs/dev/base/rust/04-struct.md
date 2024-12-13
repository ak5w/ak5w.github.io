[返回](/docs/dev/base/index.md)

[toc]

### 结构体

#### 定义和使用

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    let mut user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };
}
// 此时，更改结构体实例的属性值，则要求结构体实例定义时是 可变的。
// 否则，有 error[E0594]: cannot assign to `user1.email`, as `user1` is not declared as mutable 的错误提示。
user1.email = String::from("anotheremail@example.com");
```

**字段初始化简写语法**

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

因为 `email` 字段与 `email` 参数有着相同的名称，则只需编写 `email` 而不是 `email: email`。

**使用结构体更新语法从其他实例创建实例**

```rust
fn main() {
    // --snip--

    let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };
}
```

使用结构体更新语法，我们可以通过更少的代码来达到相同的效果，`..` 语法指定了剩余未显式设置值的字段应有与给定实例对应字段相同的值。

```rust
fn main() {
    // --snip--

    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```

请注意，结构更新语法就像带有 `=` 的赋值，因为它**移动了数据**。我们在创建 `user2` 后**不能再使用 `user1`**，因为 `user1` 的 `username` 字段中的 `String` 被<b> 移到 </b>`user2` 中。

#### 元组结构体

**元组结构体**（*tuple structs*）。元组结构体有着结构体名称提供的含义，但**没有具体的字段名，只有字段的类型**。

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}

```
注意 black 和 origin 值的类型不同，因为它们是不同的元组结构体的实例。你定义的每一个结构体有其自己的类型，即使结构体中的字段可能有着相同的类型。

#### **类单元结构体**（*unit-like structs*）
类单元结构体常常在你想要在某个类型上实现 trait 但不需要在类型中存储数据的时候发挥作用

```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}

```

#### 结构体使用案例
- 案例： 计算长方形面积。

如下：
```rust
fn main() {
    let width = 30;
    let height = 50;

    println!(
        "The area of the rectangle is {} square pixels.",
        area(width, height)
    );
}

fn area(width: u32, height: u32) -> u32 {
    width * height
}
```
输出：
```shell
The area of the rectangle is 1500 square pixels.

```
##### 1. 使用原组重构

由于长宽的数据类型一致，所以使用元组进行参数简化。

```rust
fn main() {
    let width = 30;
    let height = 50;

    let rect = (30, 50);

    println!(
        "The area of the rectangle is {} square pixels.",
        area(rect)
    );
}

fn area(input: (u32, u32)) -> u32 {
    input.0 * input.1
}
```

###### 2. 使用结构体进行重构

```rust

#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}



fn main() {

    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };
    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
    println!("rect1 ares is: {:?}", area(&rect1));
    println!("rect1 ares is: {:?}", rect1.area();
}

fn area(rect: &Rectangle) -> u32 {
    rect.width * rect.height
}
```
###### 3. 结构体方法

```rust

#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, that &Rectangle) -> bool {
        self.width > that.width && self.height * that.height
    }
}


fn main() {

    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };
    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
    println!("rect1 ares is: {:?}", area(&rect1));

    println!("rect1 ares is: {}", rect1.area());

    let rect2 = Rectangle {
        width: 10,
        height: 40,
    };
    let rect3 = Rectangle {
        width: 60,
        height: 45,
    };
    
     dbg!(&rect2);                                                                                                                    
}

fn area(rect: &Rectangle) -> u32 {
    rect.width * rect.height
}
```