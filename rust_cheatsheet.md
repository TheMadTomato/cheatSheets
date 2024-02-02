# Rust cheat-sheet

## Importing Modules
To use crates that are no built in, user must add the crate name and version under the `[dependencies]` header. After that you can proceed by using the `use` function. When we `cargo build` it will install the dependencies. 
- `use`                         # import modules
i.e. *importing stdin module for input operations*
``` use std::io; ```

## Output Commands
- `println!("")`                # output string to the console, {} is used as place holder to show variables. This function always go to new line.
- `print!("")`                  # does not go to new line.
*intersting note, in a program where u ask user for an input and you write print!() before the statement it will only print the message after the input is made. But when println!() is used it prints the message then ask for input. still not sure for the actual reason.*

## Input Commands
- `let mut input = String::new();`                          # create a mutable variable to store user input. it only take stores string.
- `io::stdin().read_line(&mut input)`                       # read user input from the console equivalen to `input()` in python. to makee the input into an int a few extra steps must be taken.
- `.expect("error message")`:                               # often used with the above command to handle errors. It might sound a lot for small programs but keep in mind rust is made for large scale program where such features are integral.
- `let num: u32 = input.trim().parse().expect("Invlid input")`       # turn the input: str to int. equivalent to `int(input())` in  a way.
- `.trim()`                                                 # this method is used to remove any white space characters.
- `.parse()`                                                # This method is what convert strings to other data types. it does so by parsing the string input hence it's name.

## Variables Management
- `let x = 5;`                  # assign a constant variable, in rust a var is immutable by default.
- `let mut y = 5;`              # assign a mutable variable with the *mut* key word.
- `y += 1;`                     # increment a mutable variable.
- `let (x, y) = (1, 2);`        # multiple assignment.
- `let x: [i32; 5] = [1, 2, 3, 4, 5];` # array declaration and initialization.

## Variable Types
- `let x: i32 = 5;`             # 32-bit signed integer
- `let x: u32 = 5;`             # 32-bit unsigned integer
- `let x: f64 = 5.0;`           # 64-bit floating point number
- `let x: bool = true;`         # Boolean
- `let x: char = 'a';`          # Character
- `let x: &str = "hello";`      # String slice
- `let x: String = String::from("hello");` # String
- `let x: [i32; 5] = [1, 2, 3, 4, 5];` # Array of i32
- `let x: (i32, f64, u8) = (500, 6.4, 1);` # Tuple
- `let x: Option<i32> = Some(5);` # Option, for nullable types

## Conditional Statements
- `if x < 5 { .. } else { .. }` # if-else statement.
- `let x = if condition { 5 } else { 6 };` # assigning the result of an if-else expression to a variable.
- `if let x = 5 { .. }`         # if-let statement.

## Match .cmp vs If-Else

- `match` with `.cmp()`
```rust
let result = a.cmp(&b);
match result {
    Ordering::Less => println!("a is less than b"),
    Ordering::Equal => println!("a is equal to b"),
    Ordering::Greater => println!("a is greater than b"),
}
```
To use the .cmp() method with match, you need to import the std::cmp::Ordering enum. The match statement with .cmp() is used to compare two values and execute different code blocks based on the comparison result. It's powerful and flexible, especially when dealing with complex conditions and pattern matching.

```rust
if a < b {
    println!("a is less than b");
} else if a == b {
    println!("a is equal to b");
} else {
    println!("a is greater than b");
}
```
The if-else statement is used to perform different actions based on whether a certain condition is true or false. It's simpler and more readable for simple conditions.

## Loop structures
- `loop { .. }`                 # infinite loop.
- `while x != 0 { .. }`         # while loop.
- `for x in 0..5 { .. }`        # for loop.
- `let result = loop { .. };`   # assigning the result of a loop to a variable.

## Functions
- `fn function_name() { .. }`               # define a function with no parameters
- `fn function_name(x: i32) { .. }`         # define a function with parameters
- `fn function_name(x: i32) -> i32 { .. }`  # define a function with a return type
- `fn function_name(x: i32) -> Result<i32, Error> { .. }`  # define a function with a Result return type
- `pub fn function_name() { .. }`           # define a public function
- `fn function_name(x: i32, y: i32) { .. }` # define a function with multiple parameters

## Result, Ok and Err
- `Result<T, E>`: A type used for returning and propagating errors. It is an enum with the variants, `Ok(T)`, representing success and containing a value, and `Err(E)`, representing error and containing an error value.

- `Ok(value)`: An `Ok` variant of `Result` is returned when a function is successful. It contains the successful value.

- `Err(error)`: An `Err` variant of `Result` is returned when a function fails. It contains the error value.

- `match result { Ok(val) => { .. }, Err(err) => { .. } }`: Used to handle `Result`. If `Result` is `Ok`, do something with the `val`. If `Result` is `Err`, do something with the `err`.

- `let res = result.unwrap();`: Unwraps a `Result`, yielding the content of an `Ok`. If the value is an `Err` then it will panic.

- `let res = result.expect("Failed to read file");`: Similar to `unwrap`, but it allows you to add an error message that will be displayed in case of an `Err`.

- `let res = result.unwrap_or(default);`: Returns the contained `Ok` value or a provided default.

- `let res = result.unwrap_or_else(|err| { .. });`: Similar to `unwrap_or`, but it allows you to provide a function that generates a default value from the `Err` value.

## Comments
- `// This is a comment`        # single-line comment.
- `/* This is a
    multi-line comment */`     # multi-line comment.
- `/// Documentation comment`   # documentation comment, used by the documentation generator.

## Extra
- `use rand::Rng;`              # use the Rng trait from the rand crate
- `let secret_number = rand::thread_rng().gen_range(1, 101);` # generate a random number