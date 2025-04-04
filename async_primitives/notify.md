# Notify
An alert signalling that event has occured. How to make sure that notification is received by the correct task? Notify struct allows that.
* Acts as both a transmitter and a receiver
* Create thread-safe clone of your notify instance to a task you want to receive an event
* Pass the clone to all functions that need them


### Receiver
Call **notified().await** on your notify instance if you want your notify clone to receive a notification.

### Transmitter
Call **notify_one()** or **notify_waiters()** on your notify instance. **notify_one()** will send a notification to a first receiver in your line of waiting receivers. **notify_waiters()** will send a notification to all waiting receivers.

## Example
We simulate buying things online with an emphasise on the notification you recieve that package has been delivered.

```rust
use std::sync::Arc;

use tokio::sync::Notify;
use tokio::time::{sleep, Duration};


async fn order_package(package_delivered: Arc<Notify>){
    sleep(Duration::from_secs(2)).await;
    println!("Find package in the warehouse");

    sleep(Duration::from_secs(2)).await;
    println!("Ship the package");

    sleep(Duration::from_secs(2)).await;
    println!("Package has been delivered");
    package_delivered.notify_one();
}

async fn grab_package(package_delivered: Arc<Notify>){
    package_delivered.notified().await;
    println!("Look outside of the house for a package");
    sleep(Duration::from_secs(2)).await;
    println!("Grab the package");
}

#[tokio::main]
async main() {
    let package_delivered = Notify::new();
    let package_delivered_arc = Arc::new(package_delivered);

    let order_package_handle = tokio::spawn(order_package(package_delivered_arc.clone()));

    let grab_package_handle = tokio::spawn(grab_package(package_delivered_arc.clone()));

    order_package_hadle.await.unwrap();
    grab_package_handle.await.unwrap();
}
```