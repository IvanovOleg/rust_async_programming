# Calling non-async function in async task
**Example:**
* You call a non-async function within your async code that takes 10 seconds to run.
* What is your async code doing during those 10 seconds? Nothing!

### await Call
An async runtime makes progress by jumping betwen functions, working on each for a short amount of time. These jumps happen when we hit the **await** call in our code.

### Starving a function
When the runtime is unable to jump to a function and make a progress. Each time we run sync code in async function we have a risk of starving of our async function.

To ensure  we don't starv our function with our sync block of code, we need to spawn a blocking task.

### Spawn Blocking Tasks
* Run in their own blocking threads
* Do not block other tasks since they don't share execution resources

```rust
let join_handle = tokio::task::spawn_blocking(slow_function);
```

We need to call **await** in order to get a return from the blocking task.
