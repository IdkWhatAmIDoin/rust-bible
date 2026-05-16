# The Rust Bible
*The Holy Scripture of the Compiler, the Borrow Checker, and the Way of Safe Abstraction*

*Translated from the original `rustc` errors*

---

## Book I — Ownership: How It Works

**1:1** When your program starts, there's a stack and a heap. The heap doesn't manage itself, and without ownership rules, that's where segfaults come from.

**1:2** *Every value has exactly one owner.* When the owner goes out of scope, the value is dropped and its memory is freed automatically.

**1:3** When you pass a value into a function, ownership moves into that function. *You can't use it after that* — it's not yours anymore.

```rust
let s = String::from("hello");
takes_ownership(s);
println!("{}", s); // E0382: use of moved value
```

**1:4** If your type implements `Copy`, none of this applies — it just gets duplicated when you pass it around. This works for small stack values like integers and booleans.

---

## Book II — Borrowing

**2:1** *You can have one mutable reference, or any number of immutable references — but not both at the same time.* This is the core rule. Everything else follows from it.

**2:2** A borrow ends at its last use, not at the end of the block. This is Non-Lexical Lifetimes, added in Rust 2018 — the compiler is smarter than you think.

```rust
let mut s = String::from("hello");
let r1 = &s;
let r2 = &s;
println!("{} {}", r1, r2); // r1 and r2 end HERE
let r3 = &mut s; // their borrows are gone, so this is fine
```

**2:3** *A reference can't outlive the value it points to.* That's a dangling pointer, and the compiler will not let it happen.

> compiler says: "this function's return type contains a borrowed value, but there is no value for it to be borrowed from"

---

## Book III — Lifetimes

**3:1** Every reference has a lifetime. The compiler can usually figure it out on its own — you only need to write `'a` when it can't.

**3:2** If your function takes two references and returns one, you need to tell the compiler which input the output is tied to. It can't figure that out on its own.

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

**3:3** `'static` means the reference lives for the entire duration of the program. Don't use it as a lazy fix — it's a real commitment.

---

## Book IV — Option and Result

**4:1** There's no null in Rust. Instead you have `Option<T>` — either `Some(value)` or `None`. You have to handle both cases or the compiler won't let you compile.

**4:2** *You can call `.unwrap()`*, but if the value is `None`, your thread panics and crashes. Your coworkers will not be happy.

> use `match`, or `.unwrap_or()`, or at least `.expect("a message so future you knows what happened")`

**4:3** For operations that can fail, use `Result<T, E>`. The `?` operator propagates errors up the call stack automatically, so you don't have to write a `match` for every single fallible call.

```rust
fn read_file() -> Result<String, std::io::Error> {
    let contents = std::fs::read_to_string("file.txt")?; // ? handles the error
    Ok(contents)
}
```

---

## Book V — The Confessions

**5:1** `unsafe` is allowed, but you need to be able to justify it — to your reviewer, to your future self debugging at 2am, and to yourself right now.

**5:2** `.clone()` is not wrong. Sometimes cloning is the correct call. Don't be precious about it.

**5:3** `Rc<RefCell<T>>` means you gave up on explaining your ownership model to the borrow checker and decided to check it at runtime instead. Sometimes that's the right move. Own it.

**5:4** *Don't fight the borrow checker.* It is correct. When you think the borrow checker is wrong, you are wrong about your own code. Sit with that.

**5:5** Clippy is not your enemy. Clippy is the compiler quietly telling you *"this is more complicated than it needs to be and you know it."*

**5:6** At some point a C++ developer will tell you that Rust's memory safety is unnecessary because they can manage memory themselves. Let them talk. Nod. Say nothing. They'll come back.

---

## Book VI — Traits (They're Not Interfaces)

**6:1** *There's no inheritance.* There are Traits, and they're more flexible. If you miss inheritance, you haven't fully clicked with Traits yet.

**6:2** `impl Trait` and `dyn Trait` are different. `impl Trait` is monomorphized at compile time — fast, no allocation. `dyn Trait` is a fat pointer on the heap — flexible, but has a cost. Know which one you're using and why.

**6:3** If you implement `Iterator`, `IntoIterator`, and `FromIterator` on your types, you get `.map()`, `.filter()`, `.collect()`, and the rest of the iterator adapter chain for free. It's worth it.

---

## Appendix — What the Compiler Is Actually Telling You

**A:1** *"cannot borrow `x` as mutable because it is also borrowed as immutable"* — you violated the rule from Book II. Go re-read it.

**A:2** *"value used here after move"* — you gave ownership to something else and then tried to use the value again. You can't.

**A:3** *"mismatched types"* — you mixed up `&str` and `String` again. This happens to everyone. It will stop happening eventually.

**A:4** *"the trait `Display` is not implemented for..."* — you need to implement it yourself. The compiler won't do it for you.

**A:5** *"consider deriving `Clone`"* — just add `#[derive(Clone)]`. The compiler is helping you.

---

*That's the first edition of the Rust Bible.*

*Keep your borrows short, annotate your lifetimes, and ship with no warnings.*

*Written for rustc 1.87.0 · compiled with `--release`*
