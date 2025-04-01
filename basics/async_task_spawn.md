# Tokio Task
A lightweight, non-blocking unit of execution.

## Join Handle
When you spawn a task it returns a join handle which can be considered as a bridge between a task and the main thread. Calling the await method to the joint handle will allow you to wait until that task is completed and gives you access to the return value.

### Join Handle Example
```rust
use tokio::task::JoinHandle;

async fn hello(name: &str) -> String {
    format!("Hello {}", name)
}

#[tokio::main]
async fn main() {
    let join_handle: JoinHandle<String> = tokio.spawn(hello("Oleg"));
    let value = join_handle.await.unwrap();
    println!("{}", value);
}
```