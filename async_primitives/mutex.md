# Mutex
A programming mechanism that allows exlusive access to a resource (sharing resources).

### Mutex rules

* Only one task at a time can have control over a resource
* To manipulate an item protected by **Mutex**, you must request access

### Example

```rust
use tokio::sync::Mutex;

#[tokio::main]
async main() {
    let tv_channel = 10;
    let remote = Mutex::new(tv_channel);
}
```

### Make Mutext Thread Safe
We can't send same mutex to diferent threads, we can create a countable pointer to a mutex and send it instead.
```rust
use tokio::sync::Mutex;

#[tokio::main]
async main() {
    let tv_channel = 10;
    let remote = Mutex::new(tv_channel);
    let remote_arc = Arc::new(remote);

    let handle_1 = tokio::spawn(func(remote_arc.clone()));
    let handle_2 = tokio::spawn(func(remote_arc.clone()));
}
```
### Request Access to Mutex
A call to a **.lock()** method allows us to get access to a resource behind the mutex.
```rust
let tv_channel = remote_arc.lock().await;
*tv_channel = 32;
```

## More Examples

```rust
use std::sync::Arc;
use tokio::sync::Mutex;
use tokio::time::{sleep, Duration};

async fn person(remote_arc: Arc<Mutex<i32>>, name: String, new_channel: i32) {
    // request access to the remote
    let mut real_remote = remote_arc.lock().await;

    // changing the tv channel
    *real_remote = new_channel;

    println!("{} changed the channel", name);
    println!("Watching channel: {}", new_channel);


    sleep(Duration::from_sec(5)).await;
}

#[tokio::main]
async main() {
    let tv_channel = 10;
    let remote = Mutex::new(tv_channel);
    let remote_arc = Arc::new(remote);

    let mut task_handles = Vec::new();

    for (name, new_channel) in [("Oleg", 11), ("Tetiana", 32), ("Halyna", 43)] {
        task_handles.push(tokio::spawn(person(
            remote_arc.clone(), name.to_string(), new_channel
        )));
    }

    for handler in task_handlers {
        handler.await.unwrap();
    }
}
```
