# String
`std::string::String` 是 UTF-8 编码、可增长的动态字符串. 它也是我们日常开发中最常用的字符串类型，同时对于它所拥有的内容拥有所有权。

### 基本操作
1. 🌟🌟
```rust,editable

// 填空并修复错误
// 1. 不要使用 `to_string()`
// 2. 不要添加/删除任何代码行
fn main() {
    let mut s: String = "hello, ";
    s.push_str("world".to_string());
    s.push(__);

    move_ownership(s);

    assert_eq!(s, "hello, world!");

    println!("Success!")
}

fn move_ownership(s: String) {
    println!("ownership of \"{}\" is moved here!", s)
}
```

### String and &str
虽然 `String` 的底层是 `Vec<u8>` 也就是字节数组的形式存储的，但是它是基于 UTF-8 编码的字符序列。`String` 分配在堆上、可增长且不是以 `null` 结尾。

而 `&str` 是[切片引用](https://course.rs/confonding/slice.html)类型( `&[u8]` )，指向一个合法的 UTF-8 字符序列，总之，`&str` 和 `String` 的关系类似于 `&[T]` 和 `Vec<T>` 。

如果大家想了解更多，可以看看[易混淆概念解析 - &str 和 String](https://course.rs/confonding/string.html)。


2. 🌟🌟
```rust,editable
// 填空
fn main() {  
   let mut s = String::from("hello, world");

   let slice1: &str = __; // 使用两种方法
   assert_eq!(slice1, "hello, world");

   let slice2 = __;
   assert_eq!(slice2, "hello");

   let slice3: __ = __; 
   slice3.push('!');
   assert_eq!(slice3, "hello, world!");

   println!("Success!")
}
```

3. 🌟🌟
```rust,editable

// 问题:  我们的代码中发生了多少次堆内存分配？
// 你的回答: 
fn main() {  
    // 基于 `&str` 类型创建一个 String,
    // 字符串字面量的类型是 `&str`
   let s: String = String::from("hello, world!");

   // 创建一个切片引用指向 String `s`
   let slice: &str = &s;

   // 基于刚创建的切片来创建一个 String
   let s: String = slice.to_string();

   assert_eq!(s, "hello, world!");

   println!("Success!")
}
```

### UTF-8 & 索引
由于 String 都是 UTF-8 编码的，这会带来几个影响:

- 如果你需要的是非 UTF-8 字符串，可以考虑 [OsString](https://doc.rust-lang.org/stable/std/ffi/struct.OsString.html) 
- 无法通过索引的方式访问一个 String

具体请看[字符串索引](https://course.rs/basic/compound-type/string-slice.html#字符串索引)。

4. 🌟🌟🌟 我们无法通过索引的方式访问字符串中的某个字符，但是可以通过切片的方式来获取字符串的某一部分 `&s1[start..end]`

```rust,editable

// 填空并修复错误
fn main() {
    let s = String::from("hello, 世界");
    let slice1 = s[0]; //提示: `h` 在 UTF-8 编码中只占用 1 个字节
    assert_eq!(slice1, "h");

    let slice2 = &s[3..5];// 提示: `中` 在 UTF-8 编码中占用 3 个字节
    assert_eq!(slice2, "世");
    
    // 迭代 s 中的所有字符
    for (i, c) in s.__ {
        if i == 7 {
            assert_eq!(c, '世')
        }
    }

    println!("Success!")
}
```


#### utf8_slice
我们可以使用 [utf8_slice](https://docs.rs/utf8_slice/1.0.0/utf8_slice/fn.slice.html) 来按照字符的自然索引方式对 UTF-8 字符串进行切片访问，与之前的切片方式相比，它索引的是字符，而之前的方式索引的是字节.

**示例**
```rust
use utf_slice;
fn main() {
   let s = "The 🚀 goes to the 🌑!";

   let rocket = utf8_slice::slice(s, 4, 5);
   // Will equal "🚀"
}
```


5. 🌟🌟🌟
> 提示: 也许你需要使用 `from_utf8` 方法

```rust,editable

// 填空
fn main() {
    let mut s = String::new();
    __;

    let v = vec![104, 101, 108, 108, 111];

    // 将字节数组转换成 String
    let s1 = __;
    
    
    assert_eq!(s, s1);

    println!("Success!")
}
```

### Representation
A String is made up of three components: a pointer to some bytes, a length, and a capacity. 

The pointer points to an internal buffer String uses to store its data. The length is the number of bytes currently stored in the buffer( always stored on the heap ), and the capacity is the size of the buffer in bytes. As such, the length will always be less than or equal to the capacity.

6. 🌟🌟 If a String has enough capacity, adding elements to it will not re-allocate
```rust,editable

// modify the code below to print out: 
// 25
// 25
// 25
// Here, there’s no need to allocate more memory inside the loop.
fn main() {
    let mut s = String::new();

    println!("{}", s.capacity());

    for _ in 0..2 {
        s.push_str("hello");
        println!("{}", s.capacity());
    }

    println!("Success!")
}
```

7. 🌟🌟🌟
```rust,editable

// FILL in the blanks
use std::mem;

fn main() {
    let story = String::from("Rust By Practice");

    // Prevent automatically dropping the String's data
    let mut story = mem::ManuallyDrop::new(story);

    let ptr = story.__();
    let len = story.__();
    let capacity = story.__();

    // story has nineteen bytes
    assert_eq!(16, len);

    // We can re-build a String out of ptr, len, and capacity. This is all
    // unsafe because we are responsible for making sure the components are
    // valid:
    let s = unsafe { String::from_raw_parts(ptr, len, capacity) };

    assert_eq!(*story, s);

    println!("Success!")
}
```


### Common methods
More exercises of String methods can be found [here](../std/String.md).

> You can find the solutions [here](https://github.com/sunface/rust-by-practice)(under the solutions path), but only use it when you need it