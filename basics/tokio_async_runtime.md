# Who calls the first async function?
That happens in the tokio main macro

```rust
#[tokio::main]
 async fn main() {
    println!("Hello, world!");
 }
```

### #[tokio::main]
* Copies cod trom main
* Starts an async runtime
* Pastes code into the runtime and calls it

**It may look like this:**
```rust
fn main() {
    tokio::runtime::Builder::new_multi_thread()
        .enable_all() // enables all features
        .build() // returns a new runtime as a result
        .unwrap()
        .block_on( async { println!("Hello, World!"); }); // runs this block of code to completion
}
```
