# How does Rust know which type of code it is running?
Keywords:
* async
* await

**async** keyword lets compiler know that following code is asynchronous. We need to place it at a start of the async function definition.

**await** keyword is used as a method call on async function. It waits for the function state to change from **pending** to **done**. And then return a value associated with that function.

## How does Rust run an async code?
Running async code requires an async runtime. This runtimes allows us to start many functions at once and periodically check on them to see if they are done running. By default Rust doesn't provide us with an async runtime. Several crates do it instead. We are going to explore the most popular - **Tokio**.

## What is Tokio?
Tokio is a runtime for writing reliable network applications without compromising speed.
Tokio is event-driven, non-blocking I/O platform forwriting asyncronous applications with the Rust programming language.
It takes things that implements the future trait and returns the thing which is in the associated output type. It runs these futures and produces the output that a future represents the computation of.

```rust
#[tokio::main]
async fn main() {
    // your async code here
}
```

### Example:
```rust
async fn hello_world() -> String {
    "Hello World".to_string()
}

#[tokio::main]
async fn main() {
    let value = hello_world().await;
    println!("{}", value);
}
```