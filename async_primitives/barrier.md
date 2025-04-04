# Barrier
A **barrier** is an async primitive that insures a certain number of tasts reach a rendezvous point before they can proceed with treir execution. Barriers are not used alone.

## Monitor the flow of the barier
* Pause the flow of tasks
* Pause and resume using **notify()**

### Example
Soda company produces packs of soda (batches of 12). We need to wait for 12 cans to be ready in order to pack them.

```rust
use std::sync::Arc;

use tokio::sync::{Barrier, BarrierWaitResult, Notify};
use tokio::time::{sleep, Duration};

async fn barrier_example(barrier: Arc<Barrier>, notify: Arc<Notify>) -> BarrierWaitResult {
    println!("Waiting at barrier");
    let wait_result = barrier.wait().await;
    println!("Passed through the barrier");

    if wait_result.is_leader() {
        notify.notify_one();
    }

    wait_result
}

#[tokio::main]
async fn main() {
    let total_cans_needed = 12;
    let barrier = Arc::new(Barrier::new(total_cans_needed));

    let notify = Arc::new(Notify::new());

    // To send the first batch of cans to the barrier
    notify.notify_one();

    let mut task_handles = Vec::new();

    for can_count in 0..60 {
        if can_count % 12 == 0 {
            notify.notified().await;

            // Give the barrier some time to close
            sleep(Duration::from_millis(1)).await;
        }

        task_handles.push(tokio::spawn(
            barrier_example(barrier.clone(), notify.clone())
        ))
    }

    let mut number_of_leaders = 0;
    for handle in task_handles {
        let wait_result = handle.await.unwrap();

        if wait_result.is_leader() {
            number_of_leaders += 1;
        }
    }

    println!("total num of leaders: {}", number_of_leaders);
}
```

The **.is_leader()** method is used with the **BarrierWaitResult** type in **tokio::sync**. When a task reaches a barrier, it waits until a specified number of tasks (the barrier count) arrive at the same point. Once all tasks have reached the barrier, one of them is designated as the "leader."

The **.is_leader()** method returns **true** for the task that has been chosen as the leader and false for all others. This is useful for performing actions that should only be done once, like notifying another process or performing cleanup, without duplicating the effort across tasks.

In our code, the leader task uses **notify.notify_one()** to signal progress to other parts of the program. It ensures only the designated leader handles this notification responsibility.
