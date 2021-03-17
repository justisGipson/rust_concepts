### Keywords
<br>

Rust has a set of *keywords* that are reserved for use by the language only, much like other languages. Keep in mind that you cannot use these words as names of variables or functions. Most of the keywords have special meanings, and you'll be using them to do various task in Rust programs; a few have no current functionality associated with them but have been reserved for functionality that might be added to Rust in the future.

<br>

### Variable and Mutability

<br>

By Default variables are immutable. Rust does this to nudge you to write your code in a way that takes advantage of the safety and easy concurrency that Rust offers. However, you still have the option to make you variable mutable. Rust encourages you to favor immutability and sometimes you may want to opt out.

When a variable is immutable, once a value is bound to a name, you can't change that value. To illustrate this, generate a new project called [variables](./variables/variables/src/main.rs). Then in the new project, open `src/main.rs` and replace its code with the following code that won't compile just yet:

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

Save and run the program using `cargo run`. You should receive an error message, as shown below:

```bash
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         -
  |         |
  |         first assignment to `x`
  |         help: make this binding mutable: `mut x`
3 |     println!("The value of x is: {}", x);
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable

error: aborting due to previous error
```

This example shows how the compiler helps you find errors in your programs.

This error message indicated that the cause of the error is that you `cannot assign twice to immutable variable x`, because you tried to assign a second value to the immutable `x` variable.

It's important that we get compile-time errors when we attempt to change a value that was previously designated as immutable because this very situation can lead to bugs. If one part of our code operates on the assumption that a value will never change and another part of out code changes that value, it's possible that the first part of the code won't do what it was designed to do. The cause of this kind of bug can be difficult to track down after the fact, especially when the second piece of code changes the value only *sometimes*.

In rust, the compiler guarantees that when you state that a value won't change, it really won't change. That means that when you're reading and writing code, you don't have to keep track of how and where a value might change. Your code is thus easier to reason through.

But mutability can be very useful. Variables are immutable only by default, you can make the mutable by adding `mut` in front of the variable name. In addition to allowing this values to change, `mut` conveys intent to future readers of the code by indicating that other parts of the code will be changing this variable's value.

Let's change `src/main.rs` to the following:

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

When the program is run, we get this:

```bash
  Compiling variables v0.1.0 (/Users/jgipson/Desktop/Dev/rust/concepts/variables/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.71s
     Running `target/debug/variables`
The value of x is: 5
The value of x is: 6
```

We're allowed to change the value that `x` binds to from 5 to 6 when `mut` is used. In some cases, you'll want to make a variable mutable because it makes the code more convenient to write than is it had only immutable variables.
