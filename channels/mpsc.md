# Multiple-producer Single-consumer Channel
Steps:
* Teacher assigns homework
* Students complete the homework
* Teacher collects all homework
* Teacher grades homework
* Repeat

```rust
async fn student(id: i32, tx: Arc<mpsc::Sender<String>>) {
    println!("Student {id} is getting his homework");

    tx.send(format!("Student {id}'s homework")).await.unwrap();
}

async fn teacher(mut rx: Arc<mpsc::Receiver<String>>) -> Vec<String> {
    let mut homework = Vec::new();

    while let Some(student_hw) = rx.recv().await {
        println!("Recieved homework: {}", &student_hw);
        homework.push(student_hw);
    }

    homework
}

#[tokio::main]
async fn main() {
    let (tx, rx) = mpsc::channel(100);
    let tx_arc = Arc::new(tx);
    ...
}
```

A channel closes when there are no more messages in transit and all of the transmitters have been dropped from memory. It will break the wile loop and continue with the function.