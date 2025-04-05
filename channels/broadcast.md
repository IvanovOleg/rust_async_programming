# Broadcast Channel
A multiple-producer, multiple-consumer channe. In a group chat each member is a producer and a consumer at the same time. Each person has access to a producer and a receiver.

```rust
#[tokio::main]
async fn main() {
    let (tx, _rx) = broadcast::channel(100);

    let tx_clone_1 = tx.clone();
    let tx_clone_2 = tx_clone_1.clone();
    let tx_clone_3 = tx_clone_1.clone();

    let mut rx_clone_1 = tx_clone_1.subscribe();
    let mut rx_clone_2 = tx_clone_1.subscribe();
    let mut rx_clone_3 = tx_clone_2.subscribe();

    ...
}
```

If a member of the chat is unavailable, should they receive messages that they missed? Yes! Only messages in the buffer will be sent. Buffer is uset as the queue. When the member backs online, every messages i nthe queue will be sent to him.

## Questions
* How many messages can this buffer hold?
* What happens when you have more messages than the buffer can hold?

In tokio we set the buffer size when we initialize the broadcast channel. In our example it is **100**.

If a full buffer recieves a new message, oldest message gets dropped to make room for new message (added to the end of the buffer). To inform the programm that messages were dropped, the reciever will return **RecvErr:Lagged** and then coninue tu return the rest of messages. **RecvErr:Lagged** gives the programmer a chanse to implement error handling for dropped messages.