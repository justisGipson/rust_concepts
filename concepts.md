### Keywords
<br>

Rust has a set of *keywords* that are reserved for use by the language only, much like other languages. Keep in mind that you cannot use these words as names of variables or functions. Most of the keywords have special meanings, and you'll be using them to do various task in Rust programs; a few have no current functionality associated with them but have been reserved for functionality that might be added to Rust in the future.

<br>

### Variable and Mutability

<br>

By Default variables are immutable. Rust does this to nudge you to write your code in a way that takes advantage of the safety and easy concurrency that Rust offers. However, you still have the option to make you variable mutable. Rust encourages you to favor immutability and sometimes you may want to opt out.

When a variable is immutable, once a value is bound to a name, you can't change that value. To illustrate this, generate a new project called [variables](./variables/variables/src/main.rs). Then in the new project, open `src/main.rs` and replace its code with the following code that won't compile just yet:
<br>

[variable.rs](variable.rs)

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

[variable1.rs](variable1.rs)

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

[constant.rs](contant.rs)

<br>

Constants are valid for the entire time a program runs, within the scope they were declared in, making them a useful choice for values in your application domain that multiple parts of the program might need to know about, such as the maximum number of points any player of a game is allowed to earn or the speed of light.

Naming hardcoded values used throughout your program as constants is useful in conveying the meaning of that value to future maintainers of the code. It also helps to have only one place in your code you would need to change if the hardcoded value needed to be updated in the future.
<br>

### Shadowing

<br>

As shown in the `guessing_game` tutorial, you can declare a new variable with the same name as a previous variable, and the new variable shadows the previous variable. Rustaceans say that the first variable is *shadowed* by the second, which means that the second variable's value is what appears when the variable is used. We can shadow a variable by using the same variable's name and repeating the use of the `let` keyword as follows:

<br>

[shadowing.rs](shadowing.rs)

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

[shadowing1.rs](shadowing1.rs)

<br>

This construct is allowed because the first `spaces` variable is a string type and the second `spaces` variable, which is a brand-new variable that happens to have the same name as the first one, is a number type. Shadowing thus spares us from having to come up wth different names, such as `spaces_str` and `spaces_num`; instead, we can reuse the simpler `spaces` name. However, if we try to use `mut` for this, as shown here, we'll get a compile-time error:

<br>

[shadowing_err.rs](shadowing_err.rs)

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

A scalar type represents a single value. Rust has four primary scalar types: integers, floating-point numbers, Booleans, and characters. They should be recognizable from other languages.
<br>

### Integer Types
<br>

An *integer* is a number without a fractional component. This type declaration indicates that the value it's associated with should be an unsigned integer (signed integer types start with `i`, instead of `u`) that takes up 32 bits of space. The table below shows the built-in types in Rust. Each variant in the Signed and Unsigned columns can be used to declare the type of an integer value.
<br>

|Length |Signed |Unsigned|
|-------|-------|--------|
|8-bit | i8 | u8 |
|16-bit| i16 | u16 |
|32-bit| i32 | u32 |
|64-bit| i64 | u64 |
|128-bit| i128 | u128 |
|arch | isize | usize|

<br>

Each variant can be either signed or unsigned and has an explicit size. *Signed* and *unsigned* refer to whether it's possible for the number to be negative or positive – in other words, whether the number needs to have a sign with it (signed) or whether it will only ever be positive and can therefore be represented without a sign (unsigned). It's like writing numbers on paper: when the sign matters, a number is shown with a plus sign or a minus sign; however, when it's safe to assume the number is positive, it's shown with no sign. Signed numbers are stored using two's complement representation.

> **Two's complement** is a [mathematical operation](https://en.wikipedia.org/wiki/Mathematical_operation) on [binary numbers](https://en.wikipedia.org/wiki/Binary_number), and is an example of a [radix complement](https://en.wikipedia.org/wiki/Method_of_complements). It is used in [computing](https://en.wikipedia.org/wiki/Computing) as a method of [signed number representation](https://en.wikipedia.org/wiki/Signed_number_representation).
>
> The two's complement of an *N*-bit number is defined as its [complement](https://en.wikipedia.org/wiki/Method_of_complements) with respect to 2*N*; the sum of a number and its two's complement is 2*N*. For instance, for the three-bit number 010, the two's complement is 110, because 010 + 110 = 8 which is equal to 2*3*. The two's complement is calculated by inverting the bits and adding one
>
> Two's complement is the most common method of representing signed [integers](https://en.wikipedia.org/wiki/Integer_(computer_science)) on computers,[[1\]](https://en.wikipedia.org/wiki/Two's_complement#cite_note-1) and more generally, [fixed point binary](https://en.wikipedia.org/wiki/Fixed-point_arithmetic) values. In this scheme, if the binary number 0102 encodes the signed integer 210, then its two's complement, 1102, encodes the inverse: −210. In other words, to reverse the sign of most integers (all but one of them) in this scheme, you can take the two's complement of its binary representation.[[2\]](https://en.wikipedia.org/wiki/Two's_complement#cite_note-2) The tables at right illustrate this property.
>
> Compared to other systems for representing signed numbers (*e.g.,* [ones' complement](https://en.wikipedia.org/wiki/Ones'_complement)), two's complement has the advantage that the fundamental arithmetic operations of [addition](https://en.wikipedia.org/wiki/Addition), [subtraction](https://en.wikipedia.org/wiki/Subtraction), and [multiplication](https://en.wikipedia.org/wiki/Multiplication) are identical to those for unsigned binary numbers (as long as the inputs are represented in the same number of bits as the output, and any [overflow](https://en.wikipedia.org/wiki/Integer_overflow) beyond those bits is discarded from the result). This property makes the system simpler to implement, especially for higher-precision arithmetic. Unlike ones' complement systems, two's complement has no representation for [negative zero](https://en.wikipedia.org/wiki/Signed_zero), and thus does not suffer from its associated difficulties.
>
> Conveniently, another way of finding the two's complement of a number is to take its ones' complement and add one: the sum of a number and its ones' complement is all '1' bits, or 2*N* − 1; and by definition, the sum of a number and its *two's* complement is 2*N*.

Each signed variant can store numbers from `-(2<sup>n-1</sup>)` to `2<sup>n-1</sup> -1` inclusive, where *n* is the number of bits the variant uses. So an `i8` can store numbers from `-(2<sup>7</sup>)` to `2<sup>7</sup> - 1`, which equals -128 to 127. Unsigned variants can store numbers from `0` to `2<sup>n</sup> - 1`, so a `u8` can store numbers from  `0` to `2<sup>8</sup> - 1`, which equals 0 to 255.

Additionally, the `isize` and `usize` types depend on the kind of computer your program is running on: 64 bits if you're on a 64-bit architecture and 32 bits if you're on a 32-bit architecture.

You can write integer literals in any of the forms shown in the table below. Note that all number literals except the byte literal allow a type suffix, such as `57u8` and `_` as a visual separator, such as `1_000`.
<br>

|Number literals|Example|
|---------------|-------|
|Decimal| `98_222`|
|Hex|`0xff`|
|Octal|`0o77`|
|Binary|`0b1111_0000`|
|Byte (u8 only)|`b'A'`|

<br>

So how do you know which type of integer to use? If you're unsure, Rust's defaults are generally good choices, and integer types defaultto `i32`: this type is generally the fastest, even of 64-bit systems. The primary situation in which you'd use `isize` or `usize` is when indexing some sort of collection.
<br>

### Integer Overflow
<br>

Let's say you have a variable of type `u8` that can hold values between 0 and 255. If you try to change the variable to a value outside of that range, such as 256, *integer overflow* will occur. Rust has some interesting rules involving this behavior. When you're compiling in debug mode, Rust includes checks for integer overflow that cause your program to *panic* at runtime if this behavior occurs. Rust uses the term panicking when a program exits with an error; more on this in 'Unrecoverable Error with `panic`' later on.

<br>

### Floating Point Types
<br>

Rust also has 2 primitive types for *floating-point numbers*, which are numbers with decimal points. Rust's floating-point types are `f23` and `f64`, which are 32 bits and 64 bits in size, respectively. The default type is `f64` because on modern CPUs it's roughly the same speed as `f32` but is capable of more precision.

Here's an example that shows floating-point numbers in action:
<br>

```rust
fn main() {
  let x= 2.0; // f64

  let y: f32 = 3.0; // f32
}
```
<br>

Floating-point numbers are represented according to the IEEE-754 Standard. The `f32` type is a single-precision float, and `f64` has double precision.
<br>

### Numeric Operations
<br>

Rust supports the basic mathematical operations you'd expect for all of the number types; addition, subtraction, multiplication, division, and remainder. The following example shows how you would use each one in a `let` statement:
<br>

```rust
fn main() {
  // addition
  let sum = 5 + 10;

  // subtraction
  let difference = 95.5 -4.3;

  // multiplication
  let product = 4 * 30;

  // division
  let quotient = 56.7 / 32.2;

  // remainder
  let remainder = 43 % 5;
}
```
<br>

Each expression in these statements uses a mathematical operator and evaluates to a single value, which is then bound to a variable. Appendix B contains a list of all operators that Rust provides
<!-- TODO: link Appendix B -->

<br>

### Boolean Type
<br>

As in most programming languages, a Boolean types in Rust has two possible values: `true` and `false`. Booleans are only 1 byte in size. The Boolean type in Rust is specified using `bool`. For example:
<br>

```rust
fn main() {
  let t = true;

  let f: bool = false; // with explicit type annotation
}
```
<br>

The main way to use Boolean values is through conditionals, such as an `if` expression. More in "Control Flow"
<!-- TODO: link Control Flow -->
<br>

### Character Type
<br>

So far we've only worked with numbers, but Rust supports letters too. Rust's `char` type is the language's most primitive alphabetic type, and the following example shows one way to use it.

**Note**: `char` literals are specified with single quotes, as opposed to string literals, which use double quotes.
<br>

```rust
fn main() {
  let c = 'z';
  let z = 'Ƶ';

  let heart_eyed_cat = '😻';
}
```
<br>

Rust's `char` types if four bytes in size and represents a Unicode Scalar Value, which means in can represent a lot more than just ASCII. Accented letters; Chinese, Japanese, and Korean characters; emoji; and zero-width spaces are all valid `char` values in Rust. Unicode Scalar Values ranges from `U+0000` to `U+D7FF` and `U+E000` to `U+10FFFF` inclusive. However, a "character" isn't really a concept in Unicode, so your human intuition for what a "character" is may not match up with what a `char` is in Rust.
<br>

### Compound Types
<br>

*Compound Types* can group multiple values into one type. Rust has two primitive compound types: tuples and arrays.

<br>

### The Tuple Type
<br>

A tuple is a general way of grouping together some number of other values with a variety of types into one compound type. Tuples have a fixed length: once declared, they cannot grow or shrink in size.

We create a tuple by writing a comma-separated list of values inside parentheses. Each position in the tuple has a type, and the types of the different values in the tuple don't have to be the same. Here's an example with some optional type annotations:
<br>

```rust
fn main() {
  let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```
<br>

The variable `tup` binds to the entire tuple, because a tuple is considered a single compound element. To get the individual values out of a tuple, we can use pattern matching to destructure a tuple value, like this:
<br>

```rust
fn main() {
  let tup = (500, 6.4, 1);

  let (x, y, z) = tup;

  println!("The value of y is: {}", y)
}
```
<br>

This program first creates a tuple and binds it to the variable `tup`. It then uses a pattern with `let` to take `tup` and turn it into three separate variables, `x`, `y`, and `z`. This is called *destructuring*, because it breaks the single tuple into three parts. Finally the program prints the value of `y`, which is 6.4.

In addition to destructuring through pattern matching, we can access a tuple element directly by using a period (.) followed by the index of the value we want to access, for example:
<br>

```rust
fn main() {
  let x: (i32, f64, u8) = (500, 6.4, 1);

  let five_hundred = x.0;

  let six_point_four = x.1;

  let one = x.2;
}
```
<br>

This program creates a tuple, `x`, then makes new variables for each element by using their index. As with most programming languages, the first index in a tuple is 0.
<br>

### The Array Type
<br>

Another way to have a collection of multiple values is with an *array*. Unlike a tuple, every element of an array must have the same type. Arrays in Rust are different from arrays in some other languages because arrays in Rust have a fixed length, like tuples.

In Rust, the values going into an array are written as a comma-separated list inside square brackets:
<br>

```rust
fn main() {
  let a = [1, 2, 3, 4, 5];
}
```
<br>

<!-- TODO: Link to understanding ownership here and to 8.1 common collections Vectors-->
Arrays are useful when you want your data allocated on the stack rather than the heap (more in Understanding Ownership) or when you want to ensure you always have a fixed number of elements. An array isn't as flexible as the vector type, though. A vector is a similar collection type provided by the standard library that *is* allowed to grow or shrink in size. If you're unsure whether to use an array or vector, you should probably use a vector.

An example of when you might to use an array rather than a vector is in a program that needs to know the names of the months of the year. It's very unlikely that such a program will need to add or remove months, so you can use an array because you know it will always contain 12 elements:
<br>

```rust
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```
<br>

You would write an array's type by using square brackets, and within the brackets include the type of each element, a semicolon, and then the number of elements in the array, like so:
<br>

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```
<br>

Here, `i32` is the type of each element. After the semicolon, the number `5` indicated the array contains five elements.

Writing an array's type this way looks similar to an alternative syntax for initializing an array: if you want to create an array that contains the same value for each element, you can specify the initial value, followed by a semicolon, and then the length of the array in square brackets, like:
<br>

```rust
let a = [3; 5];
```
<br>

The array named `a` will contain `5` elements that will all be set to the value `3` initially. This is the same as writing:
<br>

```rust
let a = [3, 3, 3, 3, 3];
```
<br>

But in a more concise way.
<br>

### Accessing Array Elements
<br>

An array is a single chunk of memory allocated on the stack. You can access elements of an array using indexing, like this:
<br>

```rust
fn main() {
  let a = [1, 2, 3, 4, 5];

  let first = a[0];
  let second = a[1];
}
```
<br>

in the example above, the variable named `first` will get the value `1`, because that is the first value at index `[0]` in the array. The variable named `second` will get the value `2` from index `[1]` in the array.
<br>

### Invalid Array Element Access
<br>

What happens if you try to access an element of an array that is past the end of the array? Say you change the example to the following, which uses similar code to the guessing game to get an array index from the user:
<br>

```rust
use std::io;

fn main() {
    let a = [1, 2, 3, 4, 5];

    println!("Please enter an array index.");

    let mut index = String::new();

    io::stdin()
        .read_line(&mut index)
        .expect("Failed to read line");

    let index: usize = index
        .trim()
        .parse()
        .expect("Index entered was not a number");

    let element = a[index];

    println!(
        "The value of the element at index {} is: {}",
        index, element
    );
}
```
<br>

This code compiles successfully. If you run the code using `cargo run` and enter 0, 1, 2, 3, or 4, the program will print out the corresponding value at that index in the array. If you instead enter a number past the end of the array, such as 10, you'll see output like this:
<br>

```bash
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 10', src/main.rs:19:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```
<br>

The program resulted in a *suntime* error at the point of using an invalid value in the indexing operation. The program exited at that point with an error message and didn't execute the final `println!`. When you attempt to accedd an element using indexing, Rust will check that the index you've specified is less than the array length. If the index is greater than or equal to the array length, Rust will panic. This check has to happen at runtime, especially in the case, because the compiler can't possible know what the value a user running the code will enter.

This is the first example of Rust's safety principles in action. In many low-level languages, this kind of check is not done, and when you provide an incorrect index, invalid memory can be accessed. Rust protects you against this kind of error by immediately exiting instead of allowing the memory access and continuing. More in error handling.
<!-- TODO: link to error handling here -->
<br>

### Functions
<br>

Functions are pervasive in Rust code. We've already seen one of the most important functions in the language: the `main` function, which is the entry point of many programs. You've also seen the `fn` keyword, which allows you to declare functions.

Rust code uses *snake case* as the conventional style for function and variable names. In snake case, all letters are lowercase and underscores separate words. Here's an example:
<br>

```rust
fn main() {
    println!("Hello, world!");

    another_function();
}

fn another_function() {
    println!("Another function.");
}
```
<br>

Function definitions in Rust start with `fn` and have a set of parentheses after the function name. The curly brackets tell the compiler where the function body begins and ends.

We can call any function we’ve defined by entering its name followed by a set of parentheses. Because `another_function` is defined in the program, it can be called from inside the `main` function. Note that we defined `another_function` after the `main` function in the source code; we could have defined it before as well. Rust doesn’t care where you define your functions, only that they’re defined somewhere.

Let’s start a new binary project named functions to explore functions further. Place the another_function example in src/main.rs and run it. You should see the following output:
<br>

```bash
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.28s
     Running `target/debug/functions`
Hello, world!
Another function.
```
<br>

The lines execute in the order in which they appear in the main function. First, the “Hello, world!” message prints, and then another_function is called and its message is printed.
<br>

### Function Parameters
<br>

Functions can also be defined to have _parameters_, which are special variables that are part of a function's signature. When a function has parameters, you can provide it with concrete values for those parameters. Technically, the concrete values are called _arguments_, but in casual conversation, people tend to use the words _parameter_ and _argument_ interchangeably for either the variables in a function's definition or the concrete values passed in when you call a function.

The following rewritten version of `another_function` shows what parameters look like in Rust:
<br>

```rust
fn main() {
  another_function(5);
}

fn another_function(x: i32) {
  println!("The value of x is: {}", x);
}
```
<br>

When ran, the following is what the output should be:

```bash
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 1.21s
     Running `target/debug/functions`
The value of x is: 5
```
<br>
