# Tokio Streams

## TCP stream without arc mutex, actor pattern
Avoid putting TCP streams behind the arc mutex since it may introiduce performance issues. Use this method instead:
```rust
#[tokio::main]
async fn main() {
    let tx,rx = tokio::sync::mpsc::Channel(8); // 8 is concurrency
    let tcp = tokio::net::TcpStream::connect("127.0.0.1:8080").await.unwrap();
    tokio::spawn(async move {
        while let Some(bytes) = rx.next().await {
            tcp.write_all(bytes).await.unwrap();
            // or read from the tcp stream and send to a channel
        }
    });

    tx.send(vec![0, 1, 2, 3, 4]);

    // or also we can use multiple task that each can send data individually

    {
        let tx = tx.clone();
        tokio::spawn(async move {
            loop {
                tx.send(vec![0, 1, 2, 3, 4]).await; // we need to allocate a vector for each sequence of bytes you want to send
            }
        });
    }

    {
        let tx = tx.clone();
        tokio::spawn(async move {
            loop {
                tx.send(vec![0, 1, 2, 3, 4]).await; // advantage is that this structure doesn't need to wait, and no need for mutex around the stream
            }
        });
    }
}
```

## TCP stream without arc mutex, actor pattern with responses
Avoid putting TCP streams behind the arc mutex since it may introiduce performance issues. Use this method instead:
```rust
#[tokio::main]
async fn main() {
    let tx,rx = tokio::sync::mpsc::Channel(8); // 8 is concurrency
    let tcp = tokio::net::TcpStream::connect("127.0.0.1:8080").await.unwrap();
    tokio::spawn(async move {
        while let Some((bytes, ack)) = rx.next().await {
            ack.send(tcp.write_all(bytes).await);
            // or read from the tcp stream and send to a channel
        }
    });

    tx.send(vec![0, 1, 2, 3, 4]);

    // to insure that the response will be sent to the one who sent a request

    {
        let tx = tx.clone();
        tokio::spawn(async move {
            loop {
                let (syn, ack) = tokio::sync::oneshot::channel();
                tx.send((vec![0, 1, 2, 3, 4], syn)).await;
                let num_written = ack.await.unwrap();
            }
        });
    }

    {
        let tx = tx.clone();
        tokio::spawn(async move {
            loop {
                let (syn, ack) = tokio::sync::oneshot::channel();
                tx.send((vec![0, 1, 2, 3, 4], syn)).await;
                let num_written = ack.await.unwrap();
            }
        });
    }
}
```

## When it comes to the tokio stream we need to be able to tell a stream how to separate bytes in frames. This is a codec.
Try to use tokio_util::codec, tokio_stream crates
