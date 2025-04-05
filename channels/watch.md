# Watch Channel
A single-producer, multiple-consumer channel typically used to monitor state changes of an object.

## Technical Details
* There is no guarantee that every message will be sent through the channel
* Only the most recent message is sent
* If two messages are sent one after the other and the receiver has yet to receive those messages, the older message will be dropped and the newest one will be sent.

```rust
async fn listens_for_changes(mut rx: watch::Receiver<Config>) {
    while rx.changed().await.is_ok() {
        let new_config = rx.borrow().clone();

        println!("New config is {:#?}", new_config);
    }
}


#[tokio::main]
async fn main() {
    let mut config = Config::new("db_path".to_string);

    let (tx, rx_1) = watch::cahnnel(config.clone());
    let rx_2 = tx.subscribe();

    let handle_1 = tokio::spawn(listens_for_changes(rx_1));
    let handle_2 = tokio::spawn(listens_for_changes(rx_2));

    config.update("new_db_path".to_string());
    tx.send(config).unwrap();

    // closing the channel
    drop(tx);

    handle_1.await.unwrap();
    handle_2.await.unwrap();
}
```
