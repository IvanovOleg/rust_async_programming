# Tokio Streams

## TCP stream without arc mutex, actor pattern
Avoid putting TCP streams behind the arc mutex since it may introiduce performance issues. Use this method instead:
```rust
use tokio::io::AsyncWriteExt;

#[tokio::main]
async fn main() {
    let (tx, mut rx) = tokio::sync::mpsc::channel::<Vec<u8>>(8); // 8 is a buffer
    let mut tcp = tokio::net::TcpStream::connect("127.0.0.1:8080")
        .await
        .unwrap();
    tokio::spawn(async move {
        while let Some(bytes) = rx.recv().await {
            tcp.write_all(&bytes).await.unwrap();
        }
    });

    let data = vec![0, 1, 2, 3, 4];
    tx.send(data).await.unwrap();

    // or also we can use multiple task that each can send data individually
    {
        let tx = tx.clone();
        tokio::spawn(async move {
            loop {
                let data = vec![0, 1, 2, 3, 4];
                tx.send(data).await.unwrap();
            }
        });
    }

    {
        let tx = tx.clone();
        tokio::spawn(async move {
            loop {
                let data = vec![0, 1, 2, 3, 4];
                tx.send(data).await.unwrap();
            }
        });
    }
}
```

## TCP stream without arc mutex, actor pattern with responses
Avoid putting TCP streams behind the arc mutex since it may introduce performance issues. Use this method instead:
```rust
use tokio::io::AsyncWriteExt;

#[tokio::main]
async fn main() {
    let (tx, mut rx) = tokio::sync::mpsc::channel::<(Vec<u8>, tokio::sync::oneshot::Sender<Result<usize, std::io::Error>>)>(8);
    let mut tcp = tokio::net::TcpStream::connect("127.0.0.1:8080").await.unwrap();
    tokio::spawn(async move {
        while let Some((bytes, ack)) = rx.recv().await {
            let result = tcp.write_all(&bytes).await;
            let _ = ack.send(result.map(|_| bytes.len())); // or read from the tcp stream and send to a channel
        }
    });

    // to insure that the response will be sent to the one who sent a request
    {
        let tx = tx.clone();
        tokio::spawn(async move {
            loop {
                let (syn, ack) = tokio::sync::oneshot::channel();
                tx.send((vec![0, 1, 2, 3, 4], syn)).await.unwrap();
                let num_written = ack.await.unwrap().unwrap();
                println!("Wrote {} bytes", num_written);
            }
        });
    }

    {
        let tx = tx.clone();
        tokio::spawn(async move {
            loop {
                let (syn, ack) = tokio::sync::oneshot::channel();
                tx.send((vec![0, 1, 2, 3, 4], syn)).await.unwrap();
                let num_written = ack.await.unwrap().unwrap();
                println!("Wrote {} bytes", num_written);
            }
        });
    }
}
```

## When it comes to the tokio stream we need to be able to tell a stream how to separate bytes in frames. This is a codec.
Try to use tokio_util::codec, tokio_stream crates
