---
title: 'Rust: Embrace Captured Identifier in Format Strings'
date: 2025-11-16 08:39 +0200
tags:
- Rust
---

While playing with Rust recently I've noticed that most Rust tutorials suggest
writing code like this:

```rust
println!("Hello, {}!", get_person());                // implicit position
println!("Hello, {0}!", get_person());               // explicit index
println!("Hello, {person}!", person = get_person()); // named
```

Clippy (Rust's mighty linter), however, doesn't like it and suggests
doing this instead:

```rust
let person = get_person();
// ...
println!("Hello, {person}!"); // captures the local `person`

// This may also be used in formatting parameters:

let (width, precision) = get_format();
for (name, score) in get_scores() {
  println!("{name}: {score:width$.precision$}");
}

let number: f64 = 1.0;
let width: usize = 5;
println!("{number:>width$}");
```

I hope you'll agree that this looks cleaner. It certainly seems that way to me.

At this point you might be wondering what's the point of this article. Well,
I thought I had come across some recent Rust feature and this was the reason
why it wasn't adopted much in the wild. Turns out, however, that this was
introduced almost 4 years ago in [Rust 1.58](https://blog.rust-lang.org/2022/01/13/Rust-1.58.0/#captured-identifiers-in-format-strings).

I think there are two reasons why the new style hasn't taken off:

- There are many outdated tutorials out there
- LLMs were trained on large samples of code using the old style and they naturally favor it

If you ask something like ChatGPT how to print variables in Rust you'll get mostly answers like:

```rust
let x = 42;
println!("{}", x);
```

LLMs are great, but one certainly has to be aware of their limitations.

You also have to be aware of the limitations of the updated syntax - most importantly
it works only for variable names, as opposed to arbitrary Rust expressions:

```rust
# this won't work
let x = 42;
println!("{x + 1}");
```

Perhaps future versions of Rust will address this limitation. Time will tell.

So, that's all from me. Let's hope some LLMs will pick up on it and suggest
to more people the "new" and improved syntax for captured identifiers in
format strings. Keep hacking!
