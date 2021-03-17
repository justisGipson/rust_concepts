### Keywords
<br>

Rust has a set of *keywords* that are reserved for use by the language only, much like other languages. Keep in mind that you cannot use these words as names of variables or functions. Most of the keywords have special meanings, and you'll be using them to do various task in Rust programs; a few have no current functionality associated with them but have been reserved for functionality that might be added to Rust in the future.

<br>

### Variable and Mutability

<br>

By Default variables are immutable. Rust does this to nudge you to write your code in a way that takes advantage of the safety and easy concurrency that Rust offers. However, you still have the option to make you variable mutable. Rust encourages you to favor immutability and sometimes you may want to opt out.

When a variable is immutable, once a value is bound to a name, you can't change that value. To illustrate this, generate a new project called [variables](./variables/variables/src/main.rs). Then in the new project, open `src/main.rs` and replace its code with the following code that won't compile just yet:
<br>

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```
<br>

Save and run the program using `cargo run`. You should receive an error message, as shown below:
<br>

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
<br>

This example shows how the compiler helps you find errors in your programs.

This error message indicated that the cause of the error is that you `cannot assign twice to immutable variable x`, because you tried to assign a second value to the immutable `x` variable.

It's important that we get compile-time errors when we attempt to change a value that was previously designated as immutable because this very situation can lead to bugs. If one part of our code operates on the assumption that a value will never change and another part of out code changes that value, it's possible that the first part of the code won't do what it was designed to do. The cause of this kind of bug can be difficult to track down after the fact, especially when the second piece of code changes the value only *sometimes*.

In rust, the compiler guarantees that when you state that a value won't change, it really won't change. That means that when you're reading and writing code, you don't have to keep track of how and where a value might change. Your code is thus easier to reason through.

But mutability can be very useful. Variables are immutable only by default, you can make the mutable by adding `mut` in front of the variable name. In addition to allowing this values to change, `mut` conveys intent to future readers of the code by indicating that other parts of the code will be changing this variable's value.

Let's change `src/main.rs` to the following:
<br>

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```
<br>

When the program is run, we get this:

```bash
  Compiling variables v0.1.0 (/Users/jgipson/Desktop/Dev/rust/concepts/variables/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.71s
     Running `target/debug/variables`
The value of x is: 5
The value of x is: 6
```

We're allowed to change the value that `x` binds to from 5 to 6 when `mut` is used. In some cases, you'll want to make a variable mutable because it makes the code more convenient to write than is it had only immutable variables.

There are multiple trade-offs to consider in addition to the prevention of bugs. For example, in cases where you're using large data structures, mutating an instance in place may be faster than copying and returning newly allocated instances. With smaller data structures, creating new instances and writing in a more functional programming style may be easier to think through, so lower performance might be a worthwhile penalty for gaining that clarity.
<br>

### Differences between Variable and Constants

<br>

Being unable to change the value of a variable might have reminded you of another programming concept that most other languages have: *constants*. Like immutable variables, constants are values that are bound to a name and are not allowed to change, but there are a few differences between constants and variables.

First, you aren't allowed to use `mut` with constants. Constants aren't just immutable by default – they're always immutable.

You declare constants using the `const` keyword instead of the `let` keyword, and the type of the value *must* be annotated. Types and data types will be covered soon. Just know that you always must annotate the type.

Constants can be declared in any scope, including global scope, which makes them useful for values that many parts of the code need to know about.

The last difference is that constants may be set only to a constant expression, not to the result of a function call or any other value that could only be computed at runtime.

Below is an example of a constant declaration where the constant's name is `MAX_POINTS` and its value is set to 100,000. (Rust's naming convention for constants is to use all uppercase with underscores between words, and underscores can be inserted in numeric literals to improve readability):
<br>

```rust
const MAX_POINTS: u32 = 100_000;
```
<br>

Constants are valid for the entire time a program runs, within the scope they were declared in, making them a useful choice for values in your application domain that multiple parts of the program might need to know about, such as the maximum number of points any player of a game is allowed to earn or the speed of light.

Naming hardcoded values used throughout your program as constants is useful in conveying the meaning of that value to future maintainers of the code. It also helps to have only one place in your code you would need to change if the hardcoded value needed to be updated in the future.
<br>

### Shadowing

<br>

As shown in the `guessing_game` tutorial, you can declare a new variable with the same name as a previous variable, and the new variable shadows the previous variable. Rustaceans say that the first variable is *shadowed* by the second, which means that the second variable's value is what appears when the variable is used. We can shadow a variable by using the same variable's name and repeating the use of the `let` keyword as follows:

<br>

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    let x = x * 2;

    println!("The value of x is: {}", x);
}
```
<br>

This program first binds `x` to a value of 5. Then it shadows `x` b repeating `let x =`, taking the original value and adding 1 so the value of `x` is then 6. The third `let` statement also shadows also shadows `x`, multiplying the previous value by 2 to give `x` a final value of 12. When we run this program, it will output the following:

<br>

```bash
   Compiling constants v0.1.0 (/Users/jgipson/Desktop/Dev/rust/constants)
    Finished dev [unoptimized + debuginfo] target(s) in 1.30s
     Running `target/debug/constants`
The value of x is: 12
```
<br>

Shadowing is different from marking a variable as `mut`, because we'll get a compile-time error if we accidentally try to reassign to this variable without using the `let` keyword. By using `let`, we can perform a few transformations on a value but have the variable be immutable after those transformations have been completed.

The other difference between `mut` and shadowing is that because we're effectively creating a new variable when we use the `let` keyword again, we can change the type of the value but reuse the same name. For example, say our program asks a user to show how many spaces they want between some text by inputting space characters, but we really want to store that input as a number:

<br>

```rust
let spaces = "   ";
let spaces = spaces.len();
```
<br>

This construct is allowed because the first `spaces` variable is a string type and the second `spaces` variable, which is a brand-new variable that happens to have the same name as the first one, is a number type. Shadowing thus spares us from having to come up wth different names, such as `spaces_str` and `spaces_num`; instead, we can reuse the simpler `spaces` name. However, if we try to use `mut` for this, as shown here, we'll get a compile-time error:

<br>

```rust
let mut spaces = "   ";

spaces = spaces.len()
```
<br>

The error says we're not allowed to mutate a variable's type:

<br>

```bash
error[E0308]: mismatched types
--> src/main.rs:3:14
  |
3 |   spaces = spaces.len();
  |            ^^^^^^^^^^^^ expected &str, found usize
  |
= note: expected type `&str`
           found type `usize`
```
<br>

### Data Types

<br>

Every value in Rust is of a certain data type, which tells Rust what kind of data is being specified so it knows how to work with that data. We'll look at two data type subsets: scalar and compound.

Keep in mind that Rust is a *statically typed* language, which means that it must know the types of all variables at compile time. The compiler can usually infer what type we want based on the value and how we use it. In cases when many types are possible, such as when we converted a `String` to a numeric type using `parse` in the `guessing_game`, we must add a type annotation, like this:
<br>

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```
<br>

If we don't add the type annotation here, Rust will display the following error, which means the compiler needs more information from us to know which type we want to use:
<br>

```bash
error[E0308]: mismatched types
--> src/main.rs:3:14
  |
2 |   let guess = "42".parse().expect("Not a number!")
  |       ^^^^^
  |       |
  |       cannot infer type for `_`
  |       consider giving `guess` a type
```
<br>

You'll see different type annotations for other data types.

<br>

### Scalar Types
<br>

