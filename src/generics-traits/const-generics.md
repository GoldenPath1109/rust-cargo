# Const Generics
Const generics are generic arguments that range over constant values, rather than types or lifetimes. This allows, for instance, types to be parameterized by integers. In fact, there has been one example of const generic types since early on in Rust's development: the array types [T; N], for some type T and N: usize. However, there has previously been no way to abstract over arrays of an arbitrary size: if you wanted to implement a trait for arrays of any size, you would have to do so manually for each possible value. For a long time, even the standard library methods for arrays were limited to arrays of length at most 32 due to this problem.

## Examples
1. Here's an example of a type and implementation making use of const generics: a type wrapping a pair of arrays of the same size.
```rust,editable
struct ArrayPair<T, const N: usize> {
    left: [T; N],
    right: [T; N],
}

impl<T: Debug, const N: usize> Debug for ArrayPair<T, N> {
    // ...
}
```


2. Currently, const parameters may only be instantiated by const arguments of the following forms:

- A standalone const parameter.
- A literal (i.e. an integer, bool, or character).
- A concrete constant expression (enclosed by {}), involving no generic parameters.
  
```rust,editable
fn foo<const N: usize>() {}

fn bar<T, const M: usize>() {
    foo::<M>(); // ok: `M` is a const parameter
    foo::<2021>(); // ok: `2021` is a literal
    foo::<{20 * 100 + 20 * 10 + 1}>(); // ok: const expression contains no generic parameters
    
    foo::<{ M + 1 }>(); // error: const expression contains the generic parameter `M`
    foo::<{ std::mem::size_of::<T>() }>(); // error: const expression contains the generic parameter `T`
    
    let _: [u8; M]; // ok: `M` is a const parameter
    let _: [u8; std::mem::size_of::<T>()]; // error: const expression contains the generic parameter `T`
}

fn main() {}
```

3. Const generics can also let us avoid some runtime checks.
```rust
/// A region of memory containing at least `N` `T`s.
pub struct MinSlice<T, const N: usize> {
    /// The bounded region of memory. Exactly `N` `T`s.
    pub head: [T; N],
    /// Zero or more remaining `T`s after the `N` in the bounded region.
    pub tail: [T],
}

fn main() {
    let slice: &[u8] = b"Hello, world";
    let reference: Option<&u8> = slice.get(6);
    // We know this value is `Some(b' ')`,
    // but the compiler can't know that.
    assert!(reference.is_some());

    let slice: &[u8] = b"Hello, world";
    // Length check is performed when we construct a MinSlice,
    // and it's known at compile time to be of length 12.
    // If the `unwrap()` succeeds, no more checks are needed
    // throughout the `MinSlice`'s lifetime.
    let minslice = MinSlice::<u8, 12>::from_slice(slice).unwrap();
    let value: u8 = minslice.head[6];
    assert_eq!(value, b' ')
}
```


## Exercises
1. 🌟🌟 `<T, const N: usize>` is part of the struct type, it means `Array<i32, 3>` and `Array<i32, 4>` are different types.
   
```rust,editable
struct Array<T, const N: usize> {
    data : [T; N]
}

fn main() {
    let arrays = [
        Array{
            data: [1, 2, 3],
        },
        Array {
            data: [1.0, 2.0, 3.0],
        },
        Array {
            data: [1, 2]
        }
    ];
}
```

2. 🌟🌟 
```rust,editable

// fill in the blanks to make it work
fn print_array<__>(__) {
    println!("{:?}", arr);
}
fn main() {
    let arr = [1, 2, 3];
    print_array(arr);

    let arr = ["hello", "world"];
    print_array(arr);
}
```

3. 🌟🌟🌟 Sometimes we want to limit the size of an variable, e.g when using in embedding evironments, then `const expressions` will fit your need.
   
```rust,editable
#![allow(incomplete_features)]
#![feature(generic_const_exprs)]

fn check_size<T>(val: T)
where
    Assert<{ core::mem::size_of::<T>() < 768 }>: IsTrue,
{
    //...
}

// fix the errors in main
fn main() {
    check_size([0u8; 767]); 
    check_size([0i32; 191]);
    check_size(["hello你好"; __]); // size of &str ?
    check_size(["hello你好".to_string(); __]);  // size of String?
    check_size(['中'; __]); // size of char ?
}



pub enum Assert<const CHECK: bool> {}

pub trait IsTrue {}

impl IsTrue for Assert<true> {}
```