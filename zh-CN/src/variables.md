# 变量绑定与解构

### 绑定和可变性
🌟 修复下面代码的错误并尽可能少的修改

```rust,editable

fn main() {
    let x: i32; // 未初始化，但被使用
    let y: i32; // 未初始化，也未被使用
    println!("{} is equal to 5", x); 
}
```

🌟🌟 完形填空，让代码编译
```rust,editable

fn main() {
    // 使用合适的变量名替代 __ 
    let __ =  1;
    __ += 2; 
    
    println!("{} = 3", x); 
}
```

### 变量作用域
🌟 修复下面代码的错误并使用尽可能少的改变
```rust,editable

fn main() {
    let x: i32 = 10;
    {
        let y: i32 = 5;
        println!("x 的值是 {}, y 的值是 {}", x, y);
    }
    println!("x 的值是 {}, y 的值是 {}", x, y); 
}
```

🌟🌟 使用你所掌握的知识来修复错误
```rust,editable

fn main() {
    println!("{}, world", x); 
}

fn define_x() {
    let x = "hello";
}
```

### 变量遮蔽( Shadowing )
🌟🌟 只允许修改 `assert_eq!` 来让 `println!` 工作(在终端输出 `42`)

```rust,editable

fn main() {
    let x: i32 = 5;
    {
        let x = 12;
        assert_eq!(x, 5);
    }

    assert_eq!(x, 12);

    let x =  42;
    println!("{}", x); // 输出 "42".
}
```

🌟🌟 删除一行代码以通过编译
```rust,editable

fn main() {
    let mut x: i32 = 1;
    x = 7;
    // 遮蔽且再次绑定
    let x = x; 
    x += 3;


    let y = 4;
    // 遮蔽
    let y = "I can also be bound to text!"; 
}
```

### 未使用的变量
使用以下方法来修复编译器输出的 warning :

- 🌟  一种方法
- 🌟🌟  两种方法

> 注意: 你可以使用两种方法解决，但是它们没有一种是移除 `let x = 1` 所在的代码行

```rust,editable

fn main() {
    let x = 1; 
}

// compiler warning: unused variable: `x`
```

### 变量解构
🌟🌟 修复下面代码的错误并尽可能少的修改

> 提示: 可以使用变量遮蔽或可变性

```rust,editable

fn main() {
    let (x, y) = (1, 2);
    x += 2;

    assert_eq!(x, 3);
    assert_eq!(y, 2);
}
```

### 解构式赋值
🌟🌟 使用两种方式解决问题:

- 通过变量遮蔽： 添加 `let` 
- 令类型兼容

> Note: 解构式赋值只能在 Rust 1.59 或者更高版本中使用

```rust,editable

fn main() {
    let (mut x, mut y) = (1.0, 2.0);
    (x,y) = (3, 4);

    assert_eq!([x,y],[3, 4]);
} 
```