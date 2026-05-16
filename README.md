# The Rust Bible
*Being the Holy Scripture of the Compiler, the Borrow Checker, and the Way of Safe Abstraction*

*Translated from the original `rustc` errors*

---

## Book I — Genesis of Ownership

**1:1** In the beginning, the compiler created the stack and the heap. And the heap was without form, and void of ownership. And darkness was upon the face of the segfault.

**1:2** *Every value shall have one owner, and one owner only.* When the owner goeth out of scope, the value shall be dropped, and its memory returned unto the allocator.

**1:3** And yea, when thou passeth a value unto a function, the ownership is moved thither. *Thou mayest not use it thereafter*, for it belongeth to another.

```rust
let s = String::from("hello");
takes_ownership(s);
println!("{}", s); // E0382: use of moved value
```

**1:4** But if thy value implementeth `Copy`, it shall be duplicated freely, for it is small and lives upon the stack, and the compiler regardeth it with favour.

---

## Book II — The Laws of Borrowing

**2:1** *Thou shalt have one mutable reference that is still alive, or multiple immutable references that are still alive — but not both.* This is the first and greatest commandment.

**2:2** Know that a borrow endeth at its last use, not at the closing of the brace. This is the gift of Non-Lexical Lifetimes, granted unto the people in the Year of Rust 2018.

```rust
let mut s = String::from("hello");
let r1 = &s;
let r2 = &s;
println!("{} {}", r1, r2); // r1 and r2 die HERE
let r3 = &mut s; // ✅ their borrows are dead, this is fine
```

**2:3** *Thou shalt not let a reference outlive the value it referenceth.* For this is the dangling pointer, which is an abomination, and the compiler shall not permit it.

> compiler says: "this function's return type contains a borrowed value, but there is no value for it to be borrowed from"

---

## Book III — The Book of Lifetimes

**3:1** And the Lord spake: *`'a`.* And it was so. Every reference beareth a lifetime, though the compiler may infer it for thee if thou art humble and thy function is simple.

**3:2** If thy function taketh two references and returneth one, thou must declare from whence the returned reference doth come. For the compiler is wise but not a prophet.

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

**3:3** And there is `'static`, the lifetime that endureth for the entire program. Use it not carelessly, for it is a great commitment, and leaketh memory unto the runtime.

---

## Book IV — The Parables of Option and Result

**4:1** There is no null. In its place the Lord hath given thee `Option<T>`, which is either `Some(value)` or `None`, and thou shalt handle both or face the wrath of the exhaustiveness check.

**4:2** *Thou mayest call `.unwrap()`*, but know this: if `None` cometh, thy thread shall panic and perish, and thy coworkers shall judge thee at code review.

> use `match`, or `.unwrap_or()`, or at the very least `.expect("a message so future you knows what happened")`

**4:3** And for fallible operations there is `Result<T, E>`. The `?` operator is a mercy granted by the compiler — it propagateth the error upward, that thy functions need not drown in `match` expressions.

```rust
fn read_file() -> Result<String, std::io::Error> {
    let contents = std::fs::read_to_string("file.txt")?; // ? = blessed
    Ok(contents)
}
```

---

## Book V — The Heresies and Confessions

**5:1** `unsafe` is not forbidden, but thou must justify thyself before god, the code reviewer, and thy future self at 2am when the segfault cometh.

**5:2** `.clone()` is not a sin. It is a confession of weakness. Sometimes weakness is the correct architectural decision.

**5:3** `Rc<RefCell<T>>` is a sign that thou hath given up explaining thy ownership model to the borrow checker, and hath chosen to argue with it at runtime instead.

**5:4** *Thou shalt not fight the borrow checker.* For it is always right. When thou thinkest the borrow checker is wrong, thou art wrong about thy own code. Sit with this knowledge.

**5:5** Clippy is not thine enemy. Clippy is the still small voice of the compiler whispering *"this is needlessly complicated and you know it."*

**5:6** And lo, the C++ programmer shall come unto thee and say "this is unnecessary, I can manage memory myself." Let them speak. Nod. Say nothing. For they will return.

---

## Book VI — The Traits, Which Are Not Interfaces (They Are Different)

**6:1** *Thou shalt not reach for inheritance.* There is no inheritance. There are Traits, which are better, and if thou misseth inheritance thou hath not yet understood Traits.

**6:2** `impl Trait` and `dyn Trait` are not the same. One is resolved at compile time and is fast. The other liveth on the heap behind a fat pointer. Know which thou art using.

**6:3** The holy trinity of iterator traits: `Iterator`, `IntoIterator`, and `FromIterator`. Implement these on thy types and the compiler shall reward thee with `.map()`, `.filter()`, and `.collect()`, which are gifts beyond measure.

---

## Appendix — Proverbs of the Compiler

**A:1** *"cannot borrow `x` as mutable because it is also borrowed as immutable"* — thou hath violated the first commandment. Read Book II again.

**A:2** *"value used here after move"* — thou hath given thy value to another and then reached for it again like a fool.

**A:3** *"mismatched types"* — thou hath confused `&str` and `String` again. It is okay. Everyone doeth this. It passeth.

**A:4** *"the trait `Display` is not implemented for..."* — implement it. The compiler wilt not do it for thee. This is thy task.

**A:5** *"consider deriving `Clone`"* — the compiler is trying to help thee. Add `#[derive(Clone)]`. Let go of thy stubbornness.

---

*Here endeth the first edition of the Rust Bible.*

*May thy borrows be short, thy lifetimes well-annotated, and thy `cargo build` emit no warnings.*

*Transcribed in the year of Our Lord rustc 1.87.0 · compiled with `--release`*
