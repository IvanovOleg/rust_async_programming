# Read Write Lock
You have multiple readers and multiple writers concurrently accessing a single source.

### Writers
* Modify the file
* Exclusive access

### Readers
* Do not modify the file
* Allow concurrent reads

**Solution:**
```rust
// Initialization
let file=5;
let lock = RwLock::new(file);

// Reader
let reader_1 = lock.read().await;

// Writer
let writer = lock.write().await;
```

## Example
```rust
use std::sync::Arc;

use::tokio::sync::RwLock;
use tokio::time::{sleep, Duration};


async fn read_from_document(id: i32, document: Arc<RwLock<String>>) {
    let reader = document.read().await;

    println!("reader_{}: {}", id, reader);
}

async fn write_to_document(document: Arc<RwLock<String>>, new_string: &str) {
    let mut writer = document.write().await;

    writer.push_str(new_string);
    writer.push_str(" ");
}

#[tokio::main]
async fn main() {
    let document = Arc::new(RwLock::new("".to_string()));

    let mut handles = Vec::new();
    for new_string in "I can read and write a b c d e f g h".split_whitespace() {
        handles.push(tokio::spawn(read_from_document(1, document.clone())));
        handles.push(tokio::spawn(write_to_document(document.clone(), new_string)));
        sleep(Duration::from_millis(1)).await;
        handles.push(tokio::spawn(read_from_document(2, document.clone())));
        handles.push(tokio::spawn(read_from_document(3, document.clone())));
    }

    for handle in handles {
        handle.await.unwrap();
    }
}
```