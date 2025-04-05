# Oneshot Channel
Single-producer, single-consumer channel that can send one message from a transmitter to the reciever. The message doesn't self-destruct. The transmitter gets destroyed which makes the receiver useless.

### Steps:
1. Create a oneshot channel
```rust
    let tx, rx = oneshot::channel();
```
2. Pass tx to task (spy agency) **tx = transmitter**
3. Pass rx to task (spy) **rx = receiver**
4. Send a message
```rust
tx.send("spy message").unwrap();
```

## tokio::select!
Allows you to wait for multiple concurrent branches while only returning the first branch that completes. Once that happens all other branches get cancelled.

```rust
async fn double_agent(
    mut rx_1: oneshot::Receiver<String>,
    mut rx_2: oneshot::Receiver<String>
) {
    let msg = tokio::select! {
        msg_1=&mut rx_1 => msg_1.unwrap(),
        msg_2=&mut rx_2 => msg_2.unwrap(),
    };
    println!("Spy has received the message: {}", msg);
}
```