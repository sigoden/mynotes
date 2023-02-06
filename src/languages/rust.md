# Rust

## Installation

```sh
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## Rustup

### Concepts

- target: `x86_64-pc-windows-msvc`, `x86_64-unknown-linux-gnu`, `wasm32-unknown-unknown`  
- toolchain: `stable-x86_64-pc-windows-msvc`, `nightly-2021-05-11-x86_64-unknown-linux-gnu`
- component: `rustc`,`rustsrc`,`rustfmt`, `rust-analyzer`

### Snippets

```sh
rustup update 

rustup toolchain install nightly

rustup target add wasm32-unknown-unknown --toolchain nightly

rustup default nightly

rustup override set nightly
```

## Crates 

- [tokio](https://docs.rs/tokio): Async 
- [crossbeam](https://docs.rs/crossbeam): Concurrency
- [rayon](https://docs.rs/rayon): Concurrency computation
- [axum](https://docs.rs/axum): web framework
- [poem](https://docs.rs/poem): web framework，supports openapi
- [sqlx](https://docs.rs/sqlx): sql
- [sea-orm](https://docs.rs/sea-orm): orm
- [reqwest](https://docs.rs/reqwest): http client
- [anyhow](https://docs.rs/anyhow): generic error framework
- [thiserror](https://docs.rs/thiserror): library error framework
- [clap](https://docs.rs/clap): cli framework
- [nom](https://docs.rs/nom): bytes/charactor parser
- [assert_cmd](https://docs.rs/assert_cmd): cli test framework
- [insta](https://docs.rs/insta): snapshot test framework

## Cargo

### Plugins

```sh
cargo install cargo-outdated
cargo install cargo-insta
```
### Snippets

```sh
cargo add serde
cargo add serde -F derive
cargo add -D serde
cargo add -B serde

cargo tree
cargo tree -p libc
cargo tree -p libc -i
```

### Cargo.toml


```sh
[profile.release]
lto = true
strip = true
opt-level = "z"
```

## Resources

- [Rust Language Cheat Sheet](https://cheats.rs/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example)
- [Rust Design Patterns](https://rust-unofficial.github.io/patterns/intro.html)
- [Rust Atomics and Locks](https://marabos.nl/atomics/)
- [learn project: toydb](https://github.com/erikgrinaker/toydb)
- [learn project: mini-redis](https://github.com/tokio-rs/mini-redis)
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/about.html)
- [pretzelhammer Blog](https://github.com/pretzelhammer/rust-blog)
- [Jon Gjengset Youtube](https://www.youtube.com/channel/UC_iD0xppBwwsrM9DegC5cQQ)