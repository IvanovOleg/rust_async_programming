# Unit Testing of Async Code
Unit tests needs tobe async functions that run in async runtime.

```rust
#[tokio::test]
async fn test_add() {
    assert_eq!(add(1, 1).await, 2);
}
```
**#[tokio::test]** macro creates a single-threaded runtime by default.

In order to create a multi-threaded runtime use:

```rust
#[tokio::test(flavor="multi_thread", worker_threads=1)]
```