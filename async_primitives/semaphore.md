# Semaphore
An async primitive that limits access to a resource or a critical section of the code. Let's imagine that we have a function that runs multiple times at once and each execution happens in it's own async task. A semaphore reference is passed to each function. Each function is responsible for requesting or acquiring a permit from it's semaphore reference. Once a function has a permit it may proceed to the resource. If all permits are in use, then next function needs to wait until one permit is available.

### Example
People are waiting in line to a bank teller. We need to limit access to a teller.

```rust
use std::sync::Arc;
use tokio::sync::Semaphore;
use tokio::time::{sleep, Duration};

async fn person(semaphore:: Arc<Semaphore>, name: String){
    println!("{} is waiting in line", name);

    teller(semaphore, name).await;
}

async fn teller(semaphore: Arc<Semaphore>, customer: String){
    let permit = semaphore.acquire().await.unwrap();

    sleep(Duration::from_secs(2)).await;
    println!("{} is being served by the teller", customer);
    sleep(Duration::from_secs(5)).await;
    println!("{} is no leaving the teller", customer);

    drop(permit); // it will happen anywey at the end of the function, it is unnecessary to define it like this
}

#[tokio::main]
async main() {
    let num_of_tellers = 4;
    let semaphore = Semaphore::new(num_of_tellers);
    let semaphore_arc = Arc::new(semaphore);

    let mut people_handles = Vec::new();
    for num in 0..10 {
        people_handles.push(tokio::spawn(person(
            semaphore_arc.clone(),
            format!("Person_{num}")
        )));
    }

    for handle in people_handles {
        handle.await.unwrap();
    }
}
```
