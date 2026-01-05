# Rust

- Rust Source files have `.rs` extension.
- **Cargo:** Is a colletion of package manager, build system, documentation generator and test runner
- **toml files:** Tom's Obvious Minimal Language, contains meta information about the projects such as name, version, authors and edition. Also contains depedencies. Fetches the email from global git configuration if there is any!
- `rustc` is the rust compiler bundled into `cargo`

## Few basic cargo commands:
- `cargo new <Project_Name>` will create a project named '<Project_Name>'
- `cargo run` will build and run the project (From target/debug/<Project_Name>)
- `cargo run --release` will build and run the release version of the <Project_Name>, the execution is faster as compated to debug version and compilation process is slower than debug version.

## Few things to know beforehand:
- Scope rules with `{` and `}` are similar to C/C++
- `let` is used to declare a variable
- `let` statement can destructure the data on RHS and use it to initialize the patterns on the LHS. `let (foo, bar, baz) = {10, 1, 89);` will set the value of `foo` to `10`, `bar` to `1` and `baz` to `89`. It is as good as declaring each of these 3 variables separately.
- Rust is a strongly type-annotated language, however, you can leave the type-annotation aside if Rust can determine the correct data-type for the variable
- All variable by-default are immutable. This is for speed optimization, safety and concurrency.
- `let mut` syntax is used to override the immutability.
- `const` provide further immutability within the module, making it global immutable variable in the module.
- Uninitialized variables are forbidden. In such a the code doesn't even compile -- This provide better safety by evading the Undefined Behavior due to uninitialized variables.

## Functions:

Rust allows you to write functions anywhere in the source code, not requiring those to be declared/defined before the actual function call.

    fn foo(bar: f64, baz: f64) -> f64 {
        return bar * baz;
    }

This declares a function named `foo()` taking two `f64` arguments `bar` and `baz`, returning a `f64` value.  
**Minor note:** `{ return bar * baz }` can also be replaced by `{ bar * baz }`, without the keyword `return` and the `;`.

And, `foo()` will be used in the following manner:

    fn main(
        let x = foo(10.10, 20.20);
        println!(x is {}, x);
    }

## Primitive Scalar data types:
- Unsigned Integers: `u8`, `u16`, `u32`, `u64`, `u128`, `usize`
- Signed Integers: `i8`, `i16`, `i32`, `i64`, `i128`, `isize`
- Floating point: `f32` and `f64`
- Boolean type: `bool`, regular `true` and 'false`
- Character type: Effectively always of 4 bytes! 
- Compound types: 
  - Tuple: Can store multiple values of differetn data types -- `let config = (1, 3.14, 0xA5A5);` and the individuals can be accessed as `let channel = config.0;`, `let gain = config.1;` and `let checksum = config.2;` _Or_ `let (channel, gain, checksum) = config;`
  - Arrays: Store items of the same data type. `let buf = [1, 2, 3];` or better yet `let buf: [u8; 3] = [1, 2, 3];` The latter declares `buf` as an array of 3 elements of type `u8` with values initialized to `1`, `2` and `3`. Indexing work just as it does in C/C++ -- `buf[<index_value>]`. 

**Note 1:** If not explicitly annotated, the default is `i32` for obvious reasons.

**Note 2:** `let x: u16 = 5;` can also be expressed as `let x = 5u16` or even better readble by using `let x = 5_u16`. This is called suffix notation. This is consistant acress all primitive data types. This is useful for implementations where it is required to pass a literals to a generic function that accepts multiple numeric types.

**Note 3:** `usize` and/or `isize` is the size of the pointer type for the target system/platform (16-bit vs 32-bit vs 64-bt). Typically used to index for arrays or vectors.

**Note 4:** _Arity_ is the count of the items in the tuple. As of today, maximum arity can be 12.

**Note 5**: Maximum count of elements in arrays is restricted to 32.

## Control Flow -- `if - else if - else` ~~statements~~ expressions
The `if - else if - else` statements works pretty much the same, they are syntactically differ from C/C++ in a manner that the parenthesis over the test condiions are not required. Secondly, in rust programming paradigm, the `if` statements are in fact expressions and NOT statements. So, going ahead refrain from using the word _statement_ and use _expression_ instead! The thought behind this, is that the expressions can/do return a value, statements can't! This allows following:

    // Assume msg is mutable
    msg = if count == 32 {
        "Receving"
    } else if count == 64 {
        "received"
    } else {
        "Empty"
    }; 
    
**Notes:**
- No `;` after the branch
- In above case, a `return` can not be used as the end results needs to be populated into `msg`
- In above case, all the blocks return the same type -- A string in this case
- Notice the `;` at the end above. If the `msg =` assignment is omitted, then the `;` is not required.
- Fortunately, Braces `{` and `}` are mandatory!
- No C-style ternary if-else using `?` and `:`, instead `num = if a { b } else { c }` is permitted.

## Control Flow -- Unconditional loops
The best place to being is with

     loop { }

Or,

    loop { 
        break;
    }

I guess the C equivalent of these would be `while(1) { break; }`
Sooner or later there would be nested loops --

    'foo: loop {
        loop {
            loop {
                break 'bob;
            }
        }
    }

This will break all the way thorugh inner loops, out of the outer-nmost loop annotated by using `'foo`. The same behavior is observed with `continue` statement. In simple words, tell to break which loop you want to break out of! No more ugly flag-based mechanism required for breaking from nested loops!

## Control Flow -- `while` loops
Obviously, `while` loops exhibit all the same behaviors as unconditional loops. except that they are also terminated when the condition evaluates to `false`.

So,

    while foo() {
        // Some code
    }

will break, if `foo()` is evaluated as `false`, or unless there is a `break` statement somewhere in the underlying code.

A unconditional loop form of the same would look like:

    loop {
        if !foo() { break }
        // Some code
    }
    
Rust doesn't have a dedicated syntax for a `do-while` loop. However, it can be implemented with a little trick we all know:

    loop {
        // Some code
        
        if !foo() { break } 
    }
    
## Control Flow -- `for` loops
Just like other languages, the `for` loop iterares over any iterable value. Compound and collection types typically have a few different ways to get an interable value for them. For that matter, `iter()` method is one of the most common ways to get an iterator. The iterator you use determines which items are returned and the other order that they are returned in. `iter()` iterates over all items in a collection in order, _if_ the collection is ordered, and randomly if the collection is unordered. 


    for foo in [7, 8, 9].iter() {
        // Do some stuff with foo
    }

The `for` loop can take a pattern to destructure the items it received and bind the inside parts to variables. Only such that, the variables are local to the body of the `for` loop.

    let foo = [(10,20), (30,40)];
    for (bar, baz) in foo.iter() {
        // Do some stuff with bar and baz
    }
    
Generally, we need range-based loops --

    for foo in 0..64 { // Starts at 0 and goes upto 63
        // Some stuff
    }
    
    for foo in 0..=64 { // Starts at 0 and goes upto 64
        // Some stuff
    } 
    
